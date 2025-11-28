# Plan de Migración de Datos - Fase 1: Base de Datos y Migraciones

## 📋 Información del Proyecto
- **Proyecto:** InmoTech - Sistema de Gestión Inmobiliaria
- **Fase:** Fase 1 - Base de Datos y Migraciones
- **Período de Migración:** 08/01/2026 - 09/01/2026
- **Responsable Principal:** Carlos Martínez - Database Administrator
- **Data Migration Lead:** Ana García - Backend Lead Developer
- **Versión:** 1.0

---

## 🎯 Objetivos del Plan de Migración de Datos

### Objetivo Principal
Ejecutar la migración completa y segura de la estructura de base de datos y datos semilla para el sistema InmoTech, asegurando integridad, performance y disponibilidad durante todo el proceso de transformación.

### Objetivos Específicos
- [ ] Migrar estructura de base de datos PostgreSQL desde diseño a implementación
- [ ] Cargar datos semilla para desarrollo, testing y demostración
- [ ] Establecer procedimientos de validación de integridad de datos
- [ ] Implementar estrategias de rollback para cada etapa de migración
- [ ] Asegurar cero pérdida de datos durante el proceso completo
- [ ] Optimizar performance post-migración con índices y configuraciones

---

## 🗂️ Inventario de Datos para Migración

### Datos de Estructura (Schema Migration)

#### Tablas Principales - Prioridad 1 (Crítica)
1. **roles** - Tabla base para sistema de permisos
   - Tipo: Estructura + Datos base
   - Tamaño Estimado: < 1KB
   - Dependencias: Ninguna
   - Tiempo Estimado: 5 min

2. **users** - Usuarios del sistema
   - Tipo: Estructura + Datos semilla
   - Tamaño Estimado: 50KB (100 usuarios de prueba)
   - Dependencias: roles
   - Tiempo Estimado: 10 min

3. **properties** - Propiedades inmobiliarias
   - Tipo: Estructura + Datos semilla
   - Tamaño Estimado: 200KB (500 propiedades de prueba)
   - Dependencias: users
   - Tiempo Estimado: 15 min

#### Tablas Relacionales - Prioridad 2 (Alta)
4. **transactions** - Transacciones inmobiliarias
   - Tipo: Estructura + Datos semilla
   - Tamaño Estimado: 100KB (200 transacciones)
   - Dependencias: properties, users
   - Tiempo Estimado: 10 min

5. **messages** - Sistema de mensajería
   - Tipo: Estructura + Datos semilla
   - Tamaño Estimado: 300KB (1000 mensajes)
   - Dependencias: users, properties
   - Tiempo Estimado: 20 min

6. **notifications** - Sistema de notificaciones
   - Tipo: Estructura + Datos semilla
   - Tamaño Estimado: 150KB (500 notificaciones)
   - Dependencias: users
   - Tiempo Estimado: 10 min

#### Tablas Auxiliares - Prioridad 3 (Media)
7. **files** - Metadatos de archivos
   - Tipo: Estructura + Datos semilla
   - Tamaño Estimado: 75KB (200 archivos)
   - Dependencias: properties, users
   - Tiempo Estimado: 8 min

8. **offers** - Ofertas sobre propiedades
   - Tipo: Estructura + Datos semilla
   - Tamaño Estimado: 120KB (300 ofertas)
   - Dependencias: properties, users
   - Tiempo Estimado: 12 min

### Resumen de Volúmen de Datos
- **Total Registros:** ~2,600 registros semilla
- **Tamaño Total:** ~1MB de datos semilla
- **Tiempo Total Estimado:** 90 minutos

---

## 📅 Cronograma de Migración Detallado

### Miércoles 08/01/2026 - Día 3: Preparación y Migración de Estructura

#### 09:00-10:00: Preparación Pre-Migración
```bash
# Verificaciones pre-migración
echo "=== INICIO PREPARACIÓN MIGRACIÓN $(date) ===" | tee -a /var/log/migration.log

# 1. Verificar estado del servidor PostgreSQL
systemctl status postgresql | tee -a /var/log/migration.log
psql -c "SELECT version();" | tee -a /var/log/migration.log

# 2. Crear backup pre-migración
pg_dump -h localhost -U postgres -d template1 --clean --create \
    --file="/backups/pre_migration_$(date +%Y%m%d_%H%M%S).sql"

# 3. Verificar espacio en disco
df -h | tee -a /var/log/migration.log

# 4. Crear base de datos inmotech
createdb -h localhost -U postgres inmotech
```

#### 10:00-11:30: Migración de Estructura Base
```sql
-- Archivo: migrations/001_create_base_structure.sql
BEGIN;

-- Crear tabla de control de migraciones
CREATE TABLE IF NOT EXISTS migration_versions (
    id SERIAL PRIMARY KEY,
    version VARCHAR(50) UNIQUE NOT NULL,
    applied_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    applied_by VARCHAR(100) DEFAULT current_user
);

-- Tabla roles (sin dependencias)
CREATE TABLE roles (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL,
    description TEXT,
    permissions JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabla users (depende de roles)
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    role_id INTEGER NOT NULL REFERENCES roles(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabla properties (depende de users)
CREATE TABLE properties (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(12,2) NOT NULL,
    location VARCHAR(200) NOT NULL,
    coordinates POINT,
    property_type VARCHAR(50) NOT NULL,
    user_id INTEGER NOT NULL REFERENCES users(id),
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Registrar migración
INSERT INTO migration_versions (version) VALUES ('001_create_base_structure');

COMMIT;
```

**Ejecución:**
```bash
psql -h localhost -U postgres -d inmotech -f migrations/001_create_base_structure.sql | tee -a /var/log/migration.log

# Verificar tablas creadas
psql -d inmotech -c "\dt" | tee -a /var/log/migration.log
```

#### 11:30-12:00: Migración de Tablas Relacionales
```sql
-- Archivo: migrations/002_create_relational_tables.sql
BEGIN;

-- Tabla transactions
CREATE TABLE transactions (
    id SERIAL PRIMARY KEY,
    property_id INTEGER NOT NULL REFERENCES properties(id),
    buyer_id INTEGER REFERENCES users(id),
    seller_id INTEGER REFERENCES users(id),
    amount DECIMAL(12,2) NOT NULL,
    transaction_date DATE NOT NULL,
    status VARCHAR(20) DEFAULT 'pending',
    commission DECIMAL(5,2) DEFAULT 0.00,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabla messages
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    sender_id INTEGER NOT NULL REFERENCES users(id),
    receiver_id INTEGER NOT NULL REFERENCES users(id),
    property_id INTEGER REFERENCES properties(id),
    content TEXT NOT NULL,
    read_status BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabla notifications
CREATE TABLE notifications (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id),
    title VARCHAR(100) NOT NULL,
    content TEXT NOT NULL,
    type VARCHAR(30) NOT NULL,
    read_status BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabla files
CREATE TABLE files (
    id SERIAL PRIMARY KEY,
    property_id INTEGER REFERENCES properties(id),
    file_name VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    file_type VARCHAR(50) NOT NULL,
    file_size BIGINT,
    uploaded_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabla offers
CREATE TABLE offers (
    id SERIAL PRIMARY KEY,
    property_id INTEGER NOT NULL REFERENCES properties(id),
    user_id INTEGER NOT NULL REFERENCES users(id),
    amount DECIMAL(12,2) NOT NULL,
    message TEXT,
    status VARCHAR(20) DEFAULT 'pending',
    expires_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Registrar migración
INSERT INTO migration_versions (version) VALUES ('002_create_relational_tables');

COMMIT;
```

#### 13:00-14:00: Almuerzo y Verificación Intermedia

#### 14:00-15:00: Creación de Índices Optimizados
```sql
-- Archivo: migrations/003_create_indexes.sql
BEGIN;

-- Índices para tabla users
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role_id ON users(role_id);
CREATE INDEX idx_users_created_at ON users(created_at);

-- Índices para tabla properties
CREATE INDEX idx_properties_user_id ON properties(user_id);
CREATE INDEX idx_properties_type_status ON properties(property_type, status);
CREATE INDEX idx_properties_price ON properties(price);
CREATE INDEX idx_properties_location ON properties(location);
CREATE INDEX idx_properties_coordinates ON properties USING GIST(coordinates);
CREATE INDEX idx_properties_created_at ON properties(created_at);

-- Índices para tabla transactions
CREATE INDEX idx_transactions_property_id ON transactions(property_id);
CREATE INDEX idx_transactions_buyer_id ON transactions(buyer_id);
CREATE INDEX idx_transactions_seller_id ON transactions(seller_id);
CREATE INDEX idx_transactions_date_status ON transactions(transaction_date, status);

-- Índices para tabla messages
CREATE INDEX idx_messages_sender_id ON messages(sender_id);
CREATE INDEX idx_messages_receiver_id ON messages(receiver_id);
CREATE INDEX idx_messages_property_id ON messages(property_id);
CREATE INDEX idx_messages_read_status ON messages(read_status);
CREATE INDEX idx_messages_created_at ON messages(created_at);

-- Índices para tabla notifications
CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_type ON notifications(type);
CREATE INDEX idx_notifications_read_status ON notifications(read_status);
CREATE INDEX idx_notifications_created_at ON notifications(created_at);

-- Índices para tabla files
CREATE INDEX idx_files_property_id ON files(property_id);
CREATE INDEX idx_files_uploaded_by ON files(uploaded_by);
CREATE INDEX idx_files_file_type ON files(file_type);

-- Índices para tabla offers
CREATE INDEX idx_offers_property_id ON offers(property_id);
CREATE INDEX idx_offers_user_id ON offers(user_id);
CREATE INDEX idx_offers_status ON offers(status);
CREATE INDEX idx_offers_expires_at ON offers(expires_at);

-- Registrar migración
INSERT INTO migration_versions (version) VALUES ('003_create_indexes');

COMMIT;
```

#### 15:00-16:00: Validación de Estructura
```sql
-- Archivo: validations/validate_structure.sql

-- Verificar todas las tablas
SELECT 'Table Count' as check_type, COUNT(*) as result 
FROM information_schema.tables 
WHERE table_schema = 'public' AND table_type = 'BASE TABLE';

-- Verificar foreign keys
SELECT 'Foreign Key Count' as check_type, COUNT(*) as result
FROM information_schema.table_constraints 
WHERE constraint_type = 'FOREIGN KEY';

-- Verificar índices
SELECT 'Index Count' as check_type, COUNT(*) as result
FROM pg_indexes 
WHERE schemaname = 'public';

-- Verificar integridad referencial
SELECT 
    tc.table_name,
    tc.constraint_name,
    tc.constraint_type,
    kcu.column_name,
    ccu.table_name AS references_table,
    ccu.column_name AS references_column
FROM information_schema.table_constraints tc
JOIN information_schema.key_column_usage kcu 
    ON tc.constraint_name = kcu.constraint_name
JOIN information_schema.constraint_column_usage ccu 
    ON ccu.constraint_name = tc.constraint_name
WHERE tc.constraint_type = 'FOREIGN KEY';
```

### Jueves 09/01/2026 - Día 4: Carga de Datos Semilla

#### 09:00-10:30: Carga de Datos Base y Usuarios

**Datos de Roles Base:**
```sql
-- Archivo: data/001_seed_roles.sql
BEGIN;

INSERT INTO roles (name, description, permissions) VALUES
('admin', 'Administrador del sistema', '{"users": ["create", "read", "update", "delete"], "properties": ["create", "read", "update", "delete"], "system": ["admin"]}'),
('agent', 'Agente inmobiliario', '{"properties": ["create", "read", "update"], "messages": ["create", "read"], "offers": ["read", "update"]}'),
('buyer', 'Comprador', '{"properties": ["read"], "messages": ["create", "read"], "offers": ["create", "read", "update"]}'),
('seller', 'Vendedor', '{"properties": ["create", "read", "update"], "messages": ["read"], "offers": ["read"]}'),
('guest', 'Usuario invitado', '{"properties": ["read"]}');

COMMIT;
```

**Datos de Usuarios Semilla:**
```javascript
// Script: data/002_generate_users.js
const { faker } = require('faker');
const bcrypt = require('bcrypt');

async function generateUsers() {
    const users = [];
    
    // Admin user
    users.push({
        email: 'admin@inmotech.com',
        password: await bcrypt.hash('admin123', 10),
        firstName: 'Carlos',
        lastName: 'Administrador',
        phone: '+34600123456',
        roleId: 1 // admin
    });

    // Agentes inmobiliarios (20 usuarios)
    for (let i = 0; i < 20; i++) {
        users.push({
            email: faker.internet.email(),
            password: await bcrypt.hash('agent123', 10),
            firstName: faker.person.firstName(),
            lastName: faker.person.lastName(),
            phone: faker.phone.number('+34#########'),
            roleId: 2 // agent
        });
    }

    // Compradores (40 usuarios)
    for (let i = 0; i < 40; i++) {
        users.push({
            email: faker.internet.email(),
            password: await bcrypt.hash('buyer123', 10),
            firstName: faker.person.firstName(),
            lastName: faker.person.lastName(),
            phone: faker.phone.number('+34#########'),
            roleId: 3 // buyer
        });
    }

    // Vendedores (30 usuarios)
    for (let i = 0; i < 30; i++) {
        users.push({
            email: faker.internet.email(),
            password: await bcrypt.hash('seller123', 10),
            firstName: faker.person.firstName(),
            lastName: faker.person.lastName(),
            phone: faker.phone.number('+34#########'),
            roleId: 4 // seller
        });
    }

    // Usuarios invitados (10 usuarios)
    for (let i = 0; i < 10; i++) {
        users.push({
            email: faker.internet.email(),
            password: await bcrypt.hash('guest123', 10),
            firstName: faker.person.firstName(),
            lastName: faker.person.lastName(),
            phone: faker.phone.number('+34#########'),
            roleId: 5 // guest
        });
    }

    return users;
}

module.exports = { generateUsers };
```

**Ejecución de Carga de Usuarios:**
```bash
# Ejecutar generador de usuarios
node scripts/load_users.js | tee -a /var/log/migration.log

# Verificar carga
psql -d inmotech -c "SELECT role_id, COUNT(*) FROM users GROUP BY role_id;" | tee -a /var/log/migration.log
```

#### 10:30-12:00: Carga de Propiedades

**Generador de Propiedades:**
```javascript
// Script: data/003_generate_properties.js
const { faker } = require('faker');

const propertyTypes = ['apartment', 'house', 'villa', 'townhouse', 'studio', 'duplex'];
const spanishCities = ['Madrid', 'Barcelona', 'Valencia', 'Sevilla', 'Bilbao', 'Málaga', 'Zaragoza'];
const statuses = ['active', 'pending', 'sold', 'rented'];

async function generateProperties() {
    const properties = [];
    
    for (let i = 0; i < 500; i++) {
        const city = faker.helpers.arrayElement(spanishCities);
        const coordinates = getCoordinatesForCity(city);
        
        properties.push({
            title: faker.lorem.words(4),
            description: faker.lorem.paragraph(3),
            price: faker.number.int({ min: 50000, max: 2000000 }),
            location: `${faker.location.streetAddress()}, ${city}, España`,
            coordinates: `(${coordinates.lat}, ${coordinates.lng})`,
            property_type: faker.helpers.arrayElement(propertyTypes),
            user_id: faker.number.int({ min: 2, max: 21 }), // Solo agentes pueden crear propiedades
            status: faker.helpers.arrayElement(statuses)
        });
    }
    
    return properties;
}

function getCoordinatesForCity(city) {
    const cityCoords = {
        'Madrid': { lat: 40.4168, lng: -3.7038 },
        'Barcelona': { lat: 41.3851, lng: 2.1734 },
        'Valencia': { lat: 39.4699, lng: -0.3763 },
        'Sevilla': { lat: 37.3891, lng: -5.9845 },
        'Bilbao': { lat: 43.2630, lng: -2.9350 },
        'Málaga': { lat: 36.7213, lng: -4.4214 },
        'Zaragoza': { lat: 41.6488, lng: -0.8891 }
    };
    
    const baseCoord = cityCoords[city];
    return {
        lat: baseCoord.lat + (Math.random() - 0.5) * 0.1,
        lng: baseCoord.lng + (Math.random() - 0.5) * 0.1
    };
}

module.exports = { generateProperties };
```

#### 13:00-14:00: Almuerzo

#### 14:00-15:30: Carga de Datos Relacionales

**Transacciones:**
```javascript
// Script: data/004_generate_transactions.js
async function generateTransactions() {
    // Obtener propiedades vendidas
    const soldProperties = await db.query(
        "SELECT id, user_id, price FROM properties WHERE status = 'sold'"
    );
    
    const transactions = [];
    
    for (const property of soldProperties) {
        // Buyer random (role_id = 3)
        const buyerId = faker.number.int({ min: 22, max: 61 }); // Rango de buyers
        
        transactions.push({
            property_id: property.id,
            buyer_id: buyerId,
            seller_id: property.user_id,
            amount: property.price * (0.95 + Math.random() * 0.1), // ±5% del precio listado
            transaction_date: faker.date.past({ years: 2 }),
            status: 'completed',
            commission: (property.price * 0.03) // 3% comisión
        });
    }
    
    return transactions;
}
```

**Mensajes:**
```javascript
// Script: data/005_generate_messages.js
async function generateMessages() {
    const messages = [];
    
    // Obtener propiedades activas
    const activeProperties = await db.query(
        "SELECT id, user_id FROM properties WHERE status = 'active'"
    );
    
    for (const property of activeProperties) {
        // Generar 1-5 mensajes por propiedad
        const messageCount = faker.number.int({ min: 1, max: 5 });
        
        for (let i = 0; i < messageCount; i++) {
            // Sender random (buyers principalmente)
            const senderId = faker.number.int({ min: 22, max: 91 }); // Buyers y algunos sellers
            
            messages.push({
                sender_id: senderId,
                receiver_id: property.user_id, // Owner de la propiedad
                property_id: property.id,
                content: generatePropertyMessage(),
                read_status: faker.datatype.boolean({ probability: 0.7 }), // 70% leídos
                created_at: faker.date.past({ years: 1 })
            });
        }
    }
    
    return messages;
}

function generatePropertyMessage() {
    const templates = [
        "Hola, estoy interesado en visitar esta propiedad. ¿Cuándo podríamos coordinar?",
        "¿La propiedad está disponible para visitas este fin de semana?",
        "Me gustaria conocer más detalles sobre el estado de la propiedad.",
        "¿Existe flexibilidad en el precio? Tengo una oferta seria.",
        "¿La propiedad incluye garaje y trastero?",
        "¿Cuáles son los gastos de comunidad mensuales?",
        "¿Se aceptan mascotas en el edificio?",
        "Quisiera programar una segunda visita con mi familia."
    ];
    
    return faker.helpers.arrayElement(templates);
}
```

#### 15:30-16:30: Carga de Ofertas y Archivos

**Ofertas:**
```javascript
// Script: data/006_generate_offers.js
async function generateOffers() {
    const offers = [];
    
    // Obtener propiedades activas
    const activeProperties = await db.query(
        "SELECT id, price, user_id FROM properties WHERE status IN ('active', 'pending')"
    );
    
    for (const property of activeProperties) {
        // 30% de propiedades tienen ofertas
        if (Math.random() < 0.3) {
            const offerCount = faker.number.int({ min: 1, max: 3 });
            
            for (let i = 0; i < offerCount; i++) {
                const buyerId = faker.number.int({ min: 22, max: 61 }); // Buyers
                const offerAmount = property.price * (0.85 + Math.random() * 0.2); // 85-105% del precio
                
                offers.push({
                    property_id: property.id,
                    user_id: buyerId,
                    amount: Math.round(offerAmount),
                    message: generateOfferMessage(),
                    status: faker.helpers.arrayElement(['pending', 'accepted', 'rejected', 'expired']),
                    expires_at: faker.date.future({ days: 30 }),
                    created_at: faker.date.past({ days: 60 })
                });
            }
        }
    }
    
    return offers;
}
```

**Archivos (Metadata):**
```javascript
// Script: data/007_generate_files.js
async function generateFiles() {
    const files = [];
    const fileTypes = ['image/jpeg', 'image/png', 'application/pdf', 'image/webp'];
    const fileNames = ['plano.pdf', 'fachada.jpg', 'interior_1.png', 'cocina.jpg', 'bano.jpeg', 'certificado_energetico.pdf'];
    
    // Obtener todas las propiedades
    const properties = await db.query("SELECT id, user_id FROM properties");
    
    for (const property of properties) {
        // Cada propiedad tiene 2-8 archivos
        const fileCount = faker.number.int({ min: 2, max: 8 });
        
        for (let i = 0; i < fileCount; i++) {
            const fileName = faker.helpers.arrayElement(fileNames);
            const fileType = fileName.endsWith('.pdf') ? 'application/pdf' : faker.helpers.arrayElement(fileTypes);
            
            files.push({
                property_id: property.id,
                file_name: fileName,
                file_path: `/uploads/properties/${property.id}/${fileName}`,
                file_type: fileType,
                file_size: faker.number.int({ min: 51200, max: 10485760 }), // 50KB - 10MB
                uploaded_by: property.user_id,
                created_at: faker.date.past({ days: 180 })
            });
        }
    }
    
    return files;
}
```

#### 16:30-17:00: Validación Final de Datos

```sql
-- Archivo: validations/validate_data_integrity.sql

-- Verificar conteos por tabla
SELECT 'roles' as table_name, COUNT(*) as records FROM roles
UNION ALL
SELECT 'users' as table_name, COUNT(*) as records FROM users
UNION ALL
SELECT 'properties' as table_name, COUNT(*) as records FROM properties
UNION ALL
SELECT 'transactions' as table_name, COUNT(*) as records FROM transactions
UNION ALL
SELECT 'messages' as table_name, COUNT(*) as records FROM messages
UNION ALL
SELECT 'notifications' as table_name, COUNT(*) as records FROM notifications
UNION ALL
SELECT 'files' as table_name, COUNT(*) as records FROM files
UNION ALL
SELECT 'offers' as table_name, COUNT(*) as records FROM offers;

-- Verificar integridad referencial
SELECT 'users_without_valid_role' as check_type, COUNT(*) as violations
FROM users u LEFT JOIN roles r ON u.role_id = r.id WHERE r.id IS NULL
UNION ALL
SELECT 'properties_without_valid_user' as check_type, COUNT(*) as violations
FROM properties p LEFT JOIN users u ON p.user_id = u.id WHERE u.id IS NULL
UNION ALL
SELECT 'transactions_with_invalid_property' as check_type, COUNT(*) as violations
FROM transactions t LEFT JOIN properties p ON t.property_id = p.id WHERE p.id IS NULL;

-- Verificar rangos de datos
SELECT 'properties_with_invalid_price' as check_type, COUNT(*) as violations
FROM properties WHERE price <= 0 OR price > 10000000
UNION ALL
SELECT 'users_with_invalid_email' as check_type, COUNT(*) as violations
FROM users WHERE email IS NULL OR email = '' OR email NOT LIKE '%@%';

-- Verificar distribución de datos por roles
SELECT r.name as role, COUNT(u.id) as user_count
FROM roles r
LEFT JOIN users u ON r.id = u.role_id
GROUP BY r.id, r.name
ORDER BY r.id;
```

---

## 🔍 Procedimientos de Validación

### Validación de Integridad de Datos

#### Checks Automáticos Pre-Migración
```bash
#!/bin/bash
# Archivo: scripts/pre_migration_checks.sh

echo "=== CHECKS PRE-MIGRACIÓN $(date) ===" | tee -a /var/log/migration_validation.log

# 1. Verificar conectividad PostgreSQL
psql -d postgres -c "SELECT 'PostgreSQL está funcionando' as status;" 2>/dev/null
if [ $? -eq 0 ]; then
    echo "✅ PostgreSQL: OPERATIVO" | tee -a /var/log/migration_validation.log
else
    echo "❌ PostgreSQL: ERROR - No se puede conectar" | tee -a /var/log/migration_validation.log
    exit 1
fi

# 2. Verificar espacio en disco
AVAILABLE_SPACE=$(df /var/lib/postgresql | tail -1 | awk '{print $4}')
REQUIRED_SPACE=1048576  # 1GB en KB

if [ $AVAILABLE_SPACE -gt $REQUIRED_SPACE ]; then
    echo "✅ Espacio en disco: SUFICIENTE ($(($AVAILABLE_SPACE/1024/1024))GB disponibles)" | tee -a /var/log/migration_validation.log
else
    echo "❌ Espacio en disco: INSUFICIENTE" | tee -a /var/log/migration_validation.log
    exit 1
fi

# 3. Verificar que no existe base de datos inmotech
DB_EXISTS=$(psql -d postgres -tAc "SELECT 1 FROM pg_database WHERE datname='inmotech'")
if [ "$DB_EXISTS" = "1" ]; then
    echo "⚠️ Base de datos 'inmotech' ya existe - se requerirá limpieza" | tee -a /var/log/migration_validation.log
else
    echo "✅ Base de datos: LISTA para creación" | tee -a /var/log/migration_validation.log
fi

# 4. Verificar archivos de migración
MIGRATION_FILES=("001_create_base_structure.sql" "002_create_relational_tables.sql" "003_create_indexes.sql")
for file in "${MIGRATION_FILES[@]}"; do
    if [ -f "migrations/$file" ]; then
        echo "✅ Archivo migración: $file ENCONTRADO" | tee -a /var/log/migration_validation.log
    else
        echo "❌ Archivo migración: $file FALTANTE" | tee -a /var/log/migration_validation.log
        exit 1
    fi
done

echo "=== PRE-MIGRACIÓN CHECKS COMPLETADOS ===" | tee -a /var/log/migration_validation.log
```

#### Checks Automáticos Post-Migración
```bash
#!/bin/bash
# Archivo: scripts/post_migration_validation.sh

echo "=== VALIDACIÓN POST-MIGRACIÓN $(date) ===" | tee -a /var/log/migration_validation.log

# 1. Verificar estructura de tablas
EXPECTED_TABLES=("roles" "users" "properties" "transactions" "messages" "notifications" "files" "offers")
for table in "${EXPECTED_TABLES[@]}"; do
    TABLE_EXISTS=$(psql -d inmotech -tAc "SELECT 1 FROM information_schema.tables WHERE table_name='$table'")
    if [ "$TABLE_EXISTS" = "1" ]; then
        echo "✅ Tabla $table: CREADA" | tee -a /var/log/migration_validation.log
    else
        echo "❌ Tabla $table: FALTANTE" | tee -a /var/log/migration_validation.log
        exit 1
    fi
done

# 2. Verificar foreign keys
FK_COUNT=$(psql -d inmotech -tAc "SELECT COUNT(*) FROM information_schema.table_constraints WHERE constraint_type='FOREIGN KEY'")
EXPECTED_FK=12
if [ "$FK_COUNT" -eq "$EXPECTED_FK" ]; then
    echo "✅ Foreign Keys: $FK_COUNT de $EXPECTED_FK creadas" | tee -a /var/log/migration_validation.log
else
    echo "❌ Foreign Keys: $FK_COUNT de $EXPECTED_FK (INCOMPLETO)" | tee -a /var/log/migration_validation.log
    exit 1
fi

# 3. Verificar índices
INDEX_COUNT=$(psql -d inmotech -tAc "SELECT COUNT(*) FROM pg_indexes WHERE schemaname='public'")
EXPECTED_INDEXES=20
if [ "$INDEX_COUNT" -ge "$EXPECTED_INDEXES" ]; then
    echo "✅ Índices: $INDEX_COUNT creados (mín: $EXPECTED_INDEXES)" | tee -a /var/log/migration_validation.log
else
    echo "❌ Índices: $INDEX_COUNT de $EXPECTED_INDEXES (INSUFICIENTES)" | tee -a /var/log/migration_validation.log
    exit 1
fi

# 4. Verificar datos básicos
ROLES_COUNT=$(psql -d inmotech -tAc "SELECT COUNT(*) FROM roles")
if [ "$ROLES_COUNT" -eq 5 ]; then
    echo "✅ Roles base: $ROLES_COUNT cargados" | tee -a /var/log/migration_validation.log
else
    echo "❌ Roles base: $ROLES_COUNT de 5 (INCOMPLETO)" | tee -a /var/log/migration_validation.log
    exit 1
fi

# 5. Test de performance básico
START_TIME=$(date +%s%N)
psql -d inmotech -c "SELECT COUNT(*) FROM users;" > /dev/null
END_TIME=$(date +%s%N)
DURATION=$(( (END_TIME - START_TIME) / 1000000 )) # Convert to milliseconds

if [ "$DURATION" -lt 100 ]; then
    echo "✅ Performance básico: ${DURATION}ms (< 100ms)" | tee -a /var/log/migration_validation.log
else
    echo "⚠️ Performance básico: ${DURATION}ms (> 100ms - revisar)" | tee -a /var/log/migration_validation.log
fi

echo "=== VALIDACIÓN POST-MIGRACIÓN COMPLETADA ===" | tee -a /var/log/migration_validation.log
```

### Validación de Calidad de Datos

#### Data Quality Checks
```sql
-- Archivo: validations/data_quality_checks.sql

-- Reporte de calidad de datos
WITH data_quality_report AS (
    -- Completitud de campos obligatorios
    SELECT 
        'users_email_completeness' as metric,
        ROUND(
            100.0 * COUNT(CASE WHEN email IS NOT NULL AND email != '' THEN 1 END) / COUNT(*),
            2
        ) as percentage,
        'Expected: 100%' as target
    FROM users
    
    UNION ALL
    
    SELECT 
        'properties_price_validity' as metric,
        ROUND(
            100.0 * COUNT(CASE WHEN price > 0 AND price <= 10000000 THEN 1 END) / COUNT(*),
            2
        ) as percentage,
        'Expected: 100%' as target
    FROM properties
    
    UNION ALL
    
    -- Consistencia referencial
    SELECT 
        'users_role_consistency' as metric,
        ROUND(
            100.0 * COUNT(u.id) / (SELECT COUNT(*) FROM users),
            2
        ) as percentage,
        'Expected: 100%' as target
    FROM users u
    JOIN roles r ON u.role_id = r.id
    
    UNION ALL
    
    -- Distribución esperada de datos
    SELECT 
        'property_status_distribution' as metric,
        ROUND(
            100.0 * COUNT(CASE WHEN status IN ('active', 'pending', 'sold', 'rented') THEN 1 END) / COUNT(*),
            2
        ) as percentage,
        'Expected: 100%' as target
    FROM properties
)

SELECT * FROM data_quality_report
ORDER BY metric;

-- Verificar outliers y datos anómalos
SELECT 'Properties with extreme prices' as anomaly_check,
       COUNT(*) as count,
       MIN(price) as min_price,
       MAX(price) as max_price
FROM properties 
WHERE price < 10000 OR price > 5000000;

-- Verificar duplicados potenciales
SELECT 'Potential duplicate users' as anomaly_check,
       COUNT(*) as count
FROM (
    SELECT email, COUNT(*) 
    FROM users 
    GROUP BY email 
    HAVING COUNT(*) > 1
) duplicates;
```

---

## 🔄 Procedimientos de Rollback de Migración

### Rollback Completo
```bash
#!/bin/bash
# Archivo: scripts/migration_rollback_complete.sh

echo "=== INICIO ROLLBACK COMPLETO $(date) ===" | tee -a /var/log/migration_rollback.log

# 1. Backup de estado actual antes de rollback
CURRENT_BACKUP="/backups/pre_rollback_$(date +%Y%m%d_%H%M%S).sql"
pg_dump -h localhost -U postgres -d inmotech --clean --create --file="$CURRENT_BACKUP"

if [ $? -eq 0 ]; then
    echo "✅ Backup pre-rollback creado: $CURRENT_BACKUP" | tee -a /var/log/migration_rollback.log
else
    echo "❌ ERROR: No se pudo crear backup pre-rollback" | tee -a /var/log/migration_rollback.log
    exit 1
fi

# 2. Eliminar base de datos completamente
echo "Eliminando base de datos inmotech..." | tee -a /var/log/migration_rollback.log
dropdb -h localhost -U postgres inmotech

if [ $? -eq 0 ]; then
    echo "✅ Base de datos eliminada exitosamente" | tee -a /var/log/migration_rollback.log
else
    echo "❌ ERROR: No se pudo eliminar base de datos" | tee -a /var/log/migration_rollback.log
    exit 1
fi

# 3. Limpiar archivos temporales
rm -f /tmp/migration_*.tmp
rm -f /tmp/data_generation_*.tmp

echo "=== ROLLBACK COMPLETO FINALIZADO $(date) ===" | tee -a /var/log/migration_rollback.log
```

### Rollback Selectivo por Componente
```sql
-- Archivo: scripts/rollback_data_only.sql
-- Para mantener estructura pero limpiar datos

BEGIN;

-- Deshabilitar foreign keys temporalmente
SET session_replication_role = replica;

-- Limpiar datos en orden correcto
TRUNCATE TABLE offers CASCADE;
TRUNCATE TABLE files CASCADE;
TRUNCATE TABLE notifications CASCADE;
TRUNCATE TABLE messages CASCADE;
TRUNCATE TABLE transactions CASCADE;
TRUNCATE TABLE properties CASCADE;
TRUNCATE TABLE users CASCADE;
TRUNCATE TABLE roles CASCADE;

-- Re-habilitar foreign keys
SET session_replication_role = DEFAULT;

-- Resetear secuencias
ALTER SEQUENCE roles_id_seq RESTART WITH 1;
ALTER SEQUENCE users_id_seq RESTART WITH 1;
ALTER SEQUENCE properties_id_seq RESTART WITH 1;
ALTER SEQUENCE transactions_id_seq RESTART WITH 1;
ALTER SEQUENCE messages_id_seq RESTART WITH 1;
ALTER SEQUENCE notifications_id_seq RESTART WITH 1;
ALTER SEQUENCE files_id_seq RESTART WITH 1;
ALTER SEQUENCE offers_id_seq RESTART WITH 1;

-- Log del rollback
INSERT INTO migration_versions (version) VALUES ('rollback_data_only_' || to_char(now(), 'YYYYMMDDHH24MISS'));

COMMIT;
```

---

## 📊 Monitoreo During Migration

### Métricas en Tiempo Real

#### Performance Monitoring
```bash
#!/bin/bash
# Archivo: scripts/monitor_migration_performance.sh

while true; do
    echo "=== MIGRATION PERFORMANCE $(date) ==="
    
    # CPU y memoria
    top -bn1 | grep "Cpu(s)" | awk '{print "CPU: " $2}' | sed 's/%us,//'
    free -h | awk 'FNR==2{print "Memory: " $3 "/" $2 " (" $3/$2*100 "%)"}'
    
    # Conexiones PostgreSQL
    CONNECTIONS=$(psql -d postgres -tAc "SELECT count(*) FROM pg_stat_activity")
    echo "DB Connections: $CONNECTIONS"
    
    # Progreso de migración (aproximado)
    if psql -d inmotech -c "SELECT 1;" 2>/dev/null; then
        TABLES_COUNT=$(psql -d inmotech -tAc "SELECT count(*) FROM information_schema.tables WHERE table_schema='public'")
        RECORDS_COUNT=$(psql -d inmotech -tAc "SELECT sum(n_tup_ins) FROM pg_stat_user_tables")
        echo "Tables created: $TABLES_COUNT"
        echo "Records inserted: $RECORDS_COUNT"
    fi
    
    echo "---"
    sleep 30
done
```

#### Error Monitoring
```bash
#!/bin/bash
# Archivo: scripts/monitor_migration_errors.sh

# Monitor PostgreSQL logs for errors during migration
tail -f /var/log/postgresql/postgresql-14-main.log | while read line; do
    echo "$line" | grep -E "(ERROR|FATAL|PANIC)" && {
        echo "🚨 PostgreSQL Error detected: $line" | tee -a /var/log/migration_errors.log
        
        # Enviar alerta si es crítico
        echo "$line" | grep -E "(FATAL|PANIC)" && {
            echo "CRITICAL PostgreSQL Error during migration: $line" | \
            mail -s "URGENT: Migration Error - InmoTech" carlos.martinez@inmotech.com
        }
    }
done
```

### Dashboard de Progreso
```javascript
// Archivo: scripts/migration_dashboard.js
const express = require('express');
const { Pool } = require('pg');
const app = express();

const pool = new Pool({
    user: 'postgres',
    host: 'localhost',
    database: 'inmotech',
    password: 'password',
    port: 5432,
});

app.get('/migration-status', async (req, res) => {
    try {
        // Progress overview
        const tablesResult = await pool.query(
            "SELECT count(*) FROM information_schema.tables WHERE table_schema='public'"
        );
        
        const recordsResult = await pool.query(`
            SELECT 
                'roles' as table_name, count(*) as records FROM roles
            UNION ALL
            SELECT 'users' as table_name, count(*) as records FROM users
            UNION ALL
            SELECT 'properties' as table_name, count(*) as records FROM properties
            UNION ALL
            SELECT 'transactions' as table_name, count(*) as records FROM transactions
            UNION ALL
            SELECT 'messages' as table_name, count(*) as records FROM messages
        `);
        
        const migrationsResult = await pool.query(
            "SELECT version, applied_at FROM migration_versions ORDER BY applied_at"
        );
        
        res.json({
            timestamp: new Date(),
            tables_created: tablesResult.rows[0].count,
            records_by_table: recordsResult.rows,
            migrations_applied: migrationsResult.rows,
            progress_percentage: calculateProgress(recordsResult.rows)
        });
        
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

function calculateProgress(records) {
    const targets = {
        roles: 5,
        users: 100,
        properties: 500,
        transactions: 200,
        messages: 1000
    };
    
    let totalProgress = 0;
    let tableCount = 0;
    
    records.forEach(record => {
        if (targets[record.table_name]) {
            totalProgress += Math.min(100, (record.records / targets[record.table_name]) * 100);
            tableCount++;
        }
    });
    
    return tableCount > 0 ? Math.round(totalProgress / tableCount) : 0;
}

app.listen(3001, () => {
    console.log('Migration dashboard running on http://localhost:3001/migration-status');
});
```

---

## ⚠️ Gestión de Issues Durante Migración

### Problemas Comunes y Soluciones

#### Issue 1: Foreign Key Constraint Violations
**Síntoma:** Error durante inserción de datos por violación FK
```
ERROR: insert or update on table "users" violates foreign key constraint "users_role_id_fkey"
```

**Diagnóstico:**
```sql
-- Verificar datos huérfanos
SELECT u.id, u.role_id 
FROM users u 
LEFT JOIN roles r ON u.role_id = r.id 
WHERE r.id IS NULL;
```

**Solución:**
```sql
-- Opción 1: Corregir role_id inválidos
UPDATE users SET role_id = 5 WHERE role_id NOT IN (SELECT id FROM roles);

-- Opción 2: Insertar roles faltantes
INSERT INTO roles (id, name, description) 
SELECT DISTINCT role_id, 'temp_role_' || role_id, 'Temporary role'
FROM users 
WHERE role_id NOT IN (SELECT id FROM roles);
```

#### Issue 2: Duplicate Key Violations
**Síntoma:** Error por duplicación en campos únicos
```
ERROR: duplicate key value violates unique constraint "users_email_key"
```

**Diagnóstico:**
```sql
-- Encontrar duplicados
SELECT email, COUNT(*) 
FROM users 
GROUP BY email 
HAVING COUNT(*) > 1;
```

**Solución:**
```sql
-- Agregar sufijo numérico a emails duplicados
WITH numbered_duplicates AS (
    SELECT id, email, 
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) as rn
    FROM users
)
UPDATE users 
SET email = u.email || '_' || nd.rn
FROM numbered_duplicates nd
WHERE u.id = nd.id AND nd.rn > 1;
```

#### Issue 3: Performance Degradation
**Síntoma:** Queries lentas durante o después de migración

**Diagnóstico:**
```sql
-- Verificar estadísticas de tablas
SELECT schemaname, tablename, n_tup_ins, n_tup_upd, n_tup_del, last_analyze
FROM pg_stat_user_tables
ORDER BY n_tup_ins DESC;

-- Verificar uso de índices
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read
FROM pg_stat_user_indexes 
ORDER BY idx_scan DESC;
```

**Solución:**
```sql
-- Actualizar estadísticas
ANALYZE;

-- Re-indexar si es necesario
REINDEX DATABASE inmotech;

-- Verificar configuración PostgreSQL
SHOW shared_buffers;
SHOW work_mem;
SHOW effective_cache_size;
```

### Escalación de Issues

#### Level 1: Auto-Recovery (0-15 min)
- Scripts automáticos de retry para fallos temporales
- Validaciones automáticas y corrección de datos
- Rollback automático de transacciones fallidas

#### Level 2: DBA Intervention (15-60 min)
- Carlos Martínez interviene para issues técnicos de BD
- Análisis manual de logs y diagnóstico
- Implementación de fixes manuales

#### Level 3: Team Escalation (1-4 hours)
- Escalación a Ana García para issues de aplicación
- Escalación a Miguel Torres para decisiones de proyecto
- Evaluación de rollback parcial vs continuación

#### Level 4: Management Decision (4+ hours)
- Escalación a Director Técnico
- Decisión de rollback completo vs re-planificación
- Comunicación a stakeholders ejecutivos

---

## 📋 Checklist Final de Migración

### Pre-Migración ✅
- [ ] **Backup verificado:** Backup completo pre-migración creado y verificado
- [ ] **Scripts validados:** Todos los scripts de migración probados en staging
- [ ] **Equipo disponible:** Carlos (DBA) y Ana (Backend) en standby
- [ ] **Comunicación enviada:** Stakeholders notificados del inicio
- [ ] **Monitoreo activo:** Scripts de monitoreo ejecutándose

### Durante Migración ✅
- [ ] **Estructura creada:** 8 tablas principales implementadas
- [ ] **Relaciones establecidas:** 12 foreign keys configuradas
- [ ] **Índices aplicados:** 20+ índices optimizados creados
- [ ] **Datos base cargados:** Roles y usuarios administrativos
- [ ] **Datos semilla cargados:** 500 propiedades, 100 usuarios, datos relacionales
- [ ] **Performance validado:** Queries principales < 100ms

### Post-Migración ✅
- [ ] **Integridad verificada:** Cero violaciones de constraints
- [ ] **Performance confirmado:** Todos los SLAs cumplidos
- [ ] **Backup post-migración:** Nuevo baseline de backup creado
- [ ] **Documentación actualizada:** Procedimientos y cambios documentados
- [ ] **Equipo capacitado:** Knowledge transfer completado
- [ ] **Handover ejecutado:** Operaciones transferidas a equipo de soporte

---

## 📚 Referencias y Documentos

### Documentos Relacionados
- **Plan de Implementación Fase 1:** `fase-01-plan-implementacion.md`
- **Procedimientos de Rollback:** `fase-01-procedimientos-rollback.md`
- **Checklist de Pruebas:** `fase-01-checklist-pruebas.md`

### Scripts Repository
```
migrations/
├── 001_create_base_structure.sql
├── 002_create_relational_tables.sql
├── 003_create_indexes.sql
└── rollback/
    ├── 001_rollback_indexes.sql
    ├── 002_rollback_tables.sql
    └── 003_rollback_structure.sql

data/
├── 001_seed_roles.sql
├── 002_generate_users.js
├── 003_generate_properties.js
├── 004_generate_transactions.js
├── 005_generate_messages.js
├── 006_generate_offers.js
└── 007_generate_files.js

scripts/
├── pre_migration_checks.sh
├── post_migration_validation.sh
├── monitor_migration_performance.sh
├── migration_rollback_complete.sh
└── migration_dashboard.js

validations/
├── validate_structure.sql
├── data_quality_checks.sql
└── performance_validation.sql
```

---

## ✅ Aprobaciones

### Aprobación Técnica de Migración
**Database Administrator:** Carlos Martínez  
**Firma:** ________________  
**Fecha:** __/__/____

### Validación de Datos
**Backend Lead Developer:** Ana García  
**Firma:** ________________  
**Fecha:** __/__/____

### Aprobación de Proceso
**Project Manager:** Miguel Torres  
**Firma:** ________________  
**Fecha:** __/__/____

### Validación de Calidad
**QA Lead:** Laura Pérez  
**Firma:** ________________  
**Fecha:** __/__/____

---

*Plan de Migración de Datos para el Proyecto InmoTech - Sistema de Gestión Inmobiliaria*  
*Fase 1: Base de Datos y Migraciones | Enero 2026 | Equipo de Migración de Datos*