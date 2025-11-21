# Plan de Migración de Datos - Fase 7: Sistema de Mensajería y Chat

**📋 Proyecto:** InmoTech - Sistema Integral de Gestión Inmobiliaria  
**📊 Fase:** 07 - Sistema de Mensajería y Chat  
**📅 Fecha del Plan:** 20/11/2025  
**👤 Especialista en Migración:** Patricia Jiménez - Data Migration Lead  
**🔍 Revisado por:** Ana García - Database Architect  

---

## 📋 Resumen Ejecutivo del Plan de Migración

### 🎯 Objetivo de la Migración
Establecer una migración segura y eficiente de datos relacionados con comunicaciones existentes hacia el nuevo sistema de mensajería y chat en tiempo real, garantizando integridad, disponibilidad y minimización del tiempo de inactividad durante la transición.

### 📊 Alcance de la Migración
```yaml
Datos a Migrar:
  💬 Conversaciones existentes del sistema legacy (Email threads)
  👥 Información de participantes y relaciones
  📱 Configuraciones de notificaciones por usuario
  🔐 Preferencias de privacidad y configuración de chat
  📊 Historial de interacciones agente-cliente
  📁 Archivos adjuntos de comunicaciones previas

Datos Nuevos a Crear:
  🆔 Identificadores únicos para conversaciones (UUID)
  🔄 Estados de mensajes (enviado, entregado, leído)
  ⏰ Timestamps normalizados y metadata de sistema
  🏷️ Etiquetas y categorización automática
  📈 Métricas base para analytics de chat
```

### ⏱️ Ventana de Migración
```yaml
Duración Total: 12 horas (Viernes 21:00 - Sábado 09:00)
  Preparación: 2 horas (21:00 - 23:00)
  Migración Core: 6 horas (23:00 - 05:00)  
  Validación: 3 horas (05:00 - 08:00)
  Rollback Buffer: 1 hora (08:00 - 09:00)

Downtime Estimado: 4 horas (01:00 - 05:00)
Usuarios Afectados: 2,847 usuarios activos
Impacto en Negocio: Mínimo (weekend maintenance window)
```

---

## 🏗️ Arquitectura de Migración

### 📊 Mapeo de Datos Origen → Destino

#### Sistema Legacy (Email/Phone Logs)
```sql
-- Estructura de Datos Actual
legacy_communications (
  id INT PRIMARY KEY,
  user_id INT,
  agent_id INT,
  property_id INT,
  message_content TEXT,
  communication_type ENUM('email', 'phone', 'sms'),
  created_at TIMESTAMP,
  attachments JSON
)

legacy_user_preferences (
  user_id INT,
  email_notifications BOOLEAN,
  sms_notifications BOOLEAN,
  preferred_communication VARCHAR(20)
)
```

#### Sistema Nuevo (Chat Integrado)
```sql
-- Nueva Estructura Optimizada
conversations (
  id UUID PRIMARY KEY,
  title VARCHAR(255),
  type ENUM('direct', 'group', 'support') DEFAULT 'direct',
  property_id UUID,
  created_by UUID,
  last_message_at TIMESTAMP,
  is_archived BOOLEAN DEFAULT false,
  metadata JSON,
  migrated_from VARCHAR(50), -- Trackear origen de migración
  created_at TIMESTAMP DEFAULT NOW()
)

conversation_participants (
  id UUID PRIMARY KEY,
  conversation_id UUID REFERENCES conversations(id),
  user_id UUID REFERENCES users(id),
  role ENUM('owner', 'participant') DEFAULT 'participant',
  joined_at TIMESTAMP DEFAULT NOW(),
  last_read_at TIMESTAMP,
  is_active BOOLEAN DEFAULT true
)

messages (
  id UUID PRIMARY KEY,
  conversation_id UUID REFERENCES conversations(id),
  sender_id UUID REFERENCES users(id),
  content TEXT,
  message_type ENUM('text', 'file', 'system') DEFAULT 'text',
  file_id UUID,
  metadata JSON,
  migrated_from_id INT, -- Link a mensaje original
  created_at TIMESTAMP,
  
  INDEX idx_conversation_created (conversation_id, created_at),
  INDEX idx_migration_tracking (migrated_from_id)
)
```

### 🔄 Proceso de Transformación de Datos

#### Etapa 1: Agrupación de Comunicaciones
```sql
-- Query para agrupar comunicaciones por contexto
CREATE TEMPORARY TABLE migration_conversations AS
SELECT 
  UUID() as new_conversation_id,
  COALESCE(property_id, 0) as property_context,
  user_id,
  agent_id,
  MIN(created_at) as first_communication,
  MAX(created_at) as last_communication,
  COUNT(*) as message_count,
  GROUP_CONCAT(id) as legacy_message_ids
FROM legacy_communications 
WHERE created_at >= DATE_SUB(NOW(), INTERVAL 2 YEAR) -- Solo últimos 2 años
GROUP BY property_id, user_id, agent_id
HAVING message_count >= 3; -- Solo conversaciones con engagement mínimo
```

#### Etapa 2: Creación de Conversaciones
```sql
-- Script de conversión de conversaciones
INSERT INTO conversations (
  id, title, type, property_id, created_by, 
  last_message_at, metadata, migrated_from, created_at
)
SELECT 
  new_conversation_id,
  CONCAT('Conversación sobre Propiedad #', property_context),
  'direct',
  CASE WHEN property_context > 0 THEN 
    (SELECT uuid FROM properties WHERE legacy_id = property_context) 
    ELSE NULL 
  END,
  (SELECT uuid FROM users WHERE legacy_id = user_id),
  last_communication,
  JSON_OBJECT(
    'legacy_message_count', message_count,
    'migration_date', NOW(),
    'original_participants', JSON_ARRAY(user_id, agent_id)
  ),
  'legacy_email_phone',
  first_communication
FROM migration_conversations;
```

#### Etapa 3: Migración de Participantes
```sql
-- Insertar participantes (usuarios)
INSERT INTO conversation_participants (
  id, conversation_id, user_id, role, joined_at
)
SELECT 
  UUID(),
  mc.new_conversation_id,
  u.id,
  'owner',
  mc.first_communication
FROM migration_conversations mc
INNER JOIN users u ON u.legacy_id = mc.user_id;

-- Insertar participantes (agentes)
INSERT INTO conversation_participants (
  id, conversation_id, user_id, role, joined_at
)
SELECT 
  UUID(),
  mc.new_conversation_id,
  u.id,
  'participant',
  mc.first_communication
FROM migration_conversations mc
INNER JOIN users u ON u.legacy_id = mc.agent_id;
```

---

## 📁 Migración de Archivos y Attachments

### 🔄 Estrategia de Migración de Files

#### Inventario de Archivos Existentes
```yaml
Tipos de Archivos Legacy:
  📄 Documentos PDF: 15,847 archivos (2.3 GB)
  📊 Spreadsheets Excel: 3,291 archivos (845 MB)
  🖼️ Imágenes JPG/PNG: 8,456 archivos (1.7 GB)
  📝 Documentos Word: 2,103 archivos (567 MB)
  🗂️ Archives ZIP: 891 archivos (3.2 GB)
  
Total a Migrar: 30,588 archivos (8.6 GB)

Ubicaciones Actuales:
  - /legacy/email_attachments/ (5.2 GB)
  - /legacy/uploaded_docs/ (2.8 GB)
  - /legacy/temp_files/ (0.6 GB - revisar validez)
```

#### Proceso de Migración de Files
```bash
#!/bin/bash
# Script de migración de archivos adjuntos

# Fase 1: Validación y catalogación
echo "Iniciando catalogación de archivos..."
find /legacy/email_attachments -type f -exec file {} \; > /tmp/file_inventory.txt
find /legacy/uploaded_docs -type f -exec file {} \; >> /tmp/file_inventory.txt

# Fase 2: Filtrado de archivos válidos (no temp, no corruptos)
python3 migrate_files.py --validate --source=/legacy/ --log=/tmp/validation.log

# Fase 3: Copia segura con verificación de integridad
rsync -avz --checksum --progress \
  /legacy/email_attachments/ \
  /new_storage/chat_files/migrated/

# Fase 4: Actualización de referencias en BD
mysql -u migration_user -p inmotech_db < update_file_references.sql

# Fase 5: Verificación final
python3 verify_file_migration.py --target=/new_storage/chat_files/migrated/
```

### 📊 Script de Actualización de Referencias
```sql
-- Crear tabla temporal para mapear archivos
CREATE TEMPORARY TABLE file_migration_map (
  legacy_path VARCHAR(500),
  new_file_uuid UUID,
  original_filename VARCHAR(255),
  file_size BIGINT,
  mime_type VARCHAR(100),
  created_at TIMESTAMP
);

-- Procesar attachments de legacy communications
INSERT INTO files (
  id, filename, file_path, file_size, mime_type, 
  uploaded_by, uploaded_at, is_temporary, metadata
)
SELECT 
  UUID() as id,
  JSON_UNQUOTE(JSON_EXTRACT(attachments, '$[0].filename')) as filename,
  CONCAT('/migrated/', JSON_UNQUOTE(JSON_EXTRACT(attachments, '$[0].path'))),
  JSON_UNQUOTE(JSON_EXTRACT(attachments, '$[0].size')),
  JSON_UNQUOTE(JSON_EXTRACT(attachments, '$[0].mime_type')),
  (SELECT uuid FROM users WHERE legacy_id = user_id),
  created_at,
  false,
  JSON_OBJECT(
    'migrated_from', 'legacy_communication',
    'original_communication_id', id
  )
FROM legacy_communications 
WHERE attachments IS NOT NULL 
AND JSON_LENGTH(attachments) > 0;
```

---

## 🔐 Migración de Configuraciones y Preferencias

### 👤 User Preferences Migration

#### Mapeo de Configuraciones Legacy
```sql
-- Migración de preferencias de notificación
INSERT INTO user_chat_settings (
  id, user_id, email_notifications, push_notifications, 
  sound_enabled, desktop_notifications, mobile_notifications,
  created_at, migrated_from
)
SELECT 
  UUID(),
  u.id,
  CASE WHEN lup.email_notifications = 1 THEN true ELSE false END,
  CASE WHEN lup.sms_notifications = 1 THEN true ELSE false END,
  true, -- Default sound enabled
  true, -- Default desktop notifications
  CASE WHEN lup.sms_notifications = 1 THEN true ELSE false END,
  NOW(),
  'legacy_user_preferences'
FROM legacy_user_preferences lup
INNER JOIN users u ON u.legacy_id = lup.user_id;

-- Configuraciones de privacidad por defecto
INSERT INTO user_privacy_settings (
  id, user_id, show_online_status, allow_unknown_contacts,
  message_preview_enabled, read_receipts_enabled
)
SELECT 
  UUID(),
  u.id,
  true, -- Default visible
  false, -- Solo contactos conocidos
  true, -- Preview habilitado
  true -- Read receipts habilitados
FROM users u
WHERE u.migrated_from = 'legacy_system';
```

### 🏢 Agent-Specific Configurations
```sql
-- Configuraciones específicas para agentes
INSERT INTO agent_chat_config (
  id, agent_id, auto_response_enabled, business_hours_only,
  max_concurrent_chats, response_time_target, 
  template_messages, created_at
)
SELECT 
  UUID(),
  u.id,
  false, -- Auto-response deshabilitado inicialmente
  true, -- Solo horario de negocio
  5, -- Máximo 5 chats concurrentes
  INTERVAL 15 MINUTE, -- Target 15 minutos respuesta
  JSON_ARRAY(
    'Gracias por contactarnos. En breve le responderemos.',
    'Estamos revisando su consulta sobre la propiedad.',
    'Le enviaremos más información por este medio.'
  ),
  NOW()
FROM users u
INNER JOIN user_roles ur ON ur.user_id = u.id
INNER JOIN roles r ON r.id = ur.role_id
WHERE r.name IN ('agent', 'agent_senior', 'manager');
```

---

## ⚡ Scripts de Migración y Automatización

### 🔄 Script Principal de Migración
```python
#!/usr/bin/env python3
# migration_coordinator.py - Coordina toda la migración

import logging
import subprocess
import mysql.connector
from datetime import datetime
import json
import os

class ChatMigrationCoordinator:
    def __init__(self):
        self.setup_logging()
        self.db_config = {
            'host': os.getenv('DB_HOST'),
            'user': os.getenv('DB_USER'),
            'password': os.getenv('DB_PASS'),
            'database': os.getenv('DB_NAME')
        }
        
    def setup_logging(self):
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler('/var/log/inmotech/migration.log'),
                logging.StreamHandler()
            ]
        )
        self.logger = logging.getLogger(__name__)

    def run_migration(self):
        """Ejecuta el proceso completo de migración"""
        try:
            self.logger.info("🚀 Iniciando migración de datos de chat...")
            
            # Fase 1: Pre-validación
            self.validate_source_data()
            self.create_backup()
            
            # Fase 2: Migración de esquema
            self.deploy_new_schema()
            
            # Fase 3: Migración de datos
            self.migrate_conversations()
            self.migrate_messages()
            self.migrate_participants()
            
            # Fase 4: Migración de archivos
            self.migrate_attachments()
            
            # Fase 5: Configuraciones
            self.migrate_user_preferences()
            self.setup_default_configs()
            
            # Fase 6: Validación final
            self.validate_migration()
            self.update_search_indexes()
            
            self.logger.info("✅ Migración completada exitosamente")
            return True
            
        except Exception as e:
            self.logger.error(f"❌ Error durante migración: {str(e)}")
            self.rollback_migration()
            return False

    def validate_source_data(self):
        """Valida integridad de datos origen"""
        self.logger.info("📊 Validando datos origen...")
        
        conn = mysql.connector.connect(**self.db_config)
        cursor = conn.cursor()
        
        # Verificar conteos de registros
        validation_queries = [
            ("SELECT COUNT(*) FROM legacy_communications", "comunicaciones legacy"),
            ("SELECT COUNT(*) FROM legacy_user_preferences", "preferencias usuario"),
            ("SELECT COUNT(DISTINCT user_id) FROM legacy_communications", "usuarios únicos"),
            ("SELECT COUNT(DISTINCT agent_id) FROM legacy_communications", "agentes únicos")
        ]
        
        for query, description in validation_queries:
            cursor.execute(query)
            count = cursor.fetchone()[0]
            self.logger.info(f"   - {description}: {count:,} registros")
            
        cursor.close()
        conn.close()

    def migrate_conversations(self):
        """Migra conversaciones agrupando comunicaciones relacionadas"""
        self.logger.info("💬 Migrando conversaciones...")
        
        sql_file = "/migration/scripts/create_conversations.sql"
        self.execute_sql_file(sql_file)
        
        # Log del progreso
        conn = mysql.connector.connect(**self.db_config)
        cursor = conn.cursor()
        cursor.execute("SELECT COUNT(*) FROM conversations WHERE migrated_from = 'legacy_email_phone'")
        count = cursor.fetchone()[0]
        self.logger.info(f"   ✅ {count:,} conversaciones migradas")
        cursor.close()
        conn.close()

    def migrate_messages(self):
        """Migra mensajes individuales"""
        self.logger.info("📝 Migrando mensajes...")
        
        # Procesar en lotes para evitar memory issues
        batch_size = 1000
        offset = 0
        
        conn = mysql.connector.connect(**self.db_config)
        cursor = conn.cursor()
        
        # Obtener total de mensajes
        cursor.execute("SELECT COUNT(*) FROM legacy_communications")
        total_messages = cursor.fetchone()[0]
        
        while offset < total_messages:
            self.logger.info(f"   Procesando lote {offset:,} - {offset + batch_size:,}")
            
            query = f"""
            INSERT INTO messages (
                id, conversation_id, sender_id, content, 
                message_type, metadata, migrated_from_id, created_at
            )
            SELECT 
                UUID(),
                mc.new_conversation_id,
                u.id,
                lc.message_content,
                CASE lc.communication_type
                    WHEN 'email' THEN 'text'
                    WHEN 'sms' THEN 'text' 
                    ELSE 'text'
                END,
                JSON_OBJECT(
                    'original_type', lc.communication_type,
                    'migration_batch', {offset // batch_size}
                ),
                lc.id,
                lc.created_at
            FROM legacy_communications lc
            INNER JOIN migration_conversations mc ON FIND_IN_SET(lc.id, mc.legacy_message_ids)
            INNER JOIN users u ON u.legacy_id = lc.user_id
            LIMIT {batch_size} OFFSET {offset}
            """
            
            cursor.execute(query)
            offset += batch_size
            
        conn.commit()
        cursor.close()
        conn.close()
        
        self.logger.info(f"   ✅ {total_messages:,} mensajes migrados")

    def execute_sql_file(self, file_path):
        """Ejecuta archivo SQL"""
        with open(file_path, 'r') as file:
            sql_content = file.read()
            
        conn = mysql.connector.connect(**self.db_config)
        cursor = conn.cursor()
        
        # Ejecutar cada statement
        for statement in sql_content.split(';'):
            if statement.strip():
                cursor.execute(statement)
                
        conn.commit()
        cursor.close()
        conn.close()

    def validate_migration(self):
        """Valida que la migración fue exitosa"""
        self.logger.info("🔍 Validando migración...")
        
        conn = mysql.connector.connect(**self.db_config)
        cursor = conn.cursor()
        
        validation_checks = [
            {
                'name': 'Conversaciones migradas',
                'query': "SELECT COUNT(*) FROM conversations WHERE migrated_from IS NOT NULL",
                'expected_min': 100
            },
            {
                'name': 'Mensajes migrados',
                'query': "SELECT COUNT(*) FROM messages WHERE migrated_from_id IS NOT NULL",
                'expected_min': 1000
            },
            {
                'name': 'Participantes asignados',
                'query': "SELECT COUNT(*) FROM conversation_participants",
                'expected_min': 200
            },
            {
                'name': 'Archivos migrados',
                'query': "SELECT COUNT(*) FROM files WHERE JSON_EXTRACT(metadata, '$.migrated_from') IS NOT NULL",
                'expected_min': 50
            }
        ]
        
        all_passed = True
        for check in validation_checks:
            cursor.execute(check['query'])
            count = cursor.fetchone()[0]
            
            if count >= check['expected_min']:
                self.logger.info(f"   ✅ {check['name']}: {count:,} registros")
            else:
                self.logger.error(f"   ❌ {check['name']}: {count:,} registros (esperado mínimo: {check['expected_min']})")
                all_passed = False
                
        cursor.close()
        conn.close()
        
        if not all_passed:
            raise Exception("Falló la validación de migración")

if __name__ == "__main__":
    coordinator = ChatMigrationCoordinator()
    success = coordinator.run_migration()
    exit(0 if success else 1)
```

### 🛡️ Script de Backup Pre-Migración
```bash
#!/bin/bash
# backup_pre_migration.sh

BACKUP_DIR="/backups/pre_chat_migration_$(date +%Y%m%d_%H%M%S)"
mkdir -p $BACKUP_DIR

echo "🔄 Creando backup completo antes de migración..."

# Backup de base de datos
mysqldump --single-transaction --routines --triggers \
  --host=$DB_HOST --user=$DB_USER --password=$DB_PASS \
  inmotech_db > $BACKUP_DIR/database_full_backup.sql

# Backup de archivos legacy
tar -czf $BACKUP_DIR/legacy_files.tar.gz /legacy/email_attachments /legacy/uploaded_docs

# Backup de configuraciones
cp -r /etc/inmotech/ $BACKUP_DIR/config_backup/

# Verificación de backups
if [ -f "$BACKUP_DIR/database_full_backup.sql" ] && [ -f "$BACKUP_DIR/legacy_files.tar.gz" ]; then
    echo "✅ Backup completado exitosamente en: $BACKUP_DIR"
    echo "📊 Tamaño del backup:"
    du -sh $BACKUP_DIR/*
else
    echo "❌ Error durante creación de backup"
    exit 1
fi
```

---

## 🔄 Plan de Rollback y Contingencias

### 🚨 Procedimiento de Rollback Rápido

#### Criterios para Activar Rollback
```yaml
Automatico (Immediate):
  🔴 Data corruption detectada (>5% registros)
  🔴 Performance degradation >300% baseline
  🔴 Critical errors en >10% de operaciones
  🔴 Security breach durante migración

Manual (Decision-based):
  🟡 User experience significativamente degradada
  🟡 Missing data reportado por usuarios
  🟡 Integration issues con otros módulos
  🟡 Business critical functionality broken
```

#### Scripts de Rollback Automatizado
```bash
#!/bin/bash
# emergency_rollback.sh

echo "🚨 INICIANDO ROLLBACK DE EMERGENCIA"

# 1. Detener servicios de chat
systemctl stop inmotech-chat
systemctl stop inmotech-socketio

# 2. Restaurar backup de BD
mysql -u$DB_USER -p$DB_PASS inmotech_db < /backups/latest/database_full_backup.sql

# 3. Restaurar archivos
rm -rf /new_storage/chat_files/migrated/
tar -xzf /backups/latest/legacy_files.tar.gz -C /

# 4. Restaurar configuraciones
cp -r /backups/latest/config_backup/* /etc/inmotech/

# 5. Reiniciar servicios legacy
systemctl start inmotech-legacy-comm
systemctl start nginx

echo "✅ Rollback completado. Sistema restaurado a estado pre-migración."
```

### 🔧 Plan de Contingencia por Scenarios

#### Scenario 1: Pérdida de Datos Durante Migración
```yaml
Detección:
  - Automated data validation scripts fallan
  - Conteo de registros no coincide con origen
  - Usuarios reportan mensajes perdidos

Respuesta Inmediata:
  1. Pausar migración inmediatamente
  2. Activar modo de solo lectura en sistema
  3. Analizar logs para identificar punto de falla
  4. Determinar scope de pérdida de datos

Recovery Actions:
  - Partial rollback a último checkpoint válido
  - Re-ejecutar migración desde punto de falla
  - Manual data recovery para registros críticos
  - Extended testing antes de continuar
```

#### Scenario 2: Performance Crítica Post-Migración
```yaml
Síntomas:
  - Response times >5 segundos en chat
  - Database queries timeout frecuentes  
  - Memory usage >90% sustained
  - User complaints sobre lentitud

Mitigación Inmediata:
  1. Activate performance monitoring intensive
  2. Implement query optimization automática
  3. Escalar recursos temporalmente
  4. Enable caching agresivo

Long-term Solutions:
  - Database index optimization
  - Query rewriting para efficiency
  - Horizontal scaling de BD
  - Code optimization basado en profiling
```

---

## 📊 Validación y Testing Post-Migración

### 🔍 Suite de Validación Automática

#### Data Integrity Testing
```python
#!/usr/bin/env python3
# validate_migration.py

import mysql.connector
import json
import hashlib
from datetime import datetime

class MigrationValidator:
    def __init__(self, db_config):
        self.db = mysql.connector.connect(**db_config)
        self.validation_results = []
        
    def run_all_validations(self):
        """Ejecuta todas las validaciones post-migración"""
        validations = [
            self.validate_data_counts,
            self.validate_referential_integrity,
            self.validate_conversation_structure,
            self.validate_file_integrity,
            self.validate_user_preferences,
            self.validate_timestamps_consistency
        ]
        
        for validation in validations:
            try:
                result = validation()
                self.validation_results.append(result)
            except Exception as e:
                self.validation_results.append({
                    'test': validation.__name__,
                    'status': 'FAILED',
                    'error': str(e)
                })
                
        return self.generate_report()
    
    def validate_data_counts(self):
        """Valida que los conteos de datos coincidan"""
        cursor = self.db.cursor()
        
        # Conteo de comunicaciones legacy vs mensajes migrados
        cursor.execute("SELECT COUNT(*) FROM legacy_communications")
        legacy_count = cursor.fetchone()[0]
        
        cursor.execute("SELECT COUNT(*) FROM messages WHERE migrated_from_id IS NOT NULL")
        migrated_count = cursor.fetchone()[0]
        
        success_rate = (migrated_count / legacy_count) * 100
        
        return {
            'test': 'data_counts',
            'status': 'PASSED' if success_rate >= 95 else 'FAILED',
            'details': {
                'legacy_records': legacy_count,
                'migrated_records': migrated_count,
                'success_rate': f"{success_rate:.2f}%"
            }
        }
    
    def validate_conversation_structure(self):
        """Valida que las conversaciones tengan estructura correcta"""
        cursor = self.db.cursor()
        
        # Verificar que todas las conversaciones tengan al menos 2 participantes
        query = """
        SELECT c.id, COUNT(cp.id) as participant_count
        FROM conversations c
        LEFT JOIN conversation_participants cp ON c.id = cp.conversation_id
        WHERE c.migrated_from IS NOT NULL
        GROUP BY c.id
        HAVING participant_count < 2
        """
        
        cursor.execute(query)
        invalid_conversations = cursor.fetchall()
        
        return {
            'test': 'conversation_structure',
            'status': 'PASSED' if len(invalid_conversations) == 0 else 'WARNING',
            'details': {
                'invalid_conversations': len(invalid_conversations),
                'details': invalid_conversations[:10]  # Primeras 10 para review
            }
        }
```

### 📱 User Acceptance Testing (UAT)

#### Script de Testing Automatizado de UX
```javascript
// uat_automation.js - Puppeteer testing para UX
const puppeteer = require('puppeteer');

class ChatUATTester {
    constructor() {
        this.browser = null;
        this.page = null;
        this.results = [];
    }
    
    async runUATTests() {
        this.browser = await puppeteer.launch({headless: false});
        this.page = await this.browser.newPage();
        
        try {
            await this.testLogin();
            await this.testConversationsList();
            await this.testSendMessage();
            await this.testFileUpload();
            await this.testSearchFunctionality();
            await this.testNotifications();
            
            return this.generateUATReport();
        } finally {
            await this.browser.close();
        }
    }
    
    async testConversationsList() {
        console.log('🧪 Testing conversaciones migradas...');
        
        await this.page.goto('https://inmotech.local/chat');
        await this.page.waitForSelector('.conversations-list');
        
        // Verificar que aparezcan conversaciones migradas
        const migratedConversations = await this.page.$$('.conversation-item[data-migrated="true"]');
        
        this.results.push({
            test: 'migrated_conversations_visibility',
            status: migratedConversations.length > 0 ? 'PASSED' : 'FAILED',
            details: `${migratedConversations.length} conversaciones migradas visibles`
        });
    }
    
    async testSendMessage() {
        console.log('🧪 Testing envío de mensajes...');
        
        // Click en primera conversación
        await this.page.click('.conversation-item:first-child');
        await this.page.waitForSelector('.message-input');
        
        // Enviar mensaje de test
        const testMessage = `Test message ${Date.now()}`;
        await this.page.type('.message-input', testMessage);
        await this.page.click('.send-button');
        
        // Verificar que aparezca en el chat
        await this.page.waitForFunction(
            (message) => document.querySelector('.message:last-child .message-content')?.textContent?.includes(message),
            {},
            testMessage
        );
        
        this.results.push({
            test: 'send_message_functionality',
            status: 'PASSED',
            details: 'Mensaje enviado y mostrado correctamente'
        });
    }
}
```

---

## 📈 Monitoreo Post-Migración

### 📊 Métricas Críticas de Monitoreo

#### Dashboard de Health de Migración
```yaml
Métricas Técnicas (Primeras 48h):
  🔴 Query Performance: <200ms promedio
  🟡 Connection Pool: <80% utilización
  🔴 Error Rate: <1% en operaciones de chat
  🟡 Memory Usage: <75% en servidores de BD
  🔴 File Access: <5s para archivos migrados

Métricas de Negocio (Primera semana):
  📊 User Adoption: >60% usuarios que prueban chat
  💬 Message Volume: >1000 mensajes/día
  📱 Mobile Usage: >40% tráfico desde mobile
  🔄 Conversation Recovery: >95% conversaciones accesibles
  😊 User Satisfaction: >4.0/5.0 en feedback inicial
```

#### Alertas Automáticas Configuradas
```yaml
Críticas (Respuesta Inmediata):
  🚨 Database corruption detectada
  🚨 >50% usuarios reportando issues
  🚨 Data loss confirmado
  🚨 Security breach en chat data

Importantes (Respuesta en 2h):
  ⚠️ Performance degradation >200%
  ⚠️ Error rate >5% sustained
  ⚠️ File access issues >20% requests
  ⚠️ User satisfaction <3.5/5.0

Información (Respuesta en 24h):
  📊 Usage patterns inesperados
  📱 Mobile compatibility issues
  🔄 Integration warnings con otros módulos
  📈 Resource usage trending al límite
```

### 📋 Plan de Optimización Continua

#### Semana 1 Post-Migración
```yaml
Actividades Diarias:
  - Monitoreo intensivo 24/7
  - Daily standup con equipo de migración
  - User feedback collection y analysis
  - Performance tuning basado en metrics real

Optimizaciones Inmediatas:
  - Index optimization basado en query patterns
  - Cache tuning para mejor performance
  - Query optimization para queries lentas
  - Storage optimization para archivos
```

#### Primer Mes Post-Migración
```yaml
Evaluación Semanal:
  - Review completo de métricas vs targets
  - User feedback analysis y action items
  - Technical debt assessment y roadmap
  - Process improvement recommendations

Optimizaciones Planificadas:
  - Database partitioning para mejor performance
  - Advanced caching strategies implementation
  - Mobile app optimization basado en usage
  - Integration improvements con otros módulos
```

---

**📅 Fecha de Creación:** 20/11/2025  
**📅 Última Revisión:** 20/11/2025  
**📋 Versión del Documento:** 1.0  
**👤 Preparado por:** Patricia Jiménez - Data Migration Lead  
**✅ Revisado por:** Ana García - Database Architect  
**🔍 Aprobado por:** Equipo de Arquitectura InmoTech  

---

**🚀 MIGRACIÓN FASE 7: TRANSICIÓN SEGURA AL FUTURO DE LA COMUNICACIÓN INMOBILIARIA** 💬