# Informe Integrado de Auditoría de Liberación — ReservaLab

**Curso:** Aseguramiento de la Calidad de Software — Unidad 3 · Clase 16  
**Universidad:** UIDE  
**Grupo:** Grupo 7 (`g7` · Equipo de 3 — Trío)  
**Fecha de auditoría:** 23/11/2026  
**Fuente normativa:** `Clase16_Fundamentos_Teoricos.pdf` (Nielsen, WCAG 2.2 AA, Google SRE, OWASP Top 10:2021/2025, ISO/IEC 25010)

---

## 1. Equipo Auditor y Comprobantes Individuales

| Integrante | Correo institucional | Rol asignado | Nota parcial (taller) | Código de comprobante |
| :--- | :--- | :--- | :--- | :--- |
| **Daniel Cadena** | `dacadenave@uide.edu.ec` | `eq3/ab` — Estaciones A + B (núcleo ★: Usabilidad y Accesibilidad) | **`9.50 / 9.5`** (`1.20 + 6.00 + 0.80 + 1.50`) | `1MUJ1BH` |
| **Gonzalo Cárdenas** | `gocardenasme@uide.edu.ec` | `eq3/c` — Estación C (completa: Ingeniero/a SRE · Confiabilidad) | **`9.50 / 9.5`** (`1.20 + 6.00 + 0.80 + 1.50`) | `FMXIP8M` |
| **Gabriel Vásquez** | `gavasquezse@uide.edu.ec` | `eq3/d` — Estación D (completa: Analista de seguridad) | **`7.95 / 9.5`** (`1.10 + 4.85 + 0.80 + 1.20`) | `17GA1YN` |

*(Los `0.5` puntos restantes corresponden a la evaluación docente de este informe y las capturas en `clase16/evidencias/`.)*

---

## 2. Veredicto de Liberación (Go / No-Go)

> **VEREDICTO FINAL:** **`No-Go`**  
> **Ancla normativa:** `§5.2 R9` (Regla de liberación) y `§5.1 T11` (Matriz de prioridad unificada).

### Justificación técnica
De acuerdo con **`§5.2 R9`** (*"No-Go: existe al menos un hallazgo P1 abierto"*), **ReservaLab NO puede salir a producción** porque existen **5 hallazgos P1 bloqueantes** abiertos, además de **3 hallazgos P2** que también requieren plan de remediación:

- **F1 (P1 · Bloqueante · Seguridad — #1 en el Top 5 por desempate `§5.1`):** Inyección SQL en `/labs` (`E4`), severidad **Crítica** (`A03:2021 Injection` / `A05:2025`, `CWE-89`, `T8–T10`, `T11`).
- **F2 (P1 · Bloqueante · Seguridad):** IDOR / BOLA en reservas (`E1`), severidad **Alta** (`A01:2021 Broken Access Control` / `A01:2025`, `CWE-639`, `§4.2`, `T8–T10`, `T11`).
- **F3 (P1 · Bloqueante · Accesibilidad Nivel A):** Botón `"Reservar"` implementado como `<div onclick>` no operable con teclado (`S3`), viola WCAG `2.1.1` Nivel A bloqueando el flujo principal e impidiendo cualquier conformidad WCAG (`§2.3 R2`, `§2.4 T4`, `T11`).
- **F4 (P1 · Bloqueante · Confiabilidad SRE):** Presupuesto de error mensual consumido al **`116.7 %`** (`252 min / 216 min`, estado **Rojo** — SLO `99.5 %` incumplido, `§3.3 T6`, `R5`, `T11`).
- **F5 (P1 · Bloqueante · Usabilidad):** Campo `Fecha` en texto libre interpretado mes/día sin aviso ni confirmación visible (`O2`), provoca reservas en fecha equivocada silenciosamente (**Severidad 4 — Catástrofe**, `H5`, `T1`, `T2`, `R1`, `T11`).

**Hallazgos P2 (Alta):**
- **F6 (P2 · Seguridad):** Cabeceras de seguridad ausentes (`E5`), severidad **Media** (`A05:2021` / `A02:2025`, `CWE-693`, `T9–T11`).
- **F7 (P2 · Accesibilidad):** Texto de ayuda con contraste `3.45:1` en `14 px` normal (`S2`), incumple `1.4.3` Nivel AA (`T4`, `T5`, `F1`, `T11`).
- **F8 (P2 · Confiabilidad SRE):** Intensidad de fallas proyectada `λ(12) = 2.98 defectos/semana > 1` al fin de la semana 12; incumple el criterio de liberación `§3.5 R6` (se cumple recién desde la semana `20`, `F9`, `R6`, `T11`).

**Hallazgos P3 (Media):**
- **F9 (P3 · Usabilidad):** Pantalla de inicio saturada de avisos (`O8`, `H8`, severidad `2`, `T1`, `T2`, `T11`).
- **F10 (P3 · Seguridad):** El servidor revela su versión de Apache, severidad **Baja** (`A05:2021`, `T9–T11`).

---

## 3. Hallazgos Detallados por Estación (`§5.3`)

### 3.1 Estación A · Usabilidad (Heurísticas de Nielsen — Daniel Cadena)

| ID | Obs. | Problema (Evidencia) | Heurística | Severidad | Prioridad | Recomendación concreta | Ancla |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `UX-01` | `O1` | Tras pulsar `Reservar` la pantalla no cambia durante ~6 s; 4/5 participantes pulsaron otra vez generando reservas duplicadas. | `H1` (sec. `H5`) | `3 · Mayor` | `P2` | Mostrar indicador de carga (`< 1 s`), deshabilitar `Reservar` durante el envío y usar clave de idempotencia en servidor. | `T1`, `T3`, `R1`, `§1.4` |
| `UX-02` | `O2` | Campo `Fecha` en texto libre interpreta `03/12/2026` como 12 de marzo sin mostrar la fecha en confirmación (error silencioso). | `H5` | `4 · Catástrofe` | **`P1`** | Reemplazar por selector de calendario nativo (`DD/MM/AAAA`) y mostrar fecha textual completa antes y después de confirmar. | `T1`, `T2`, `R1`, `T11` |
| `UX-03` | `O4` | Exige memorizar y digitar código de laboratorio (`LB-07`) desde un PDF externo; 2/5 escribieron un código equivocado. | `H6` | `3 · Mayor` | `P2` | Sustituir por lista desplegable con búsqueda mostrando nombre, edificio, capacidad y código visible. | `T1`, `T2`, `R1` |
| `UX-04` | `O6` | Botón principal cambia de color/lado entre pasos, icono 💾 cambia de significado y `Eliminar` tiene el mismo tamaño que `Reservar`. | `H4` | `2 · Menor` | `P3` | Unificar sistema de diseño (color, posición, iconografía consistente y menor jerarquía visual para acciones destructivas). | `T1`, `T2`, `R1`, `§1.4` |
| `UX-05` | `O8` | Inicio saturado con 6 avisos y 3 carruseles deja `Nueva reserva` bajo el pliegue (promedio 14 s para encontrarlo). | `H8` | `2 · Menor` | `P3` | Colapsar avisos secundarios y ubicar `Nueva reserva` sobre el pliegue con jerarquía visual destacada. | `T1`, `T2`, `§1.4`, `T11` |

- **Impacto negocio (`A9`):** `O2` afecta más la tasa de **conversión** (reservas correctas completadas); `O6` (y `O8`) afectan más la **retención** (`§1.4`).

---

### 3.2 Estación B · Accesibilidad WCAG 2.2 AA (Daniel Cadena)

#### Auditoría en vivo sobre W3C BAD (`before/home.html` vs `after/home.html`)
- **`B1.1` (`image-alt`):** Imágenes sin `alt` → SC `1.1.1` Nivel `A` (`T4`). Corrección: `alt` descriptivo en imágenes informativas y `alt=""` en decorativas.
- **`B1.2` (`color-contrast`):** Contraste insuficiente → SC `1.4.3` Nivel `AA` (`T4`, `T5`, `F1`). Corrección: alcanzar `≥ 4.5:1` en texto normal y `≥ 3:1` en texto grande.
- **`B1.3` (`html-has-lang`):** Falta atributo `lang` en `<html>` → SC `3.1.1` Nivel `A` (`T4`). Corrección: declarar `<html lang="en">` (o `"es"`).
- *(Adjuntar capturas de axe DevTools y Lighthouse `before`/`after` en `clase16/evidencias/`.)*

#### Revisión de fragmentos de código de ReservaLab (`S1–S10`)

| ID | Frag. | Evidencia en código | Criterio WCAG | Nivel | POUR | Prioridad | Corrección concreta | Ancla |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `A11Y-01` | `S1` | `<img src="mapa-labs.png">` sin `alt` | `1.1.1` | `A` | Perceptible | `P2` | Añadir `alt` descriptivo y resumen textual accesible de ubicaciones. | `T4` |
| `A11Y-02` | `S2` | `#8A8A8A` en `14 px` normal (`3.45:1 < 4.5:1`) | `1.4.3` | `AA` | Perceptible | `P2` | Oscurecer texto al menos a `#767676` (`4.54:1`); nota: `3.45:1` sí cumpliría en texto grande (`24 px` normal o `18.66 px` negrita, `T5`). | `T4`, `T5`, `F1` |
| `A11Y-03` | `S3` | `<div class="btn-primario" onclick="reservar()">Reservar</div>` | `2.1.1` | `A` | Operable | **`P1`** | Reemplazar por `<button type="button">` nativo operable por teclado y con foco visible. | `T4`, `R2`, `T11` |
| `A11Y-04` | `S6` | `<html>` sin atributo `lang` | `3.1.1` | `A` | Comprensible | `P2` | Declarar `<html lang="es">`. | `T4` |
| `A11Y-05` | `S8` | `.icono-accion { width:16px; height:16px; margin:2px; }` (`20 px < 24 px`) | `2.5.8` | `AA` | Operable | `P2` | Ampliar objetivo táctil/clic a al menos `24 × 24 px` CSS o espaciado equivalente. | `T4`, `§2.6` |
| `A11Y-06` | `S10` | `<span class="switch on" onclick="toggle(this)"></span>` | `4.1.2` | `A` | Robusto | `P2` | Usar `<button role="switch" aria-checked="true" aria-label="Recibir recordatorio">` o `<input type="checkbox">` con `<label>`. | `T4` |

- **Conformidad actual (`B12`):** **`Ninguno`** (un criterio A incumplido impide declarar cualquier nivel, `R2`). El fragmento que bloquea completar la reserva solo con teclado es **`S3`**.

---

### 3.3 Estación C · Confiabilidad SRE (Gonzalo Cárdenas)

#### Métricas del piloto de noviembre (`T = 30 días = 720 h = 43 200 min`, SLO = `99.5 %`)
- **Conteo de fallas (`C1`, `R4`):** `n = 6` fallas no planificadas (`INC-01`..`INC-06`), `D = 42 + 18 + 95 + 12 + 30 + 55 = 252 min = 4.2 h`, `U = 715.8 h`. `MNT-01` (`60 min`, anunciada con 4 días = `96 h ≥ 48 h`) **se excluye** (`R4`).
- **MTTF y MTTR (`C2`, `F2`, `F3`, `R4`):** `MTTF = 715.8 / 6 = 119.3 h`; `MTTR = 252 / 6 = 42 min`.
- **MTBF y Disponibilidad (`C3`, `F4`, `F5`, `R4`):** `MTBF = 119.3 + 0.7 = 120.0 h`; `A = 715.8 / 720 = 99.42 %`.
- **Presupuesto de error (`C4`, `T6`, `R5`):** Presupuesto mensual = `216 min` (`0.5 %` de 30 días); consumido = `252 / 216 × 100 = 116.7 %` → Estado **`Rojo` (SLO incumplido → `P1`, congelamiento de nuevas funcionalidades y postmortem obligatorio)**.
- **Modelo exponencial (`C5`, `F6`, `F7`):** `R(24 h) = e^(-24/119.3) = 81.78 %`; `R(168 h) = e^(-168/119.3) = 24.46 %`.
- **Acción prioritaria (`C6`, `§3.2`):** Bajar el `MTTR`: automatizar el rollback (`INC-03` sumó `95 min`) y alertar certificados TLS por vencer (`INC-02`).

#### Crecimiento de confiabilidad — Goel-Okumoto (`a = 120`, `b = 0.15/sem`, liberación planificada semana `12`)
- **Semana 10 (`C7`, `F8`, `F10`):** `m(10) = 93.22` defectos esperados; `Restantes(10) = 26.78`.
- **Criterio de liberación (`C8`, `F9`, `R6`):** `λ(12) = 120 × 0.15 × e^(-1.8) = 2.98 defectos/semana > 1` → **No cumple** (`P2`). Se cumple a partir de la **semana completa `20`** (`t* = ln(18)/0.15 = 19.27`).
- **Detección del 95 % (`C9`, `F10`):** `t = -ln(0.05)/0.15 = 19.97 ≈ 20 semanas`.

#### Recuperación ante desastres (`RPO ≤ 5 min`, `RTO ≤ 30 min`)
- **Estrategia recomendada (`C10`, `T7`, `R7`):** **`Espera en caliente (warm standby)`** (`RPO ≤ 1 min`, `RTO 10–30 min`, `$$$`). Se descarta `Luz piloto` (`$$`) porque su `RTO` típico (`1–4 h`) y `RPO` (`≤ 15 min`) superan los límites del negocio.
- **Deficiencias del respaldo actual (`C11`, `§3.6`):** Hay una sola copia, en un solo medio (mismo servidor), sin copia fuera del sitio (incumple regla `3-2-1`), con `RPO` real de hasta `24 h` y sin pruebas de restauración.

---

### 3.4 Estación D · Seguridad OWASP (Gabriel Vásquez — Integración)
- **`SEC-01` (`E4` / `F1` — `P1`):** Inyección SQL en `/labs`, severidad **Crítica** (`A03:2021` / `A05:2025`, `CWE-89`, `T8–T11`). Mitigación: consultas parametrizadas/ORM, principio de mínimo privilegio y ocultar errores SQL.
- **`SEC-02` (`E1` / `F2` — `P1`):** IDOR / BOLA en reservas, severidad **Alta** (`A01:2021` / `A01:2025`, `CWE-639`, `§4.2`, `T8–T11`). Mitigación: denegar por defecto, validar propiedad del recurso en servidor (`WHERE id = ? AND owner_id = usuario_actual`) y devolver `403/404`.
- **`SEC-03` (`E5` / `F6` — `P2`):** Cabeceras de seguridad ausentes (`CSP`, `X-Content-Type-Options`, etc.), severidad **Media** (`A05:2021` / `A02:2025`, `CWE-693`, `T9–T11`).
- **`SEC-04` (`F10` — `P3`):** Exposición de versión de servidor Apache, severidad **Baja** (`A05:2021`, `T9–T11`).

---

## 4. Cumplimiento Ético (`§4.6 R8`)
Los escaneos de seguridad en vivo autorizados se restringen exclusivamente a **OWASP Juice Shop** ejecutado localmente (`localhost:3000`), conforme a la regla `§4.6 R8`.
