# Plan de Migración de Datos - Fase 8: Sistema de Notificaciones

**📋 Proyecto:** InmoTech - Sistema Integral de Gestión Inmobiliaria  
**📊 Fase:** 08 - Sistema de Notificaciones  
**📅 Fecha de Migración:** 15/12/2025  
**👤 Responsable de Migración:** Ricardo Fernández - Ingeniero de Datos Senior  
**🔍 Revisado por:** Equipo de Arquitectura de Datos InmoTech  

---

## 🎯 Resumen Ejecutivo de Migración

### 📊 Panorama de la Migración
La **migración del Sistema de Notificaciones InmoTech** representa una **expansión y consolidación** del sistema de notificaciones existente, integrando nuevas funcionalidades avanzadas mientras preserva la compatibilidad con notificaciones históricas. Esta migración NO es una migración tradicional de sistema legacy, sino una **evolución estructurada** que añade capacidades críticas de personalización, categorización, y multi-canal.

### 🎖️ Objetivos Estratégicos de la Migración
```yaml
🎯 Objetivo Principal:
  Expandir el sistema actual de notificaciones para incluir funcionalidades
  avanzadas sin pérdida de datos históricos o interrupción del servicio.

📊 Objetivos Específicos:
  - Migrar 50,000+ notificaciones históricas al nuevo esquema
  - Integrar preferencias de usuario existentes con configuración avanzada
  - Implementar categorización retroactiva de notificaciones
  - Consolidar tokens de dispositivos para push notifications
  - Preservar 100% de la funcionalidad existente durante migración
  - Zero downtime durante el proceso de migración

🎪 Beneficios Esperados:
  - Personalización granular de tipos de notificaciones
  - Mejor organización y búsqueda de historial
  - Soporte para múltiples canales de entrega
  - Analítica avanzada de participación
  - Mejora en rendimiento de consultas de notificaciones
```

---

## 📊 Análisis del Estado Actual

### 🗄️ Inventario de Datos Existentes

#### Tabla: `notifications` (Actual)
```yaml
📋 Estructura Actual:
  - id (UUID): Identificador único
  - user_id (UUID): Usuario destinatario
  - title (VARCHAR 255): Título de notificación
  - message (TEXT): Contenido del mensaje
  - type (VARCHAR 50): Tipo básico de notificación
  - read_at (TIMESTAMP): Marca temporal de lectura
  - created_at (TIMESTAMP): Fecha de creación
  - updated_at (TIMESTAMP): Última actualización

📊 Volumen de Datos:
  - Registros totales: ~52,000 notificaciones
  - Usuarios con notificaciones: ~3,200 usuarios
  - Promedio por usuario: ~16 notificaciones
  - Tamaño total: ~850 MB
  - Crecimiento mensual: ~8,000 nuevas notificaciones

🎯 Tipos Existentes:
  - property_update: 35% (18,200 registros)
  - offer_received: 25% (13,000 registros)  
  - chat_message: 20% (10,400 registros)
  - appointment_reminder: 15% (7,800 registros)
  - system_announcement: 5% (2,600 registros)
```

#### Sistema de Push Notifications Existente
```yaml
📱 Infraestructura Actual:
  - Firebase Project: inmotech-notifications-prod
  - Tokens registrados: ~4,500 dispositivos
  - Proyectos iOS: 1 (com.inmotech.app)
  - Proyectos Android: 1 (com.inmotech.android)
  - Web push subscriptions: ~1,200 navegadores

📊 Métricas de Entrega:
  - Tasa de éxito promedio: 94.2%
  - Dispositivos inactivos: ~15% (675 tokens obsoletos)
  - Volumen diario: ~2,500 push notifications
  - Latencia promedio: 850ms
```

#### Configuraciones de Usuario Actuales
```yaml
🎛️ Preferencias Básicas (en user_settings):
  - email_notifications: true/false (3,200 usuarios)
  - push_notifications: true/false (3,200 usuarios)
  - sms_notifications: true/false (800 usuarios opt-in)
  
📊 Distribución Actual:
  - Push habilitado: 78% (2,496 usuarios)
  - Email habilitado: 92% (2,944 usuarios)
  - SMS habilitado: 25% (800 usuarios)
  - Todas deshabilitadas: 3% (96 usuarios)
```

---

## 🗂️ Diseño del Nuevo Esquema

### 🆕 Tabla: `notifications` (Nueva Estructura)

```sql
CREATE TABLE notifications_new (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  type VARCHAR(50) NOT NULL,
  category VARCHAR(50) NOT NULL,
  title VARCHAR(255) NOT NULL,
  message TEXT NOT NULL,
  data JSON DEFAULT '{}',
  priority ENUM('low', 'medium', 'high', 'urgent') DEFAULT 'medium',
  channels JSON DEFAULT '["push"]',
  action_url VARCHAR(500),
  expires_at TIMESTAMP NULL,
  read_at TIMESTAMP NULL,
  delivered_at TIMESTAMP NULL,
  failed_at TIMESTAMP NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  
  -- Índices para optimización
  INDEX idx_notifications_user_unread (user_id, read_at),
  INDEX idx_notifications_type_category (type, category),
  INDEX idx_notifications_priority (priority),
  INDEX idx_notifications_created (created_at),
  FULLTEXT INDEX idx_notifications_search (title, message)
);
```

### 🎛️ Tabla: `notification_preferences` (Nueva)

```sql
CREATE TABLE notification_preferences (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  notification_type VARCHAR(50) NOT NULL,
  category VARCHAR(50) NOT NULL,
  enabled BOOLEAN DEFAULT true,
  channels JSON DEFAULT '["push", "email"]',
  quiet_hours JSON DEFAULT '{"enabled": false, "start": "22:00", "end": "07:00"}',
  frequency ENUM('immediate', 'hourly', 'daily', 'weekly') DEFAULT 'immediate',
  priority_filter ENUM('all', 'medium_high', 'high_only', 'urgent_only') DEFAULT 'all',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  
  UNIQUE KEY unique_user_type_category (user_id, notification_type, category),
  INDEX idx_pref_user_enabled (user_id, enabled)
);
```

### 📱 Tabla: `push_subscriptions` (Nueva)

```sql
CREATE TABLE push_subscriptions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  platform ENUM('ios', 'android', 'web') NOT NULL,
  token TEXT NOT NULL,
  endpoint TEXT,
  p256dh TEXT,
  auth TEXT,
  device_name VARCHAR(100),
  device_model VARCHAR(100),
  app_version VARCHAR(20),
  is_active BOOLEAN DEFAULT true,
  last_used_at TIMESTAMP DEFAULT NOW(),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  
  UNIQUE KEY unique_user_token (user_id, token),
  INDEX idx_push_active (is_active, last_used_at)
);
```

---

## 🔄 Plan de Migración Detallado

### 📅 Cronograma de Migración

```yaml
🗓️ Fase 1: Preparación y Validación (Días 1-2)
📅 Fechas: 15-16 Diciembre 2025
⏰ Duración: 16 horas de trabajo

🗓️ Fase 2: Migración de Esquemas (Día 3)  
📅 Fechas: 17 Diciembre 2025
⏰ Duración: 8 horas de trabajo

🗓️ Fase 3: Migración de Datos (Día 4)
📅 Fechas: 18 Diciembre 2025  
⏰ Duración: 12 horas de trabajo

🗓️ Fase 4: Validación y Activación (Día 5)
📅 Fechas: 19 Diciembre 2025
⏰ Duración: 6 horas de trabajo

🗓️ Fase 5: Monitoreo Post-Migración (Días 6-12)
📅 Fechas: 20-26 Diciembre 2025
⏰ Monitoreo continuo: 24/7
```

### 🔧 Fase 1: Preparación y Validación

#### 📋 Día 1 (15 Dic): Análisis y Backup

```yaml
⏰ 09:00 - 11:00: Análisis Exhaustivo de Datos
Tareas:
  ✅ Ejecutar scripts de inventario completo
  ✅ Validar integridad de datos existentes
  ✅ Identificar registros órfanos o corruptos
  ✅ Analizar patrones de uso y volúmenes
  ✅ Documentar findings críticos

Scripts Específicos:
```sql
-- Inventario completo de notificaciones
SELECT 
  type,
  COUNT(*) as total,
  MIN(created_at) as oldest,
  MAX(created_at) as newest,
  AVG(CASE WHEN read_at IS NOT NULL THEN 1 ELSE 0 END) as read_rate
FROM notifications 
GROUP BY type;

-- Análisis de usuarios activos
SELECT 
  COUNT(DISTINCT user_id) as users_with_notifications,
  AVG(notification_count) as avg_per_user,
  MAX(notification_count) as max_per_user
FROM (
  SELECT user_id, COUNT(*) as notification_count
  FROM notifications 
  GROUP BY user_id
) user_stats;

-- Detección de datos problemáticos
SELECT 
  'Notifications with invalid user_id' as issue,
  COUNT(*) as count
FROM notifications n
LEFT JOIN users u ON n.user_id = u.id
WHERE u.id IS NULL

UNION ALL

SELECT 
  'Notifications with null required fields',
  COUNT(*)
FROM notifications
WHERE title IS NULL OR message IS NULL OR type IS NULL;
```

```yaml
⏰ 11:00 - 13:00: Backup Completo del Sistema

📦 Backup de Base de Datos:
  ✅ Full dump de tabla notifications
  ✅ Backup de user_settings relacionadas
  ✅ Export de configuraciones Firebase
  ✅ Backup de tokens push activos
  ✅ Verificación de integridad de backups

🔧 Comandos de Backup:
```bash
# Backup completo de notificaciones
pg_dump -h $DB_HOST -U $DB_USER -d $DB_NAME \
  --table=notifications \
  --data-only \
  --format=custom \
  -f notifications_backup_20251215.dump

# Backup de configuraciones relacionadas
pg_dump -h $DB_HOST -U $DB_USER -d $DB_NAME \
  --table=user_settings \
  --data-only \
  --format=custom \
  -f user_settings_backup_20251215.dump

# Export de tokens Firebase (script custom)
node scripts/export-firebase-tokens.js > firebase_tokens_backup_20251215.json

# Verificación de integridad
md5sum *_backup_20251215.* > backup_checksums.md5
```

```yaml
⏰ 14:00 - 17:00: Preparación del Entorno de Testing

🧪 Ambiente de Testing:
  ✅ Copia completa de datos de producción
  ✅ Configuración de entorno aislado
  ✅ Testing de scripts de migración
  ✅ Validación de procedimientos de reversión
  ✅ Configuración de monitoreo temporal
```

#### 📋 Día 2 (16 Dic): Scripts y Validación

```yaml
⏰ 09:00 - 12:00: Desarrollo de Scripts de Migración

📝 Script 1: Migración de Notificaciones Históricas
```javascript
// scripts/migrate-notifications.js
const migrateNotifications = async () => {
  console.log('🚀 Iniciando migración de notificaciones...');
  
  const batchSize = 1000;
  let offset = 0;
  let totalMigrated = 0;
  
  while (true) {
    const notifications = await db.query(`
      SELECT * FROM notifications 
      ORDER BY created_at 
      LIMIT ${batchSize} OFFSET ${offset}
    `);
    
    if (notifications.length === 0) break;
    
    for (const notification of notifications) {
      const migratedNotification = {
        id: notification.id,
        user_id: notification.user_id,
        type: notification.type,
        category: categorizeNotification(notification.type),
        title: notification.title,
        message: notification.message,
        data: extractDataFromMessage(notification),
        priority: determinePriority(notification.type),
        channels: ['push'], // Default para notificaciones existentes
        action_url: extractActionUrl(notification),
        read_at: notification.read_at,
        created_at: notification.created_at,
        updated_at: notification.updated_at
      };
      
      await insertNewNotification(migratedNotification);
      totalMigrated++;
      
      if (totalMigrated % 100 === 0) {
        console.log(`✅ Migrados ${totalMigrated} registros...`);
      }
    }
    
    offset += batchSize;
  }
  
  console.log(`🎉 Migración completada: ${totalMigrated} notificaciones`);
};
```

```yaml
📝 Script 2: Migración de Preferencias de Usuario
```javascript
// scripts/migrate-user-preferences.js
const migrateUserPreferences = async () => {
  console.log('🚀 Iniciando migración de preferencias...');
  
  const users = await db.query(`
    SELECT u.id, us.email_notifications, us.push_notifications, us.sms_notifications
    FROM users u
    LEFT JOIN user_settings us ON u.id = us.user_id
  `);
  
  const notificationTypes = [
    'property_update', 'offer_received', 'chat_message', 
    'appointment_reminder', 'system_announcement'
  ];
  
  for (const user of users) {
    for (const type of notificationTypes) {
      const preference = {
        user_id: user.id,
        notification_type: type,
        category: getCategoryForType(type),
        enabled: user.push_notifications !== false,
        channels: buildChannelsArray(user),
        quiet_hours: getDefaultQuietHours(),
        frequency: 'immediate',
        priority_filter: 'all'
      };
      
      await insertNotificationPreference(preference);
    }
  }
  
  console.log(`🎉 Preferencias migradas para ${users.length} usuarios`);
};
```

```yaml
⏰ 13:00 - 17:00: Testing Exhaustivo

🧪 Pruebas de Migración:
  ✅ Migración en ambiente de testing
  ✅ Validación de integridad de datos
  ✅ Pruebas de reversión completa
  ✅ Verificación de rendimiento
  ✅ Testing de funcionalidad post-migración
```

### 🔄 Fase 2: Migración de Esquemas (17 Dic)

#### ⏰ 09:00 - 12:00: Creación de Nuevas Estructuras

```yaml
🗂️ Secuencia de Creación:

1. Creación de nuevas tablas:
  ✅ notifications_new (estructura expandida)
  ✅ notification_preferences (nueva)
  ✅ push_subscriptions (consolidación)

2. Creación de índices optimizados:
  ✅ Índices de rendimiento para consultas frecuentes
  ✅ Índices de texto completo para búsqueda
  ✅ Índices compuestos para filtros complejos

3. Configuración de constraints:
  ✅ Foreign keys hacia users
  ✅ Unique constraints donde apropiado
  ✅ Check constraints para enums
```

#### ⏰ 13:00 - 17:00: Configuración de Servicios

```yaml
🔧 Configuración de Infraestructura:

📱 Firebase Configuration:
  ✅ Configuración de múltiples proyectos para redundancia
  ✅ Migration de tokens existentes a nueva estructura
  ✅ Testing de endpoints con nueva configuración
  ✅ Configuración de service account keys

🔄 Redis Configuration:
  ✅ Configuración de cache para notificaciones frecuentes
  ✅ Setup de queues para procesamiento asíncrono
  ✅ Configuración de clustering para alta disponibilidad

⚙️ Application Configuration:
  ✅ Variables de entorno para nueva estructura
  ✅ Feature flags para rollout gradual
  ✅ Configuración de logging mejorado
```

### 📊 Fase 3: Migración de Datos (18 Dic)

#### ⏰ 09:00 - 15:00: Migración Principal

```yaml
🔄 Proceso de Migración por Lotes:

Lote 1 (09:00-10:30): Notificaciones Críticas Recientes
  - Scope: Últimos 30 días, tipos critical/urgent
  - Volumen: ~8,000 registros
  - Estrategia: Zero downtime con dual-write

Lote 2 (10:30-12:00): Notificaciones Estándar Recientes  
  - Scope: Últimos 90 días, tipos standard
  - Volumen: ~20,000 registros
  - Estrategia: Migración con replica lag mínimo

Lote 3 (13:00-14:30): Notificaciones Históricas
  - Scope: >90 días, todos los tipos
  - Volumen: ~24,000 registros
  - Estrategia: Batch processing durante low-traffic

Lote 4 (14:30-15:00): Validación y Limpieza
  - Verificación de integridad
  - Limpieza de datos temporales
  - Finalización de índices
```

#### 🔧 Scripts de Migración por Lotes

```javascript
// Migración con estrategia dual-write para zero downtime
const migrateBatch = async (batchConfig) => {
  const { 
    dateFrom, 
    dateTo, 
    types, 
    batchSize = 500,
    enableDualWrite = false 
  } = batchConfig;
  
  console.log(`🚀 Iniciando migración de lote: ${dateFrom} a ${dateTo}`);
  
  if (enableDualWrite) {
    await enableDualWriteMode();
  }
  
  let offset = 0;
  let totalProcessed = 0;
  
  while (true) {
    const batch = await db.query(`
      SELECT * FROM notifications 
      WHERE created_at BETWEEN $1 AND $2
        AND type = ANY($3)
      ORDER BY created_at ASC
      LIMIT $4 OFFSET $5
    `, [dateFrom, dateTo, types, batchSize, offset]);
    
    if (batch.length === 0) break;
    
    // Procesar en paralelo pero controlando concurrencia
    await Promise.all(
      batch.map(notification => 
        migrateIndividualNotification(notification)
      )
    );
    
    totalProcessed += batch.length;
    offset += batchSize;
    
    // Progress tracking y health checks
    await logProgress(totalProcessed);
    await performHealthCheck();
    
    // Rate limiting para no impactar rendimiento
    if (totalProcessed % 1000 === 0) {
      await sleep(2000); // 2 second pause every 1000 records
    }
  }
  
  if (enableDualWrite) {
    await verifyDualWriteConsistency();
  }
  
  console.log(`✅ Lote completado: ${totalProcessed} registros migrados`);
};
```

#### ⏰ 15:00 - 18:00: Migración de Configuraciones

```yaml
👥 Migración de Preferencias de Usuario:
  ✅ Migrar configuraciones básicas existentes
  ✅ Crear preferencias por defecto para nuevos tipos
  ✅ Migrar tokens de dispositivos push
  ✅ Configurar quiet hours por defecto

📱 Migración de Push Subscriptions:
  ✅ Consolidar tokens de Firebase existentes
  ✅ Migrar configuraciones de dispositivos
  ✅ Limpiar tokens obsoletos/inactivos
  ✅ Validar conectividad con nuevos endpoints

🔧 Migración de Configuración del Sistema:
  ✅ Migrar templates de notificaciones
  ✅ Configurar nuevas categorías y prioridades
  ✅ Migrar configuraciones de rate limiting
  ✅ Establecer configuraciones de retention
```

### ✅ Fase 4: Validación y Activación (19 Dic)

#### ⏰ 09:00 - 12:00: Validación Exhaustiva

```yaml
🔍 Validación de Integridad de Datos:

📊 Verificación de Conteos:
```sql
-- Verificar que todos los registros fueron migrados
SELECT 
  'Original' as source, 
  COUNT(*) as total,
  COUNT(CASE WHEN read_at IS NOT NULL THEN 1 END) as read_count
FROM notifications

UNION ALL

SELECT 
  'Migrated',
  COUNT(*),
  COUNT(CASE WHEN read_at IS NOT NULL THEN 1 END)
FROM notifications_new;

-- Verificar distribución por tipos
SELECT 
  type,
  COUNT(*) as original_count,
  (SELECT COUNT(*) FROM notifications_new n2 WHERE n2.type = n1.type) as migrated_count
FROM notifications n1
GROUP BY type;
```

```yaml
🧪 Testing Funcional:
  ✅ Crear notificación de prueba en cada canal
  ✅ Verificar entrega de push notifications
  ✅ Testing de configuración de preferencias
  ✅ Validar filtros y búsquedas
  ✅ Testing de APIs con nueva estructura
  ✅ Verificar funcionamiento del centro de notificaciones

📈 Testing de Rendimiento:
  ✅ Benchmark de consultas críticas
  ✅ Testing de carga con volumen real
  ✅ Validar latencia de APIs
  ✅ Testing de concurrencia
```

#### ⏰ 13:00 - 15:00: Activación Gradual

```yaml
🚀 Rollout por Fases:

Fase A (13:00): Activación para Admin Users (5% tráfico)
  ✅ Habilitar nuevas funcionalidades para administradores
  ✅ Monitoreo intensivo de métricas
  ✅ Validación de funcionalidad completa

Fase B (13:30): Activación para Agentes (25% tráfico)  
  ✅ Expandir a usuarios de tipo agente
  ✅ Monitoreo de adoption y feedback
  ✅ Ajustes menores según feedback

Fase C (14:00): Activación para Usuarios Premium (50% tráfico)
  ✅ Incluir usuarios premium en rollout
  ✅ Validar personalización avanzada
  ✅ Monitoreo de participación

Fase D (14:30): Activación Completa (100% tráfico)
  ✅ Activar para todos los usuarios
  ✅ Decommisionar sistema anterior gradualmente
  ✅ Monitoreo continuo post-activación
```

### 📊 Fase 5: Monitoreo Post-Migración

#### 📈 Métricas Críticas de Monitoreo

```yaml
⚡ Métricas de Rendimiento:
  - Latencia promedio de APIs: <200ms
  - Tiempo de carga del centro de notificaciones: <1.5s
  - Throughput de notificaciones: >5000/minute
  - Error rate: <0.5%

📊 Business Metrics:
  - Tasa de entrega de notificaciones: >95%
  - Participación de usuarios con nuevas funcionalidades: tracking diario
  - Adopción de configuración de preferencias: >60% en 7 días
  - Satisfacción de usuario: surveys diarios

🛡️ Métricas de Estabilidad:
  - Uptime del sistema: >99.5%
  - Incidentes críticos: 0 tolerance
  - Data consistency checks: diarios automáticos
  - Preparación para reversión: validación continua
```

---

## 📋 Mapeo de Transformación de Datos

### 🔄 Transformaciones de Notificaciones

```yaml
Campo Original → Campo Nuevo + Lógica de Transformación:

id → id (sin cambios)
user_id → user_id (sin cambios)
type → type (sin cambios) + category (nueva lógica)
title → title (sin cambios)
message → message + data (extracción estructurada)
read_at → read_at (sin cambios)
created_at → created_at (sin cambios)
updated_at → updated_at (sin cambios)

Nuevos campos con valores por defecto:
- priority: determinado por lógica basada en type
- channels: ['push'] para todas las notificaciones históricas
- action_url: extraído del message si está presente
- expires_at: null para notificaciones históricas
- delivered_at: assumed igual a created_at si no hay fallos
- failed_at: null para notificaciones históricas
```

### 🧠 Lógica de Categorización

```javascript
const categorizeNotification = (type) => {
  const categoryMap = {
    'property_update': 'properties',
    'property_new_match': 'properties', 
    'property_price_change': 'properties',
    'property_sold': 'properties',
    
    'offer_received': 'offers',
    'offer_accepted': 'offers',
    'offer_rejected': 'offers',
    'offer_counter': 'offers',
    'offer_expired': 'offers',
    
    'chat_message': 'communication',
    'chat_mention': 'communication',
    'agent_assigned': 'communication',
    
    'appointment_reminder': 'appointments',
    'appointment_confirmed': 'appointments',
    'appointment_cancelled': 'appointments',
    'visit_scheduled': 'appointments',
    
    'system_announcement': 'system',
    'maintenance_notice': 'system',
    'feature_update': 'system',
    'security_alert': 'system'
  };
  
  return categoryMap[type] || 'general';
};

const determinePriority = (type) => {
  const priorityMap = {
    'security_alert': 'urgent',
    'offer_expired': 'urgent',
    'maintenance_notice': 'high',
    'offer_received': 'high',
    'chat_mention': 'high',
    'appointment_reminder': 'medium',
    'property_update': 'medium',
    'feature_update': 'low',
    'system_announcement': 'low'
  };
  
  return priorityMap[type] || 'medium';
};
```

### 📱 Migración de Tokens Push

```javascript
const migratePushTokens = async () => {
  // Obtener todos los tokens desde Firebase y user settings
  const existingTokens = await getFirebaseTokens();
  const userSettings = await getUserPushSettings();
  
  for (const token of existingTokens) {
    const subscription = {
      user_id: token.user_id,
      platform: detectPlatform(token.token),
      token: token.token,
      endpoint: token.endpoint || null,
      device_name: token.device_name || 'Unknown Device',
      app_version: token.app_version || '1.0.0',
      is_active: !isTokenExpired(token),
      last_used_at: token.last_used || token.created_at,
      created_at: token.created_at
    };
    
    await insertPushSubscription(subscription);
  }
  
  // Limpiar tokens obsoletos
  await cleanupExpiredTokens();
};
```

---

## 🔧 Scripts de Reversión

### 🚨 Procedimientos de Rollback de Emergencia

#### Script 1: Rollback Inmediato (< 5 minutos)

```bash
#!/bin/bash
# rollback-immediate.sh
echo "🚨 INICIANDO ROLLBACK DE EMERGENCIA - FASE 8 NOTIFICACIONES"

# 1. Restaurar configuración anterior
echo "📁 Restaurando configuración de aplicación..."
cp config/notifications.backup.json config/notifications.json
sudo systemctl restart inmotech-api

# 2. Restaurar routing de base de datos
echo "🗄️ Restaurando routing de base de datos..."
psql -h $DB_HOST -U $DB_USER -d $DB_NAME << EOF
-- Redireccionar queries a tabla original
DROP VIEW IF EXISTS notifications_current;
CREATE VIEW notifications_current AS SELECT * FROM notifications;

-- Restaurar índices críticos si fueron modificados
REINDEX TABLE notifications;
EOF

# 3. Restaurar configuración de Firebase
echo "📱 Restaurando configuración de Firebase..."
firebase use inmotech-notifications-backup --token $FIREBASE_TOKEN
firebase deploy --only functions:sendNotification

# 4. Validar que sistema anterior funciona
echo "✅ Validando funcionalidad..."
curl -X POST "https://api.inmotech.com/notifications/test" \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"test": true}'

echo "🎉 Rollback de emergencia completado en $(date)"
```

#### Script 2: Rollback Completo de Datos

```javascript
// rollback-data-complete.js
const rollbackDataMigration = async () => {
  console.log('🔄 Iniciando rollback completo de migración...');
  
  // 1. Restaurar desde backup completo
  await restoreFromBackup('notifications_backup_20251215.dump');
  
  // 2. Validar integridad de datos restaurados
  const integrityCheck = await validateDataIntegrity();
  if (!integrityCheck.isValid) {
    throw new Error('Data integrity failed after rollback');
  }
  
  // 3. Restaurar configuraciones de usuario
  await restoreUserPreferences();
  
  // 4. Limpiar tablas nuevas
  await db.query('DROP TABLE IF EXISTS notifications_new');
  await db.query('DROP TABLE IF EXISTS notification_preferences');
  await db.query('DROP TABLE IF EXISTS push_subscriptions');
  
  // 5. Restaurar índices originales
  await rebuildOriginalIndexes();
  
  console.log('✅ Rollback completo finalizado');
};
```

### 🎯 Criterios de Activación de Rollback

```yaml
🚨 Rollback Automático se activa si:
  - Error rate >5% por más de 5 minutos
  - Latencia promedio >2 segundos por más de 3 minutos
  - Pérdida de datos detectada en validaciones automáticas
  - Fallo completo de servicio de notificaciones
  - >100 reportes de usuarios en 10 minutos

⚠️ Rollback Manual se considera si:
  - Error rate entre 2-5% sostenido por >10 minutos
  - Quejas específicas de funcionalidad de >20 usuarios
  - Problemas de rendimiento que afecten UX significativamente
  - Detección de bugs críticos en nueva funcionalidad

✅ Criterios para NO hacer rollback:
  - Problemas menores de UI que no afecten funcionalidad core
  - Issues de rendimiento menores (<1.5x baseline)
  - Bugs en funcionalidades nuevas que no impacten features existentes
  - Problemas con menos de 5% de usuarios afectados
```

---

## 📊 Validación y Testing

### 🧪 Plan de Testing de Migración

#### Testing Pre-Migración
```yaml
🔍 Validation Scripts:
```sql
-- Script 1: Validar datos fuente
SELECT 
  COUNT(*) as total_notifications,
  COUNT(DISTINCT user_id) as unique_users,
  COUNT(CASE WHEN read_at IS NOT NULL THEN 1 END) as read_notifications,
  MIN(created_at) as oldest_notification,
  MAX(created_at) as newest_notification
FROM notifications;

-- Script 2: Validar referencias
SELECT 
  'Orphaned notifications' as issue,
  COUNT(*) as count
FROM notifications n
LEFT JOIN users u ON n.user_id = u.id
WHERE u.id IS NULL;
```

#### Testing Post-Migración
```yaml
🧪 Comprehensive Validation:

1. Data Integrity Tests:
  ✅ Verificar conteos exactos por tabla
  ✅ Validar que no hay registros duplicados
  ✅ Confirmar que todas las referencias son válidas
  ✅ Verificar que los índices funcionan correctamente

2. Functional Tests:
  ✅ Crear notificación nueva en cada categoría
  ✅ Modificar preferencias de usuario
  ✅ Probar búsqueda y filtros
  ✅ Validar envío de push notifications
  ✅ Verificar centro de notificaciones

3. Tests de Rendimiento:
  ✅ Benchmark de consultas críticas
  ✅ Testing de carga con volumen real
  ✅ Validar que latencia está dentro de SLA
  ✅ Testing de concurrencia alta
```

### 📈 Métricas de Success

```yaml
✅ Criterios de Éxito Obligatorios:
  - Zero pérdida de datos: 100% de notificaciones migradas
  - Funcionalidad preservada: 100% de features existentes operativas
  - Rendimiento mantenido: <20% degradación en latencia
  - Uptime sostenido: >99.5% durante migración
  - User satisfaction: <5% de quejas relacionadas con migración

📊 Métricas Opcionales (Nice-to-Have):
  - Mejora en rendimiento: 10-30% reducción en latencia de consultas
  - Adopción de nuevas features: >30% usuarios usan configuración avanzada
  - Reducción de costos: 15% menos en costos de infraestructura
  - Mejora en participación: 10% más interacción con notificaciones
```

---

## ⚠️ Gestión de Riesgos de Migración

### 🚨 Riesgos Críticos

```yaml
🔥 Riesgo 1: Pérdida de Datos Durante Migración
Probabilidad: Baja (10%) | Impacto: Crítico
Mitigación:
  ✅ Backups múltiples antes de iniciar
  ✅ Validación en cada paso
  ✅ Rollback automático en caso de inconsistencias
  ✅ Testing exhaustivo en ambiente de staging

⚡ Riesgo 2: Downtime Extendido
Probabilidad: Media (25%) | Impacto: Alto
Mitigación:
  ✅ Estrategia de migración con dual-write
  ✅ Despliegue progresivo por lotes pequeños
  ✅ Monitoreo en tiempo real
  ✅ Plan de rollback en <5 minutos

🔧 Riesgo 3: Fallo de Servicios Externos (Firebase)
Probabilidad: Baja (15%) | Impacto: Alto
Mitigación:
  ✅ Configuración de múltiples proyectos Firebase
  ✅ Fallback a email para notificaciones críticas
  ✅ Testing de conectividad antes de migración
  ✅ Plan B con proveedor alternativo
```

### 📊 Plan de Contingencia por Escenario

```yaml
Escenario A: Migración toma más tiempo del esperado
Plan:
  ✅ Extender ventana de mantenimiento comunicando a usuarios
  ✅ Activar modo de degradación elegante
  ✅ Priorizar migración de datos críticos
  ✅ Diferir datos históricos para fase posterior

Escenario B: Rendimiento degradado post-migración  
Plan:
  ✅ Rollback inmediato si impacto >50% rendimiento
  ✅ Optimización de índices en caliente si impacto <50%
  ✅ Análisis de queries lentas y optimización
  ✅ Scale up temporal de recursos si necesario

Escenario C: Usuarios reportan funcionalidad rota
Plan:
  ✅ Triage inmediato para determinar alcance
  ✅ Fix hot si afecta <20% usuarios
  ✅ Rollback si afecta >20% usuarios
  ✅ Comunicación proactiva sobre status y timeline
```

---

## 📞 Contactos y Escalación

### 👥 Equipo de Migración

```yaml
👤 Líder de Migración:
Ricardo Fernández - ricardo.fernandez@inmotech.com
📱 Móvil: +34 666-777-888 (24/7 durante migración)
🎯 Responsabilidades: Coordinación general, decisiones técnicas

👤 DBA Principal:
Carmen López - carmen.lopez@inmotech.com  
📱 Móvil: +34 666-777-889
🎯 Responsabilidades: Migración de datos, rollback, rendimiento

👤 DevOps Lead:
Miguel Rodríguez - miguel.rodriguez@inmotech.com
📱 Móvil: +34 666-777-890
🎯 Responsabilidades: Infraestructura, monitoreo, despliegue

👤 QA Lead:
Carlos Vega - carlos.vega@inmotech.com
📱 Móvil: +34 666-777-891
🎯 Responsabilidades: Validación, testing, criterios de éxito
```

### 🚨 Escalación de Incidentes

```yaml
Nivel 1: Issues Menores (0-4 horas)
  - Contacto: Líder de Migración
  - Ejemplos: Performance levemente degradado, bugs menores UI

Nivel 2: Issues Significativos (4-1 hora)  
  - Contacto: Líder de Migración + DBA Principal
  - Ejemplos: Funcionalidad rota para grupo de usuarios

Nivel 3: Issues Críticos (<1 hora)
  - Contacto: Todo el equipo + CTO
  - Ejemplos: Sistema down, pérdida de datos detectada

Nivel 4: Crisis (Inmediato)
  - Contacto: Todo el equipo + C-Level
  - Ejemplos: Pérdida masiva de datos, violación de seguridad
```

---

**📅 Fecha de Creación:** 20/11/2025  
**📅 Última Actualización:** 20/11/2025  
**📋 Versión del Documento:** 1.0  
**👤 Preparado por:** Ricardo Fernández - Ingeniero de Datos Senior  
**✅ Revisado por:** Equipo de Arquitectura de Datos InmoTech  
**🔍 Aprobado por:** Miguel Rodríguez - Technical Director  

---

**🔄 FASE 8: MIGRANDO HACIA COMUNICACIÓN INTELIGENTE** 📊🔔💾