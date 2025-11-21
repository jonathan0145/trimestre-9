# Procedimientos de Rollback - Fase 7: Sistema de Mensajería y Chat

**📋 Proyecto:** InmoTech - Sistema Integral de Gestión Inmobiliaria  
**📊 Fase:** 07 - Sistema de Mensajería y Chat  
**📅 Fecha del Procedimiento:** 20/11/2025  
**👤 DevOps Lead:** Ricardo Fernández - Infrastructure & Deployment Manager  
**🔍 Revisado por:** Ana García - Technical Architecture Lead  

---

## 🚨 Resumen de Procedimientos de Rollback

### 🎯 Objetivo del Rollback
Proporcionar procedimientos claros y probados para revertir el Sistema de Mensajería y Chat a un estado funcional anterior en caso de fallas críticas, garantizando la continuidad del negocio y minimizando el impacto en los usuarios.

### ⚠️ Triggers para Activar Rollback
```yaml
Críticos (Rollback Inmediato):
  🚨 Data corruption >5% de conversaciones
  🚨 Security breach confirmado
  🚨 System downtime >30 minutos sustained
  🚨 User data loss confirmado
  🚨 Performance degradation >500% baseline

Altos (Rollback en 2 horas):
  🔴 Error rate >25% en funciones críticas
  🔴 WebSocket connections failing >50%
  🔴 Database performance degradation >300%
  🔴 File upload/download failures >75%
  🔴 Integration failures con sistemas críticos

Medios (Rollback planificado en 24h):
  🟡 User satisfaction <3.0/5.0 sustained
  🟡 Adoption rate <30% después de 14 días
  🟡 Business metrics failing targets >50%
  🟡 Multiple non-critical pero persistent issues
```

### ⏱️ RTO y RPO Targets
```yaml
Recovery Time Objective (RTO):
  🚨 Critical: 1 hora máximo downtime
  🔴 High: 4 horas máximo downtime
  🟡 Medium: 24 horas máximo downtime

Recovery Point Objective (RPO):
  💬 Chat messages: 15 minutos máximo data loss
  📁 File attachments: 1 hora máximo data loss
  👥 User configurations: 4 horas máximo data loss
  📊 Analytics data: 24 horas máximo data loss
```

---

## 🔄 Estrategias de Rollback por Componente

### 📱 Rollback de Frontend (React App)

#### FRONT-RB-001: Rollback de Aplicación Web
```yaml
⏱️ Tiempo Estimado: 15 minutos
👤 Responsable: Frontend Lead + DevOps
🎯 Impacto: Usuarios pierden funciones de chat, vuelven a legacy

Pasos de Ejecución:
  1. 🔄 Revert deployment en CDN a versión anterior
  2. 📝 Update configuration para disable chat routes
  3. 🔧 Enable legacy communication methods
  4. 🧪 Smoke test de funcionalidades críticas
  5. 📢 Notify users via system banner
  6. 📊 Monitor user behavior y error rates

Comandos de Rollback:
  ```bash
  # Revert to previous version in CDN
  aws s3 sync s3://inmotech-frontend-backup/v2.4.1/ s3://inmotech-frontend-prod/
  
  # Update CloudFront to invalidate cache
  aws cloudfront create-invalidation --distribution-id E1234567890123 --paths "/*"
  
  # Verify rollback
  curl -I https://app.inmotech.com | grep "X-Version"
  ```

Validación Post-Rollback:
  ✅ Login functionality works
  ✅ Property browsing functional
  ✅ Legacy contact methods enabled
  ✅ No JavaScript errors en console
  ✅ Mobile app compatibility maintained
```

#### FRONT-RB-002: Rollback de Mobile App
```yaml
⏱️ Tiempo Estimado: 30 minutos
👤 Responsable: Mobile Developer + DevOps
🎯 Impacto: Users need to update app or use web version

Procedimiento:
  1. 📱 Trigger emergency app store rollback
  2. 🔄 Revert app store listing to previous version
  3. 📡 Send push notification sobre update requirement
  4. 🌐 Enable web app fallback en mobile devices
  5. 📞 Enable phone/email contact methods prominently

App Store Emergency Rollback:
  🍎 iOS: Contact Apple Developer Support for expedited rollback
  🤖 Android: Use Google Play Console immediate rollback
  ⏰ Estimated approval time: 2-4 horas

Communication Plan:
  📱 Push notification: "Por favor actualiza la app para continuar"
  📧 Email to active users: Link to web version
  📞 Customer service: Prepare for increased call volume
```

### 🗄️ Rollback de Backend (Node.js API)

#### BACK-RB-001: Application Server Rollback
```yaml
⏱️ Tiempo Estimado: 20 minutos
👤 Responsable: Backend Lead + DevOps
🎯 Impacto: Chat functionality disabled, API reverts

Kubernetes Rollback Procedure:
  ```bash
  # Check rollout history
  kubectl rollout history deployment/inmotech-chat-api -n production
  
  # Rollback to previous version
  kubectl rollout undo deployment/inmotech-chat-api -n production
  
  # Monitor rollback progress
  kubectl rollout status deployment/inmotech-chat-api -n production
  
  # Verify pods are healthy
  kubectl get pods -l app=inmotech-chat-api -n production
  
  # Check logs for errors
  kubectl logs -l app=inmotech-chat-api -n production --tail=100
  ```

Load Balancer Configuration:
  1. 🔄 Remove new chat endpoints from load balancer
  2. ⚡ Route traffic to legacy communication APIs
  3. 🔒 Disable WebSocket proxy rules
  4. 📊 Monitor traffic distribution

Health Check Validation:
  ✅ API health endpoint responding
  ✅ Database connections established
  ✅ Redis cache connectivity
  ✅ File storage access working
  ✅ External integrations functional
```

#### BACK-RB-002: Socket.io Server Rollback
```yaml
⏱️ Tiempo Estimado: 10 minutos
👤 Responsable: DevOps Engineer
🎯 Impacto: Real-time features disabled

Procedimiento Rápido:
  1. 🛑 Stop all Socket.io server instances
  2. 🔄 Drain existing WebSocket connections gracefully
  3. 🚫 Update load balancer to block WebSocket traffic
  4. 📝 Update application config to disable real-time features
  5. 📢 Show "real-time features temporarily unavailable" message

Graceful Connection Termination:
  ```javascript
  // Send disconnect message to all connected clients
  io.emit('system_maintenance', {
    message: 'Chat temporalmente no disponible. Usa email/teléfono.',
    redirectUrl: '/contact'
  });
  
  // Wait 30 seconds then force disconnect
  setTimeout(() => {
    io.close();
  }, 30000);
  ```
```

### 🗃️ Rollback de Base de Datos

#### DB-RB-001: Schema Rollback
```yaml
⏱️ Tiempo Estimado: 45 minutos
👤 Responsable: Database Administrator + Backend Lead
🎯 Impacto: Data loss potential, extended downtime

Pre-Rollback Checklist:
  ✅ Current database backup confirmed recent
  ✅ Rollback script tested en staging
  ✅ Data migration impact assessment completed
  ✅ Stakeholder approval for data loss obtained
  ✅ Maintenance window scheduled y communicated

Schema Rollback Procedure:
  ```sql
  -- 1. Create emergency backup
  mysqldump --single-transaction --host=prod-db 
    --user=admin --password inmotech_prod > emergency_backup_$(date +%Y%m%d_%H%M%S).sql
  
  -- 2. Drop new chat tables (WITH EXTREME CAUTION)
  DROP TABLE IF EXISTS message_status;
  DROP TABLE IF EXISTS messages;
  DROP TABLE IF EXISTS conversation_participants;
  DROP TABLE IF EXISTS conversations;
  DROP TABLE IF EXISTS chat_files;
  
  -- 3. Restore previous schema
  SOURCE /backups/schema_pre_phase7.sql;
  
  -- 4. Verify legacy tables intact
  SELECT COUNT(*) FROM legacy_communications;
  SELECT COUNT(*) FROM users;
  SELECT COUNT(*) FROM properties;
  ```

Data Recovery Strategy:
  📊 Export chat data before rollback for future migration
  💾 Store chat history en JSON format for potential recovery
  🔄 Plan re-migration strategy si rollback es temporal
```

#### DB-RB-002: Data-Only Rollback
```yaml
⏱️ Tiempo Estimado: 30 minutos
👤 Responsable: Database Administrator
🎯 Impacto: Preserves schema, reverts data only

Scenario: Chat tables corruptas pero schema OK

Procedimiento:
  ```sql
  -- 1. Stop all application connections to chat tables
  SET GLOBAL innodb_fast_shutdown = 1;
  
  -- 2. Truncate corrupted tables
  TRUNCATE TABLE messages;
  TRUNCATE TABLE conversations;
  TRUNCATE TABLE conversation_participants;
  
  -- 3. Restore from last good backup (point-in-time)
  mysql inmotech_prod < /backups/chat_data_$(date -d '1 hour ago' +%Y%m%d_%H00).sql
  
  -- 4. Verify data integrity
  SELECT COUNT(*) as msg_count FROM messages;
  SELECT COUNT(*) as conv_count FROM conversations;
  ```

Validation Queries:
  ✅ No orphaned messages without conversations
  ✅ All conversations have at least 2 participants
  ✅ Message timestamps are logical
  ✅ File references point to existing files
```

---

## 📁 Rollback de Archivos y Storage

### 💾 File Storage Rollback

#### STOR-RB-001: AWS S3 Bucket Rollback
```yaml
⏱️ Tiempo Estimado: 25 minutos
👤 Responsable: DevOps Engineer
🎯 Impacto: New uploaded files may be lost

S3 Versioning Rollback:
  ```bash
  # List all versions of files uploaded after deployment
  aws s3api list-object-versions --bucket inmotech-chat-files \
    --prefix "uploads/" --query 'Versions[?LastModified > `2026-03-01T00:00:00Z`]'
  
  # Mark new versions as delete markers
  aws s3api put-object --bucket inmotech-chat-files \
    --key "uploads/new-file.pdf" --delete-marker
  
  # Restore previous version for critical files
  aws s3api copy-object --copy-source inmotech-chat-files/uploads/file.pdf?versionId=abc123 \
    --bucket inmotech-chat-files --key uploads/file.pdf
  ```

Cleanup Process:
  1. 📋 Identify files uploaded during problematic deployment
  2. 🗑️ Move new files to quarantine bucket
  3. 🔄 Restore file references en database
  4. 📊 Generate report of affected files
  5. 📧 Notify users of potential file loss
```

### 🗂️ CDN and Cache Rollback

#### CDN-RB-001: CloudFront Distribution Rollback
```yaml
⏱️ Tiempo Estimado: 10 minutos
👤 Responsable: DevOps Engineer
🎯 Impacto: File delivery may be slower during cache rebuild

CloudFront Rollback:
  ```bash
  # Update origin to point to backup S3 bucket
  aws cloudfront update-distribution --id E1234567890 \
    --distribution-config file://backup-distribution-config.json
  
  # Invalidate all cached files
  aws cloudfront create-invalidation --distribution-id E1234567890 \
    --paths "/*"
  
  # Monitor invalidation progress
  aws cloudfront get-invalidation --distribution-id E1234567890 \
    --id I1234567890
  ```

Validation:
  ✅ Origin pointing to correct backup location
  ✅ Cache invalidation completed successfully
  ✅ File delivery working from all edge locations
  ✅ Performance metrics within acceptable range
```

---

## 🔄 Procedimientos de Rollback Específicos

### 🚨 Emergency Rollback (< 1 hora)

#### EMRG-RB-001: Complete System Rollback
```yaml
⏰ Execution Time: 45 minutos máximo
👥 Required Team: DevOps Lead, Database Admin, Frontend Lead
🎯 Scenario: Critical system failure, data corruption

Emergency Response Team:
  📞 Contact List:
    - DevOps Lead: +1-555-0101 (Primary)
    - Database Admin: +1-555-0102 (Primary)
    - Frontend Lead: +1-555-0103 (Secondary)
    - Product Manager: +1-555-0104 (Notification)

Rollback Sequence (Parallel Execution):
  Minute 0-5:
    🚨 Incident declared
    📞 Emergency team contacted
    🔒 System put in maintenance mode
    📱 User notification sent

  Minute 5-15:
    🗄️ Database rollback initiated (DB Admin)
    📱 Frontend rollback started (Frontend Lead)
    🔄 API services rollback (DevOps Lead)

  Minute 15-30:
    💾 File storage rollback
    🌐 CDN cache invalidation
    🔄 Load balancer reconfiguration

  Minute 30-40:
    ✅ System validation
    🧪 Smoke testing
    📊 Metrics verification

  Minute 40-45:
    📢 User notification of restoration
    📝 Incident documentation
    📊 Post-incident review scheduled
```

### 📋 Planned Rollback (24-48 horas)

#### PLAN-RB-001: Business Decision Rollback
```yaml
⏰ Execution Time: 4 horas
👥 Required Approval: Product Owner, Technical Lead, Business Stakeholders
🎯 Scenario: Feature not meeting business objectives

Pre-Rollback Activities (24h before):
  📊 Data export y archiving
  📧 User communication campaign
  🔄 Alternative solution preparation
  📝 Detailed rollback plan review

Rollback Communication Plan:
  📧 Email to all users (48h notice):
    "Improvements Coming: Chat feature temporarily disabled for enhancements"
  
  📱 In-app notification (24h notice):
    "Chat will be unavailable starting [date]. Use phone/email for urgent needs."
  
  📞 Agent training (12h notice):
    "Revert to phone/email workflows. Chat rollback tomorrow."

Data Preservation Strategy:
  💾 Full chat history export to JSON
  📁 File attachments moved to archive storage
  📊 Analytics data preserved for future analysis
  🔄 Migration script prepared for re-deployment
```

---

## 🧪 Testing de Procedimientos de Rollback

### 🔬 Rollback Testing Schedule

#### Monthly Rollback Drills
```yaml
Frequencia: Primer sábado de cada mes
Duración: 2 horas
Participantes: DevOps team, Database Admin, Product Owner

Testing Scenarios:
  🧪 Scenario 1: Frontend rollback (Month 1, 3, 5...)
  🧪 Scenario 2: Backend API rollback (Month 2, 4, 6...)
  🧪 Scenario 3: Database rollback (Month 6, 12...)
  🧪 Scenario 4: Complete system rollback (Quarterly)

Testing Environment:
  - Staging environment replica of production
  - Production-like data volumes
  - Real user traffic simulation
  - Monitoring tools active

Success Criteria:
  ✅ Rollback completed within time targets
  ✅ Data integrity maintained
  ✅ User impact minimized
  ✅ Team follows procedures correctly
  ✅ Communication plan executed properly
```

#### Rollback Automation Testing
```yaml
Automated Test Suite:
  🤖 Infrastructure rollback scripts
  🤖 Database backup/restore procedures
  🤖 Application deployment rollback
  🤖 Health check validations

CI/CD Pipeline Integration:
  - Rollback tests run on every deployment
  - Automated validation of backup procedures
  - Performance impact assessment
  - Notification system testing

Test Data Management:
  📊 Synthetic test data generation
  🔄 Test data refresh procedures
  🗑️ Test data cleanup protocols
  📈 Test environment monitoring
```

---

## 📊 Post-Rollback Procedures

### 📈 Monitoring y Validation

#### Immediate Post-Rollback (First 2 hours)
```yaml
Critical Validations:
  ✅ System availability confirmed
  ✅ Core business functions operational
  ✅ User authentication working
  ✅ Database integrity verified
  ✅ File access functional
  ✅ Performance metrics within normal range

User Experience Monitoring:
  📱 Monitor user login success rates
  📧 Track contact form submissions
  📞 Monitor call center volume
  😊 Customer satisfaction surveys sent
  📊 Error rate monitoring intensified

Business Impact Assessment:
  💰 Revenue tracking resumed
  📈 Lead generation metrics
  👥 Agent productivity monitoring
  📊 Conversion rate tracking
```

#### Extended Monitoring (24-48 hours)
```yaml
System Health Validation:
  🔄 Long-running process verification
  💾 Database performance optimization
  📁 File storage integrity check
  🌐 CDN performance validation
  🔐 Security posture assessment

Business Continuity Verification:
  📊 Daily business metrics comparison
  👥 User adoption of alternative workflows
  📧 Communication effectiveness assessment
  💼 Agent workflow efficiency
  😊 Customer satisfaction impact analysis
```

### 📝 Post-Incident Review

#### Incident Analysis (Within 48h)
```yaml
Root Cause Analysis:
  🔍 Timeline reconstruction
  📊 Contributing factors identification
  🎯 Primary root cause determination
  🔄 Secondary factors analysis
  📋 Lessons learned documentation

Process Improvement:
  📈 Rollback procedure effectiveness review
  ⏱️ Time-to-recovery analysis
  👥 Team coordination assessment
  📢 Communication effectiveness evaluation
  🔧 Tool and automation gaps identified

Action Items:
  🎯 Process improvements (Owner: DevOps Lead)
  🔧 Tool enhancements (Owner: Engineering)
  📚 Training updates (Owner: Team Leads)
  📋 Documentation updates (Owner: Technical Writer)
  📊 Monitoring improvements (Owner: SRE)
```

#### Knowledge Sharing
```yaml
Documentation Updates:
  📚 Rollback procedure refinements
  🔧 Tool configuration updates
  📊 Monitoring threshold adjustments
  📢 Communication template improvements

Team Training:
  🎓 Rollback drill debrief session
  📚 Updated procedure walkthrough
  🧪 Hands-on practice session
  📊 Metrics and monitoring training

Stakeholder Communication:
  📊 Executive summary report
  💼 Business impact assessment
  🎯 Future risk mitigation strategy
  📈 Process improvement roadmap
```

---

## 📚 Referencias y Documentos de Soporte

### 🔗 Enlaces a Documentos Relacionados
```yaml
Documentación Técnica:
  - Infrastructure Architecture Diagram
  - Database Schema Documentation
  - API Documentation y Endpoints
  - Deployment Pipeline Configuration

Procedimientos Operacionales:
  - Incident Response Playbook
  - Business Continuity Plan
  - Disaster Recovery Procedures
  - Change Management Process

Contactos y Escalación:
  - Emergency Contact List
  - Vendor Support Contacts
  - Stakeholder Notification Matrix
  - External Communication Templates
```

### 📞 Emergency Contact Matrix
```yaml
Level 1 (Immediate Response):
  🚨 DevOps Lead: Ricardo Fernández (+1-555-0101)
  🗄️ Database Admin: Ana García (+1-555-0102)
  📱 Frontend Lead: David Chen (+1-555-0103)

Level 2 (30 min Response):
  💼 Product Manager: Patricia Jiménez (+1-555-0104)
  🏢 Technical Director: Miguel Rodríguez (+1-555-0105)
  📊 QA Lead: Carlos Vega (+1-555-0106)

Level 3 (2 hour Response):
  👔 VP Technology: (+1-555-0201)
  📊 Chief Technology Officer: (+1-555-0202)
  💼 Product Owner: (+1-555-0203)

Vendor Contacts:
  ☁️ AWS Premium Support: (+1-866-227-2701)
  📊 MongoDB Atlas Support: (+1-855-424-4638)
  🔧 New Relic Support: (+1-855-639-7354)
```

---

**📅 Fecha de Creación:** 20/11/2025  
**📅 Última Revisión:** 20/11/2025  
**📋 Versión del Documento:** 1.0  
**👤 Preparado por:** Ricardo Fernández - DevOps & Infrastructure Lead  
**✅ Revisado por:** Ana García - Technical Architecture Lead  
**🔍 Aprobado por:** Equipo de Tecnología InmoTech  

---

**🔄 ROLLBACK FASE 7: PREPARADOS PARA CUALQUIER EVENTUALIDAD** 🛡️