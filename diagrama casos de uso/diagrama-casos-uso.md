# Diagrama de Casos de Uso INMOTECH

**Actores:**
- Administrador
- Agente
- Usuario final

**Casos de Uso Principales:**
- Administrar usuarios
- Administrar roles y permisos
- Verificar usuarios y propiedades
- Gestionar propiedades
- Gestionar ofertas
- Monitorear calidad y reportes
- Autenticarse
- Chat y notificaciones en tiempo real
- Buscar propiedades
- Realizar ofertas
- Verificar información

**Relaciones:**
- El Administrador puede realizar todos los casos de uso administrativos y de gestión.
- El Agente puede autenticarse, gestionar propiedades y ofertas, chatear y verificar propiedades.
- El Usuario final puede autenticarse, buscar propiedades, realizar ofertas, chatear y verificar información.

```mermaid
%%{init: {"theme": "default"}}%%
actor Administrador
actor Agente
actor UsuarioFinal as "Usuario final"

Administrador --> (Administrar usuarios)
Administrador --> (Administrar roles y permisos)
Administrador --> (Verificar usuarios y propiedades)
Administrador --> (Gestionar propiedades)
Administrador --> (Gestionar ofertas)
Administrador --> (Monitorear calidad y reportes)

Agente --> (Autenticarse)
Agente --> (Gestionar propiedades)
Agente --> (Gestionar ofertas)
Agente --> (Chat y notificaciones en tiempo real)
Agente --> (Verificar propiedades)

UsuarioFinal --> (Autenticarse)
UsuarioFinal --> (Buscar propiedades)
UsuarioFinal --> (Realizar ofertas)
UsuarioFinal --> (Chat y notificaciones en tiempo real)
UsuarioFinal --> (Verificar información)