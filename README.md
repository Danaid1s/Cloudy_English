# ☁️ Cloudy English

Plataforma de inglés especializado estilo Duolingo con cuentas en la nube (Supabase), 4 especialidades (Ingeniería, Medicina y Salud, Moda y Confección, General/Negocios), ruta A1→C1, 6 vidas con retos de castigo, tutor con voz y tutor de IA opcional. Todo en un único `index.html` (sin build), listo para GitHub Pages.

## Puesta en marcha con Supabase

1. En Supabase abre **SQL Editor**, pega el contenido de `supabase-setup.sql` y ejecútalo **una sola vez** (crea `profiles` y `daily_progress` con RLS).
2. En **Project Settings → API Keys** copia la **Project URL** y la **clave publicable** (`sb_publishable_...`; sirve también la `anon` heredada).
3. Abre `index.html`, busca el bloque `CREDENCIALES DE SUPABASE` y completa:

```js
const SUPABASE_URL="https://TU-PROYECTO.supabase.co";
const SUPABASE_ANON_KEY="sb_publishable_...";
```

4. **Authentication → Providers → Email**: si exiges confirmar el correo, el usuario debe confirmarlo antes de iniciar sesión (para probar rápido puedes desactivar la confirmación).
5. **Authentication → URL Configuration**: pon tu dirección de GitHub Pages como *Site URL*.

> 🔒 La clave publicable es segura en el navegador **solo con RLS activado** (ya lo hace el SQL). **Nunca** pegues la clave secreta (`sb_secret_...`) ni `service_role` en el frontend.

Sin credenciales, la app funciona en **modo local simulado** (cuentas solo en ese navegador).

## Qué se guarda y dónde

| Dato | Tabla / columna |
|---|---|
| Nombre, especialidad, nivel actual, meta | `profiles.name`, `field`, `level`, `goal_level` |
| Racha, XP | `profiles.streak`, `xp` |
| Vidas (0–6) y su reloj de recuperación | `profiles.hearts`, `hearts_at` |
| Tema, tiempo diario, plan, onboarding | `profiles.settings` (jsonb) |
| Progreso diario (minutos, meta cumplida, XP) | `daily_progress` (una fila por día) |

El guardado es automático ~0,8 s después de cada cambio. La clave de IA del tutor (Anthropic/OpenAI) **no** se sube a Supabase: queda solo en el navegador.

## Estructura del código

`index.html` → capa de cuentas (`hydrate`, `persist`, `cloudAuth`, `loadCloud`, `cloudSave`) · `FIELDS` (contenido por especialidad) · gamificación (XP, vidas, castigos, ruta) · tutor (local o IA real).

Para añadir una especialidad, copia la entrada `moda` en `FIELDS` y añade su clave al `check` de `profiles.field` en el SQL.

## Despliegue en GitHub Pages

Sube `index.html` a un repo → **Settings → Pages → Deploy from a branch → main → / (root)**.
