# Procedimientos de Rollback - Fase 3: Gestión de Usuarios y Agentes

## Información de la Fase

**Nombre de la Fase:** Gestión de Usuarios y Agentes  
**Número de Fase:** 03  
**Fecha de Implementación:** 15-21 Enero 2026  
**Responsable de Rollback:** DevOps Engineer  
**Responsable Técnico:** Backend Lead  
**Ventana de Rollback:** 2-24 horas máximo

---

## 🚨 Información Crítica de Rollback

### Estado Actual del Rollback
- [x] **🟢 Plan Preparado** - Todo listo para rollback si es necesario
- [ ] **🟡 En Evaluación** - Decidiendo si ejecutar rollback
- [ ] **🔴 Rollback en Progreso** - Ejecutando procedimientos de reversión
- [ ] **✅ Rollback Completado** - Reversión exitosa ejecutada
- [ ] **❌ Rollback Fallido** - Proceso de rollback tuvo problemas

### Ventana de Tiempo Crítica - Fase 3 User Management
| Tiempo desde Deploy | Acción Disponible | Complejidad | Tiempo Estimado |
|---------------------|------------------|-------------|-----------------|
| **0-2 horas** | 🟢 Rollback Inmediato | Baja | 30-45 min |
| **2-12 horas** | 🟡 Rollback Estándar | Media | 1-3 horas |
| **12-48 horas** | 🔴 Rollback Complejo | Alta | 4-8 horas |
| **>48 horas** | ⚫ Rollback Crítico | Muy Alta | 8-24 horas |

### Componentes Específicos Afectados
- User Management APIs (CRUD operations)
- Profile Management System (user & agent profiles)
- Search & Filtering capabilities
- Admin Dashboard user tools
- Frontend user management pages

---

## 🎯 Criterios de Activación de Rollback

### Criterios Automáticos (Rollback Inmediato)
- [x] **Errores Críticos en User Management**
  - Tasa de falla en user operations > 20% por más de 10 minutos
  - Database corruption en tablas de user profiles
  - Complete loss of user search functionality

- [x] **Performance Degradación Severa**
  - User search response time > 5 segundos consistentemente
  - Profile loading time > 10 segundos
  - Database queries timing out (>30 segundos)

- [x] **Security Issues**
  - Unauthorized access to user data detectado
  - Privacy violations en profile visibility
  - Data leakage entre user profiles

### Criterios de Evaluación (Decisión de Rollback)
- [x] **User Experience Impact**
  - User satisfaction score < 2.5/5
  - Task completion rate < 70% en user management
  - >50 support tickets relacionados con new features en 24 horas

- [x] **Business Impact**
  - Agent adoption rate < 40% después de 3 días
  - Admin productivity decrease > 30%
  - User retention decrease > 10% week-over-week

---

## 🔧 Procedimientos de Rollback Técnico

### 📦 Rollback de User Management (Backend/Frontend/Database)

#### Paso 1: Activación de Rollback (10 min)
```bash
# 1. Activar modo de mantenimiento inmediato
curl -X POST https://api.inmotech.com/admin/maintenance/enable \
  -H "Authorization: Bearer $ADMIN_TOKEN"

# 2. Notificar inicio de rollback
slack-notify "#alerts" "🚨 ROLLBACK FASE-3 INICIADO - User Management"

# 3. Identificar versión de rollback
export ROLLBACK_VERSION="fase-02-stable-v2.1.0"
export CURRENT_VERSION=$(git rev-parse HEAD)
export ROLLBACK_TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

# 4. Backup estado actual para análisis
pg_dump -h localhost -U postgres -t user_profiles -t agent_profiles -t user_settings \
  inmotech_db > backup_user_mgmt_pre_rollback_${ROLLBACK_TIMESTAMP}.sql

# 5. Log inicio de rollback
echo "ROLLBACK USER-MGMT FASE-03: $(date) - De ${CURRENT_VERSION} a ${ROLLBACK_VERSION}" >> /var/log/rollback.log
```

#### Paso 2: Rollback de Base de Datos (30 min)
```sql
-- Conectar a base de datos como superuser
psql -h localhost -U postgres -d inmotech_db

-- 1. Backup completo estado actual
\copy user_profiles TO 'backup_user_profiles_rollback.csv' CSV HEADER;
\copy agent_profiles TO 'backup_agent_profiles_rollback.csv' CSV HEADER;
\copy user_settings TO 'backup_user_settings_rollback.csv' CSV HEADER;

BEGIN;

-- 2. Remove foreign key dependencies first
ALTER TABLE user_profiles DROP CONSTRAINT IF EXISTS user_profiles_user_id_fkey;
ALTER TABLE agent_profiles DROP CONSTRAINT IF EXISTS agent_profiles_user_id_fkey;
ALTER TABLE user_settings DROP CONSTRAINT IF EXISTS user_settings_user_id_fkey;

-- 3. Drop new indexes que pueden causar dependency issues
DROP INDEX IF EXISTS idx_user_profiles_city_state;
DROP INDEX IF EXISTS idx_agent_profiles_verified;
DROP INDEX IF EXISTS idx_agent_profiles_specializations;
DROP INDEX IF EXISTS idx_agent_profiles_service_areas;

-- 4. Drop triggers
DROP TRIGGER IF EXISTS update_user_profiles_updated_at ON user_profiles;
DROP TRIGGER IF EXISTS update_agent_profiles_updated_at ON agent_profiles;
DROP TRIGGER IF EXISTS update_user_settings_updated_at ON user_settings;

-- 5. Drop functions
DROP FUNCTION IF EXISTS update_updated_at_column() CASCADE;

-- 6. Drop nuevas tablas en orden correcto
DROP TABLE IF EXISTS user_settings CASCADE;
DROP TABLE IF EXISTS agent_profiles CASCADE;
DROP TABLE IF EXISTS user_profiles CASCADE;

-- 7. Verificar que tablas fueron eliminadas
SELECT COUNT(*) as remaining_tables 
FROM information_schema.tables 
WHERE table_schema = 'public' 
  AND table_name IN ('user_profiles', 'agent_profiles', 'user_settings');

-- 8. Clean up cualquier data residual en otras tablas
-- (No hay data dependiente en esta fase)

COMMIT;

-- 9. Refresh database statistics
ANALYZE;

-- 10. Verificar integridad de tablas existentes
SELECT COUNT(*) as users FROM users WHERE is_active = true;
SELECT COUNT(*) as roles FROM roles;
SELECT COUNT(*) as permissions FROM permissions;
```

#### Paso 3: Rollback de Código Backend (20 min)
```bash
# Backend Rollback
cd /app/backend

# 1. Backup configuraciones actuales
cp .env .env.backup.${ROLLBACK_TIMESTAMP}
cp package.json package.backup.${ROLLBACK_TIMESTAMP}.json

# 2. Checkout a versión estable anterior
git checkout ${ROLLBACK_VERSION}

# 3. Restaurar dependencias de la versión anterior
npm ci --production

# 4. Verificar que módulos user management no están presentes
if [ -f "src/controllers/userController.js" ]; then
    echo "ERROR: userController todavía presente post-rollback"
    exit 1
fi

# 5. Verificar que APIs básicas de Fase 2 funcionan
node -e "
  const app = require('./src/index.js');
  console.log('Backend rollback verification passed');
  process.exit(0);
"

# 6. Reiniciar servicios backend
pm2 restart inmotech-backend
pm2 restart inmotech-worker

# 7. Health check
sleep 10
curl -f http://localhost:3001/api/health || echo "Backend health check failed"
```

#### Paso 4: Rollback de Frontend (15 min)
```bash
# Frontend Rollback
cd /app/frontend

# 1. Backup estado actual
cp package.json package.backup.${ROLLBACK_TIMESTAMP}.json
cp src/App.js src/App.backup.${ROLLBACK_TIMESTAMP}.js

# 2. Checkout a versión anterior
git checkout ${ROLLBACK_VERSION}

# 3. Restaurar dependencies
npm ci

# 4. Verificar que componentes user management no están presentes
if [ -d "src/pages/users" ]; then
    echo "ERROR: User management pages todavía presentes"
    exit 1
fi

# 5. Build nueva versión
npm run build

# 6. Deploy build anterior
pm2 restart inmotech-frontend

# 7. Verificar que app carga
curl -f http://localhost:3000 || echo "Frontend health check failed"
```

#### Paso 5: Verificación y Cleanup (15 min)
```bash
# 1. Verificar que user management endpoints no están disponibles
curl -X GET http://localhost:3001/api/users && echo "ERROR: User management API still active" || echo "✅ User management API properly removed"

curl -X GET http://localhost:3001/api/agents && echo "ERROR: Agent API still active" || echo "✅ Agent API properly removed"

# 2. Verificar que authentication funciona (Fase 2)
curl -X POST http://localhost:3001/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"testpass"}' \
  | grep -q "token" && echo "✅ Authentication working" || echo "❌ Authentication broken"

# 3. Verificar que database está en estado pre-Fase 3
USER_PROFILES_COUNT=$(psql -h localhost -U postgres -d inmotech_db -t -c \
  "SELECT COUNT(*) FROM information_schema.tables WHERE table_name = 'user_profiles';" 2>/dev/null || echo "0")

if [ "$USER_PROFILES_COUNT" -eq "0" ]; then
    echo "✅ User profiles table successfully removed"
else
    echo "❌ ERROR: User profiles table still exists"
fi

# 4. Verificar que usuarios básicos siguen funcionando
ACTIVE_USERS=$(psql -h localhost -U postgres -d inmotech_db -t -c \
  "SELECT COUNT(*) FROM users WHERE is_active = true;")

echo "✅ Active users after rollback: $ACTIVE_USERS"

# 5. Cleanup archivos temporales
rm -f /tmp/rollback_*
rm -f /tmp/user_mgmt_*

# 6. Update monitoring dashboards
curl -X POST https://monitoring.inmotech.com/api/events \
  -H "Content-Type: application/json" \
  -d "{\"event\": \"rollback_completed\", \"phase\": \"fase-3\", \"timestamp\": \"$(date -u +%Y-%m-%dT%H:%M:%SZ)\"}"
```

#### Paso 6: Desactivar Modo Mantenimiento y Notificaciones (5 min)
```bash
# 1. Desactivar modo mantenimiento
curl -X POST https://api.inmotech.com/admin/maintenance/disable \
  -H "Authorization: Bearer $ADMIN_TOKEN"

# 2. Notificar completion
slack-notify "#alerts" "✅ ROLLBACK FASE-3 COMPLETADO - Sistema restaurado a Fase-2 stable"

# 3. Send email notification a stakeholders
cat > rollback_notification.html << EOF
<h2>Rollback Fase 3 Completado</h2>
<p><strong>Timestamp:</strong> $(date)</p>
<p><strong>Duración:</strong> $(($(date +%s) - $START_TIME)) seconds</p>
<p><strong>Estado:</strong> Sistema restaurado a Fase 2 stable</p>
<p><strong>Próximos pasos:</strong> Análisis de causa raíz en progreso</p>
EOF

# 4. Log completion
echo "ROLLBACK FASE-3 COMPLETED: $(date) - Duration: $(($(date +%s) - $START_TIME))s" >> /var/log/rollback.log
```

---

## 🔄 Verificación Post-Rollback

### Checklist de Validación Funcional
- [ ] **Authentication System:** Login/logout funciona correctamente
- [ ] **User Basic Info:** User data basic accessible
- [ ] **Roles y Permissions:** Authorization working como en Fase 2
- [ ] **Database Integrity:** Todas las tablas core intactas
- [ ] **API Endpoints:** Solo endpoints de Fase 1-2 disponibles
- [ ] **Frontend Navigation:** UI vuelve a estado Fase 2
- [ ] **Performance:** Response times back to Fase 2 baseline
- [ ] **Security:** No security regressions introducidas

### Tests de Regresión Automáticos
```bash
# Script: post_rollback_tests.sh

echo "🔄 Ejecutando tests post-rollback..."

# Test 1: Authentication endpoints
echo "Testing authentication..."
LOGIN_RESPONSE=$(curl -s -X POST http://localhost:3001/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@inmotech.com","password":"admin123"}')

if echo "$LOGIN_RESPONSE" | grep -q "token"; then
    echo "✅ Authentication: PASS"
else
    echo "❌ Authentication: FAIL"
    exit 1
fi

# Test 2: User basic endpoints (should work)
TOKEN=$(echo "$LOGIN_RESPONSE" | jq -r '.token')
PROFILE_RESPONSE=$(curl -s -X GET http://localhost:3001/api/auth/me \
  -H "Authorization: Bearer $TOKEN")

if echo "$PROFILE_RESPONSE" | grep -q "email"; then
    echo "✅ Basic user profile: PASS"
else
    echo "❌ Basic user profile: FAIL"
    exit 1
fi

# Test 3: User management endpoints (should NOT work)
USER_MGMT_RESPONSE=$(curl -s -X GET http://localhost:3001/api/users \
  -H "Authorization: Bearer $TOKEN")

if echo "$USER_MGMT_RESPONSE" | grep -q "Cannot GET"; then
    echo "✅ User management endpoints removed: PASS"
else
    echo "❌ User management endpoints still active: FAIL"
    exit 1
fi

# Test 4: Database structure validation
DB_TABLES=$(psql -h localhost -U postgres -d inmotech_db -t -c \
  "SELECT COUNT(*) FROM information_schema.tables 
   WHERE table_name IN ('user_profiles', 'agent_profiles', 'user_settings');")

if [ "$DB_TABLES" -eq "0" ]; then
    echo "✅ Database structure rollback: PASS"
else
    echo "❌ Database structure rollback incomplete: FAIL"
    exit 1
fi

echo "✅ Todos los tests post-rollback pasaron exitosamente"
```

---

## 📊 Métricas de Rollback

### Tiempo de Rollback Target por Escenario

#### Rollback Inmediato (0-2 horas post-deploy)
- **Frontend:** 5 minutos
- **Backend APIs:** 10 minutos  
- **Database:** 15 minutos
- **Verificación:** 10 minutos
- **Total:** 40 minutos

#### Rollback Estándar (2-12 horas post-deploy)
- **Frontend:** 10 minutos
- **Backend APIs:** 20 minutos
- **Database:** 45 minutos (más data to rollback)
- **Verificación:** 15 minutos
- **Total:** 90 minutos

#### Rollback Complejo (12-48 horas)
- **Frontend:** 15 minutos
- **Backend APIs:** 30 minutos
- **Database:** 2 horas (data migration required)
- **Data migration:** 1 hora
- **Verificación:** 30 minutos
- **Total:** 4.25 horas

### Success Criteria
- **RTO (Recovery Time Objective):** < 2 horas para cualquier scenario
- **RPO (Recovery Point Objective):** 0 data loss
- **Availability Target:** > 99.8% uptime durante rollback
- **Functionality Restoration:** 100% de Fase 2 features working

---

## 🚨 Escalation y Communication Durante Rollback

### Notification Matrix
| Evento | Audiencia | Canal | Timeframe |
|--------|-----------|-------|-----------|
| **Rollback Initiated** | Tech Team, Management | Slack + Email | Inmediato |
| **25% Complete** | Tech Team | Slack | Progress update |
| **50% Complete** | Tech Team, Stakeholders | Slack + Dashboard | Progress update |
| **75% Complete** | Tech Team | Slack | Progress update |
| **Rollback Completed** | All Stakeholders | Slack + Email + Dashboard | Inmediato |
| **Issues Durante Rollback** | Crisis Team | Phone + Slack + Email | Inmediato |

### Roles y Responsabilidades
- **Rollback Commander:** DevOps Engineer - Overall coordination
- **Database Lead:** DBA - Database rollback execution
- **Backend Lead:** Senior Developer - Code rollback y verification
- **Frontend Lead:** Frontend Developer - UI rollback y testing
- **QA Lead:** QA Engineer - Post-rollback validation
- **Communications:** Project Manager - Stakeholder communication

---

## 📋 Lessons Learned Template

### Rollback Analysis Framework
```markdown
## Rollback Post-Mortem - Fase 3

### Executive Summary
- **Rollback Trigger:** [Describe what caused rollback]
- **Duration:** [Total rollback time]
- **Impact:** [Business/user impact during rollback]
- **Success:** [Was rollback successful? Any issues?]

### Timeline
- **Issue Detection:** [When was problem first detected?]
- **Decision to Rollback:** [When was rollback decision made?]
- **Rollback Started:** [Actual rollback start time]
- **Rollback Completed:** [Actual rollback completion time]
- **Service Restored:** [When was service fully operational?]

### What Went Well
- [List successful aspects of rollback]

### What Could Be Improved  
- [List areas for improvement]

### Action Items
- [ ] [Specific improvement actions]

### Recommendations for Future Phases
- [Learning to apply to Fase 4 and beyond]
```

---

**Plan Preparado por:** DevOps Engineer  
**Revisión Técnica:** Backend Lead  
**Aprobación:** Project Manager & Technical Lead  
**Fecha de Creación:** 14/01/2026  
**Última Actualización:** 14/01/2026  
**Testing Status:** Procedimientos validados en staging  

---

**🛡️ Estado Actual: ROLLBACK PLAN READY**  
**⚡ RTO Target: < 2 horas**  
**🎯 Success Rate Goal: 100%**  
**📋 Testing: COMPLETED**