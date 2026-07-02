# Sistema Financiero — Core (Módulo 1)

Un solo archivo (`index.html`) con Supabase conectado directo, sin build, sin Next.js, sin variables de entorno en Vercel. Igual que GESTION-SANT.

## Qué incluye este módulo

- **Login** (Supabase Auth)
- **Dashboard**: saldo total, ingresos/egresos de hoy, resultado del mes, saldo por cuenta, últimos movimientos, gráfico de evolución mensual
- **Ingresos**: carga y listado (fecha, monto, cuenta, categoría, cliente, medio de pago)
- **Egresos**: carga y listado (fecha, monto, cuenta, categoría, proveedor, medio de pago)
- **Bancos / Caja**: alta de cuentas con saldo inicial
- **Gastos fijos**: plantillas recurrentes + botón "Generar gastos de este mes" (no duplica si lo apretás dos veces)

**Todavía NO incluye** (quedan para los próximos módulos, según el orden que ya charlamos): vencimientos y alertas, conciliación bancaria con PDF/OCR, cuotas de viviendas/terrenos, agenda/calendario, asistente de IA. Esas piezas necesitan un servidor corriendo código (funciones serverless en Vercel, o similar) y conviene sumarlas una vez que esta base esté probada en el día a día.

## 1. Base de datos (Supabase)

Ya tenés el proyecto creado (`aportesceclaboulaye-dot's Project`) con la migración `0001_init.sql` corrida. Ahora sumá esta:

1. Andá a **SQL Editor** en Supabase → **New query**
2. Pegá todo el contenido de `sql/0002_core_financiero.sql`
3. **Run**

Esto agrega las tablas `cuentas`, `gastos_fijos`, `gastos_fijos_generados`, y extiende `movimientos_caja` con `cuenta_id` y `cliente_proveedor`. También crea dos cuentas base ("Caja efectivo" y "Cuenta bancaria principal") que podés renombrar o borrar desde la app.

Si todavía no tenés tu usuario admin creado, seguí usando los pasos que ya conocés: **Authentication → Users → Add user**, y después en SQL Editor:

```sql
update public.profiles set role = 'admin'
where id = (select id from auth.users where email = 'tu-email@ejemplo.com');
```

## 2. Configurar el HTML

Abrí `index.html` con cualquier editor de texto (el Bloc de notas alcanza) y buscá estas dos líneas, cerca del final del archivo, dentro de `<script>`:

```javascript
const SUPABASE_URL = "https://TU-PROYECTO.supabase.co";
const SUPABASE_ANON_KEY = "TU_ANON_KEY";
```

Reemplazalas con tus datos reales de Supabase (**Project Settings → API Keys**):
- `SUPABASE_URL` → el "Project URL"
- `SUPABASE_ANON_KEY` → la clave `anon` `public` de la pestaña **"Legacy anon, service_role API keys"** (la que empieza con `eyJ...` — es la que ya usamos y funciona bien; la nueva `sb_publishable_...` todavía puede dar problemas con la versión del cliente que carga por CDN)

Guardá el archivo.

## 3. Probarlo local, sin subir nada todavía

Como es un solo HTML, ni siquiera necesitás un servidor: hacé doble clic en `index.html` y se abre en el navegador. Probá loguearte y cargar un ingreso de prueba.

Si tu navegador bloquea algo por seguridad al abrirlo como archivo local (poco común, pero puede pasar), instalá la extensión "Live Server" en VS Code, o simplemente saltá directo al paso 4 y probalo ya en Vercel.

## 4. GitHub

Subí este archivo (`index.html`) al repo `SISTCEC`, reemplazando o al lado de lo que ya subiste. Como es un solo archivo, esta vez el drag-and-drop en GitHub va a ser mucho más simple: arrastralo directo a la página del repo, sin pelear con carpetas.

## 5. Vercel

Al ser un HTML estático (sin build, sin framework), Vercel lo va a servir directo sin necesitar ningún "Framework Preset" especial — de hecho conviene que ese campo diga **"Other"**, no "Next.js". Si el proyecto ya está importado, andá a **Settings → Build and Deployment** y confirmá:
- Framework Preset: `Other`
- Build Command: vacío
- Output Directory: vacío (o `./`)

Ya no hace falta cargar ninguna variable de entorno en Vercel — la conexión a Supabase ya está adentro del HTML.

Redeploy, y listo.

## Cómo seguimos sumando funciones

Cada vez que quieras un cambio o un módulo nuevo, el flujo es: te paso el `index.html` actualizado (o las secciones nuevas) → lo subís a GitHub reemplazando el archivo → Vercel redespliega solo con cada push.
