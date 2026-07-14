# Comparativa Quechua Central

## Estructura

```
quechua_comparativa/
├── backend/
│   ├── main.py
│   └── requirements.txt
└── frontend/
    ├── gestor.html      ← interfaz admin (configura los 16 cmid)
    └── index.html       ← interfaz docente (abierta desde Moodle)
```

---

## Levantar el backend

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

Edita en `main.py`:
```python
MOODLE_URL   = "https://tu-moodle.edu.pe"
MOODLE_TOKEN = "TU_TOKEN_WEBSERVICE"
```

El backend también sirve el frontend automáticamente en `/`.

---

## Gestor (admin) — `gestor.html`

Aquí registras las **16 actividades** (8 evaluaciones × 2 intentos).

Cada actividad necesita:

| Campo | Descripción |
|-------|-------------|
| CMID | ID del course module en Moodle |
| Feedback ID | ID del módulo feedback |
| Course ID (curid) | ID del curso en Moodle |
| N° Evaluación | 1 al 8 |
| Intento | 1 o 2 |
| Nombre | Etiqueta descriptiva (ej. "U2S1 P1 – Haka uchu") |
| Texto correcto | Transcripción guía para comparar |

---

## Interfaz docente — `index.html`

Moodle abre esta URL con los parámetros del usuario:

```
https://tu-servidor.com/?id_user=4&feedbackid=12035&cmid=4821&curid=2501&nombre_usuario=Guido
```

### Lo que muestra según el caso

| Caso | Muestra |
|------|---------|
| Todos los cmid | Puntaje `0/1` o `1/1` + respuesta del docente |
| Solo intento 2 | Lo anterior + texto correcto como retroalimentación |
| Ya evaluado antes | El resultado guardado sin volver a llamar a Moodle |

---

## Endpoints API

| Método | Ruta | Uso |
|--------|------|-----|
| GET | `/api/actividades?curid=X` | Lista actividades (filtrable por curid) |
| POST | `/api/actividades` | Crea actividad |
| PUT | `/api/actividades/{cmid}` | Edita actividad |
| DELETE | `/api/actividades/{cmid}` | Elimina actividad |
| GET | `/api/resultado/{cmid}/{id_user}` | Consulta resultado guardado |
| POST | `/api/comparar` | Evalúa y guarda (idempotente) |
| GET | `/api/resumen/{curid}` | Todos los resultados del curso (admin) |

---

## Nota sobre Moodle WebService

Función: `mod_feedback_get_responses_analysis`

Permisos requeridos en el token: `mod/feedback:viewreports`

Tipo de ítem soportado: `textarea` y `textfield`
