# 🎮 Simulador de Reglas y Motivaciones — Octalysis Educativo

Aplicación web interactiva y de diagnóstico psicopedagógico en tiempo real basada en el framework **Octalysis** (Yu-kai Chou), diseñada para talleres docentes y sesiones de formación en gamificación educativa.

---

## 🎯 Propósito Pedagógico

El simulador plantea un reto pedagógico concreto:
> *"Los estudiantes llegan a la clase sin haber revisado las lecturas previas asignadas, dificultando el debate y la profundización en el aula."*

A partir de este reto, los participantes diseñan una estrategia seleccionando mecánicas de juego, descubren el impacto motivacional de sus decisiones (**Sombrero Blanco vs. Sombrero Negro** y **Motivación Intrínseca vs. Extrínseca**) y calibran su dominio conceptual identificando los **8 Core Drives (Impulsadores)** del modelo Octalysis.

---

## 🚀 Flujo de la Experiencia

### 1. Onboarding y Recuperación Automática de Sesión
- Al ingresar, el participante registra su nombre o apodo.
- **Protección contra recargas accidentales (`localStorage`):** El progreso se guarda automáticamente en cada paso. Si el participante actualiza o cierra la página por error (por ejemplo, tras haber enviado la Fase 1), al volver a cargar aparece una pantalla que le permite elegir entre:
  - **▶️ Continuar por donde estaba:** Restaura su apodo, token de sesión, mecánicas seleccionadas, diagnóstico octagonal y avance exacto en los lotes de calibración.
  - **🆕 Empezar de cero:** Limpia la sesión local e inicia un diseño nuevo.

### 2. Fase 1: Selección de Mecánicas y Diagnóstico Octalysis
- **Catálogo de 24 mecánicas** (3 por cada uno de los 8 Core Drives).
- **Barajado por ciclos sin repetición:** El botón *"Barajar / ver otras 6 mecánicas"* recorre las 24 tarjetas sin repetir ninguna hasta completar el ciclo.
- **Presupuesto máximo:** Hasta **6 mecánicas** por propuesta.
- **Diagnóstico psicopedagógico interactivo:**
  - **Gráfico Radar Octagonal (`<canvas>`):** Posicionamiento geométrico exacto de los 8 Core Drives. Al hacer clic o tocar cualquier vértice, se abre un modal con la fundamentación teórica del impulsador.
  - **Ejes de equilibrio:**
    - *Sombrero Blanco* (`CD1 + CD2 + CD3`) vs. *Sombrero Negro* (`CD6 + CD7 + CD8`).
    - *Motivación Intrínseca / Lado Derecho* (`CD3 + CD5 + CD7`) vs. *Motivación Extrínseca / Lado Izquierdo* (`CD2 + CD4 + CD6`).
  - **Reporte cualitativo automatizado:** Retroalimentación sobre fortalezas, riesgos y balance motivacional.

### 3. Compuerta Pedagógica (Phase Gate)
- Tras generar el diagnóstico de la Fase 1, la Fase de Calibración permanece bloqueada mediante una palabra clave controlada por el docente.
- **Palabras clave válidas** (insensibles a mayúsculas, minúsculas y tildes): `octalysis`, `calibrar` u `8`.

### 4. Fase 2: Ejercicio de Calibración por Lotes (Metacognición)
- **Lote 1 (Base):** Compuesto por 6 mecánicas (hasta 4 elegidas por el participante en su diseño + tarjetas complementarias no elegidas).
- **Aprendizaje de dominio (Mastery Learning):** Al validar un lote, los aciertos quedan fijados en verde y el participante reintenta únicamente las tarjetas con error hasta alcanzar el **100% de dominio**.
- **Multilote progresivo (Lotes 1 a 4):** Al dominar un lote al 100%, se habilita opcionalmente un nuevo lote de 6 tarjetas inéditas hasta cubrir las **24 mecánicas** completas del simulador.
- **Métricas del participante:**
  - **Precisión al 1.er intento (%)**
  - **Dominio final / Mastery (%)**
  - **Perfil de persistencia y esfuerzo:** *🌟 Dominio directo* (1 intento sin errores), *💪 Persistencia reflexiva* (2 a 3 intentos por lote) o *🎲 Ensayo y error* (> 3 intentos por lote).

### 5. Panel de Diagnóstico Docente (En Vivo)
- Sincronización automática cada 5 segundos y desduplicación por participante (conservando siempre la actualización más reciente de cada estudiante).
- **5 tarjetas KPI en tiempo real:**
  1. Total de diseños registrados y cuántos han completado la calibración.
  2. Tendencia dominante de Sombrero (Blanco / Negro / Equilibrado).
  3. Balance motivacional (Intrínseca / Extrínseca / Equilibrada).
  4. Evolución grupal: **Precisión al 1.er intento → Dominio final** y distribución de perfiles de persistencia.
  5. **Mecánica más difícil:** Tarjeta con mayor número de estudiantes con error y conteo de fallos acumulados.
- **Visualizaciones colectivas:** Radar Octalysis promedio de la clase y **Cuadrante 2D de dispersión** (`Eje X: Extrínseco ↔ Intrínseco`, `Eje Y: Sombrero Negro ↔ Sombrero Blanco`).
- **Disparadores de debate automatizados:** Preguntas generadas dinámicamente según las tendencias y errores reales del grupo.

---

## 🔗 Parámetros URL Soportados

Puedes compartir enlaces directos personalizados para tus sesiones:

| Parámetro | Ejemplo | Comportamiento |
| :--- | :--- | :--- |
| `mode=student` | `index.html?mode=student` | Fija la vista de estudiante/diseñador y oculta la barra de cambio de vista. |
| `mode=teacher` | `index.html?mode=teacher` | Abre directamente el **Panel de diagnóstico docente** y oculta el selector de vistas. |
| `nick` / `name` | `index.html?mode=student&nick=ProfeAna` | Pre-carga el apodo del participante y salta el modal de bienvenida. |

---

## 🛠️ Arquitectura Técnica

- **Stack Zero-Build:** Todo el proyecto reside en [`index.html`](./index.html) utilizando HTML5 semántico, CSS3 moderno (Variables CSS, Grid, Flexbox, diseño responsivo móvil/tablet/escritorio) y Vanilla JavaScript.
- **Diseño Responsivo:** Adaptado para teléfonos móviles (`>= 320px`), tablets y pantallas de escritorio, con redibujado dinámico de gráficos `<canvas>` en alta definición (`devicePixelRatio`) al rotar o redimensionar la pantalla.
- **Backend Serverless (Google Apps Script + Google Sheets):**
  - Configurado mediante la constante `WEBHOOK_URL` en [`index.html`](./index.html).
  - Envía payloads JSON mediante `POST` (`mode: 'no-cors'`, `Content-Type: text/plain`) para máxima compatibilidad en navegadores móviles (iOS Safari / Android Chrome) y consulta el consolidado mediante `GET`.
  - Los metadatos enriquecidos de calibración (`[Errores-> ...]` y `[Calib-> ...]`) viajan codificados dentro de `mecanicas_elegidas`, garantizando retrocompatibilidad sin requerir columnas adicionales en la hoja de cálculo.

---

## 💻 Ejecución Local y Continuidad

1. Clona el repositorio:
   ```bash
   git clone https://github.com/chepechavez/octalysis-game.git
   cd octalysis-game
   ```
2. Abre `index.html` directamente en cualquier navegador moderno o sírvelo con una extensión como *Live Server*.
3. Para dar continuidad al desarrollo entre distintos equipos, consulta el archivo [`ESTADO_PROYECTO.md`](./ESTADO_PROYECTO.md).
