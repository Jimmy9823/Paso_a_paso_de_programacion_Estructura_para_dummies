# 📌 Guía de Notación en Angular

| Elemento en Angular       | Notación recomendada | Ejemplo correcto | Ejemplo incorrecto ❌ |
|----------------------------|----------------------|------------------|-----------------------|
| **Clases** (Componentes, Servicios, Pipes, Directivas) | **PascalCase** | `LoginComponent`, `UsuarioService` | `logincomponent`, `usuario_service` |
| **Interfaces**             | **PascalCase** | `Usuario`, `Solicitud` | `usuario`, `solicitud_datos` |
| **Enums**                  | **PascalCase** | `EstadoSolicitud { PENDIENTE, APROBADA }` | `estado_solicitud`, `estadoSolicitud` |
| **Variables**              | **camelCase** | `let nombreUsuario`, `const edadPromedio` | `NombreUsuario`, `nombre_usuario` |
| **Propiedades de clase**   | **camelCase** | `this.totalVentas`, `this.idUsuario` | `TotalVentas`, `total_ventas` |
| **Funciones y métodos**    | **camelCase** | `obtenerUsuarios()`, `cargarDatos()` | `Obtener_Usuarios()`, `CargarDatos` |
| **Archivos**               | **kebab-case** | `login.component.ts`, `usuario.service.ts` | `LoginComponent.ts`, `usuario_service.ts` |
| **Selectores de componentes** | **kebab-case** | `<app-login-form></app-login-form>` | `<appLoginForm>`, `<app_login_form>` |
| **Módulos (NgModules)**    | **PascalCase** | `AppModule`, `UsuarioModule` | `app_module`, `usuariomodule` |
| **Constantes globales**    | **UPPER_CASE_SNAKE** (opcional) | `API_URL = "http://localhost:8080"` | `apiUrl`, `ApiUrl` |
