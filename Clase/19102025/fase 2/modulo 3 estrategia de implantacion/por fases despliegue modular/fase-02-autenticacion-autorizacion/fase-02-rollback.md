# Plan de Rollback - Fase 2: Autenticación y Autorización

## Información de la Fase

**Nombre de la Fase:** Autenticación y Autorización  
**Número de Fase:** 02  
**Fecha de Implementación:** 25-30 Noviembre 2024  
**Fecha del Plan de Rollback:** 15 Noviembre 2024  
**Responsable Técnico:** Lead Developer Backend  
**Responsable de Rollback:** DevOps Engineer  
**Ventana de Rollback:** 4-24 horas máximo

---

## 🚨 Información Crítica de Rollback

### Estado Actual del Rollback
- [x] **🟢 Plan Preparado** - Todo listo para rollback si es necesario
- [ ] **🟡 En Evaluación** - Decidiendo si ejecutar rollback
- [ ] **🔴 Rollback en Progreso** - Ejecutando procedimientos de reversión
- [ ] **✅ Rollback Completado** - Reversión exitosa ejecutada
- [ ] **❌ Rollback Fallido** - Proceso de rollback tuvo problemas

### Ventana de Tiempo Crítica - Fase 2 Autenticación
| Tiempo desde Deploy | Acción Disponible | Complejidad | Tiempo Estimado |
|---------------------|------------------|-------------|-----------------|
| **0-4 horas** | 🟢 Rollback Inmediato | Baja | 15-30 min |
| **4-24 horas** | 🟡 Rollback Estándar | Media | 1-2 horas |
| **24-72 horas** | 🔴 Rollback Complejo | Alta | 4-8 horas |
| **>72 horas** | ⚫ Rollback Crítico | Muy Alta | 8-24 horas |

### Componentes Específicos de Autenticación Afectados
- Sistema JWT (JSON Web Tokens)
- Middleware de autenticación
- Controladores de auth (login/register)
- Modelo de Usuario y Roles
- Frontend: servicios de autenticación
- Routes protegidas por roles

---

## 🎯 Criterios de Activación de Rollback

### Criterios Automáticos (Rollback Inmediato)
- [x] **Errores Críticos de Autenticación**
  - Tasa de falla en login > 15% por más de 5 minutos
  - JWT tokens inválidos > 10% de requests
  - Imposibilidad total de autenticación por > 2 minutos
  - Error 401/403 en rutas protegidas > 20%

- [x] **Problemas de Seguridad Críticos**
  - Brecha en sistema de autenticación detectada
  - Tokens JWT comprometidos o vulnerables
  - Escalation de privilegios no autorizada
  - Acceso de usuarios sin permisos a recursos restringidos

- [x] **Falla de Integración con Fase 1**
  - Pérdida de conexión con base de datos de usuarios
  - Corrupción en tabla Users, Roles o Permissions
  - APIs de gestión de usuarios no funcionan

### Criterios de Evaluación (Decisión de Rollback)
- [x] **Performance de Autenticación Degradada**
  - Tiempo de respuesta de login > 5 segundos
  - Verificación JWT > 1 segundo por request
  - Carga del servidor por auth > 80% por > 15 min

- [x] **Problemas de Usuario en Autenticación**
  - > 5 reportes de imposibilidad de login en primera hora
  - Usuarios no pueden acceder a sus roles correctos
  - Pérdida de sesiones de forma masiva

- [x] **Problemas de Datos de Autenticación**
  - Inconsistencias en permisos de usuario
  - Roles asignados incorrectamente
  - Pérdida de datos de sesión o preferencias

---

## 🔧 Procedimientos de Rollback Técnico

### 📦 Rollback de Autenticación (Backend/Frontend)

#### Paso 1: Preparación Inmediata (5 min)
```bash
# 1. Activar modo de mantenimiento
curl -X POST https://api.inmotech.com/admin/maintenance/enable \
  -H "Authorization: Bearer $ADMIN_TOKEN"

# 2. Verificar última versión estable pre-autenticación
git log --oneline -10 --grep="fase-01"
git tag | grep "fase-01-stable"

# 3. Identificar versión de rollback específica
export ROLLBACK_VERSION="fase-01-stable-v1.2.0"
export CURRENT_VERSION=$(git rev-parse HEAD)
export ROLLBACK_TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

# 4. Backup de estado actual de autenticación
pg_dump -h localhost -U postgres -t users -t roles -t permissions -t user_roles \
  inmotech_db > backup_auth_pre_rollback_${ROLLBACK_TIMESTAMP}.sql

# 5. Notificar inicio de rollback
echo "ROLLBACK AUTH FASE-02: $(date) - De ${CURRENT_VERSION} a ${ROLLBACK_VERSION}" >> /var/log/rollback.log
```

#### Paso 2: Rollback de Código de Autenticación (15 min)
```bash
# Backend Rollback - Componentes de Autenticación
cd /app/backend

# Guardar configuraciones actuales de JWT
cp config/jwt.config.js config/jwt.config.backup.${ROLLBACK_TIMESTAMP}.js
cp .env .env.backup.${ROLLBACK_TIMESTAMP}

# Rollback a versión estable
git checkout ${ROLLBACK_VERSION}

# Restaurar dependencias específicas
npm ci --production

# Verificar que los módulos de autenticación anteriores funcionan
node -e "console.log('Testing auth modules...'); require('./src/controllers/authController.js');"

# Reiniciar servicios backend
pm2 restart inmotech-backend
pm2 restart inmotech-auth-worker

# Frontend Rollback - Servicios de Autenticación
cd /app/frontend

# Backup de configuraciones de auth actuales
cp src/services/authService.js src/services/authService.backup.${ROLLBACK_TIMESTAMP}.js
cp src/store/authStore.js src/store/authStore.backup.${ROLLBACK_TIMESTAMP}.js

# Rollback frontend
git checkout ${ROLLBACK_VERSION}
npm ci

# Rebuild sin características de autenticación avanzada
npm run build

# Desplegar versión anterior
pm2 restart inmotech-frontend
```

#### Paso 3: Rollback de Base de Datos de Autenticación (20 min)
```sql
-- Conectar a base de datos
psql -h localhost -U postgres -d inmotech_db

-- 1. Backup de estado actual
\copy users TO 'backup_users_pre_rollback.csv' CSV HEADER;
\copy roles TO 'backup_roles_pre_rollback.csv' CSV HEADER;
\copy permissions TO 'backup_permissions_pre_rollback.csv' CSV HEADER;

-- 2. Eliminar nuevas estructuras de autenticación de Fase 2
DROP TABLE IF EXISTS user_sessions CASCADE;
DROP TABLE IF EXISTS password_resets CASCADE;
DROP TABLE IF EXISTS oauth_tokens CASCADE;

-- 3. Revertir columnas añadidas en Fase 2
ALTER TABLE users DROP COLUMN IF EXISTS last_login_at;
ALTER TABLE users DROP COLUMN IF EXISTS failed_login_attempts;
ALTER TABLE users DROP COLUMN IF EXISTS locked_until;
ALTER TABLE users DROP COLUMN IF EXISTS two_factor_enabled;
ALTER TABLE users DROP COLUMN IF EXISTS email_verified_at;

-- 4. Restaurar estructura simple de Fase 1
ALTER TABLE users ALTER COLUMN password_hash TYPE VARCHAR(255);
ALTER TABLE users DROP CONSTRAINT IF EXISTS unique_email_verified;

-- 5. Limpiar roles y permisos avanzados
DELETE FROM permissions WHERE name LIKE '%advanced_%';
DELETE FROM roles WHERE name IN ('moderator', 'super_admin');

-- 6. Verificar integridad
SELECT COUNT(*) FROM users WHERE email IS NOT NULL;
SELECT COUNT(*) FROM roles WHERE name IN ('buyer', 'seller', 'agent');
```

#### Paso 4: Verificación Post-Rollback (10 min)
```bash
# 1. Verificar servicios funcionando
curl -f http://localhost:3000/api/health || echo "Backend DOWN"
curl -f http://localhost:3001 || echo "Frontend DOWN"

# 2. Verificar autenticación básica funciona
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"test123"}' \
  | jq '.success'

# 3. Verificar base de datos
psql -h localhost -U postgres -d inmotech_db -c "SELECT COUNT(*) FROM users;"

# 4. Test de integración básica
npm run test:integration:fase01

# 5. Verificar logs sin errores críticos
tail -f /var/log/inmotech.log | grep -i error | head -10
```

---

## 📊 Rollback de Datos Específico de Autenticación

### Procedimiento de Backup Pre-Rollback
```bash
# Script de backup completo de autenticación
#!/bin/bash
BACKUP_DIR="/backup/rollback_fase02_$(date +%Y%m%d_%H%M%S)"
mkdir -p $BACKUP_DIR

# 1. Backup de código
git bundle create $BACKUP_DIR/fase02_code_backup.bundle HEAD

# 2. Backup de configuraciones
cp /app/backend/config/jwt.config.js $BACKUP_DIR/
cp /app/backend/.env $BACKUP_DIR/env_backup
cp /app/frontend/src/services/authService.js $BACKUP_DIR/

# 3. Backup de base de datos de auth
pg_dump -h localhost -U postgres \
  --table=users --table=roles --table=permissions --table=user_roles \
  inmotech_db > $BACKUP_DIR/auth_tables_backup.sql

# 4. Backup de logs
cp /var/log/auth.log $BACKUP_DIR/
cp /var/log/security.log $BACKUP_DIR/

echo "Backup completo en: $BACKUP_DIR"
```

### Restauración de Datos Post-Rollback
```sql
-- 1. Verificar usuarios pueden hacer login
SELECT u.email, r.name as role
FROM users u
LEFT JOIN user_roles ur ON u.id = ur.user_id
LEFT JOIN roles r ON ur.role_id = r.id
WHERE u.email LIKE '%test%'
LIMIT 5;

-- 2. Verificar permisos básicos funcionan
SELECT r.name, COUNT(rp.permission_id) as permission_count
FROM roles r
LEFT JOIN role_permissions rp ON r.id = rp.role_id
GROUP BY r.id, r.name;

-- 3. Limpiar datos inconsistentes si los hay
DELETE FROM user_roles WHERE role_id NOT IN (SELECT id FROM roles);
DELETE FROM role_permissions WHERE permission_id NOT IN (SELECT id FROM permissions);
```

---

## 🔄 Procedimientos de Rollback por Componente

### 1. Rollback de JWT Sistema
```bash
# Desactivar JWT y volver a autenticación simple
cd /app/backend/src/middlewares

# Backup del middleware actual
cp authMiddleware.js authMiddleware.fase02.backup.js

# Restaurar middleware simple de Fase 1
git checkout fase-01-stable -- src/middlewares/authMiddleware.js

# Verificar que funciona sin JWT
node -e "
const auth = require('./src/middlewares/authMiddleware.js');
console.log('Auth middleware loaded successfully');
"

# Reiniciar con nuevo middleware
pm2 restart inmotech-backend
```

### 2. Rollback de Controladores de Auth
```bash
cd /app/backend/src/controllers

# Backup controlador actual
cp authController.js authController.fase02.backup.js

# Restaurar controlador simple
git checkout fase-01-stable -- src/controllers/authController.js

# Verificar funcionalidad básica
curl -X POST http://localhost:3000/api/auth/test \
  -H "Content-Type: application/json"
```

### 3. Rollback de Frontend Auth Services
```bash
cd /app/frontend/src/services

# Backup servicio actual
cp authService.js authService.fase02.backup.js

# Restaurar servicio simple
git checkout fase-01-stable -- src/services/authService.js

# Rebuild sin características avanzadas
npm run build:simple
```

---

## 🚨 Procedimientos de Emergencia

### Rollback de Emergencia Inmediato (< 5 min)
```bash
#!/bin/bash
# Script de rollback de emergencia - Ejecutar si todo falla

echo "EJECUTANDO ROLLBACK DE EMERGENCIA - FASE 2 AUTH"

# 1. Parar todos los servicios
pm2 stop all
sudo systemctl stop nginx

# 2. Restaurar desde backup inmediato
cd /app
rm -rf backend frontend
tar -xzf /backup/fase01_stable_complete.tar.gz

# 3. Restaurar base de datos completa
sudo -u postgres psql inmotech_db < /backup/fase01_db_complete.sql

# 4. Reiniciar servicios
pm2 start ecosystem.config.js
sudo systemctl start nginx

# 5. Verificar
curl -f http://localhost:3000/api/health && echo "✅ ROLLBACK EXITOSO" || echo "❌ ROLLBACK FALLÓ"
```

### Contactos de Emergencia para Rollback
- **DevOps Lead**: +57 300 123 4567 (disponible 24/7)
- **Database Admin**: +57 300 234 5678 (disponible 8am-8pm)
- **Security Team**: security@inmotech.com (respuesta < 30 min)
- **Project Manager**: +57 300 345 6789

---

## 📋 Checklist de Verificación Post-Rollback

### ✅ Funcionalidad Básica
- [ ] Usuarios pueden hacer login con email/password
- [ ] Roles básicos (buyer, seller, agent) funcionan
- [ ] Permisos básicos se aplican correctamente
- [ ] APIs de Fase 1 funcionan sin errores
- [ ] Frontend carga sin errores de autenticación

### ✅ Integridad de Datos
- [ ] Todos los usuarios existentes mantienen sus datos
- [ ] Relaciones user-role preservadas
- [ ] No hay datos corruptos en tablas críticas
- [ ] Backups disponibles para re-implementación

### ✅ Performance y Estabilidad
- [ ] Tiempo de respuesta de login < 2 segundos
- [ ] APIs responden en < 1 segundo
- [ ] Sin memory leaks en servicios
- [ ] CPU y memoria en rangos normales

### ✅ Seguridad Básica
- [ ] Passwords siguen hasheados correctamente
- [ ] Rutas protegidas requieren autenticación
- [ ] No hay exposición de datos sensibles
- [ ] Logs de seguridad funcionando

---

## 📊 Métricas de Rollback

### Tiempos de Rollback Registrados
| Tipo de Rollback | Tiempo Planeado | Tiempo Real | Éxito |
|------------------|-----------------|-------------|-------|
| Código Backend | 15 min | - | - |
| Código Frontend | 10 min | - | - |
| Base de Datos | 20 min | - | - |
| Verificación | 10 min | - | - |
| **TOTAL** | **55 min** | **-** | **-** |

### Criterios de Éxito de Rollback
- ✅ Tiempo total < 60 minutos
- ✅ Sin pérdida de datos de usuarios
- ✅ Funcionalidad de Fase 1 100% operativa
- ✅ Performance igual o mejor que pre-Fase 2
- ✅ Cero incidentes de seguridad durante rollback

---

## 📝 Lecciones Aprendidas y Mejoras

### Para Futuras Implementaciones
1. **Backup Automático**: Crear backups automatizados cada 2 horas durante primeras 24h
2. **Testing Rollback**: Probar procedimientos de rollback en ambiente QA antes de producción  
3. **Monitoring**: Alertas automáticas cuando se cumplen criterios de rollback
4. **Documentación**: Mantener scripts de rollback actualizados con cada deploy
5. **Training**: Entrenar a todo el equipo en procedimientos de rollback

### Contacto y Escalación
**Responsable del Plan**: DevOps Engineer  
**Email**: devops@inmotech.com  
**Slack**: #emergency-rollback  
**Teléfono Emergency**: +57 300 123 4567  

---

*Documento generado el 15 de Noviembre 2024*  
*Última actualización: v2.1 - Plan de Rollback Fase 2*  
*Próxima revisión: Post-implementación Fase 2*