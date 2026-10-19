# Barber Connect – Android (Kotlin + Jetpack Compose + MVVM + Room)

Primera versión del proyecto final *Aplicación Móvil para la Gestión y Reserva de Citas en Barberías* (UPN – Desarrollo de Aplicaciones Móviles).
Avance estimado: **~65 % del código** (Sprints 1‑5 completos, Sprint 6 parcial).

## Cómo abrirlo
**Opción A (recomendada):** Android Studio → *File ▸ Open* → carpeta `BarberConnect` → esperar el *Gradle Sync* → Run ▶ en un emulador API 26+.
**Opción B (si el sync da problemas de versiones):** crear un proyecto "Empty Activity (Compose)" con package `com.upn.barberconnect`, copiar `app/src/main/java/...` y `AndroidManifest.xml`, y añadir las dependencias de `app/build.gradle.kts` (Room + KSP, Navigation Compose, Lifecycle Compose).

## Cuentas de demostración
| Rol | Correo | Contraseña |
|---|---|---|
| Cliente | cliente@barber.com | 123456 |
| Admin | admin@barber.com | admin123 |

## Arquitectura (MVVM + repositorio, para la sección "Arquitectura de la aplicación")
```
UI (Compose)  ──►  ViewModel (StateFlow)  ──►  BarberRepository  ──►  Room (DAOs / SQLite)
 ui/auth, ui/client, ui/admin   Auth/Client/AdminViewModel   data/BarberRepository   data/local
```
- **Estado reactivo:** Room expone `Flow`; los ViewModels lo convierten en `StateFlow` y Compose se actualiza solo (horarios en "tiempo real").
- **Sin doble reserva (RNF08):** `BarberRepository.book()` verifica y guarda dentro de una transacción.
- **Seguridad (RNF03):** contraseñas con hash SHA‑256 (`util/Security.kt`). Para producción, migrar a Firebase Auth.
- **Persistencia (RNF02/05/09):** Room; datos semilla en `AppDatabase.kt`.
- **Mantenibilidad (RNF10):** capas separadas, inyección manual en `BarberApp.kt` (migrable a Hilt).

## Trazabilidad con el prototipo HTML
| HU | Pantalla (maqueta) | Código | Estado |
|---|---|---|---|
| HU01 Inicio de sesión | Login (cliente/admin) + Registro | `ui/auth/*` | ✅ (recuperar contraseña pendiente) |
| HU02 Catálogo | Servicios + búsqueda | `ui/client/ServicesScreen.kt` | ✅ |
| HU03 Reserva | Barbero → fecha → horario | `ui/client/BookingScreen.kt` | ✅ (usa duración fija de 1 slot) |
| HU04 Mis citas | Próximas / Historial / Cancelar | `ui/client/MyAppointmentsScreen.kt` | ✅ (reprogramar pendiente) |
| HU05 Notificaciones | Alertas dentro de la app | `ui/client/NotificationsScreen.kt` | 🟡 in‑app; push/recordatorio del sistema pendiente |
| HU06 Agenda admin | Día / Semana / Mes + cambio de estado | `ui/admin/AgendaScreen.kt` | ✅ |
| HU07 Servicios y precios | Alta, edición, activar/desactivar | `ui/admin/AdminServicesScreen.kt` | ✅ |
| HU08 Clientes e historial | Directorio + búsqueda | `ui/admin/AdminClientsScreen.kt` | 🟡 resumen; detalle de historial por cliente pendiente |
| HU09 Dashboard | KPIs + gráfico 7 días | `ui/admin/DashboardScreen.kt` | 🟡 compartir texto; PDF/imagen pendiente |
| Anexo 2 (impacto social) | "Opciones sostenibles" + mensaje tras reservar | `ServicesScreen.kt`, `BookingScreen.kt` | ✅ |

## Pendiente (≈ 35 %)
1. Recordatorios reales (WorkManager/AlarmManager + `POST_NOTIFICATIONS` en Android 13+).
2. Exportar reporte a PDF/imagen (`PdfDocument`).
3. Reservas que bloqueen varios horarios según la duración del servicio.
4. Reprogramar cita; detalle de historial por cliente; gestión de barberos (CRUD) por el admin.
5. Recuperar contraseña / Firebase Authentication (+ sincronización en la nube).
6. Mover textos a `strings.xml`, ícono de la app, pruebas unitarias (DAO/ViewModel) y pruebas de UI.
7. Evidencias del informe: capturas, repositorio Git, video, PPT.

> Nota: el código fue escrito sin compilar en este entorno. Si Android Studio marca algún error de versión
> de dependencias, usar *Alt+Enter ▸ Update* o sincronizar versiones en `gradle/libs.versions.toml`.
