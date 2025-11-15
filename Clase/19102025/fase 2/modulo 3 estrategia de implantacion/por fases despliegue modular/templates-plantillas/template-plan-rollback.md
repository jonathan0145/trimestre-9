# Plantilla - Plan de Rollback por Fase

## Información de la Fase

**Nombre de la Fase:** [NOMBRE_FASE]
**Número de Fase:** [NUMERO]
**Fecha de Implementación:** [FECHA_IMPLEMENTACION]
**Fecha del Plan de Rollback:** [FECHA_PLAN]
**Responsable Técnico:** [RESPONSABLE_TECNICO]
**Responsable de Rollback:** [RESPONSABLE_ROLLBACK]
**Ventana de Rollback:** [TIEMPO_MAXIMO_ROLLBACK]

---

## 🚨 Información Crítica de Rollback

### Estado Actual del Rollback
- [ ] **🟢 Plan Preparado** - Todo listo para rollback si es necesario
- [ ] **🟡 En Evaluación** - Decidiendo si ejecutar rollback
- [ ] **🔴 Rollback en Progreso** - Ejecutando procedimientos de reversión
- [ ] **✅ Rollback Completado** - Reversión exitosa ejecutada
- [ ] **❌ Rollback Fallido** - Proceso de rollback tuvo problemas

### Ventana de Tiempo Crítica
| Tiempo desde Deploy | Acción Disponible | Complejidad | Tiempo Estimado |
|---------------------|------------------|-------------|-----------------|
| **0-4 horas** | 🟢 Rollback Inmediato | Baja | 15-30 min |
| **4-24 horas** | 🟡 Rollback Estándar | Media | 1-2 horas |
| **24-72 horas** | 🔴 Rollback Complejo | Alta | 4-8 horas |
| **>72 horas** | ⚫ Rollback Crítico | Muy Alta | 8-24 horas |

---

## 🎯 Criterios de Activación de Rollback

### Criterios Automáticos (Rollback Inmediato)
- [ ] **Errores Críticos de Sistema**
  - Tasa de error HTTP 5xx > 5% por más de 5 minutos
  - Tiempo de respuesta API > 10 segundos
  - Caída completa del sistema por > 2 minutos

- [ ] **Problemas de Seguridad**
  - Brecha de seguridad detectada
  - Exposición de datos sensibles
  - Acceso no autorizado a recursos críticos

- [ ] **Falla de Integración Crítica**
  - APIs de fases anteriores no funcionan
  - Corrupción de datos en base de datos
  - Pérdida de funcionalidad esencial

### Criterios de Evaluación (Decisión de Rollback)
- [ ] **Performance Degradada**
  - Tiempo de respuesta > 2x baseline por > 30 min
  - Throughput < 50% del esperado
  - Recursos del servidor > 90% por > 15 min

- [ ] **Problemas de Usuario**
  - > 10 reportes de bugs críticos en primera hora
  - Imposibilidad de completar flujos principales
  - Quejas masivas en canales de soporte

- [ ] **Problemas de Datos**
  - Inconsistencias en reportes
  - Datos faltantes o corruptos
  - Problemas de sincronización

---

## 🔧 Procedimientos de Rollback Técnico

### 📦 Rollback de Aplicación (Backend/Frontend)

#### Paso 1: Preparación Inmediata (5 min)
```bash
# 1. Activar modo de mantenimiento
curl -X POST https://api.inmotech.com/admin/maintenance/enable

# 2. Verificar última versión estable
git log --oneline -10
git tag | grep "stable"

# 3. Identificar versión de rollback
export ROLLBACK_VERSION="stable-fase-[X-1]"
export CURRENT_VERSION=$(git rev-parse HEAD)

# 4. Notificar inicio de rollback
echo "INICIO ROLLBACK: $(date) - De ${CURRENT_VERSION} a ${ROLLBACK_VERSION}" >> /var/log/rollback.log
```

#### Paso 2: Rollback de Código (15 min)
```bash
# Backend Rollback
cd /app/backend
git checkout ${ROLLBACK_VERSION}
npm ci --production
pm2 restart inmotech-backend

# Frontend Rollback  
cd /app/frontend
git checkout ${ROLLBACK_VERSION}
npm ci
npm run build
nginx -s reload

# Verificar que servicios estén activos
systemctl status nginx
systemctl status postgres
pm2 status
```

#### Paso 3: Rollback de Base de Datos (30-60 min)
```sql
-- 1. Verificar backup disponible
SELECT backup_name, backup_date 
FROM backup_log 
WHERE backup_date >= 'FECHA_PRE_DEPLOY' 
ORDER BY backup_date DESC 
LIMIT 5;

-- 2. Crear punto de restauración actual
pg_dump inmotech_db > /backups/emergency-$(date +%Y%m%d_%H%M%S).sql

-- 3. Restaurar backup pre-deploy
psql inmotech_db < /backups/pre-fase-[X]-[FECHA].sql

-- 4. Verificar integridad de datos
SELECT COUNT(*) FROM users;
SELECT COUNT(*) FROM properties;
SELECT COUNT(*) FROM [TABLA_FASE_ANTERIOR];
```

#### Paso 4: Rollback de Configuración (10 min)
```bash
# Variables de entorno
cp /config/env/.env.fase[X-1] /app/.env

# Configuración de Nginx
cp /config/nginx/nginx.fase[X-1].conf /etc/nginx/nginx.conf
nginx -t && nginx -s reload

# Configuración de PM2
cp /config/pm2/ecosystem.fase[X-1].config.js /app/ecosystem.config.js
pm2 reload ecosystem.config.js
```

#### Paso 5: Verificación Post-Rollback (10 min)
```bash
# Health checks
curl -f https://api.inmotech.com/health || echo "API FALLA"
curl -f https://inmotech.com || echo "FRONTEND FALLA"

# Tests críticos
cd /app/tests
npm run smoke-tests
npm run critical-path-tests

# Logs de errores
tail -f /var/log/inmotech/error.log
tail -f /var/log/nginx/error.log
```

---

### 📊 Rollback de Datos Específicos

#### Datos de Usuario
```sql
-- Backup de usuarios nuevos (para posible re-migración)
CREATE TABLE users_fase[X]_backup AS 
SELECT * FROM users 
WHERE created_at >= 'FECHA_DEPLOY_FASE[X]';

-- Rollback a estado anterior
DELETE FROM users WHERE created_at >= 'FECHA_DEPLOY_FASE[X]';
DELETE FROM user_roles WHERE created_at >= 'FECHA_DEPLOY_FASE[X]';
DELETE FROM user_permissions WHERE created_at >= 'FECHA_DEPLOY_FASE[X]';
```

#### Datos de Configuración
```sql
-- Restaurar configuraciones previas
UPDATE system_config 
SET config_value = backup_config.value
FROM backup_config_fase[X-1] backup_config
WHERE system_config.config_key = backup_config.config_key;
```

#### Datos de Transacciones (¡CRÍTICO!)
```sql
-- NO eliminar datos transaccionales
-- Solo deshabilitar funcionalidades nuevas
UPDATE properties SET status = 'migrated_rollback' 
WHERE created_at >= 'FECHA_DEPLOY_FASE[X]';

UPDATE offers SET is_active = false 
WHERE created_at >= 'FECHA_DEPLOY_FASE[X]';
```

---

### ☁️ Rollback de Infraestructura

#### AWS/Cloud Resources
```bash
# Terraform rollback
cd /infrastructure
terraform plan -var="version=stable-fase-[X-1]"
terraform apply -var="version=stable-fase-[X-1]"

# Docker images rollback
docker pull inmotech/backend:stable-fase-[X-1]
docker pull inmotech/frontend:stable-fase-[X-1]

# Kubernetes rollback
kubectl rollout undo deployment/inmotech-backend
kubectl rollout undo deployment/inmotech-frontend
kubectl rollout status deployment/inmotech-backend
```

#### Load Balancer/CDN
```bash
# Actualizar configuración de load balancer
aws elbv2 modify-target-group \
  --target-group-arn arn:aws:elasticloadbalancing:... \
  --health-check-path /health/fase-[X-1]

# Invalidar CDN cache
aws cloudfront create-invalidation \
  --distribution-id E123456789 \
  --paths "/*"
```

---

## 📋 Checklist de Ejecución de Rollback

### Pre-Rollback (Preparación)
- [ ] **Verificar Triggers de Rollback**
  - [ ] Criterios de activación confirmados
  - [ ] Aprobación de stakeholders obtenida (si no es automático)
  - [ ] Equipo técnico disponible y alertado

- [ ] **Preparar Entorno**
  - [ ] Backups verificados y disponibles
  - [ ] Scripts de rollback probados en staging
  - [ ] Recursos de infraestructura confirmados
  - [ ] Ventana de mantenimiento programada

- [ ] **Comunicación**
  - [ ] Stakeholders notificados del inicio
  - [ ] Usuarios informados (página de estado)
  - [ ] Equipo de soporte alertado
  - [ ] Canales de comunicación abiertos

### Durante Rollback (Ejecución)
- [ ] **Modo Mantenimiento**
  - [ ] Sistema en modo mantenimiento activado
  - [ ] Tráfico de usuarios redirigido
  - [ ] APIs críticas pausadas
  - [ ] Logs de rollback iniciados

- [ ] **Rollback de Código**
  - [ ] Código revertido a versión estable
  - [ ] Dependencias actualizadas
  - [ ] Servicios reiniciados
  - [ ] Health checks de aplicación pasando

- [ ] **Rollback de Datos**
  - [ ] Backup de estado actual creado
  - [ ] Base de datos restaurada
  - [ ] Integridad de datos verificada
  - [ ] Migraciones revertidas

- [ ] **Rollback de Infraestructura**
  - [ ] Configuraciones revertidas
  - [ ] Recursos cloud actualizados
  - [ ] CDN/Cache invalidado
  - [ ] DNS actualizado si necesario

### Post-Rollback (Verificación)
- [ ] **Verificación Técnica**
  - [ ] Smoke tests ejecutados exitosamente
  - [ ] Performance dentro de parámetros normales
  - [ ] Logs sin errores críticos
  - [ ] Monitoreo confirmando estabilidad

- [ ] **Verificación de Usuario**
  - [ ] Flujos críticos funcionando
  - [ ] Interfaces accesibles
  - [ ] Datos de usuario intactos
  - [ ] Funcionalidades esenciales operativas

- [ ] **Comunicación Final**
  - [ ] Stakeholders notificados de finalización
  - [ ] Usuarios informados de restauración
  - [ ] Modo mantenimiento desactivado
  - [ ] Post-mortem programado

---

## 📊 Monitoreo Post-Rollback

### Métricas Críticas (Primeras 4 horas)
| Métrica | Target Post-Rollback | Monitor Frequency |
|---------|---------------------|------------------|
| **Error Rate** | < 0.1% | Cada 1 min |
| **Response Time** | < baseline + 10% | Cada 1 min |
| **Database Connections** | < 80% máximo | Cada 5 min |
| **Memory Usage** | < 85% | Cada 5 min |
| **CPU Usage** | < 80% | Cada 5 min |
| **Active Users** | Progresivamente normal | Cada 10 min |

### Alertas Especiales Post-Rollback
```bash
# Configurar alertas específicas
alert_manager add --name="post-rollback-errors" \
  --condition="error_rate > 0.5%" \
  --duration="2 minutes" \
  --severity="critical"

alert_manager add --name="post-rollback-performance" \
  --condition="response_time > baseline * 1.5" \
  --duration="5 minutes" \
  --severity="warning"
```

---

## 🔄 Plan de Recuperación (Si Rollback Falla)

### Escenario 1: Rollback de Código Falla
1. **Immediate Actions:**
   ```bash
   # Force pull desde backup git
   git fetch --all
   git reset --hard origin/stable-fase-[X-1]
   
   # Restaurar desde backup físico
   cp -r /backups/code/fase-[X-1]/* /app/
   ```

2. **Alternative Deploy:**
   - Desplegar versión estable desde CI/CD
   - Usar imagen Docker de backup
   - Configurar instancia nueva con versión estable

### Escenario 2: Rollback de BD Falla
1. **Recovery Actions:**
   ```sql
   -- Restaurar desde backup secundario
   DROP DATABASE inmotech_db;
   CREATE DATABASE inmotech_db;
   psql inmotech_db < /backups/secondary/[FECHA].sql
   ```

2. **Data Reconstruction:**
   - Usar réplica de read-only
   - Reconstruir desde logs de transacciones
   - Contactar soporte de proveedor cloud

### Escenario 3: Sistema Completamente Comprometido
1. **Emergency Procedures:**
   - Activar instancia de disaster recovery
   - Redireccionar DNS a entorno de backup
   - Restaurar desde snapshot completo del sistema
   - Activar plan de comunicación de crisis

---

## 📞 Contactos de Emergency Rollback

### Equipo de Rollback (24/7)
| Rol | Nombre | Teléfono | Responsabilidad |
|-----|--------|----------|----------------|
| **Rollback Lead** | [Nombre] | [Teléfono] | Decisión y coordinación |
| **Backend Lead** | [Nombre] | [Teléfono] | Rollback de código backend |
| **Frontend Lead** | [Nombre] | [Teléfono] | Rollback de código frontend |
| **DBA** | [Nombre] | [Teléfono] | Rollback de base de datos |
| **DevOps Lead** | [Nombre] | [Teléfono] | Rollback de infraestructura |
| **Project Manager** | [Nombre] | [Teléfono] | Comunicación con stakeholders |

### Proveedores Críticos
| Servicio | Contacto | Escalación | SLA Emergency |
|----------|----------|------------|---------------|
| **AWS Support** | [Contacto] | [Proceso] | 15 min response |
| **Database Provider** | [Contacto] | [Proceso] | 30 min response |
| **CDN Provider** | [Contacto] | [Proceso] | 10 min response |

---

## 📝 Documentación Post-Rollback

### Registro de Rollback
**Fecha/Hora:** [Timestamp]
**Duración:** [X] horas [Y] minutos
**Razón:** [Motivo del rollback]
**Trigger:** [Automático/Manual]

### Componentes Afectados
- [ ] Backend: [Detalles]
- [ ] Frontend: [Detalles]  
- [ ] Base de Datos: [Detalles]
- [ ] Infraestructura: [Detalles]

### Lecciones Aprendidas
1. **¿Qué causó la necesidad de rollback?**
2. **¿El proceso de rollback funcionó como esperado?**
3. **¿Qué se puede mejorar en futuros rollbacks?**
4. **¿Necesitamos ajustar los criterios de activación?**

### Acciones de Mejora
- [ ] [Acción 1] - Responsable: [Nombre] - Fecha: [Fecha]
- [ ] [Acción 2] - Responsable: [Nombre] - Fecha: [Fecha]
- [ ] [Acción 3] - Responsable: [Nombre] - Fecha: [Fecha]

---

**📝 Notas Importantes:**
1. Mantener este plan actualizado antes de cada despliegue
2. Probar procedimientos de rollback en staging regularmente
3. Verificar backups antes de cada deploy
4. Documentar cualquier cambio en el proceso

**🔄 Última Actualización:** [Fecha]
**📌 Versión:** 1.0
**✅ Estado:** [Borrador/En Revisión/Aprobado]