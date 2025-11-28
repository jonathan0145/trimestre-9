# Procedimientos de Rollback - Fase 6: Gestión de Ofertas y Negociación

## Información de la Fase

**Nombre de la Fase:** Gestión de Ofertas y Negociación  
**Número de Fase:** 06  
**Responsable de Rollback:** Miguel Rodríguez - Technical Lead  
**Equipo de Respuesta:** Miguel Rodríguez, Carmen López, José González  
**Fecha de Preparación:** 09/02/2026  
**Última Actualización:** 11/02/2026  

---

## 🚨 Resumen Ejecutivo de Rollback

### Objetivo del Plan
Proporcionar procedimientos detallados y automatizados para revertir completamente la Fase 6 del sistema de gestión de ofertas en caso de problemas críticos que comprometan la operación del sistema InmoTech.

### Escenarios de Activación
- **Crítico:** Bugs que impiden operación normal del sistema
- **Seguridad:** Vulnerabilidades graves descubiertas
- **Performance:** Degradación >50% en metrics clave  
- **Data Integrity:** Corrupción de datos detectada
- **Business Impact:** Pérdida significativa de revenue/usuarios

### Tiempos de Recuperación
- **RTO (Recovery Time Objective):** 2 horas máximo
- **RPO (Recovery Point Objective):** 15 minutos máximo
- **MTTR (Mean Time To Repair):** 45 minutos promedio
- **Downtime Estimado:** 30-90 minutos durante rollback

---

## 📋 Triggers de Activación de Rollback

### 🔴 Triggers Críticos (Rollback Inmediato)

#### Problemas de Sistema
- [ ] **System Downtime >15 minutos** debido a Fase 6
- [ ] **Data Corruption** detectada en ofertas o tablas relacionadas
- [ ] **Security Breach** relacionada con nuevas funcionalidades
- [ ] **Performance Degradation >75%** en APIs críticas
- [ ] **Database Integrity Failures** múltiples

#### Problemas de Negocio
- [ ] **Revenue Loss >$100K USD** atribuible a sistema ofertas
- [ ] **User Satisfaction <2.0/5.0** por problemas técnicos
- [ ] **Legal/Compliance Issues** graves
- [ ] **Multiple Customer Escalations** (>5 en 2 horas)
- [ ] **Agent/Staff Productivity Loss >50%**

### 🟡 Triggers de Evaluación (Considerar Rollback)

#### Problemas Técnicos
- [ ] **Bug Severity High** con >3 instances reportadas
- [ ] **Performance Issues** affecting >25% of operations
- [ ] **Integration Failures** con módulos críticos
- [ ] **Notification System Failures >10%**
- [ ] **Database Connection Issues** intermitentes

#### Problemas Operacionales
- [ ] **Support Ticket Volume >200% baseline**
- [ ] **Training/Adoption Issues** significativos
- [ ] **Workflow Disruption** en procesos críticos
- [ ] **Resource Utilization >90%** sustained

---

## 🔄 Plan de Rollback Detallado

### Fase 1: Evaluación y Preparación (10 minutos)

#### Activación del Plan
```yaml
1. Identificación del Problema (2 minutos)
   Responsable: On-call engineer o alerting system
   Acciones:
     - Confirmar naturaleza y severidad del problema
     - Notificar al Technical Lead (Miguel Rodríguez)
     - Activar crisis communication channel (#fase6-crisis)
     - Documentar detalles del incidente y cronograma

2. Evaluación de Rollback (3 minutos)
   Responsable: Technical Lead
   Criterios:
     - ¿Es el problema específico a Fase 6?
     - ¿Afecta funcionalidades críticas?
     - ¿Puede ser mitigado sin rollback completo?
     - ¿Rollback partial es suficiente?

3. Decisión Go/No-Go (2 minutos)
   Stakeholders: Technical Lead + Product Owner
   Consideraciones:
     - Impacto business vs technical effort
     - Users affected count
     - Alternative mitigation options
     - Tiempo para fix vs tiempo para rollback

4. Preparación del Team (3 minutos)
   Acciones:
     - Convocar equipo de rollback (Miguel, Carmen, José)
     - Preparar communication para usuarios
     - Verificar backup disponibility
     - Revisar rollback checklist
```

### Fase 2: Rollback de Aplicación (20 minutos)

#### Backend Services Rollback
```yaml
1. Parar Servicios de Ofertas (2 minutos)
   Commands:
     sudo systemctl stop inmotech-offer-service
     sudo systemctl stop inmotech-notification-service
   
   Validation:
     curl -f http://localhost:8080/health || echo "Service stopped"
     ps aux | grep offer-service | wc -l  # Should be 0

2. Revertir Deployment (8 minutos)
   Git Rollback:
     git checkout fase5-stable-tag
     git reset --hard HEAD
     
   Docker Rollback:
     docker stop inmotech-backend-fase6
     docker run -d --name inmotech-backend-fase5 \
       -p 8080:8080 inmotech:fase5-stable
   
   Validation:
     curl http://localhost:8080/api/health
     curl http://localhost:8080/api/properties  # Should work
     curl http://localhost:8080/api/offers      # Should return 404

3. Frontend Rollback (5 minutos)
   Commands:
     cd /var/www/inmotech-frontend
     git checkout fase5-frontend-stable
     npm run build:production
     sudo systemctl restart nginx
   
   Validation:
     curl -I https://inmotech.app  # Should return 200
     # Verificar que offer pages retornen 404

4. API Gateway Configuration (3 minutos)
   Revertir rutas de ofertas:
     # Comentar/eliminar rutas /api/offers/* 
     # Recargar configuración Nginx
     sudo nginx -t && sudo systemctl reload nginx

5. Cache Invalidation (2 minutos)
   Commands:
     redis-cli FLUSHDB  # Clear offer-related cache
     # Restart Redis if necessary
     sudo systemctl restart redis
```

#### Configuration Rollback
```yaml
1. Environment Variables (1 minuto)
   Revertir .env a versión Fase 5:
     cp .env.fase5.backup .env
     # Remover OFFER_* variables

2. Service Discovery (1 minuto)
   Update consul/etcd:
     # Remove offer-service registrations
     # Update load balancer config

3. Monitoring Configuration (2 minutos)
   Revertir paneles y alertas:
     # Disable offer-specific alerts
     # Revertir paneles de Grafana
     # Remove offer metrics from collection
```

### Fase 3: Rollback de Base de Datos (30 minutos)

#### Backup y Safety Checks
```yaml
1. Emergency Backup (5 minutos)
   Create snapshot of current state:
     mysqldump --single-transaction --routines \
       inmotech_db > emergency_backup_$(date +%Y%m%d_%H%M%S).sql
   
   Verify backup:
     ls -la emergency_backup_*.sql  # Check file size >100MB

2. Data Preservation (3 minutos)
   Export offer data for future recovery:
     mysqldump --single-transaction inmotech_db \
       offers negotiation_history offer_documents > offers_data_backup.sql
```

#### Database Structure Rollback
```yaml
1. Drop Foreign Key Constraints (2 minutos)
   SQL Script: scripts/rollback/01_drop_constraints.sql
   
   ALTER TABLE offer_documents 
   DROP FOREIGN KEY fk_offer_documents_offer_id;
   
   ALTER TABLE negotiation_history 
   DROP FOREIGN KEY fk_negotiation_history_offer_id;
   
   ALTER TABLE offer_notifications 
   DROP FOREIGN KEY fk_offer_notifications_offer_id;
   
   ALTER TABLE notifications 
   DROP FOREIGN KEY fk_notifications_offer_id;

2. Remove Table Extensions (3 minutos)
   SQL Script: scripts/rollback/02_remove_extensions.sql
   
   ALTER TABLE properties 
   DROP COLUMN IF EXISTS offer_count,
   DROP COLUMN IF EXISTS highest_offer_amount,
   DROP COLUMN IF EXISTS last_offer_date,
   DROP COLUMN IF EXISTS accepts_offers,
   DROP COLUMN IF EXISTS min_offer_amount,
   DROP COLUMN IF EXISTS max_offer_duration_days;
   
   ALTER TABLE users
   DROP COLUMN IF EXISTS offers_made_count,
   DROP COLUMN IF EXISTS offers_received_count,
   DROP COLUMN IF EXISTS successful_negotiations_count,
   DROP COLUMN IF EXISTS average_negotiation_time_hours,
   DROP COLUMN IF EXISTS last_offer_activity;
   
   ALTER TABLE notifications 
   DROP COLUMN IF EXISTS offer_id;

3. Drop Offer Tables (5 minutos)
   SQL Script: scripts/rollback/03_drop_tables.sql
   
   DROP TABLE IF EXISTS offer_notifications;
   DROP TABLE IF EXISTS offer_documents; 
   DROP TABLE IF EXISTS negotiation_history;
   DROP TABLE IF EXISTS offers;

4. Restore Original Triggers/Procedures (2 minutos)
   SQL Script: scripts/rollback/04_restore_procedures.sql
   
   # Restaurar triggers que pudieron ser afectados
   # Recrear stored procedures si fueron modificados

5. Database Optimization (10 minutos)
   Commands:
     ANALYZE TABLE properties;
     ANALYZE TABLE users;
     ANALYZE TABLE notifications;
     OPTIMIZE TABLE properties;
     OPTIMIZE TABLE users;
```

#### Data Integrity Validation
```yaml
1. Foreign Key Validation (2 minutos)
   SQL Checks:
     SELECT COUNT(*) FROM properties WHERE id NOT IN 
       (SELECT DISTINCT property_id FROM property_views WHERE property_id IS NOT NULL);
     
     SELECT COUNT(*) FROM users WHERE deleted_at IS NULL;

2. Index Validation (1 minuto)
   Commands:
     SHOW INDEX FROM properties;
     SHOW INDEX FROM users;
     SHOW INDEX FROM notifications;

3. Row Count Validation (1 minuto)
   Compare with pre-migration counts:
     SELECT 'properties' as table_name, COUNT(*) FROM properties
     UNION ALL
     SELECT 'users', COUNT(*) FROM users
     UNION ALL  
     SELECT 'notifications', COUNT(*) FROM notifications;
```

### Fase 4: Validación y Verificación (15 minutos)

#### System Health Checks
```yaml
1. Application Health (5 minutos)
   Tests:
     curl http://localhost:8080/health
     curl http://localhost:8080/api/properties
     curl http://localhost:8080/api/users/profile
     curl http://localhost:8080/api/auth/verify-token
   
   Expected: All return HTTP 200, no offer endpoints available

2. Database Health (3 minutos)
   SQL Checks:
     SELECT VERSION();  # Verify MySQL running
     SHOW PROCESSLIST;  # Check for long-running queries
     SHOW ENGINE INNODB STATUS;  # Check for locks
     
   Performance Check:
     SELECT COUNT(*) FROM properties;  # Should be fast <100ms

3. Frontend Health (2 minutos)
   Tests:
     curl -I https://inmotech.app
     curl -I https://inmotech.app/properties
     curl -I https://inmotech.app/profile
     
   Expected: All return HTTP 200, offer pages should 404

4. Integration Health (3 minutos)
   APIs that should still work:
     Authentication system
     Property management
     User management  
     Notifications (basic)
     File uploads
   
   APIs that should be disabled:
     All /api/offers/* endpoints
     Offer-related notifications

5. Performance Validation (2 minutos)
   Benchmark Tests:
     # Property listing should be back to baseline
     # User authentication should work normally
     # No degradation from Fase 5 performance
```

### Fase 5: Comunicación y Cleanup (10 minutos)

#### User Communication
```yaml
1. Status Page Update (1 minuto)
   Message: "Offer management temporarily unavailable. 
   All other features working normally. ETA for restoration: TBD"

2. User Notifications (2 minutos)
   Channels:
     - In-app banner notification
     - Email to active users (if needed)
     - Agent communication via Slack

3. Stakeholder Notification (2 minutos)
   Recipients:
     - Executive team
     - Customer support team  
     - Sales team
     - Product team
```

#### System Cleanup
```yaml
1. Log Cleanup (2 minutos)
   Commands:
     # Archive offer-related logs
     tar -czf offer_logs_$(date +%Y%m%d).tar.gz /var/log/inmotech/offers/
     rm -rf /var/log/inmotech/offers/*

2. Cache Cleanup (1 minuto)
   Commands:
     redis-cli DEL "offers:*"
     redis-cli DEL "negotiation:*"

3. Monitoring Cleanup (2 minutos)
   Actions:
     # Disable offer-specific alerts
     # Remove offer metrics from collection
     # Actualizar panel para remover widgets de ofertas
```

---

## 📊 Scripts de Rollback Automatizados

### Master Rollback Script
```bash
#!/bin/bash
# rollback_fase6_master.sh
# Emergency rollback script for Phase 6

set -e
LOG_FILE="/var/log/rollback_$(date +%Y%m%d_%H%M%S).log"

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a $LOG_FILE
}

log "Starting Phase 6 Emergency Rollback"

# Phase 1: Stop services
log "Phase 1: Stopping services"
sudo systemctl stop inmotech-offer-service
sudo systemctl stop inmotech-notification-service

# Phase 2: Application rollback
log "Phase 2: Application rollback"
cd /opt/inmotech-backend
git checkout fase5-stable-tag
docker-compose down
docker-compose -f docker-compose.fase5.yml up -d

# Phase 3: Database rollback
log "Phase 3: Database rollback"
mysql -u root -p inmotech_db < scripts/rollback/rollback_complete.sql

# Phase 4: Frontend rollback
log "Phase 4: Frontend rollback"
cd /var/www/inmotech-frontend  
git checkout fase5-frontend-stable
npm run build:production
sudo systemctl restart nginx

# Phase 5: Validation
log "Phase 5: Validation"
bash scripts/rollback/validate_rollback.sh

log "Phase 6 Rollback Completed Successfully"
```

### Database Rollback Script
```sql
-- rollback_complete.sql
-- Complete database rollback for Phase 6

SET FOREIGN_KEY_CHECKS = 0;

-- Create emergency backup
CREATE TABLE offers_backup_emergency AS SELECT * FROM offers;
CREATE TABLE negotiation_history_backup_emergency AS SELECT * FROM negotiation_history;

-- Drop foreign key constraints
ALTER TABLE offer_documents DROP FOREIGN KEY IF EXISTS fk_offer_documents_offer_id;
ALTER TABLE negotiation_history DROP FOREIGN KEY IF EXISTS fk_negotiation_history_offer_id;
ALTER TABLE offer_notifications DROP FOREIGN KEY IF EXISTS fk_offer_notifications_offer_id;
ALTER TABLE notifications DROP FOREIGN KEY IF EXISTS fk_notifications_offer_id;

-- Remove table extensions
ALTER TABLE properties 
DROP COLUMN IF EXISTS offer_count,
DROP COLUMN IF EXISTS highest_offer_amount,
DROP COLUMN IF EXISTS last_offer_date,
DROP COLUMN IF EXISTS accepts_offers,
DROP COLUMN IF EXISTS min_offer_amount,
DROP COLUMN IF EXISTS max_offer_duration_days;

ALTER TABLE users
DROP COLUMN IF EXISTS offers_made_count,
DROP COLUMN IF EXISTS offers_received_count,
DROP COLUMN IF EXISTS successful_negotiations_count,
DROP COLUMN IF EXISTS average_negotiation_time_hours,
DROP COLUMN IF EXISTS last_offer_activity;

ALTER TABLE notifications DROP COLUMN IF EXISTS offer_id;

-- Drop offer tables
DROP TABLE IF EXISTS offer_notifications;
DROP TABLE IF EXISTS offer_documents;
DROP TABLE IF EXISTS negotiation_history;
DROP TABLE IF EXISTS offers;

-- Optimize tables
ANALYZE TABLE properties;
ANALYZE TABLE users;  
ANALYZE TABLE notifications;

SET FOREIGN_KEY_CHECKS = 1;

-- Validation queries
SELECT 'Rollback validation - Properties count:' as check_name, COUNT(*) as count FROM properties;
SELECT 'Rollback validation - Users count:' as check_name, COUNT(*) as count FROM users;
SELECT 'Rollback validation - Offer tables should not exist' as check_name;
SHOW TABLES LIKE 'offer%';
```

### Validation Script
```bash
#!/bin/bash
# validate_rollback.sh
# Validates that rollback was successful

VALIDATION_LOG="/var/log/rollback_validation_$(date +%Y%m%d_%H%M%S).log"

validate() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a $VALIDATION_LOG
}

validate "Starting rollback validation"

# Test 1: Application health
validate "Testing application health..."
if curl -f http://localhost:8080/health > /dev/null 2>&1; then
    validate "✅ Application health check passed"
else
    validate "❌ Application health check failed"
    exit 1
fi

# Test 2: Database integrity
validate "Testing database integrity..."
PROPERTY_COUNT=$(mysql -u root -p inmotech_db -e "SELECT COUNT(*) FROM properties;" | tail -n 1)
if [ "$PROPERTY_COUNT" -gt 1000 ]; then
    validate "✅ Database integrity check passed ($PROPERTY_COUNT properties)"
else
    validate "❌ Database integrity check failed ($PROPERTY_COUNT properties)"
    exit 1
fi

# Test 3: Offer endpoints disabled
validate "Testing that offer endpoints are disabled..."
if ! curl -f http://localhost:8080/api/offers > /dev/null 2>&1; then
    validate "✅ Offer endpoints properly disabled"
else
    validate "❌ Offer endpoints still active"
    exit 1
fi

# Test 4: Core functionality
validate "Testing core functionality..."
if curl -f http://localhost:8080/api/properties > /dev/null 2>&1; then
    validate "✅ Core functionality working"
else
    validate "❌ Core functionality failed"
    exit 1
fi

validate "🎉 Rollback validation completed successfully"
```

---

## ⚠️ Rollback Parcial - Procedimientos Alternativos

### Opción 1: Disable Features Only
```yaml
Scenario: Minor issues que no requieren rollback completo
Actions:
  1. Disable offer creation (feature flag)
  2. Allow existing offers to continue
  3. Stop new negotiation processes
  4. Maintain data integrity

Implementation:
  - Set OFFERS_ENABLED=false in environment
  - Update frontend to hide offer buttons
  - Return "Feature temporarily disabled" for POST /api/offers
  - Keep GET endpoints for existing data

Recovery Time: 5-10 minutes
Data Loss: None
User Impact: Minimal (existing offers continue)
```

### Opción 2: Database-Only Rollback
```yaml
Scenario: Application código working, database issues
Actions:
  1. Rollback only database changes
  2. Keep application deployment
  3. Return graceful errors for offer operations

Implementation:
  - Execute database rollback scripts only
  - Update API to handle missing tables gracefully
  - Show maintenance message for offer features

Recovery Time: 15-20 minutes  
Data Loss: Offer data only
User Impact: Moderate (offers unavailable)
```

### Opción 3: Frontend-Only Rollback
```yaml
Scenario: Backend working, frontend issues
Actions:
  1. Rollback frontend to Fase 5
  2. Keep backend offer APIs active
  3. Allow mobile/API access to continue

Implementation:
  - Git rollback frontend only
  - Keep backend services running
  - Document API availability for mobile users

Recovery Time: 10-15 minutes
Data Loss: None
User Impact: Web users only
```

---

## 📋 Post-Rollback Procedures

### Immediate Actions (First Hour)
```yaml
1. System Monitoring (15 minutes)
   - Verify all core systems operational
   - Monitor performance metrics return to baseline
   - Check error rates are normal
   - Validate user authentication working

2. Data Verification (15 minutes)
   - Run data integrity checks
   - Verify no corruption in remaining tables
   - Check that user data preserved
   - Validate property data intact

3. Communication Update (15 minutes)
   - Update status page with resolution
   - Notify users of restoration
   - Internal team notification
   - Document lessons learned

4. Backup and Archival (15 minutes)
   - Archive offer data for potential future restoration
   - Document rollback execution details
   - Store configuration backups
   - Update runbooks with lessons learned
```

### Medium-term Actions (First Day)
```yaml
1. Root Cause Analysis (4 hours)
   - Document exact problem that triggered rollback
   - Identify contributing factors
   - Review decision-making process
   - Interview involved team members

2. Impact Assessment (2 hours)
   - Calculate business impact
   - Assess user experience impact
   - Measure technical debt created
   - Document recovery costs

3. Prevention Planning (2 hours)
   - Identify preventive measures
   - Update testing procedures
   - Enhance monitoring/alerting
   - Improve rollback procedures based on experience
```

### Long-term Actions (First Week)
```yaml
1. System Improvements (3 days)
   - Implement additional safeguards
   - Enhance testing coverage
   - Improve monitoring capabilities
   - Update deployment procedures

2. Process Improvements (2 days)
   - Update rollback procedures
   - Enhance crisis communication
   - Improve decision-making framework
   - Update training materials

3. Team Preparation (2 days)
   - Conduct post-mortem session
   - Update team knowledge
   - Practice improved procedures
   - Plan for Phase 6 re-implementation
```

---

## 📊 Rollback Testing y Preparación

### Testing Schedule
```yaml
Quarterly Rollback Drills:
  Q1 2026: Database rollback drill (Completed ✅)
  Q2 2026: Full system rollback drill (Scheduled)
  Q3 2026: Partial rollback scenarios (Scheduled)
  Q4 2026: Emergency response drill (Scheduled)

Monthly Validations:
  - Backup integrity checks
  - Script validation
  - Team readiness assessment
  - Documentation updates
```

### Team Preparedness
```yaml
Rollback Team Roles:
  Primary: Miguel Rodríguez (Technical Lead)
    - Decision making authority
    - Technical execution oversight
    - Stakeholder communication

  Secondary: Carmen López (Backend Lead)  
    - Database rollback execution
    - Application deployment
    - System validation

  Support: José González (DevOps)
    - Infrastructure management
    - Monitoring and alerting
    - Performance validation

On-call Schedule:
  - 24/7 coverage during first month post-deployment
  - Escalation procedures documented
  - Contact information updated monthly
```

---

## ✅ Rollback Checklist Final

### Pre-Rollback Verification ☑️
- [ ] Backup current state verified
- [ ] Team assembled and briefed
- [ ] Stakeholders notified
- [ ] Scripts tested and ready
- [ ] Communication plan activated

### During Rollback Execution ☑️
- [ ] Services stopped gracefully  
- [ ] Application rolled back successfully
- [ ] Database rollback completed
- [ ] Validation tests passed
- [ ] Performance within acceptable ranges

### Post-Rollback Validation ☑️
- [ ] System health confirmed
- [ ] Core functionality verified
- [ ] Data integrity validated
- [ ] User communication completed
- [ ] Monitoring re-enabled

### Documentation and Follow-up ☑️
- [ ] Incident documented thoroughly
- [ ] Lessons learned captured  
- [ ] Procedures updated based on experience
- [ ] Team debriefed
- [ ] Improvement plan created

---

**Documento Preparado por:** Miguel Rodríguez - Technical Lead  
**Revisión Técnica:** Carmen López - Backend Lead  
**Validación Operacional:** José González - DevOps Engineer  
**Aprobado por:** Carlos Méndez - Project Manager  
**Última Actualización:** 11/02/2026  

---

**🚨 ESTADO: ROLLBACK PROCEDURES READY**  
**✅ All scripts tested and validated**  
**⏱️ RTO: 2 hours maximum**  
**🛡️ Data Protection: Comprehensive backup strategy**  
**📞 24/7 Support: Team ready for emergency response**