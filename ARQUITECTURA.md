# PYS Examen - Arquitectura Completa del Proyecto

## Descripcion General

**PYS Examen** es una aplicacion web de examenes de conocimiento para la empresa **Proyectos y Seguros**. Permite a los empleados realizar un examen de 15 preguntas con temporizador, y a los administradores ver resultados, estadisticas y exportar datos.

---

## Stack Tecnologico

| Componente | Tecnologia |
|---|---|
| **Frontend** | HTML5 + CSS3 + JavaScript vanilla (sin frameworks) |
| **Backend/BD** | Supabase (PostgreSQL hosted) |
| **Hosting** | Vercel (sitio estatico) |
| **Fuentes** | Google Fonts (Inter) |
| **Librerias CDN** | Supabase JS SDK v2, Canvas Confetti |

---

## Estructura de Archivos

```
pys-examen/
├── index.html          # App principal del examen (frontend publico)
├── admin/
│   └── index.html      # Panel de administracion (protegido con PIN)
├── .vercel/
│   └── project.json    # Configuracion del proyecto Vercel
├── .gitignore
└── ARQUITECTURA.md     # Este archivo
```

---

## Base de Datos (Supabase)

### Proyecto Supabase
- **URL**: `https://anwcebubrnpehtswgbfl.supabase.co`
- **Anon Key**: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImFud2NlYnVicm5wZWh0c3dnYmZsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzEzNzMyODUsImV4cCI6MjA4Njk0OTI4NX0.OqlZHliY6S1kltM4gs2RwPP7T9iBKTiJT1t8oX1vVc0`

### SQL para crear la tabla

```sql
CREATE TABLE pys_exam_submissions (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  nombre TEXT NOT NULL,
  departamento TEXT,
  puntuacion INTEGER NOT NULL,
  total_preguntas INTEGER DEFAULT 15,
  respuestas JSONB,
  tiempo_segundos INTEGER,
  aprobado BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

ALTER TABLE pys_exam_submissions ENABLE ROW LEVEL SECURITY;

CREATE POLICY "allow_insert" ON pys_exam_submissions
  FOR INSERT WITH CHECK (true);

CREATE POLICY "allow_select" ON pys_exam_submissions
  FOR SELECT USING (true);
```

### Esquema de la tabla `pys_exam_submissions`

| Columna | Tipo | Descripcion |
|---|---|---|
| `id` | UUID (PK) | Identificador unico autogenerado |
| `nombre` | TEXT | Nombre del empleado |
| `departamento` | TEXT | Departamento del empleado |
| `puntuacion` | INTEGER | Numero de respuestas correctas |
| `total_preguntas` | INTEGER | Total de preguntas (default 15) |
| `respuestas` | JSONB | Objeto con las respuestas dadas (ej: {"1":"B","2":"C",...}) |
| `tiempo_segundos` | INTEGER | Tiempo que tardo en completar el examen |
| `aprobado` | BOOLEAN | Si aprobo o no (>=50% = aprobado) |
| `created_at` | TIMESTAMPTZ | Fecha y hora de envio |

---

## Funcionalidades

### Examen (`index.html`)
- Pantalla de bienvenida: nombre + departamento (select)
- 15 preguntas tipo test (A/B/C/D) con una respuesta correcta cada una
- Temporizador de 10 minutos (600 segundos)
- Barra de progreso animada
- Navegacion entre preguntas con animaciones
- Pantalla de resultados con confetti si aprueba
- Revision detallada de respuestas (correctas/incorrectas)
- Guardado automatico en Supabase al terminar
- Umbral de aprobacion: 50% (8/15)
- Diseno responsive (mobile-first)

### Admin (`admin/index.html`)
- Login con contrasena: `exampys2026`
- Dashboard con estadisticas: total examenes, aprobados, suspensos, nota media
- Tabla ordenable con todos los resultados
- Filtro por departamento
- Modal de detalle por examen (ver respuestas individuales)
- Exportar a CSV
- Eliminar registros individuales
- Instrucciones de setup SQL si la tabla no existe

---

## Despliegue en Vercel

### Configuracion actual
- **Project ID**: `prj_JcsoZwtNqHPJ8dTgICWzU8MlkUo6`
- **Org ID**: `team_PwnmNeFEJfGvdeGTSMLHKKU4`
- **Project Name**: `pys-examen`
- **Tipo**: Sitio estatico (no requiere build)

### Pasos para desplegar en un nuevo Vercel

1. Crear cuenta en [Vercel](https://vercel.com)
2. Crear un nuevo proyecto e importar desde GitHub (o subir directamente)
3. **Framework Preset**: `Other` (sitio estatico)
4. **Build Command**: dejar vacio
5. **Output Directory**: `.` (raiz)
6. Hacer deploy

### Pasos para desplegar en GitHub

1. Crear repositorio nuevo en GitHub
2. `git init` (si no existe)
3. `git remote add origin https://github.com/TU_USUARIO/TU_REPO.git`
4. `git add .`
5. `git commit -m "Initial commit"`
6. `git push -u origin main`
7. Conectar el repo con Vercel para deploys automaticos

---

## Configuracion de Supabase (nuevo proyecto)

### Para usar con un Supabase propio:

1. Crear proyecto en [Supabase](https://supabase.com)
2. Ir a **SQL Editor** y ejecutar el SQL de arriba
3. Ir a **Settings > API** y copiar:
   - `Project URL` → reemplazar `SUPABASE_URL` en ambos archivos HTML
   - `anon public key` → reemplazar `SUPABASE_KEY` en ambos archivos HTML
4. Buscar y reemplazar en:
   - `index.html` (lineas ~758-759)
   - `admin/index.html` (lineas ~416-417)

---

## Departamentos configurados

Los departamentos disponibles en el selector del examen son:
- Administracion
- Autos
- Back Office
- Contabilidad
- Diversos
- Direccion
- Formacion
- Informatica
- Marketing
- Personales
- Post Venta
- RRHH
- Siniestros
- Vida

---

## Credenciales

| Recurso | Valor |
|---|---|
| Admin Password | `exampys2026` |
| Supabase URL | `https://anwcebubrnpehtswgbfl.supabase.co` |
| Supabase Anon Key | (ver archivos HTML) |

**IMPORTANTE**: Para produccion, cambiar la contrasena del admin y las keys de Supabase.

---

## Notas tecnicas

- Es un proyecto 100% estatico (no requiere Node.js, npm, ni build)
- Las librerias se cargan desde CDN (Supabase JS, Canvas Confetti, Google Fonts)
- No hay `.env` ni variables de entorno - todo esta en el HTML
- El diseno usa CSS custom properties (variables) y es totalmente responsive
- Tema oscuro con acentos dorados (branding PyS)
