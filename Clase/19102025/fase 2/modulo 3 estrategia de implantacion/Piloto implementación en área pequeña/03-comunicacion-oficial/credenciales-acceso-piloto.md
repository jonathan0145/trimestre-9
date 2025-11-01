# CREDENCIALES DE ACCESO TEMPORALES - PILOTO INMOTECH

**Proyecto:** Inmotech  
**Fase:** Piloto - Implementación en Área Pequeña  
**Válidas desde:** 22 de octubre de 2025  
**Válidas hasta:** 31 de octubre de 2025  
**Responsable:** María González - Líder de Proyecto  

---

## 🔐 ACCESO AL SISTEMA PILOTO

### **URLs de Acceso:**
- **Aplicación Principal (Frontend):** `https://inmotech-piloto.test.com`
- **Panel Administrativo:** `https://admin-inmotech-piloto.test.com`
- **API de Desarrollo:** `https://api-inmotech-piloto.test.com`
- **Documentación API:** `https://docs-inmotech-piloto.test.com`

### **Entorno Local (Desarrollo):**
- **Frontend Local:** `http://localhost:3000`
- **Backend Local:** `http://localhost:5000`
- **Base de Datos:** `mongodb://localhost:27017/inmotech_piloto`

---

## 👥 CREDENCIALES POR USUARIO PILOTO

### **🛒 ANA TORRES - PERFIL COMPRADOR**

| Campo | Valor |
|-------|-------|
| **Email/Usuario** | ana.torres@inmotech.com |
| **Contraseña Temporal** | `Piloto2025_Ana!` |
| **Rol en Sistema** | buyer |
| **ID de Usuario** | USR_BUYER_001 |
| **Permisos Especiales** | Acceso completo a módulo de búsqueda y ofertas |

**Módulos Asignados:**
- ✅ Dashboard Comprador
- ✅ Búsqueda de Propiedades (básica y avanzada)
- ✅ Detalle de Propiedades
- ✅ Sistema de Ofertas
- ✅ Chat con Vendedores
- ✅ Notificaciones
- ✅ Perfil de Usuario

---

### **🏢 LUIS GÓMEZ - PERFIL VENDEDOR**

| Campo | Valor |
|-------|-------|
| **Email/Usuario** | luis.gomez@inmotech.com |
| **Contraseña Temporal** | `Piloto2025_Luis!` |
| **Rol en Sistema** | seller |
| **ID de Usuario** | USR_SELLER_001 |
| **Permisos Especiales** | Gestión completa de propiedades y ofertas |

**Módulos Asignados:**
- ✅ Dashboard Vendedor
- ✅ Gestión de Propiedades (crear, editar, publicar)
- ✅ Gestión de Ofertas (recibir, evaluar, responder)
- ✅ Chat con Compradores
- ✅ Calendario de Citas
- ✅ Reportes de Actividad
- ✅ Notificaciones
- ✅ Perfil de Usuario

---

### **🤝 CARLA RUIZ - PERFIL INTERMEDIADOR**

| Campo | Valor |
|-------|-------|
| **Email/Usuario** | carla.ruiz@inmotech.com |
| **Contraseña Temporal** | `Piloto2025_Carla!` |
| **Rol en Sistema** | agent |
| **ID de Usuario** | USR_AGENT_001 |
| **Permisos Especiales** | Coordinación multi-parte y gestión documental |

**Módulos Asignados:**
- ✅ Dashboard Intermediación
- ✅ Gestión de Transacciones
- ✅ Centro de Notificaciones
- ✅ Chat Grupal (múltiples participantes)
- ✅ Gestión Documental
- ✅ Calendario de Hitos
- ✅ Reportes de Intermediación
- ✅ Perfil de Usuario

---

## 🔧 CONFIGURACIÓN TÉCNICA

### **Base de Datos de Pruebas:**
- **Servidor:** `mongodb-piloto.inmotech.test:27017`
- **Base de Datos:** `inmotech_piloto_test`
- **Usuario DB:** `piloto_user`
- **Contraseña DB:** `P1l0t0_DB_2025!`

### **API Keys para Pruebas:**
- **API Key General:** `ITP_2025_PILOTO_KEY_ABC123`
- **Google Maps API:** `AIzaSyDemoKeyForPilotoInmotech2025`
- **Email Service API:** `MAIL_PILOT_KEY_XYZ789`

### **Tokens de Acceso:**
- **Ana Torres Token:** `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.ana_torres_buyer`
- **Luis Gómez Token:** `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.luis_gomez_seller`
- **Carla Ruiz Token:** `eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.carla_ruiz_agent`

---

## 📊 DATOS DE PRUEBA PRECARGADOS

### **Propiedades de Ejemplo:**
- **20 Apartamentos** (diferentes sectores y precios)
- **15 Casas** (variedad de tamaños y ubicaciones)
- **10 Locales Comerciales** (diferentes zonas comerciales)
- **5 Oficinas** (edificios corporativos)

### **Usuarios Ficticios para Interacción:**
- **10 Compradores activos** con diferentes perfiles
- **8 Vendedores** con inventarios variados
- **3 Intermediadores** gestionando transacciones

### **Transacciones en Proceso:**
- **5 Negociaciones activas** (diferentes etapas)
- **3 Procesos de intermediación** completos
- **12 Ofertas pendientes** de respuesta

---

## 🚨 PROCEDIMIENTOS DE SEGURIDAD

### **Obligatorio - Primer Acceso:**
1. **Cambiar contraseña temporal** inmediatamente después del primer login
2. **Activar autenticación de dos factores** (2FA) si está disponible
3. **Revisar configuración de perfil** y completar información faltante
4. **Leer y aceptar términos del piloto** antes de usar el sistema

### **Durante el Piloto:**
- ✅ **NO compartir credenciales** con otros usuarios
- ✅ **Cerrar sesión** al terminar cada prueba
- ✅ **Reportar accesos no autorizados** inmediatamente
- ✅ **Usar solo datos de prueba** proporcionados
- ❌ **NO usar información real** de clientes o transacciones

### **Al Finalizar el Piloto:**
- Las credenciales serán **desactivadas automáticamente** el 31 de octubre
- Los datos del piloto serán **archivados** para análisis
- Se proporcionarán **nuevas credenciales** para la siguiente fase

---

## 🔄 PROCEDIMIENTO DE RECUPERACIÓN

### **¿Olvidaste tu contraseña?**
1. **Contactar inmediatamente** a David Paredes (Ext. 400)
2. **Proporcionar tu ID de usuario** y nombre completo
3. **Recibirás nueva contraseña temporal** vía email seguro
4. **Cambiar contraseña** en el primer acceso

### **¿Problemas de acceso?**
1. **Verificar URL** correcta del sistema
2. **Limpiar caché** del navegador
3. **Probar navegador diferente** (Chrome/Firefox)
4. **Contactar soporte técnico** si persiste el problema

---

## 📞 CONTACTOS DE SOPORTE TÉCNICO

| Tipo de Problema | Contacto | Horario | Método |
|------------------|----------|---------|--------|
| **Problemas de acceso** | David Paredes | 8:00 AM - 6:00 PM | Ext. 400 / WhatsApp |
| **Contraseñas olvidadas** | Sistema Automático | 24/7 | soporte@inmotech.com |
| **Problemas funcionales** | Carlos Mendoza | 9:00 AM - 5:00 PM | Ext. 305 |
| **Emergencias críticas** | María González | 24/7 durante piloto | Ext. 201 / WhatsApp |

---

## 📱 APLICACIÓN MÓVIL (Si está disponible)

### **Descarga:**
- **Android:** `https://play.google.com/store/apps/details?id=com.inmotech.piloto`
- **iOS:** `https://apps.apple.com/app/inmotech-piloto/id123456789`

### **Configuración:**
- **Servidor:** `https://inmotech-piloto.test.com`
- **Usar las mismas credenciales** de la aplicación web
- **Sincronización automática** con la versión web

---

## ⚠️ LIMITACIONES DEL ENTORNO PILOTO

### **Restricciones Funcionales:**
- 📧 **Emails de notificación** van solo a direcciones @inmotech.com
- 💰 **Pagos simulados** - no se procesan transacciones reales
- 📱 **SMS limitados** - solo para verificación de cuenta
- 🗺️ **Mapas con datos mock** - ubicaciones aproximadas

### **Limitaciones de Rendimiento:**
- ⏱️ **Puede ser más lento** que el sistema final
- 👥 **Máximo 5 usuarios concurrentes** por módulo
- 📁 **Límite de 100MB** por subida de archivos
- 🔄 **Respaldo cada 2 horas** - posible pérdida mínima de datos

---

## 📋 CHECKLIST DE PRIMER ACCESO

### **Antes de usar el sistema:**
- [ ] **Acceder con credenciales temporales**
- [ ] **Cambiar contraseña obligatoriamente**
- [ ] **Completar información de perfil**
- [ ] **Revisar permisos asignados**
- [ ] **Probar navegación básica**
- [ ] **Verificar recepción de notificaciones**

### **Durante las primeras pruebas:**
- [ ] **Explorar dashboard principal**
- [ ] **Probar funcionalidades básicas de tu rol**
- [ ] **Verificar datos de prueba cargados**
- [ ] **Reportar primer problema/sugerencia**
- [ ] **Confirmar funcionamiento de chat**

---

## 🎯 RECORDATORIO IMPORTANTE

**Estas credenciales son EXCLUSIVAS para el piloto y tienen propósitos de prueba únicamente.**

- 🔒 **Seguridad:** Mantén tus credenciales privadas
- 📝 **Documentación:** Registra todo lo que pruebes
- 🐛 **Reporte:** Informa problemas inmediatamente
- 💡 **Feedback:** Comparte ideas de mejora

**¡Tu participación es clave para el éxito del proyecto Inmotech!**

---

**Documento elaborado por:** David Paredes - Soporte Técnico  
**Validado por:** María González - Líder de Proyecto  
**Fecha de emisión:** 21 de octubre de 2025  
**Estado:** Activo durante piloto  
**Versión:** 1.0

---

## 📄 REGISTRO DE ENTREGA DE CREDENCIALES

| Usuario | Email | Fecha Entrega | Confirmación Recepción | Primer Acceso | Estado |
|---------|-------|---------------|------------------------|---------------|--------|
| Ana Torres | ana.torres@inmotech.com | 21/10/2025 | ✅ Confirmado | Pendiente | Activo |
| Luis Gómez | luis.gomez@inmotech.com | 21/10/2025 | ✅ Confirmado | Pendiente | Activo |
| Carla Ruiz | carla.ruiz@inmotech.com | 21/10/2025 | ✅ Confirmado | Pendiente | Activo |

**Responsable de seguimiento:** David Paredes  
**Próxima revisión:** 24 de octubre de 2025