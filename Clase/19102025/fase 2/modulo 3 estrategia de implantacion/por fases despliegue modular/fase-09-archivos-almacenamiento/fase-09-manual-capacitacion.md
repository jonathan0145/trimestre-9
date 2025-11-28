# Manual de Capacitación - Fase 9: Archivos y Almacenamiento

## Información General

**Nombre de la Fase:** Archivos y Almacenamiento
**Número de Fase:** 9
**Fecha de Capacitación:** 25/02/2026 - 26/02/2026
**Dirigido a:** Equipos de Desarrollo, DevOps, Soporte y Usuarios Finales
**Facilitador Principal:** Ricardo Fernández (DevOps Engineer)
**Duración Total:** 16 horas (2 días)

---

## 🎯 Objetivos de la Capacitación

### Objetivo Principal
Capacitar a todos los equipos en el nuevo **Sistema de Archivos y Almacenamiento InmoTech**, incluyendo funcionalidades, configuración, operación, troubleshooting y mejores prácticas de seguridad.

### Objetivos Específicos
- [ ] **Técnico:** Entender arquitectura y configuración del sistema de archivos
- [ ] **Operacional:** Dominar procedimientos de backup, monitoring y mantenimiento
- [ ] **Usuario:** Usar eficientemente las funcionalidades de upload/download
- [ ] **Seguridad:** Aplicar mejores prácticas de seguridad y control de acceso
- [ ] **Troubleshooting:** Diagnosticar y resolver problemas comunes

---

## 👥 Audiencias y Sesiones Especializadas

### 🔧 Sesión 1: Equipo Técnico (Desarrolladores + DevOps)
**Duración:** 6 horas
**Participantes:** 8 desarrolladores, 3 DevOps, 1 arquitecto
**Modalidad:** Presencial con laboratorio hands-on

### 👨‍💼 Sesión 2: Equipos de Soporte y Operaciones
**Duración:** 4 horas  
**Participantes:** 5 agentes de soporte, 2 managers de producto
**Modalidad:** Híbrida (presencial + virtual)

### 👤 Sesión 3: Usuarios Finales (Agentes Inmobiliarios)
**Duración:** 3 horas
**Participantes:** 20 agentes inmobiliarios, 5 coordinadores
**Modalidad:** Virtual con demo en vivo

### 🛡️ Sesión 4: Equipo de Seguridad y Compliance
**Duración:** 3 horas
**Participantes:** 2 especialistas en seguridad, 1 oficial de compliance
**Modalidad:** Presencial con revisión de configuraciones

---

## 📋 Agenda Detallada

### 🔧 DÍA 1: Sesión Técnica Avanzada

#### **9:00 - 10:30 AM: Arquitectura del Sistema de Almacenamiento**
**Facilitador:** Miguel Rodríguez (Arquitecto de Software)

**🏗️ Temas Cubiertos:**
- [ ] Arquitectura general del sistema de archivos
- [ ] Integración con AWS S3 y CloudFront CDN
- [ ] Flujo de datos desde upload hasta delivery
- [ ] Configuración de buckets y políticas de acceso
- [ ] Estrategias de backup y replicación

**📂 Recursos:**
- Diagrama de arquitectura actualizado
- Documentación de configuración AWS
- Scripts de infraestructura como código

---

#### **10:45 AM - 12:15 PM: APIs y Servicios Backend**
**Facilitador:** Carmen López (Desarrollador Backend)

**⚙️ Componentes Revisados:**
- [ ] `fileController.js`: Endpoints de gestión de archivos
- [ ] `storageController.js`: Operaciones de almacenamiento
- [ ] `fileService.js`: Lógica de negocio de archivos
- [ ] Middleware de validación y seguridad
- [ ] Integración con sistema de permisos existente

**🔧 Labs Prácticos:**
- [ ] Configurar environment de desarrollo local
- [ ] Pruebas de APIs con Postman
- [ ] Debug de upload/download flows
- [ ] Implementar validaciones personalizadas

```bash
# Configuración de desarrollo
npm install
cp .env.example .env.local
npm run setup:storage
npm run test:file-upload
```

---

#### **1:15 - 2:45 PM: Seguridad y Control de Acceso**
**Facilitador:** Ricardo Fernández (DevOps Engineer)

**🛡️ Configuraciones de Seguridad:**
- [ ] Políticas de bucket S3 restrictivas
- [ ] URLs firmadas y temporales
- [ ] Validación de tipos de archivo
- [ ] Scanning automático de malware
- [ ] Encriptación en tránsito y reposo
- [ ] Auditoría de accesos

**📋 Checklist de Seguridad:**
```yaml
S3 Security:
  - Public access: ❌ BLOCKED
  - Bucket policy: ✅ RESTRICTIVE
  - Versioning: ✅ ENABLED
  - MFA delete: ✅ ENABLED
  - Encryption: ✅ AES-256

CDN Security:
  - Origin access: ✅ RESTRICTED
  - Signed URLs: ✅ ENABLED
  - Geographic restrictions: ✅ CONFIGURED
  - Rate limiting: ✅ ACTIVE
```

---

#### **3:00 - 4:30 PM: Monitoreo y Observabilidad**
**Facilitador:** Miguel Rodríguez

**📊 Métricas Clave:**
- [ ] Volumen de almacenamiento utilizado
- [ ] Latencia de upload/download
- [ ] Tasa de errores en operaciones
- [ ] Costos de almacenamiento y CDN
- [ ] Patrones de uso por usuario/tipo

**🔧 Herramientas de Monitoring:**
- [ ] CloudWatch dashboards personalizados
- [ ] Alertas automáticas por umbrales
- [ ] Logs estructurados para debugging
- [ ] Métricas de negocio en tiempo real

**📈 Setup de Dashboards:**
```javascript
// Configuración de métricas personalizadas
const storageMetrics = {
  uploadLatency: 'avg(upload_time_ms)',
  errorRate: 'rate(storage_errors)',
  dailyVolume: 'sum(files_uploaded_24h)',
  costTrend: 'cost_per_gb_trend_7d'
};
```

---

### 👨‍💼 DÍA 2: Operaciones y Soporte

#### **9:00 - 10:30 AM: Procedimientos Operacionales**
**Facilitador:** Carlos Vega (QA Lead)

**🔄 Operaciones Rutinarias:**
- [ ] Backup y verificación diaria
- [ ] Limpieza de archivos temporales
- [ ] Rotación de logs de acceso
- [ ] Análisis de uso y capacidad
- [ ] Optimización de costos

**📋 Runbook de Operaciones:**
```bash
# Script diario de mantenimiento
#!/bin/bash
./backup-verify.sh
./cleanup-temp-files.sh
./analyze-usage.sh
./cost-optimization.sh
```

---

#### **10:45 AM - 12:15 PM: Troubleshooting Común**
**Facilitador:** Ricardo Fernández

**🔍 Problemas Más Frecuentes:**

**1. Upload Fails / Timeouts:**
- [ ] ✅ "Upload lento": verificar tamaño y conexión
- [ ] ✅ "Error de permisos": validar autenticación
- [ ] ✅ "Archivo rechazado": confirmar tipo permitido
- [ ] ✅ "Timeout": revisar configuración de proxy/CDN

**2. Download Issues:**
- [ ] ✅ "Archivo no encontrado": verificar existencia y permisos
- [ ] ✅ "URL expirada": regenerar link temporal
- [ ] ✅ "Carga lenta": revisar CDN y cache

**3. Problemas de Almacenamiento:**
- [ ] ✅ "Cuota excedida": revisar límites por usuario
- [ ] ✅ "Costos altos": analizar patrones de uso
- [ ] ✅ "Backup fallido": verificar conectividad y permisos

**🛠️ Herramientas de Diagnóstico:**
```bash
# Verificar estado del sistema
curl -X GET /api/storage/health
curl -X GET /api/storage/metrics
aws s3 ls s3://inmotech-files --recursive --human-readable
```

---

### 👤 Sesión de Usuarios Finales

#### **2:00 - 5:00 PM: Capacitación para Agentes Inmobiliarios**
**Facilitador:** Patricia Jiménez (UX Specialist)

**📱 Funcionalidades para Usuarios:**

**Módulo 1: Upload de Archivos de Propiedades (45 minutos)**
- [ ] ✅ Subir fotos de propiedades desde móvil
- [ ] ✅ Organizar archivos por propiedad
- [ ] ✅ Comprimir automáticamente para web
- [ ] ✅ Añadir descripciones y metadatos

**Demo Hands-On:**
```
1. Abrir app móvil InmoTech
2. Navegar a "Nueva Propiedad"
3. Tocar "Agregar Fotos"
4. Seleccionar múltiples imágenes
5. Confirmar upload automático
6. Verificar organización por propiedad
```

**Módulo 2: Compartir Archivos con Clientes (45 minutos)**
- [ ] ✅ Generar links de compartir temporales
- [ ] ✅ Controlar acceso por tiempo/visualizaciones
- [ ] ✅ Enviar archivos via chat integrado
- [ ] ✅ Notificaciones de descarga por cliente

**Módulo 3: Gestión de Documentos Legales (45 minutos)**
- [ ] ✅ Subir contratos y documentos
- [ ] ✅ Firmar digitalmente archivos
- [ ] ✅ Control de versiones automático
- [ ] ✅ Archivo seguro con encriptación

**Módulo 4: Mejores Prácticas y Tips (45 minutos)**
- [ ] ✅ Optimizar fotos para carga rápida
- [ ] ✅ Organización eficiente de archivos
- [ ] ✅ Uso de metadatos para búsquedas
- [ ] ✅ Backup personal vs automático

---

## 🔧 Configuración Técnica

### 🔐 Environment Variables
```bash
# Configuración requerida para desarrollo
AWS_REGION=us-east-1
AWS_S3_BUCKET=inmotech-files-dev
CDN_DOMAIN=cdn-dev.inmotech.com
MAX_FILE_SIZE_MB=50
ALLOWED_FORMATS=jpg,png,pdf,docx,mp4

# Seguridad
VIRUS_SCAN_ENABLED=true
URL_EXPIRATION_HOURS=24
BACKUP_REGION=us-west-2
```

### 🏗️ Infraestructura como Código
```yaml
# terraform/storage.tf
resource "aws_s3_bucket" "file_storage" {
  bucket = "inmotech-files-${var.environment}"
  
  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }
  
  versioning {
    enabled = true
  }
  
  lifecycle_rule {
    enabled = true
    expiration {
      days = 2555  # 7 años retención legal
    }
  }
}
```

---

## 📊 Métricas de Adopción y Uso

### 🎯 KPIs para Equipos Técnicos
**Métricas de Rendimiento:**
- [ ] ✅ Latencia P95 upload: <10 segundos
- [ ] ✅ Latencia P95 download: <2 segundos
- [ ] ✅ Disponibilidad del servicio: >99.9%
- [ ] ✅ Tasa de errores: <0.5%

**Métricas Operacionales:**
- [ ] ✅ Backup exitosos: >99.9%
- [ ] ✅ Tiempo de recovery: <30 minutos
- [ ] ✅ Alertas falsas: <5% del total

### 📈 KPIs para Usuarios de Negocio
**Adopción y Uso:**
- [ ] ✅ Usuarios activos con archivos: >80%
- [ ] ✅ Promedio archivos por propiedad: >5
- [ ] ✅ Tiempo de subida percibido: <5 segundos
- [ ] ✅ Satisfacción de usuario: >4.5/5

**Impacto en Negocio:**
- [ ] ✅ Reducción tiempo listing propiedades: 30%
- [ ] ✅ Incremento sharing rate: 45%
- [ ] ✅ Tickets de soporte storage: <2% del total

---

## 🛡️ Configuraciones de Seguridad Críticas

### 🔒 AWS S3 Security Hardening
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyPublicAccess",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::inmotech-files/*",
        "arn:aws:s3:::inmotech-files"
      ],
      "Condition": {
        "StringEquals": {
          "aws:PrincipalServiceName": "cloudfront.amazonaws.com"
        }
      }
    }
  ]
}
```

### 🌐 CloudFront Security Headers
```javascript
// Lambda@Edge function para headers de seguridad
exports.handler = (event, context, callback) => {
    const response = event.Records[0].cf.response;
    const headers = response.headers;

    headers['strict-transport-security'] = [{
        key: 'Strict-Transport-Security',
        value: 'max-age=31536000; includeSubDomains'
    }];
    
    headers['content-security-policy'] = [{
        key: 'Content-Security-Policy',
        value: "default-src 'self'"
    }];

    callback(null, response);
};
```

---

## 📚 Recursos de Capacitación

### 🎥 Videos Tutoriales
- [ ] **"Upload de archivos desde móvil"** (5 min)
  URL: https://training.inmotech.com/storage/mobile-upload
  
- [ ] **"Configuración de seguridad S3"** (15 min)
  URL: https://training.inmotech.com/storage/s3-security
  
- [ ] **"Troubleshooting común de archivos"** (10 min)
  URL: https://training.inmotech.com/storage/troubleshooting

### 📖 Documentación de Referencia
- [ ] **API Documentation:** https://docs.inmotech.com/api/storage
- [ ] **User Manual:** https://help.inmotech.com/storage/user-guide
- [ ] **Admin Guide:** https://help.inmotech.com/storage/admin-guide
- [ ] **Security Runbook:** https://docs.inmotech.com/security/storage

### 🧪 Entornos de Práctica
- [ ] **Desarrollo:** https://dev-storage.inmotech.com
  - Credenciales: training_user / Training123!
  - Archivos de test disponibles
  
- [ ] **Staging:** https://staging-storage.inmotech.com  
  - Réplica exacta de producción
  - Data sintética para pruebas

---

## ✅ Evaluación y Certificación

### 📝 Evaluación Técnica (Desarrolladores/DevOps)
**Duración:** 45 minutos
**Modalidad:** Práctica + Teórica

**Secciones:**
1. **Configuración (20 pts):** Setup de environment local
2. **Seguridad (25 pts):** Identificar vulnerabilidades en configuración
3. **APIs (20 pts):** Implementar endpoint personalizado
4. **Troubleshooting (20 pts):** Diagnosticar problema simulado
5. **Monitoring (15 pts):** Configurar alertas personalizadas

**Aprobación:** ≥80 puntos

### 📱 Evaluación de Usuario (Agentes)
**Duración:** 20 minutos
**Modalidad:** Demo práctica

**Tareas:**
1. Subir 5 fotos de propiedad desde móvil
2. Organizar archivos por categorías
3. Generar link de compartir temporal
4. Enviar documento via chat
5. Verificar notificaciones de descarga

**Aprobación:** 4/5 tareas completadas exitosamente

---

## 🎯 Plan de Seguimiento Post-Capacitación

### 📅 Sesiones de Refuerzo
**Semana 1:** Sesión Q&A virtual (1 hora)
- Resolución de dudas post-implementación
- Revisión de problemas identificados
- Tips y mejores prácticas adicionales

**Semana 2:** Office Hours técnicas (2 horas)
- Soporte para configuraciones específicas
- Review de código para integraciones custom
- Optimización de performance individual

**Mes 1:** Assessment de adopción
- Análisis de métricas de uso
- Identificación de gaps en conocimiento
- Plan de capacitación adicional si necesario

### 📈 Métricas de Éxito de Capacitación
- [ ] ✅ **Tasa de Finalización:** >95% de participantes completan capacitación
- [ ] ✅ **Evaluación Promedio:** >4.2/5.0 en feedback de calidad
- [ ] ✅ **Certificación:** >90% aprueba evaluación en primer intento
- [ ] ✅ **Tickets Post-Training:** <10% relacionados con storage
- [ ] ✅ **Adopción Features:** >80% usa funcionalidades avanzadas en 2 semanas

### 🔄 Programa de Entrenadores Internos
- [ ] **Carmen López:** Backend APIs y integración
- [ ] **Ricardo Fernández:** DevOps e infraestructura  
- [ ] **Patricia Jiménez:** UX y training de usuarios finales
- [ ] **Carlos Vega:** Procedimientos de Pruebas y QA

**Certificación de Entrenadores:** Programa de 4 horas para preparar a trainers internos

---

## 📋 Checklist Pre-Capacitación

### 🔧 Preparación Técnica
- [ ] ✅ Entornos de capacitación configurados y funcionando
- [ ] ✅ Credenciales de acceso generadas para participantes
- [ ] ✅ Datasets de prueba preparados y cargados
- [ ] ✅ Simuladores de problemas configurados para troubleshooting
- [ ] ✅ Videos y materiales multimedia listos
- [ ] ✅ Backup de configuraciones en caso de issues

### 👥 Preparación Logística
- [ ] ✅ Salas de capacitación reservadas y equipadas
- [ ] ✅ Equipos técnicos (laptops, proyectores) verificados
- [ ] ✅ Conectividad y VPN configurada para acceso remoto
- [ ] ✅ Materiales impresos y swag preparados
- [ ] ✅ Catering coordinado para sesiones presenciales
- [ ] ✅ Links de sesiones virtuales enviados 24h antes

### 📚 Preparación de Contenidos
- [ ] ✅ Presentaciones actualizadas con últimas configuraciones
- [ ] ✅ Labs hands-on probados y documentados
- [ ] ✅ Evaluaciones preparadas y calibradas
- [ ] ✅ Certificados diseñados y templates listos
- [ ] ✅ Plan B preparado para contingencias técnicas

---

## 🎉 Programa de Reconocimiento

### 🏆 Certificaciones Otorgadas
- **🥇 "Storage Systems Expert":** Desarrolladores/DevOps con 90%+ en evaluación
- **🥈 "File Management Specialist":** Personal de soporte con dominio completo
- **🥉 "Storage Power User":** Agentes que usen 100% de features en primera semana

### 🎁 Incentivos de Adopción
- **Early Adopters:** Primeros 20 agentes en usar todas las features → Amazon gift card $50
- **Innovation Award:** Mejor sugerencia de mejora implementada → Apple AirPods
- **Campeón de Capacitación:** Empleado que más ayude a colegas → Día libre adicional

---

**Fecha de Creación:** 21/11/2025
**Última Actualización:** 21/11/2025
**Versión:** 1.0