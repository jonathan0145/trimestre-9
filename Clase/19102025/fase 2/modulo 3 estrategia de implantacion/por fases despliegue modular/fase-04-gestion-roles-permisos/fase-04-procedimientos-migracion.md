# Procedimientos de Migración - Fase 4: Gestión de Roles y Permisos

## Información de la Fase

**Nombre de la Fase:** Gestión de Roles y Permisos  
**Número de Fase:** 04  
**Período de Migración:** 25/01/2026 - 02/02/2026 (8 días)  
**Coordinador de Migración:** Carlos Vega - QA & Migration Lead  
**Soporte Técnico:** Miguel Rodríguez - Arquitecto de Software  
**Backup Coordinator:** Carmen López - Backend Lead  
**Alcance:** 200 usuarios + roles legacy + configuraciones sistema  

---

## 🎯 Objetivos de la Migración

### Objetivo Principal
Migrar completamente el sistema de autenticación y autorización legacy al nuevo sistema RBAC sin pérdida de datos, interrupciones críticas del servicio, o degradación de la experiencia del usuario.

### Objetivos Específicos

#### Migración de Datos
- [ ] **Usuarios:** Migrar 200 cuentas de usuario con preservación de identidad y preferencias
- [ ] **Roles Legacy:** Mapear y convertir estructura de permisos anterior
- [ ] **Configuraciones:** Transferir settings personalizados y preferencias de sistema
- [ ] **Auditoría:** Mantener trazabilidad completa del proceso de migración
- [ ] **Integridad:** Validar 100% de datos migrados contra fuente original

#### Continuidad de Servicio
- [ ] **Uptime:** Mantener >99% disponibilidad durante proceso de migración
- [ ] **Performance:** No degradar respuesta del sistema >20% durante migración
- [ ] **Rollback:** Capacidad de revertir a sistema anterior en <30 minutos
- [ ] **Testing:** Validar funcionalidad completa antes de cutover final
- [ ] **Support:** Soporte 24/7 durante ventana crítica de migración

---

## 📋 Inventario de Migración

### Usuarios por Categoría

#### Análisis de Usuarios Legacy
```yaml
Distribución de Usuarios Actuales:
  total_users: 200
  
  by_legacy_role:
    administrators: 8
    sales_managers: 12  
    senior_agents: 28
    agents: 47
    premium_clients: 23
    standard_clients: 82
    
  by_activity_level:
    daily_active: 156 (78%)
    weekly_active: 32 (16%)
    monthly_active: 8 (4%)
    inactive_90_days: 4 (2%)
    
  by_data_complexity:
    standard_profile: 178 (89%)
    custom_permissions: 18 (9%)
    special_configurations: 4 (2%)
```

#### Mapeo Legacy a RBAC
```javascript
const legacyToRbacMapping = {
  // Rol Legacy → Nuevo(s) Rol(es) RBAC
  'administrator': ['super_admin'],
  'sales_manager': ['manager'],
  'senior_agent': ['agent_senior'],
  'agent': ['agent'],
  'premium_client': ['client_premium'],
  'standard_client': ['client'],
  
  // Casos especiales que requieren revisión manual
  'custom_admin_user_001': ['super_admin', 'special_audit_access'],
  'temp_contractor_role': ['agent', 'temporary_access'],
  'external_api_user': ['api_access', 'limited_integration'],
  'audit_readonly_user': ['audit_viewer', 'compliance_read']
};

const complexMappings = {
  // Usuarios con múltiples roles legacy
  'user_multi_role': {
    legacy_roles: ['agent', 'premium_client'],
    new_roles: ['agent', 'client_premium'],
    validation_required: true
  },
  
  // Usuarios con permisos personalizados
  'user_custom_perms': {
    legacy_permissions: ['custom.special_report_access'],
    new_approach: 'create_custom_role',
    approval_needed: true
  }
};
```

### Datos y Configuraciones

#### Base de Datos Legacy
```sql
-- Estructura de datos a migrar
LEGACY TABLES TO MIGRATE:
├── users (200 records)
├── user_permissions (847 records)  
├── user_roles (234 records)
├── role_definitions (23 records)
├── permission_definitions (156 records)
├── user_sessions (active: 45 records)
├── audit_logs (retain: 180 days)
└── user_preferences (187 records)

DATA INTEGRITY CHECKS:
├── Foreign key consistency: 100% verified
├── Null value constraints: 3 violations found (to fix)
├── Data type compatibility: 98% compatible
├── Character encoding: UTF-8 throughout
└── Orphaned records: 5 found (to clean)
```

#### Configuraciones de Sistema
```yaml
System Configurations:
  authentication:
    password_policy: "Migrate to new stronger policy"
    session_timeout: "Current: 24h → New: 8h (security improvement)"
    multi_factor_auth: "Legacy: disabled → New: required for admins"
    
  authorization:
    permission_cache_ttl: "Current: 1h → New: 30min (better security)"
    role_inheritance: "Legacy: none → New: hierarchical"
    audit_logging: "Current: basic → New: comprehensive"
    
  integration:
    api_tokens: "23 active tokens to migrate/regenerate"
    external_systems: "3 integrations require token updates"
    webhook_endpoints: "5 endpoints need permission updates"
```

---

## 📅 Cronograma Detallado de Migración

### Fase Pre-migración (25-26 Enero)

#### Día 1 (25 Enero): Preparación y Validación Final

**6:00 - 9:00: Preparación de Ambiente**
- [ ] **6:00-6:30:** Backup completo de base de datos producción
- [ ] **6:30-7:00:** Verificación de integridad del backup
- [ ] **7:00-7:30:** Deploy de herramientas de migración en ambiente staging
- [ ] **7:30-8:00:** Prueba de conectividad y acceso a todos los sistemas
- [ ] **8:00-8:30:** Validación de scripts de migración en staging
- [ ] **8:30-9:00:** Go/No-Go meeting con equipo técnico

**9:00 - 12:00: Migración Piloto**
- [ ] **9:00-9:30:** Selección de usuarios piloto (20 usuarios representativos)
- [ ] **9:30-10:30:** Ejecución de migración piloto en ambiente staging
- [ ] **10:30-11:00:** Validación funcional de usuarios migrados
- [ ] **11:00-11:30:** Testing de performance y carga
- [ ] **11:30-12:00:** Documentación de issues encontrados y resolución

**14:00 - 17:00: Correcciones y Optimización**
- [ ] **14:00-15:30:** Implementación de fixes basados en piloto
- [ ] **15:30-16:00:** Re-testing de migración piloto corregida
- [ ] **16:00-16:30:** Performance tuning de scripts de migración
- [ ] **16:30-17:00:** Preparación final para migración producción

**17:00 - 18:00: Briefing Equipo y Go-Live Decision**
- [ ] **17:00-17:30:** Review de resultados piloto con stakeholders
- [ ] **17:30-17:45:** Final Go/No-Go decision para migración día siguiente
- [ ] **17:45-18:00:** Comunicación de plan final a todos los equipos

#### Día 2 (26 Enero): Go-Live Day - Migración Principal

### Migración en Producción (26 Enero)

#### Ventana de Mantenimiento: 2:00-8:00 AM

**2:00 - 3:00: Preparación y Backup Final**
```bash
# Secuencia de comandos de backup
02:00 - Database backup start
02:15 - Application config backup  
02:30 - User data export validation
02:45 - File system backup verification
03:00 - Backup integrity verification complete
```

**Checklist Pre-migración:**
- [ ] **2:00:** Notification sent to all users about maintenance window
- [ ] **2:05:** Load balancer redirect to maintenance page
- [ ] **2:10:** Stop all application services gracefully
- [ ] **2:15:** Begin full database backup (estimated 30 min)
- [ ] **2:20:** Export current user sessions for restoration
- [ ] **2:25:** Backup application configurations
- [ ] **2:30:** Verify backup integrity and completeness
- [ ] **2:35:** Create migration checkpoint #1
- [ ] **2:40:** Initialize migration environment
- [ ] **2:45:** Final validation of migration scripts
- [ ] **2:50:** Team ready confirmation from all members
- [ ] **2:55:** Begin migration execution

**3:00 - 5:30: Ejecución de Migración Principal**

**Phase 1: Data Migration (3:00-4:30)**
```sql
-- Migration execution sequence
3:00-3:30: User account migration
├── Export legacy users → new user format
├── Validate email uniqueness and format
├── Hash passwords to new format (if needed)
├── Create new user records in RBAC system
└── Validate user data integrity (200 users)

3:30-4:00: Role and permission migration  
├── Create new role definitions
├── Map legacy permissions to new permission structure
├── Create role-permission associations
├── Validate role hierarchy consistency
└── Test permission inheritance logic

4:00-4:30: User-role assignment migration
├── Map users to new roles based on legacy roles
├── Handle special cases and custom permissions
├── Create user-role associations in new system
├── Validate assignment consistency
└── Generate migration report for review
```

**Phase 2: Configuration Migration (4:30-5:00)**
```yaml
4:30-4:45: System Configuration Migration
  - Authentication settings update
  - Session management configuration  
  - Password policy migration
  - Security settings enhancement
  
4:45-5:00: Integration Configuration Update
  - API token regeneration for external systems
  - Webhook endpoint permission updates
  - Third-party integration configuration
  - External system notification of changes
```

**Phase 3: Validation and Testing (5:00-5:30)**
```javascript
5:00-5:15: Data Integrity Validation
const validationChecks = {
  userCount: 'Verify 200 users migrated successfully',
  roleAssignments: 'Validate all role assignments correct',
  permissionInheritance: 'Test role hierarchy working',
  dataIntegrity: 'Check foreign key relationships',
  customConfigs: 'Validate special user configurations'
};

5:15-5:30: Functional Testing
const functionalTests = {
  authentication: 'Test login for sample users from each role',
  authorization: 'Verify permission checks working correctly', 
  userInterface: 'Test UI access based on new permissions',
  apiAccess: 'Validate API endpoints responding correctly',
  reporting: 'Check audit logging functioning'
};
```

**5:30 - 7:00: System Startup y Validation**

**5:30-6:00: Application Restart**
- [ ] **5:30:** Start RBAC authorization service
- [ ] **5:35:** Start authentication service  
- [ ] **5:40:** Start main application services
- [ ] **5:45:** Initialize caches and session storage
- [ ] **5:50:** Restore active user sessions (where possible)
- [ ] **5:55:** Start monitoring and health check systems

**6:00-6:30: Smoke Testing**
```javascript
// Automated smoke test suite
const smokeTests = {
  'Super Admin Login': 'Test super admin can access admin panel',
  'Regular User Login': 'Test regular user login and basic functions',
  'Permission Check': 'Verify permission validation working',
  'Role Assignment': 'Test admin can assign roles',
  'Audit Logging': 'Verify actions being logged correctly',
  'API Endpoints': 'Test key API endpoints responding',
  'Database Performance': 'Check query response times acceptable'
};

// Execute all smoke tests automatically
// Target: All tests pass within 30 minutes
```

**6:30-7:00: Performance Validation y User Notification**
- [ ] **6:30:** Load testing with simulated user load
- [ ] **6:35:** Performance metrics validation
- [ ] **6:40:** Cache warming and optimization
- [ ] **6:45:** Final security validation checks
- [ ] **6:50:** User notification of system availability
- [ ] **6:55:** Load balancer redirect back to application
- [ ] **7:00:** Migration checkpoint #2 - Go-Live complete

#### Post-Migration Monitoring (7:00-12:00)

**7:00 - 9:00: Intensive Monitoring Period**
```yaml
Monitoring Focus Areas:
  system_performance:
    - Response times < 200ms target
    - Database query performance
    - Cache hit ratios
    - Memory and CPU utilization
    
  user_experience:
    - Login success rates
    - Permission check latency  
    - Error rates by user type
    - Support ticket volume
    
  security_validation:
    - Unauthorized access attempts
    - Permission escalation checks
    - Audit log completeness
    - Session management verification
```

**9:00 - 12:00: User Support y Issue Resolution**
- **Dedicated support team:** 5 technical staff available
- **Response SLA:** <15 minutes for critical issues
- **Escalation path:** Direct to migration team lead
- **Communication:** Real-time updates via Slack + email
- **Documentation:** All issues logged with resolution tracking

---

## 🔄 Procedimientos de Migración Detallados

### Script Principal de Migración

#### Migración de Usuarios
```python
#!/usr/bin/env python3
"""
RBAC Migration Script - User Migration Component
Version: 2.0
Author: Carlos Vega - Migration Lead
Date: 2026-01-25
"""

import logging
import psycopg2
import redis
import json
from datetime import datetime
from typing import Dict, List, Optional

class UserMigrator:
    def __init__(self, legacy_db_config: Dict, rbac_db_config: Dict):
        self.legacy_db = psycopg2.connect(**legacy_db_config)
        self.rbac_db = psycopg2.connect(**rbac_db_config)
        self.redis_client = redis.Redis(host='localhost', port=6379, db=0)
        self.migration_log = []
        
        # Setup comprehensive logging
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler(f'migration_{datetime.now().strftime("%Y%m%d_%H%M%S")}.log'),
                logging.StreamHandler()
            ]
        )
        self.logger = logging.getLogger(__name__)
    
    def migrate_users(self) -> Dict[str, int]:
        """
        Main user migration function
        Returns: Dictionary with migration statistics
        """
        self.logger.info("Starting user migration process")
        migration_stats = {
            'total_users': 0,
            'migrated_successfully': 0, 
            'failed_migrations': 0,
            'skipped_users': 0
        }
        
        try:
            # Step 1: Extract users from legacy system
            legacy_users = self._extract_legacy_users()
            migration_stats['total_users'] = len(legacy_users)
            self.logger.info(f"Found {len(legacy_users)} users to migrate")
            
            # Step 2: Validate and transform user data
            for user_data in legacy_users:
                try:
                    # Transform user data to new format
                    rbac_user = self._transform_user_data(user_data)
                    
                    # Validate transformed data
                    if self._validate_user_data(rbac_user):
                        # Insert into new system
                        if self._insert_rbac_user(rbac_user):
                            migration_stats['migrated_successfully'] += 1
                            self.logger.info(f"Successfully migrated user: {rbac_user['email']}")
                        else:
                            migration_stats['failed_migrations'] += 1
                            self.logger.error(f"Failed to insert user: {rbac_user['email']}")
                    else:
                        migration_stats['skipped_users'] += 1
                        self.logger.warning(f"Skipped invalid user: {user_data.get('email', 'unknown')}")
                        
                except Exception as e:
                    migration_stats['failed_migrations'] += 1
                    self.logger.error(f"Error migrating user {user_data.get('email', 'unknown')}: {str(e)}")
            
            # Step 3: Validation and integrity checks
            self._validate_migration_integrity(migration_stats)
            
            self.logger.info(f"User migration completed. Stats: {migration_stats}")
            return migration_stats
            
        except Exception as e:
            self.logger.critical(f"Critical error in user migration: {str(e)}")
            raise
        
        finally:
            # Clean up database connections
            self.legacy_db.close()
            self.rbac_db.close()
    
    def _extract_legacy_users(self) -> List[Dict]:
        """Extract all users from legacy system"""
        cursor = self.legacy_db.cursor()
        
        query = """
        SELECT 
            u.id, u.email, u.username, u.password_hash, 
            u.first_name, u.last_name, u.phone, u.created_at,
            u.last_login, u.is_active, u.email_verified,
            up.preferences, ur.role_name, ur.permissions
        FROM users u
        LEFT JOIN user_preferences up ON u.id = up.user_id
        LEFT JOIN user_roles ur ON u.id = ur.user_id
        WHERE u.is_active = true
        ORDER BY u.created_at
        """
        
        cursor.execute(query)
        users = cursor.fetchall()
        cursor.close()
        
        # Convert to dictionary format
        columns = [
            'legacy_id', 'email', 'username', 'password_hash',
            'first_name', 'last_name', 'phone', 'created_at', 
            'last_login', 'is_active', 'email_verified',
            'preferences', 'legacy_role', 'legacy_permissions'
        ]
        
        return [dict(zip(columns, user)) for user in users]
    
    def _transform_user_data(self, legacy_user: Dict) -> Dict:
        """Transform legacy user data to RBAC format"""
        return {
            'email': legacy_user['email'].lower().strip(),
            'username': legacy_user['username'],
            'password_hash': self._migrate_password_hash(legacy_user['password_hash']),
            'first_name': legacy_user['first_name'],
            'last_name': legacy_user['last_name'], 
            'phone': legacy_user['phone'],
            'created_at': legacy_user['created_at'],
            'last_login': legacy_user['last_login'],
            'is_active': legacy_user['is_active'],
            'email_verified': legacy_user['email_verified'],
            'preferences': self._migrate_preferences(legacy_user['preferences']),
            'rbac_roles': self._map_legacy_role_to_rbac(legacy_user['legacy_role']),
            'migration_metadata': {
                'legacy_id': legacy_user['legacy_id'],
                'migrated_at': datetime.now().isoformat(),
                'migration_version': '2.0'
            }
        }
    
    def _map_legacy_role_to_rbac(self, legacy_role: str) -> List[str]:
        """Map legacy roles to new RBAC roles"""
        role_mapping = {
            'administrator': ['super_admin'],
            'sales_manager': ['manager'],
            'senior_agent': ['agent_senior'],
            'agent': ['agent'],
            'premium_client': ['client_premium'],
            'standard_client': ['client']
        }
        
        # Handle special cases
        if legacy_role in role_mapping:
            return role_mapping[legacy_role]
        else:
            self.logger.warning(f"Unknown legacy role: {legacy_role}, defaulting to 'client'")
            return ['client']
    
    def _validate_user_data(self, user_data: Dict) -> bool:
        """Validate user data before insertion"""
        required_fields = ['email', 'username', 'password_hash', 'rbac_roles']
        
        for field in required_fields:
            if not user_data.get(field):
                self.logger.warning(f"Missing required field: {field}")
                return False
        
        # Email format validation
        if '@' not in user_data['email'] or '.' not in user_data['email']:
            self.logger.warning(f"Invalid email format: {user_data['email']}")
            return False
        
        # Role validation
        valid_roles = ['super_admin', 'admin', 'manager', 'agent_senior', 'agent', 'client_premium', 'client']
        for role in user_data['rbac_roles']:
            if role not in valid_roles:
                self.logger.warning(f"Invalid role: {role}")
                return False
        
        return True
    
    def _insert_rbac_user(self, user_data: Dict) -> bool:
        """Insert user into RBAC system"""
        try:
            cursor = self.rbac_db.cursor()
            
            # Insert user into users table
            insert_user_query = """
            INSERT INTO users (
                email, username, password_hash, first_name, last_name,
                phone, created_at, last_login, is_active, email_verified,
                preferences, migration_metadata
            ) VALUES (
                %(email)s, %(username)s, %(password_hash)s, %(first_name)s, 
                %(last_name)s, %(phone)s, %(created_at)s, %(last_login)s,
                %(is_active)s, %(email_verified)s, %(preferences)s, %(migration_metadata)s
            ) RETURNING id
            """
            
            cursor.execute(insert_user_query, user_data)
            user_id = cursor.fetchone()[0]
            
            # Insert role assignments
            for role_name in user_data['rbac_roles']:
                # Get role ID
                cursor.execute("SELECT id FROM roles WHERE name = %s", (role_name,))
                role_result = cursor.fetchone()
                
                if role_result:
                    role_id = role_result[0]
                    # Insert user-role assignment
                    insert_role_query = """
                    INSERT INTO user_roles (user_id, role_id, assigned_at, assigned_by)
                    VALUES (%s, %s, %s, 'migration_script')
                    """
                    cursor.execute(insert_role_query, (user_id, role_id, datetime.now()))
            
            self.rbac_db.commit()
            cursor.close()
            
            # Log successful migration
            self.migration_log.append({
                'user_email': user_data['email'],
                'migrated_at': datetime.now().isoformat(),
                'new_user_id': user_id,
                'assigned_roles': user_data['rbac_roles']
            })
            
            return True
            
        except Exception as e:
            self.rbac_db.rollback()
            self.logger.error(f"Error inserting user {user_data['email']}: {str(e)}")
            return False
```

#### Migración de Roles y Permisos
```python
class RolePermissionMigrator:
    """Handles migration of roles and permissions from legacy to RBAC"""
    
    def __init__(self, rbac_db_config: Dict):
        self.rbac_db = psycopg2.connect(**rbac_db_config)
        self.logger = logging.getLogger(__name__)
    
    def create_rbac_roles(self) -> bool:
        """Create standard RBAC roles in new system"""
        roles_definition = [
            {
                'name': 'super_admin',
                'display_name': 'Super Administrador',
                'description': 'Acceso completo al sistema',
                'hierarchy_level': 1,
                'parent_role': None
            },
            {
                'name': 'admin', 
                'display_name': 'Administrador',
                'description': 'Administración del sistema',
                'hierarchy_level': 2,
                'parent_role': None
            },
            {
                'name': 'manager',
                'display_name': 'Manager',
                'description': 'Gestión de equipos',
                'hierarchy_level': 3,
                'parent_role': 'agent_senior'
            },
            {
                'name': 'agent_senior',
                'display_name': 'Agente Senior', 
                'description': 'Agente con funcionalidades avanzadas',
                'hierarchy_level': 4,
                'parent_role': 'agent'
            },
            {
                'name': 'agent',
                'display_name': 'Agente',
                'description': 'Agente inmobiliario',
                'hierarchy_level': 5,
                'parent_role': 'client_premium'
            },
            {
                'name': 'client_premium',
                'display_name': 'Cliente Premium',
                'description': 'Cliente con funcionalidades premium',
                'hierarchy_level': 6,
                'parent_role': 'client'
            },
            {
                'name': 'client',
                'display_name': 'Cliente',
                'description': 'Cliente estándar',
                'hierarchy_level': 7,
                'parent_role': None
            }
        ]
        
        try:
            cursor = self.rbac_db.cursor()
            
            for role in roles_definition:
                # Check if role already exists
                cursor.execute("SELECT id FROM roles WHERE name = %s", (role['name'],))
                if cursor.fetchone():
                    self.logger.info(f"Role {role['name']} already exists, skipping")
                    continue
                
                # Insert new role
                insert_query = """
                INSERT INTO roles (name, display_name, description, hierarchy_level, parent_role, created_at)
                VALUES (%(name)s, %(display_name)s, %(description)s, %(hierarchy_level)s, 
                        (SELECT id FROM roles WHERE name = %(parent_role)s), NOW())
                """
                cursor.execute(insert_query, role)
                self.logger.info(f"Created role: {role['name']}")
            
            self.rbac_db.commit()
            cursor.close()
            return True
            
        except Exception as e:
            self.rbac_db.rollback()
            self.logger.error(f"Error creating RBAC roles: {str(e)}")
            return False
    
    def create_permissions(self) -> bool:
        """Create permission definitions"""
        permissions = [
            # User management permissions
            ('users.view', 'users', 'Ver usuarios'),
            ('users.create', 'users', 'Crear usuarios'), 
            ('users.edit', 'users', 'Editar usuarios'),
            ('users.delete', 'users', 'Eliminar usuarios'),
            ('users.assign-roles', 'users', 'Asignar roles'),
            
            # Property management permissions
            ('properties.view', 'properties', 'Ver propiedades'),
            ('properties.create', 'properties', 'Crear propiedades'),
            ('properties.edit', 'properties', 'Editar propiedades'),
            ('properties.delete', 'properties', 'Eliminar propiedades'),
            ('properties.publish', 'properties', 'Publicar propiedades'),
            ('properties.feature', 'properties', 'Destacar propiedades'),
            
            # Offers and negotiations
            ('offers.view', 'offers', 'Ver ofertas'),
            ('offers.create', 'offers', 'Crear ofertas'),
            ('offers.edit', 'offers', 'Editar ofertas'),
            ('offers.delete', 'offers', 'Eliminar ofertas'),
            ('offers.accept', 'offers', 'Aceptar ofertas'),
            ('offers.negotiate', 'offers', 'Negociar ofertas'),
            
            # Reports and analytics  
            ('reports.view', 'reports', 'Ver reportes'),
            ('reports.create', 'reports', 'Crear reportes'),
            ('reports.advanced', 'reports', 'Reportes avanzados'),
            ('reports.team', 'reports', 'Reportes de equipo'),
            
            # System administration
            ('system.configure', 'system', 'Configurar sistema'),
            ('system.backup', 'system', 'Realizar backups'),
            ('system.audit', 'system', 'Auditoría del sistema'),
            
            # Team management
            ('team.view', 'team', 'Ver equipo'),
            ('team.manage', 'team', 'Gestionar equipo'),
            ('team.assign', 'team', 'Asignar tareas'),
            
            # Client management
            ('clients.view', 'clients', 'Ver clientes'),
            ('clients.create', 'clients', 'Crear clientes'),
            ('clients.edit', 'clients', 'Editar clientes'),
            ('clients.contact', 'clients', 'Contactar clientes'),
            
            # Search and filtering
            ('search.basic', 'search', 'Búsqueda básica'),
            ('search.advanced', 'search', 'Búsqueda avanzada'),
            
            # Notifications
            ('notifications.view', 'notifications', 'Ver notificaciones'),
            ('notifications.manage', 'notifications', 'Gestionar notificaciones'),
            
            # Profile management
            ('profile.view', 'profile', 'Ver perfil'),
            ('profile.edit', 'profile', 'Editar perfil'),
            ('profile.manage', 'profile', 'Gestionar perfil completo')
        ]
        
        try:
            cursor = self.rbac_db.cursor()
            
            for permission_name, module, description in permissions:
                # Check if permission exists
                cursor.execute("SELECT id FROM permissions WHERE name = %s", (permission_name,))
                if cursor.fetchone():
                    continue
                
                # Insert permission
                insert_query = """
                INSERT INTO permissions (name, module, description, created_at)
                VALUES (%s, %s, %s, NOW())
                """
                cursor.execute(insert_query, (permission_name, module, description))
            
            self.rbac_db.commit()
            cursor.close()
            return True
            
        except Exception as e:
            self.rbac_db.rollback()
            self.logger.error(f"Error creating permissions: {str(e)}")
            return False
```

### Validación y Testing Automático

#### Script de Validación Post-Migración
```python
class MigrationValidator:
    """Validates migration success and data integrity"""
    
    def __init__(self, legacy_db_config: Dict, rbac_db_config: Dict):
        self.legacy_db = psycopg2.connect(**legacy_db_config)
        self.rbac_db = psycopg2.connect(**rbac_db_config)
        self.logger = logging.getLogger(__name__)
    
    def validate_migration(self) -> Dict[str, any]:
        """Run comprehensive migration validation"""
        validation_results = {
            'user_count_match': False,
            'role_assignments_valid': False,
            'permission_inheritance_working': False,
            'data_integrity_check': False,
            'performance_acceptable': False,
            'security_validations_passed': False,
            'detailed_results': {}
        }
        
        try:
            # Test 1: User count validation
            validation_results['user_count_match'] = self._validate_user_count()
            
            # Test 2: Role assignments
            validation_results['role_assignments_valid'] = self._validate_role_assignments()
            
            # Test 3: Permission inheritance
            validation_results['permission_inheritance_working'] = self._validate_permission_inheritance()
            
            # Test 4: Data integrity
            validation_results['data_integrity_check'] = self._validate_data_integrity()
            
            # Test 5: Performance
            validation_results['performance_acceptable'] = self._validate_performance()
            
            # Test 6: Security
            validation_results['security_validations_passed'] = self._validate_security()
            
            # Overall success calculation
            passed_tests = sum(1 for result in validation_results.values() 
                             if isinstance(result, bool) and result)
            total_tests = len([k for k, v in validation_results.items() 
                             if isinstance(v, bool)])
            
            validation_results['overall_success'] = (passed_tests / total_tests) >= 0.85
            validation_results['success_percentage'] = (passed_tests / total_tests) * 100
            
            self.logger.info(f"Migration validation completed: {passed_tests}/{total_tests} tests passed")
            return validation_results
            
        except Exception as e:
            self.logger.error(f"Error during migration validation: {str(e)}")
            validation_results['validation_error'] = str(e)
            return validation_results
    
    def _validate_user_count(self) -> bool:
        """Validate user count matches between systems"""
        try:
            # Count users in legacy system
            legacy_cursor = self.legacy_db.cursor()
            legacy_cursor.execute("SELECT COUNT(*) FROM users WHERE is_active = true")
            legacy_count = legacy_cursor.fetchone()[0]
            
            # Count users in RBAC system
            rbac_cursor = self.rbac_db.cursor()
            rbac_cursor.execute("SELECT COUNT(*) FROM users WHERE is_active = true")
            rbac_count = rbac_cursor.fetchone()[0]
            
            legacy_cursor.close()
            rbac_cursor.close()
            
            self.logger.info(f"User count - Legacy: {legacy_count}, RBAC: {rbac_count}")
            return legacy_count == rbac_count
            
        except Exception as e:
            self.logger.error(f"Error validating user count: {str(e)}")
            return False
    
    def _validate_permission_inheritance(self) -> bool:
        """Test permission inheritance is working correctly"""
        try:
            cursor = self.rbac_db.cursor()
            
            # Test manager inherits from agent_senior
            test_query = """
            WITH RECURSIVE role_hierarchy AS (
                SELECT id, name, parent_role, 1 as level
                FROM roles 
                WHERE name = 'manager'
                
                UNION ALL
                
                SELECT r.id, r.name, r.parent_role, rh.level + 1
                FROM roles r
                INNER JOIN role_hierarchy rh ON r.id = rh.parent_role
                WHERE rh.level < 5
            )
            SELECT COUNT(*) FROM role_hierarchy WHERE name IN ('agent_senior', 'agent', 'client_premium', 'client')
            """
            
            cursor.execute(test_query)
            inherited_roles_count = cursor.fetchone()[0]
            
            # Manager should inherit from agent_senior (and its parents)
            expected_inheritance_count = 4  # agent_senior, agent, client_premium, client
            
            cursor.close()
            return inherited_roles_count >= expected_inheritance_count
            
        except Exception as e:
            self.logger.error(f"Error validating permission inheritance: {str(e)}")
            return False
```

---

## 🔄 Rollback Procedures

### Criterios de Rollback

#### Automated Rollback Triggers
```python
class RollbackTriggerMonitor:
    """Monitor system health and trigger rollback if needed"""
    
    ROLLBACK_CRITERIA = {
        'critical_errors': {
            'authentication_failure_rate': 25,  # % of auth attempts failing
            'permission_check_errors': 15,      # % of permission checks failing
            'database_connection_failures': 5,  # consecutive connection failures
            'api_response_failures': 30         # % of API calls failing
        },
        'performance_degradation': {
            'response_time_degradation': 300,   # % increase from baseline
            'database_query_timeout': 50,      # % of queries timing out
            'memory_usage_spike': 90,          # % memory utilization
            'cpu_usage_sustained': 85          # % CPU utilization for >10 min
        },
        'data_integrity_issues': {
            'user_count_mismatch': True,        # User count doesn't match
            'role_assignment_failures': 10,    # % of role assignments failing
            'permission_inconsistency': True,  # Permission inheritance broken
            'audit_log_gaps': True            # Missing audit entries
        }
    }
    
    def should_rollback(self) -> bool:
        """Determine if rollback should be triggered"""
        # Check each criteria category
        for category, criteria in self.ROLLBACK_CRITERIA.items():
            if self._check_criteria_category(category, criteria):
                self.logger.critical(f"Rollback triggered due to {category}")
                return True
        return False
```

#### Manual Rollback Decision Points
```yaml
Manual Rollback Scenarios:
  
  stakeholder_decision:
    - "Executive decision to abort migration"
    - "Unacceptable user experience feedback"
    - "Critical business function unavailable"
    - "Security concern identified"
    
  technical_issues:
    - "Data corruption detected"
    - "Integration failures with external systems"
    - "Performance below acceptable threshold"
    - "Unknown errors requiring investigation"
    
  timeline_concerns:
    - "Migration taking longer than expected"
    - "Risk of impacting business hours"
    - "Insufficient time for proper testing"
    - "Need to defer to later maintenance window"
```

### Rollback Execution

#### Automated Rollback Process
```bash
#!/bin/bash
# Emergency Rollback Script
# Execute: ./emergency_rollback.sh
# Contact: Carlos Vega (Migration Lead)

echo "🚨 INITIATING EMERGENCY ROLLBACK - $(date)"
echo "This will restore the system to pre-migration state"

# Step 1: Stop all services
echo "Stopping RBAC services..."
systemctl stop inmotech-rbac-auth
systemctl stop inmotech-rbac-api
systemctl stop inmotech-app

# Step 2: Database rollback
echo "Rolling back database..."
pg_restore --clean --if-exists -d inmotech_production /backups/pre_migration_backup_$(date +%Y%m%d).sql

# Step 3: Restore application configuration
echo "Restoring application configuration..."
cp -r /backups/config_backup_$(date +%Y%m%d)/* /opt/inmotech/config/

# Step 4: Clear RBAC caches
echo "Clearing caches..."
redis-cli FLUSHDB

# Step 5: Restart legacy services
echo "Starting legacy authentication system..."
systemctl start inmotech-legacy-auth
systemctl start inmotech-app

# Step 6: Validate rollback
echo "Validating rollback success..."
python3 /scripts/validate_rollback.py

echo "✅ Rollback completed - $(date)"
echo "System restored to pre-migration state"
echo "Contact technical team for next steps"
```

#### Rollback Validation
```python
def validate_rollback_success():
    """Validate that rollback completed successfully"""
    validation_checks = {
        'legacy_auth_working': test_legacy_authentication(),
        'user_access_restored': test_user_access_patterns(),
        'data_integrity': verify_data_integrity(),
        'system_performance': check_system_performance(),
        'all_services_running': verify_service_health()
    }
    
    success_rate = sum(validation_checks.values()) / len(validation_checks)
    
    if success_rate >= 0.95:
        logger.info("✅ Rollback validation successful")
        send_notification("Rollback completed successfully")
        return True
    else:
        logger.error("❌ Rollback validation failed")
        send_emergency_notification("Rollback validation failed - manual intervention required")
        return False
```

---

## 📊 Monitoreo y Reporting

### Dashboard de Migración en Tiempo Real

#### Métricas Clave Durante Migración
```javascript
// Real-time migration dashboard configuration
const migrationDashboard = {
  criticalMetrics: {
    'Users Migrated': {
      current: 0,
      target: 200,
      format: 'count',
      alert_threshold: 'below_90_percent'
    },
    'Migration Success Rate': {
      current: 0,
      target: 95,
      format: 'percentage',
      alert_threshold: 'below_95_percent'
    },
    'System Response Time': {
      current: 0,
      target: 200,
      format: 'milliseconds', 
      alert_threshold: 'above_300ms'
    },
    'Error Rate': {
      current: 0,
      target: 5,
      format: 'percentage',
      alert_threshold: 'above_10_percent'
    }
  },
  
  progressTracking: {
    'Database Backup': { status: 'pending', estimated_duration: 30 },
    'User Migration': { status: 'pending', estimated_duration: 90 },
    'Role Assignment': { status: 'pending', estimated_duration: 30 },
    'Validation Testing': { status: 'pending', estimated_duration: 45 },
    'System Restart': { status: 'pending', estimated_duration: 15 }
  },
  
  alerting: {
    critical_threshold: 'immediate_notification',
    warning_threshold: '5_minute_delay',
    info_threshold: 'dashboard_only'
  }
};
```

#### Reporting Automático
```python
class MigrationReporter:
    """Generate automated reports during migration process"""
    
    def generate_realtime_report(self) -> Dict:
        """Generate real-time migration status report"""
        return {
            'timestamp': datetime.now().isoformat(),
            'migration_phase': self.get_current_phase(),
            'overall_progress': self.calculate_overall_progress(),
            'users_migrated': self.count_migrated_users(),
            'errors_encountered': self.get_error_summary(),
            'performance_metrics': self.get_performance_metrics(),
            'estimated_completion': self.calculate_eta(),
            'next_checkpoint': self.get_next_milestone()
        }
    
    def generate_completion_report(self) -> Dict:
        """Generate comprehensive migration completion report"""
        return {
            'migration_summary': {
                'start_time': self.migration_start_time,
                'end_time': datetime.now(),
                'total_duration': self.calculate_total_duration(),
                'users_migrated': self.final_user_count,
                'success_rate': self.calculate_success_rate(),
                'downtime_duration': self.calculate_downtime()
            },
            'technical_results': {
                'performance_impact': self.measure_performance_impact(),
                'data_integrity_validation': self.validate_data_integrity(),
                'security_validation': self.validate_security_posture(),
                'rollback_capability_verified': self.verify_rollback_capability()
            },
            'business_impact': {
                'user_satisfaction': self.measure_user_satisfaction(),
                'support_ticket_volume': self.analyze_support_impact(),
                'system_reliability': self.measure_system_reliability(),
                'operational_efficiency': self.measure_efficiency_gains()
            }
        }
```

---

**Procedimientos Preparados por:** Carlos Vega - QA & Migration Lead  
**Revisión Técnica:** Miguel Rodríguez - Arquitecto de Software  
**Validación de Seguridad:** Ana Martín - QA Manager  
**Aprobación:** Project Manager & CTO  
**Fecha de Creación:** 25/01/2026  
**Última Actualización:** 25/01/2026  
**Versión:** 2.0 - Production Ready  

---

**🔄 Estado Actual: MIGRATION PROCEDURES READY**  
**📋 Scripts Desarrollados: 5 componentes principales**  
**⏱️ Ventana de Migración: 6 horas (2:00-8:00 AM)**  
**🎯 Target Success Rate: >95% usuarios migrados**  
**🔙 Rollback Time: <30 minutos si es necesario**