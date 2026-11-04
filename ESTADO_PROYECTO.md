# 📋 Estado del Proyecto y Guía de Continuidad Entre Equipos

> **Última actualización:** Noviembre 2026  
> **Repositorio remoto:** `https://github.com/chepechavez/octalysis-game.git` (rama `main`)  
> **Archivo principal:** [`index.html`](./index.html)

---

## 💡 ¿Por qué existe este documento si ya usamos Git?

* **Lo que SÍ hace el repositorio Git automáticamente:** Sincroniza el código exacto y la lista cronológica de commits (`git log`) cuando haces `git push` en un equipo y `git pull` en el otro.
* **Lo que NO hace Git por sí solo:** No guarda el contexto estratégico, el *porqué* de las decisiones pedagógicas y técnicas, las restricciones del backend en Google Sheets, ni el estado global para que tú (o un asistente de IA en otra computadora) retomen el trabajo al instante sin tener que leer más de 3.600 líneas de código.
* **Cómo usarlo:** Al estar versionado dentro del repositorio, este archivo viaja automáticamente entre tus computadoras con cada `git pull`. **Cuando abras el proyecto en otro equipo, basta con decirle al asistente: *"Lee `ESTADO_PROYECTO.md` para ponerte al día"*.**

---

## 🟢 1. En qué va el proyecto (Estado Actual)

El simulador se encuentra **en producción y completamente funcional** para talleres con docentes y estudiantes.

### Módulos completados:
1. **Onboarding y Recuperación de Sesión (`localStorage`):**
   - Modal inicial de registro de apodo.
   - Guardado automático en `localStorage` (`octalysis_sim_session_v1`) en cada acción del usuario.
   - Si el participante recarga la página por accidente (ej. tras haber enviado la Fase 1 o en medio de la Fase 2), el modal detecta la sesión previa y ofrece dos opciones: **«▶️ Continuar por donde estaba»** o **«🆕 Empezar de cero»**, además de un botón `🔄 Reiniciar` junto al campo de apodo.
2. **Fase 1 — Catálogo y Diagnóstico Octalysis:**
   - Base de datos de **24 mecánicas** (3 por cada Core Drive, de `CD1` a `CD8`, 5 puntos cada una).
   - Barajado exploratorio en manos de 6 tarjetas por ciclos completos sin repetición (`unseenPool`).
   - Presupuesto máximo de **6 mecánicas seleccionadas**.
   - Gráfico **Radar Octagonal interactivo** en `<canvas>` con modales teóricos al hacer clic/toque en sus vértices.
   - Barras de equilibrio (**Sombrero Blanco vs. Negro** e **Intrínseca vs. Extrínseca**) y evaluación cualitativa automática.
3. **Compuerta de Transición de Fase (Phase Gate):**
   - Bloquea el acceso al ejercicio de calibración hasta que el docente indique la palabra clave en el taller.
   - Códigos aceptados (insensibles a mayúsculas/tildes): `octalysis`, `calibrar`, `8`.
4. **Fase 2 — Ejercicio de Calibración por Lotes (Metacognición):**
   - **Lote 1 estandarizado (6 tarjetas):** Incluye hasta 4 mecánicas elegidas por el alumno + tarjetas complementarias no elegidas.
   - **Reintento por lote con fijación de aciertos:** Al validar un lote, las respuestas correctas se bloquean en verde (`✓ ¡Correcto! (Fijada)`) y el participante corrige únicamente las erróneas hasta lograr el **100% de dominio**.
   - **Lotes progresivos sin repetición (Lotes 1 a 4):** Una vez dominado un lote al 100%, el alumno puede desbloquear lotes adicionales de 6 tarjetas nuevas hasta recorrer las 24 mecánicas.
   - **Métricas de aprendizaje:** *Precisión al 1.er intento*, *Dominio final (Mastery)* y *Perfil de persistencia* (*🌟 Dominio directo*, *💪 Persistencia reflexiva*, *🎲 Ensayo y error*).
5. **Panel de Diagnóstico Docente (En Vivo):**
   - Polling cada 5 segundos al Webhook de Google Apps Script.
   - Desduplicación automática por nombre de participante (conserva siempre el último envío).
   - 5 KPIs en vivo, Radar Octalysis grupal, Cuadrante 2D de dispersión, disparadores de debate automatizados (incluyendo foco en las mecánicas con más errores) y tabla detallada con hora local formateada.
6. **Diseño Responsivo (Móvil, Tablet y Escritorio):**
   - Grillas adaptables sin desborde horizontal en pantallas desde `320px`.
   - Prevención de recarga accidental por arrastre vertical en móviles (`overscroll-behavior-y: contain`).
   - Redibujado automático de los gráficos `<canvas>` al rotar el dispositivo o redimensionar la ventana (`resize`), con etiquetas laterales protegidas contra recortes y área táctil ampliada en los vértices.

---

## 🧠 2. Decisiones Técnicas y Pedagógicas Tomadas

### A. Arquitectura en un solo archivo (`index.html`)
- **Decisión:** Mantener HTML, CSS y JS integrados en [`index.html`](./index.html) sin frameworks ni bundlers (`npm`/`webpack`).
- **Motivo:** Garantizar portabilidad total, carga instantánea en GitHub Pages o archivo local, y cero fricción al cambiar de computadora (no requiere instalar `node_modules`).

### B. Fórmulas del Modelo Octalysis
- **Sombrero Blanco (Propósito, Logro, Creatividad):** `CD1 + CD2 + CD3`.
- **Sombrero Negro (Escasez, Impredecibilidad, Evitación):** `CD6 + CD7 + CD8`.
  *(Nota: `CD4` y `CD5` actúan como sombreros neutros en el eje vertical).*
- **Motivación Intrínseca (Cerebro Derecho):** `CD3 + CD5 + CD7`.
- **Motivación Extrínseca (Cerebro Izquierdo):** `CD2 + CD4 + CD6`.
- **Estado Equilibrado:** Se declara equilibrio individual cuando la diferencia entre polos no supera `15 puntos porcentuales` (`10 puntos` en el promedio grupal del dashboard docente).

### C. Geometría del Octágono y del Cuadrante 2D
- **Octágono (`drawOctagonRadar`):** Respeta la ubicación espacial canónica de Octalysis:
  - Arriba: `CD1` (`-90°`) | Abajo: `CD8` (`90°`)
  - Derecha (Intrínsecos): `CD3` (`-45°`), `CD5` (`0°`), `CD7` (`45°`)
  - Izquierda (Extrínsecos): `CD2` (`-135°`), `CD4` (`180°`), `CD6` (`135°`)
- **Cuadrante 2D (`drawQuadrantScatter`):**
  - `Eje X`: Extrínseco a la izquierda (`-X`) e Intrínseco a la derecha (`+X`), alineado con el octágono.
  - `Eje Y`: Sombrero Negro abajo (`-Y`) y Sombrero Blanco arriba (`+Y`).

### D. Integración con Google Sheets sin alterar columnas del Backend
- **URL activa del Webhook:** Definida en la constante `WEBHOOK_URL` al inicio del `<script>` en [`index.html`](./index.html).
- **Compatibilidad CORS móvil:** El envío `POST` usa `mode: 'no-cors'` y `headers: { 'Content-Type': 'text/plain' }` para evitar bloqueos preflight (`OPTIONS`) en Safari (iOS) y Chrome móvil.
- **Codificación embebida en `mecanicas_elegidas`:** Para no tener que modificar el script de Google Apps Script ni agregar columnas nuevas en la hoja de cálculo cada vez que evoluciona la Fase de Calibración, los metadatos viajan al final del arreglo `mecanicas_elegidas` con dos etiquetas estructuradas que el Dashboard Docente decodifica con [`parseSubmissionCalibration()`](./index.html):
  - `[Errores-> NombreMecánica:conteo | ...]`
  - `[Calib-> ini:X|lote1:X|dom:X|int:X|lotes:X|lotecmp:X|perfil:codigo|eval:id1.id2...|err1:id1.id2...]`

---

## 🔄 3. Protocolo para Trabajar Entre Dos Computadoras

### Al terminar de trabajar en el Equipo A:
```powershell
git status
git add .
git commit -m "descripción breve de los cambios"
git push origin main
```
*(Recuerda actualizar la sección 1 o 4 de este archivo `ESTADO_PROYECTO.md` si tomaste una decisión nueva importante).*

### Al empezar a trabajar en el Equipo B:
```powershell
git fetch origin
git status
git pull origin main
```
*(Si usas un asistente de IA en el Equipo B, pídele primero: **"Valida que el repositorio esté actualizado y lee `ESTADO_PROYECTO.md`"**).*

---

## 📌 4. Posibles Mejoras Futuras (Backlog Opcional)

- [ ] Botón en el Panel Docente para exportar la tabla de resultados de la sesión a CSV/Excel directamente desde el navegador.
- [ ] Filtro por fecha o sesión/grupo en el Panel Docente (para separar talleres de distintos días sin limpiar la hoja de Google Sheets).
- [ ] Modo de proyección en pantalla completa o código QR integrado en el Panel Docente para compartir rápidamente el enlace con los participantes en el aula.
