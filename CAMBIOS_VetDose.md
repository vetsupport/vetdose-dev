# VetDose — Registro de Cambios y Modificaciones Pendientes
# Actualizado: Junio 2026
# Formato: [ESTADO] = ✅ Implementado | ⏳ Pendiente | 🔄 En progreso

═══════════════════════════════════════════════════════════════
## VERSIONES IMPLEMENTADAS
═══════════════════════════════════════════════════════════════

### v7 — Auditoría de tablas de dosificación (Junio 2026)
✅ BUG CRÍTICO corregido: redondeo de conversión kg→lb causaba falsos
   "fuera de rango" en límites exactos de tabla (ej. 10 kg = 22.0462 lb
   caía fuera de un límite de 22.0). Ahora se redondea a 1 decimal antes
   de comparar contra los rangos. Afectaba calculate() y shareResults().
✅ Apoquel — CORREGIDO: tabla daba 16mg a perros de 20-89 lb que en
   realidad necesitaban 5.4mg (sobredosis ~3x). Reconstruida con la
   tabla oficial completa de Zoetis (9 filas, 6.6-175.9 lb).
✅ Zenrelia — CORREGIDO: tabletas inventadas de 4/8/16 mg reemplazadas
   por las reales (4.8/6.4/8.5/15 mg). Tabla completa reconstruida con
   15 filas según label oficial de Elanco (DailyMed NADA 141-585).
✅ Solensia — CORREGIDO: viales inventados de 1/2/3/4 mg reemplazados.
   El vial real es SIEMPRE 7 mg/mL. Simplificado a 2 filas reales:
   5.5-15.4 lb = 1 vial, ≥15.5 lb = 2 viales.
✅ Cerenia tabs — CORREGIDO: prácticamente todos los rangos de peso
   estaban mal alineados con las cantidades de tabletas. Reconstruida
   con breakpoints oficiales en kg convertidos a lb (6 filas).
✅ Librela — agregada categoría faltante <11 lb (dosis 0.1 mL/kg, no
   vial completo) y precisión de límites corregida (22.1 en vez de 22.0).
✅ Verificadas SIN cambios (ya correctas): Cytopoint, Simparica,
   Simparica Trio, Trifexis, Cardalis, Interceptor Plus.
Fuentes: DailyMed FDA, labels oficiales Zoetis/Elanco/Ceva (PDFs de
   dosing chart descargados y verificados línea por línea).
Archivo modificado: app.js únicamente (TABLE_DRUGS + calculate() +
   shareResults()). index.html, sw.js, manifest.json e ícones sin cambios.
NOTA: revisar manualmente los pesos límite de Apoquel y Zenrelia
   (cambios más profundos) contra el label físico antes de uso clínico.

---

### v1 — Versión inicial
✅ PWA instalable en iPhone
✅ 17 drogas seed pre-cargadas
✅ Calculadora de dosis (cc) por peso lb/kg
✅ Calculadora de diluciones
✅ Base de datos editable
✅ Exportar/Importar JSON

### v2 — Mejoras visuales
✅ Fondo claro con letras oscuras
✅ Letra más grande
✅ Logo VetDose personalizado en header
✅ Ingrediente activo (genérico) primero, nombre comercial segundo
✅ Botón imprimir en pantalla de resultados

### v3 — Base de datos expandida
✅ 144 drogas (de 17 a 144)
✅ Campo "fuente" (source) visible en resultado
✅ Badge ⚠ para drogas pendientes de validación
✅ 14 colores de badge por categoría
✅ Modo "dosis fija" para unidades especiales (mg/m², U/kg, gota/ojo, etc.)
✅ Modal expandido con campo Fuente y Estado de validación
✅ Orden alfabético por nombre genérico en toda la app

### v4 — Mejoras clínicas mayores
✅ Selector especie 🐕 Canino / 🐈 Felino — filtra lista automáticamente
✅ 10 drogas por tabla de peso (Apoquel, Cytopoint, Simparica, etc.)
✅ Barra de búsqueda por texto en pantalla Calcular
✅ 22 filtros de categoría deslizables horizontales
✅ Antagonistas automáticos (atipamezole, naloxona, flumazenil)
✅ Ajuste de dosis post-cálculo dentro del rango Plumb
✅ CRI (Constant Rate Infusion) — reemplaza calculadora de dilución
✅ Cálculo por m² (BSA) para oncología — fórmula estándar K×kg^(2/3)/10000
✅ Campos 🐕 dosis canina / 🐈 dosis felina en modal de edición
✅ Categorías expandidas a 50+ grupos

### v5 — Tablas de peso corregidas con datos oficiales
✅ Simparica — rango + color de caja (Amarilla/Morada/Naranja/Azul/Verde/Café)
✅ Simparica Trio — mg exactos corregidos + colores de caja verificados
✅ Cytopoint — recalculada con 2 mg/kg mínimo, combinación de viales correcta
✅ Librela — tabla fija por rango de peso (NO redondeo hacia arriba), 5 viales
✅ Solensia — tabla corregida por rangos exactos en gatos
✅ Cardalis — agregado rango 88–132 lb (3 comprimidos Large)
✅ Cerenia tabs — rangos simplificados y corregidos
✅ Zenrelia — presentaciones corregidas (4/8/16 mg)

═══════════════════════════════════════════════════════════════
## MODIFICACIONES PENDIENTES — v6
═══════════════════════════════════════════════════════════════

### MOD-001 — Redondeo inteligente de comprimidos
⏳ PENDIENTE
Descripción: El resultado de comprimidos debe redondearse a entero o medio.
             Nunca mostrar decimales como 1.3 comprimidos.
             Para drogas con presentaciones conocidas: usar los tamaños
             disponibles reales y ofrecer hasta 2 opciones prácticas.
             Para cápsulas: solo cantidad entera, sin partir.
Reglas:
  - Solo entero (1.0) o medio (0.5) — nunca cuartos ni otros decimales
  - Cuando hay 2 opciones prácticas, mostrar ambas
  - Ejemplo Levotiroxina 0.25 mg:
      Opción A: 1 comprimido 0.2 mg + ½ comprimido 0.1 mg
      Opción B: ½ comprimido 0.5 mg
  - Cápsulas: redondear al entero más cercano, sin opciones de partir
Presentaciones verificadas por droga:
  Levotiroxina     → 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 1.0 mg
  Fenobarbital     → 15, 30, 60, 100 mg
  Atenolol         → 25, 50, 100 mg
  Enalapril        → 2.5, 5, 10, 20 mg
  Benazepril       → 5, 10, 20 mg
  Amlodipino       → 2.5, 5, 10 mg
  Espironolactona  → 25, 50, 100 mg
  Diltiazem        → 30, 60, 90, 120 mg
  Digoxina         → 0.0625, 0.125, 0.25 mg
  Metronidazol     → 250, 500 mg
  Doxiciclina      → 50, 100 mg
  Fluconazol       → 50, 100, 150, 200 mg
  Fludrocortisona  → 0.1 mg (única presentación)
  Alprazolam       → 0.25, 0.5, 1.0, 2.0 mg
  Metimazol        → 2.5, 5, 10 mg
  Levetiracetam    → 250, 500, 750 mg
  Clopidogrel      → 75 mg (única presentación)
  Grapiprant       → 20, 60, 100 mg
  Deracoxib        → 12, 25, 75, 100 mg
  Cefpodoxime      → 100, 200 mg
  Marbofloxacino   → 25, 50, 100, 200 mg
  Enrofloxacino    → 22.7, 68, 136 mg
Archivos a modificar: app.js (función renderResult, recalcDose)

---

### MOD-002 — CHANGELOG visible dentro de la app
⏳ PENDIENTE
Descripción: Agregar una pantalla o sección "Acerca de / Versión" dentro
             de VetDose que muestre el número de versión actual y un
             resumen del historial de cambios.
Sugerencia: Botón en la pestaña Drogas → "VetDose v5 — Ver cambios"
            que abre un modal con el historial resumido.
Archivos a modificar: index.html (modal nuevo), app.js (datos de versión)

---

### MOD-003 — Sistema de validación de drogas
⏳ PENDIENTE
Descripción: En la pantalla de resultados, debajo de cada droga calculada,
             aparece un botón "✓ Validar dosis" SOLO si la droga tiene
             status pendiente de validación.
             Al tocarlo: status cambia a "Validado", se guarda en la base
             de datos, el badge ⚠ desaparece permanentemente.
             Una vez validada, no vuelve a preguntar nunca más.
UI:
  - Si pendiente: mostrar botón pequeño verde "✓ Confirmar dosis correcta"
    debajo de la tarjeta de resultado
  - Si ya validada: no mostrar nada (limpio, sin ruido visual)
  - Al tocar: animación rápida, botón cambia a "✓ Validado" por 1 segundo
    y luego desaparece
Archivos a modificar: app.js (renderResult + función validateDrug),
                      no requiere cambios en index.html

---

### MOD-004 — Dosis canina vs felina en cálculo
⏳ PENDIENTE
Descripción: Cuando se selecciona especie felina y la droga tiene doseCat
             definida, usar doseCat en vez de dosePref.
             Actualmente los campos doseDog/doseCat se guardan pero no
             se usan en el cálculo.
Ejemplo: Meloxicam — canino 0.2 mg/kg, felino 0.05 mg/kg
Archivos a modificar: app.js (función calculate)

---

### MOD-005 — Levetiracetam ER (Extended Release)
⏳ PENDIENTE
Descripción: Agregar presentación ER (Extended Release) de Levetiracetam.
             La presentación IR (immediate release) se da c/8h (3 veces/día).
             La presentación ER se da c/12h (2 veces/día).
Presentaciones a agregar:
  Levetiracetam IR  → 250, 500, 750 mg — c/8h
  Levetiracetam ER  → 500, 750 mg — c/12h
Implementación: Agregar como droga separada en la base de datos:
  id: levetiracetam_er
  generic: Levetiracetam ER
  trade: Keppra XR
  conc: 500 mg/tab
  notas: "Extended Release — administrar c/12h (BID). NO partir ni triturar.
          Misma dosis total diaria que IR. Disponible en 500 y 750 mg."
Archivos a modificar: app.js (SEED_DRUGS — agregar nueva entrada)

---

### MOD-006 — Agregar drogas faltantes
⏳ PENDIENTE
Descripción: Agregar medicamentos solicitados que no están en la base de datos.

Drogas a AGREGAR:
  1. Proin (Fenilpropanolamina)
     - Indicación: incontinencia urinaria por incompetencia del esfínter (perros)
     - Presentación: tabletas masticables 25, 50, 75, 100 mg
     - Dosis: 1–2 mg/kg PO c/8-12h (perros). No usar en gatos.
     - Categoría: urinary
     - Notas: No usar en hipertensión, cardiopatía o hipertiroidismo.
              Monitorear presión arterial.

  2. Entyce (Capromorelin para perros)
     - Indicación: estimulante del apetito — PERROS
     - Presentación: solución oral 30 mg/mL
     - Dosis: 3 mg/kg PO SID
     - formType: liquid_oral
     - Categoría: GI
     - Notas: Solo perros ≥ 9 meses. Con o sin comida. Puede causar
              hipersalivación, vómito o diarrea transitoria.

  3. Elura (Capromorelin para gatos)
     - Indicación: estimulante del apetito — GATOS
     - Presentación: solución oral 20 mg/mL
     - Dosis: 2 mg/kg PO SID
     - formType: liquid_oral
     - Categoría: GI
     - Notas: Solo gatos. Concentración DIFERENTE a Entyce (20 vs 30 mg/mL).
              No intercambiar entre especies.

  4. Mirtazapina oral
     - Indicación: estimulante del apetito, antiemético
     - Presentación: tabletas 7.5, 15, 30 mg
     - Dosis perros: 1.88–3.75 mg/perro c/24h (dosis fija por tamaño)
     - Dosis gatos: 1.88 mg/gato PO c/48-72h
     - Categoría: GI/behavior
     - Notas: Gatos muy sensibles — síndrome serotoninérgico posible.
              Usar dosis bajas. Ambas especies.

  5. Mirataz (Mirtazapina tópica felina)
     - Indicación: estimulante del apetito en gatos
     - Presentación: ungüento 2% (1.88 mg por aplicación de 1.5 cm)
     - Dosis: 1.88 mg (1 aplicación) en pabellón auricular interno c/24h
     - formType: topical / dosis fija
     - calcMode: fixed
     - Categoría: GI
     - Notas: Solo gatos. Rotar oreja cada aplicación. Usar guantes.
              Lavar manos después. Alternativa cuando PO no es posible.

Archivos a modificar: app.js (SEED_DRUGS — agregar 5 nuevas entradas)

### MOD-007 — Rediseño visual — interfaz minimalista y elegante
⏳ PENDIENTE
Descripción: Rediseñar la interfaz manteniendo funcionalidad completa
             pero con estética más refinada y profesional.

Directrices de diseño:
  - Menos bordes visibles — usar sombras suaves en lugar de borders gruesos
  - Tipografía más refinada — considerar Inter o Plus Jakarta Sans
  - Espaciado más generoso entre elementos
  - Colores más sutiles — verde menos saturado, más sofisticado
  - Cards con sombra ligera en lugar de borde coloreado
  - Botones más delgados con menos peso visual
  - Header más limpio — solo logo y pill de paciente
  - Filtros de categoría con estilo pill más fino
  - Resultados: jerarquía visual más clara, dosis destacada con más elegancia
  - Badges de categoría más pequeños y discretos
  - Animaciones suaves en transiciones (200ms ease)
  - Modo: fondo blanco puro o gris muy claro (#f8f9fa)
  - Acento principal: verde oscuro sobrio (#1a5c38 o similar)
  - Sin exceso de iconos emoji en la UI principal

Referencia de estilo: apps médicas profesionales como Epocrates,
                      MDCalc, o interfaces de Apple Health.

Archivos a modificar: index.html (CSS completo — refactorizar variables
                      y estilos), posiblemente nueva fuente en Google Fonts

---

═══════════════════════════════════════════════════════════════

💡 Historial de pacientes guardados con peso actualizable
💡 Protocolos pre-cargados reutilizables (ej: "Pre-anestésico perro sano")
💡 Sincronización entre dispositivos vía iCloud
💡 Modo oscuro
💡 Separar dosis canina vs felina en más drogas de la base de datos
💡 Agregar más productos por tabla de peso (NexGard, Bravecto, HeartGard, etc.)
💡 Historial de cálculos por paciente
💡 Compartir base de datos entre colegas (link de exportación)

═══════════════════════════════════════════════════════════════
## PENDIENTES ADICIONALES — para v7
═══════════════════════════════════════════════════════════════

### MOD-009 — Presentaciones correctas Clavamox y Clindamicina
⏳ PENDIENTE

Clavamox (Amoxicilina-Clavulanato):
  Tabletas: 62.5, 125, 250, 375 mg
  Suspensión oral: 62.5 mg/mL
  Agregar tabSizes: [62.5, 125, 250, 375]
  Agregar entrada separada para suspensión (liquid_oral, 62.5 mg/mL)

Clindamicina:
  Suspensión oral: 25 mg/mL (liquid_oral)
  Cápsulas: 25, 75, 150 mg (capsule)
  Actualmente está como formType genérico — separar en:
    1. Clindamicina cápsulas: capsule, tabSizes [25, 75, 150]
    2. Clindamicina suspensión: liquid_oral, conc 25 mg/mL

Archivos a modificar: app.js (SEED_DRUGS — corregir entradas existentes
                      y agregar presentaciones faltantes)
⏳ PENDIENTE
Tabla correcta verificada (viales: 10, 20, 30, 40 mg, rangos de 10 lb en 10 lb):
  < 10 lb      → 10 mg  (1 vial 10 mg)
  10–<20 lb    → 20 mg  (1 vial 20 mg)
  20–<30 lb    → 30 mg  (1 vial 30 mg)
  30–<40 lb    → 40 mg  (1 vial 40 mg)
  40–<50 lb    → 50 mg  (1 vial 40 mg + 1 vial 10 mg)
  50–<60 lb    → 60 mg  (1 vial 40 mg + 1 vial 20 mg)
  60–<70 lb    → 70 mg  (1 vial 40 mg + 1 vial 30 mg)
  70–<80 lb    → 80 mg  (2 viales 40 mg)
  80–<90 lb    → 90 mg  (2 viales 40 mg + 1 vial 10 mg)
  90–<100 lb   → 100 mg (2 viales 40 mg + 1 vial 20 mg)
  100–<110 lb  → 110 mg (2 viales 40 mg + 1 vial 30 mg)
  ≥ 110 lb     → 120 mg (3 viales 40 mg)
  Sin límite superior definido por el fabricante.
Archivos a modificar: app.js (TABLE_DRUGS — cytopoint_table)


### MOD-010 — Resultado de recalcular en línea con el botón
⏳ PENDIENTE
Descripción: Al ajustar la dosis y tocar "Recalcular", el resultado aparece
             debajo del botón. Debe aparecer a la DERECHA en la misma fila.
Layout deseado:
  [ 0.15 mg/kg ]  [ Recalcular ]  [ 2.34 cc ]  ← todo en un renglón
  El resultado con fondo verde claro, fuente monospace, mismo alto
  que el campo de entrada.
Archivos a modificar: app.js (renderResult — sección adjustHTML)

---


### MOD-011 — Mejoras de presentación en pantalla de resultados
⏳ PENDIENTE

A) Rango de referencia en la misma línea que la dosis usada:
   Actual:  Dosis usada: 0.1 mg/kg
            Rango ref.:  0.1–0.15 mg/kg
   Nuevo:   Dosis usada: 0.1 mg/kg  (rango: 0.1–0.15 mg/kg)
   → Eliminar el renglón "Rango ref." separado, integrarlo entre paréntesis

B) Color del cuadro de observaciones/notas:
   Actual: fondo amarillo/ámbar — desentona con el verde
   Nuevo:  fondo azul muy claro (#f0f6ff) con borde azul suave (#b8d4f0)
           y texto azul oscuro (#1a3a5c) — más elegante y neutro

C) Comprimidos: solo entero (1.0) o medio (0.5), nunca 0.25 ni 0.33
   Ya documentado en MOD-001 — reforzar esta regla en la implementación

D) Traducir todas las notas e indicaciones al español
   Revisar SEED_DRUGS y traducir campos notes que estén en inglés

E) Frecuencia de dosificación en antibióticos (SID/BID/TID):
   Mostrar en la misma línea del rango de referencia
   Ejemplo: 0.1–0.15 mg/kg BID
   Agregar campo 'frequency' a cada droga antibiótico

F) Traducir "Compliance" y cualquier otro término en inglés
   que aparezca en la interfaz — revisar toda la UI

Archivos a modificar: app.js (renderResult), index.html (colores CSS)

---

### MOD-012 — Correcciones y actualizaciones de drogas existentes
⏳ PENDIENTE

CORREGIR presentaciones y dosis:

1. Adequan (Polisulfato de glicosaminoglicano)
   Dosis: 2 mg/lb (4.4 mg/kg) IM
   Protocolo: 2x/semana x 4 semanas, luego mensual

2. Acepromazina inyectable
   Presentaciones: 1% (10 mg/mL) y 0.2% (2 mg/mL)
   Agregar ambas concentraciones — afecta el cálculo de cc

3. Acepromazina tabletas
   Presentaciones: 10, 12.5, 25 mg
   tabSizes: [10, 12.5, 25]

4. Alprazolam
   tabSizes: [0.25, 0.5, 1.0, 2.0] — ya correcto, confirmar

5. Aluminum Hydroxide
   Dosis: 60–90 mg/kg/día dividido en 2 comidas (BID)
   Notas: Dar con las comidas como quelante de fósforo

6. Carbonato de Lantano
   Dosis: 60–90 mg/kg/día dividido en 2 comidas
   Agregar si no existe. Quelante de fósforo. Categoría: renal/phosphate binder

7. Calcio Carbonato
   Dosis: 90–150 mg/kg/día dividido en 2 comidas
   Quelante de fósforo. Categoría: renal/phosphate binder

8. Amantadina
   Presentación: jarabe 50 mg/5 mL = 10 mg/mL (liquid_oral)
   Corregir en SEED_DRUGS: formType → liquid_oral, conc → 10, NO cápsula
   Dosis: 3–5 mg/kg PO SID
   Notas: Antagonista NMDA. Adyuvante para dolor crónico.
   Puede usarse junto a gabapentina y AINEs.

9. Amlodipino
   Perros: 0.1–0.5 mg/kg PO BID. Iniciar a 0.1 mg/kg BID,
           titular semanalmente monitoreando presión arterial.
   Gatos: 0.625 mg/gato c/24h. Si >6 kg: 1.25 mg/gato c/24h
   tabSizes: [2.5, 5, 10]
   doseDog: 0.1 mg/kg BID | doseCat: dosis fija por peso

10. Amoxicilina-Clavulanato (Clavamox)
    Tabletas: 62.5, 125, 250, 375 mg
    Suspensión: 62.5 mg/mL
    tabSizes: [62.5, 125, 250, 375]

11. Ampicilina-Sulbactam
    Presentación: vial 1.5 g (ampicilina 1g + sulbactam 0.5g)
    Diluir en 50 cc NaCl 0.9% → concentración 30 mg/mL
    Dosis: 11–22 mg/kg IV BID, dosePref: 20 mg/kg

12. Atenolol
    Perros: 0.2–1 mg/kg PO c/12h
    Gatos: 1–2.5 mg/kg PO c/12h; o 6.25–12.5 mg/gato c/12h
    doseDog: 0.5 mg/kg | doseCat: dosis fija 6.25–12.5 mg/gato

13. Atropina
    Presentaciones: 0.54 mg/mL y 0.4 mg/mL (2 entradas o selector)

14. Ciclosporina oftálmica
    Notas: Iniciar TID, reducir a BID para mantenimiento

15. Ciclosporina oral
    Presentaciones cápsulas: 10, 25, 50, 100 mg
    Notas: Cápsulas congeladas producen menos problemas GI

16. Clonidina
    tabSizes: [0.1, 0.2, 0.3]

17. Clopidogrel
    Perros: loading dose 10 mg/kg PO, luego 2–3 mg/kg SID
    Gatos: 18.75 mg/gato (¼ comprimido de 75 mg) SID
    Agregar dosis de carga como nota

18. Darbepoetina Alfa
    Dosis: 0.5–0.8 mcg/kg SQ
    Presentaciones: 25 o 40 mcg/mL. No mezclar ni diluir.
    No usar si está turbia. Refrigerar, proteger de la luz.
    Gatos: iniciar 1 mcg/gato SQ semanal hasta alcanzar meta,
    luego c/2–3 semanas.

19. Deracoxib
    Dosis: 1–2 mg/kg SID (ya existe, verificar)

20. Desoxicorticosterona Pivalato (DOCP)
    Frecuencia: c/25 días o ajustar según electrolitos

21. Dexametasona SP
    Anafilaxis: 0.5 mg/kg IV una vez
    Antiinflamatorio: 0.1–0.2 mg/kg SID
    Inmunosupresor: 0.2–0.5 mg/kg SID
    Crisis addisoniana: 0.1–0.5 mg/kg IV, mantenimiento <0.1 mg/kg PO PRN
    Nota importante: DexSP NO interfiere con prueba de Addison

Archivos a modificar: app.js (SEED_DRUGS — múltiples entradas)

---

### MOD-013 — Agregar drogas nuevas
⏳ PENDIENTE

1. Amitriptilina
   Dosis: 1–4 mg/kg PO c/12–24h
   Presentación: tabletas (varias concentraciones)
   Categoría: behavior
   Notas: Extremadamente amarga — difícil de administrar si se parte
          el comprimido. No partir.

2. Clomipramina (Clomicalm)
   Dosis: 1–2 mg/kg PO c/12h x 2 semanas, luego 3 mg/kg c/24h
          Puede llegar a 4 mg/kg para trastornos obsesivo-compulsivos
   Presentaciones Clomicalm: 5, 20, 80 mg (sabor a carne)
   Categoría: behavior
   Notas: Clomicalm con sabor a carne — buena aceptación en perros y gatos.
          Dosis más altas necesarias para TOC vs ansiedad.
          Aprobado para uso veterinario.

3. Atropina oftálmica
   Presentación: solución oftálmica 1%
   Uso: midriasis, uveítis
   Categoría: ophthalmic
   calcMode: fixed / dosis en gotas

4. Azitromicina
   Presentaciones: suspensión 200 mg/5 mL (40 mg/mL), cápsulas 50 mg
   (verificar si ya existe y actualizar)

5. Carprofen inyectable
   Presentación: 50 mg/mL
   Dosis: 2.2 mg/kg SQ o IV una vez antes de cirugía
   Categoría: nsaid

6. Carprofen tabletas
   Presentaciones: 25, 75, 100 mg
   tabSizes: [25, 75, 100]
   Puede ser SID o BID según indicación

7. Cefalexina
   Presentación: cápsulas 250 mg
   Frecuencia: BID o TID

8. Cefazolina
   Profilaxis quirúrgica: 20 mg/kg IV al inicio de cirugía y c/6h
   Sepsis: c/4–8h IV

ELIMINAR:
- Amoxicilina sola (sin clavulanato) — retirar de la base de datos

Archivos a modificar: app.js (SEED_DRUGS)

---


### MOD-014 — Correcciones y nuevas drogas (lote mayo 2026)
⏳ PENDIENTE

═══ CORRECCIONES DE DROGAS EXISTENTES ═══════════════════════════════

1. Dexametasona SP → conc: 4 mg/mL (no 2 mg/mL)
   Dexametasona normal → conc: 2 mg/mL
   Verificar que son 2 entradas separadas en la base de datos

2. Dexmedetomidina
   Perros: 5–10 mcg/kg IM o IV lento
   Gatos: 7 mcg/kg IM o 3.5 mcg/kg IV
   Nota: Revertir con atipamezole después de inducción de emesis

3. Dextrosa 5% en agua
   NO usar SQ. Uso: hipernatremia, fuente de carbohidratos junto a
   solución electrolítica poliiónica.
   Puede causar: hiponatremia, hipocloremia, hipopotasemia, hipomagnesemia

4. Dextrosa 50%
   Crisis hipoglucémica: bolus 1–2 mL/kg diluido 1:4 en NaCl,
   seguido de infusión 2 mL/kg/h IV, ajustar según glucosa sanguínea

5. Diazepam
   Premedicación: 0.1–0.3 mg/kg IV
   Convulsiones/Status epiléctico: 0.5–1 mg/kg vía rectal o 0.5 mg/kg intranasal
   GATOS: Evitar — puede causar falla hepática idiosincrática

6. Difenidramina (Benadryl)
   Perros: 1–2 mg/kg IV, SQ, IM; 2–4 mg/kg PO BID o TID
   Gatos: 2–4 mg/kg PO BID o TID

7. Digoxina — agregar dosis por especie más detallada:
   FA: 0.0025–0.005 mg/kg PO BID. Perros >20 kg: 0.22 mg/m² BID
   Nunca exceder 0.25 mg/perro en 12h
   Gatos <3 kg: 0.25 tab de 0.125 mg PO c/48h
   Gatos 3–6 kg: 0.25 tab de 0.125 mg PO c/24h
   Gatos >6 kg: 0.25 tab de 0.125 mg PO c/12–24h

8. Diltiazem
   Perros: 0.5–2.5 mg/kg PO c/8h (IR); 1–4 mg/kg PO c/12h (ER)
   Gatos: 7.5 mg/gato PO c/8h (IR); 30–60 mg/gato PO c/12–24h (ER)
   Agregar entrada separada para Diltiazem ER

9. Dolasetron
   Dosis: 0.5–1 mg/kg
   Presentación: 20 mg/mL inj; tabs 50, 100 mg (corregir)

10. Ondansetron
    Dosis: 0.5–1 mg/kg (corregir doseMin a 0.5)
    Presentación: 2 mg/mL inj; tabs 4, 8 mg

11. Doxiciclina
    Dosis: 5 mg/kg BID o 10 mg/kg SID
    Gatos: administrar con agua/comida para prevenir esofagitis/estenosis

12. Dramamine (Dimenhidrinato)
    Gatos: 12.5 mg/gato para mareo por movimiento. Menos efectivo que maropitant.
    Presentaciones: 50 mg/mL inj, tab 50 mg

13. Enalapril
    Perros: BID. Gatos: SID (agregar nota de frecuencia)

14. Enrofloxacino
    Presentaciones: comprimidos 22.7, 68, 136 mg o solución inyectable 2.27%
    Gatos: riesgo de CEGUERA con dosis >5 mg/kg (reforzar nota)

15. Epinefrina
    PCR perros y gatos: 0.01 mg/kg IV; hasta 0.1 mg/kg tras PCR prolongado
    Anafilaxis: 0.01 mg/kg IM perros; 20 mcg/kg IV gatos

16. Espironolactona
    Dosis: 1–2 mg/kg PO BID o 2 mg/kg PO SID
    Presentaciones: tabs 25, 50 mg

17. Famciclovir
    Presentaciones: tabs 125 y 250 mg; BID o TID

18. Famotidina
    Presentaciones: tabs 10 y 20 mg; inyectable 10 mg/mL

19. Fenobarbital
    Presentaciones completas: 12.5, 15, 16.2, 30, 32.4, 50, 60, 64.8, 97.2, 100 mg

20. Fentanilo
    Perros y gatos: 0.01–0.04 mg/kg IV, SQ, IM
    CRI: 0.003–0.006 mg/kg/h
    Presentación: 50 mcg/mL. Vida media corta (30–120 min). Entorno quirúrgico.

21. Fluconazol
    Frecuencia: SID o BID. Presentaciones: tabs 50, 100 mg

22. Fludrocortisona
    Presentaciones: comprimido 0.1 mg; solución oral 0.1 mg/mL

23. Furosemida inyectable: 50 mg/mL
    Furosemida tabletas: 12.5 y 50 mg (agregar tabSizes)

24. Gabapentina
    Cápsulas: 100, 300, 600 mg; líquida 50 mg/5 mL (10 mg/mL)
    NOTA: Gabapentina líquida debe mantenerse refrigerada

25. Gentamicina
    Perros: 9–14 mg/kg IV, IM, SQ SID
    Gatos: 5–8 mg/kg IV, IM, SQ SID
    Nefrotóxico y ototóxico (reforzar nota)

26. Gluconato de calcio
    Gatos bloqueados: 3 mL/gato en 5 min
    Perros: 0.5–1.5 mL/kg de solución 10% (100 mg/mL)
    Ambos: IV MUY LENTO, monitorear ECG para prevenir paro cardíaco

27. GS-441524
    Dosis: 15 mg/kg SID (forma seca/húmeda)
    FIP neurológico/ocular: 20 mg/kg SID o 10 mg/kg BID
    Duración: protocolo actual 42 días (antes 84 días)

28. Hidromorfona
    Perros: 0.05–0.2 mg/kg IV, IM, SQ c/2–4h; CRI: 0.01–0.05 mg/kg/h
    Gatos: 0.05–0.1 mg/kg IV, IM, SQ c/2–6h; CRI: 0.01–0.05 mg/kg/h

29. Hidrocodona bitartrato
    Dosis: 0.2–0.5 mg/kg PO BID o TID. Raro en gatos.
    Presentación: jarabe 1 mg/mL
    CI: MAO inhibidores, hipotiroidismo, falla renal, geriátricos/débiles

30. Insulinas — corregir/completar todas las entradas:
    Lente (Vetsulin): perros y gatos 0.25–0.5 U/kg BID. U-40.
    ProZinc: gatos 1–2 U/gato BID; perros 0.25–0.5 U/kg BID o 0.5–1 U/kg SID. U-40.
    Glargina: perros 0.3 U/kg BID o SID; gatos 0.25 U/kg SID o BID. 100 o 300 U.
    NPH (Humulin/Novolin): perros 0.25–0.5 U/kg BID
    Detemir (Levemir): 0.1–0.2 U/kg BID perros grandes (dosis baja)

31. Itraconazol
    Perros y gatos: 5–10 mg/kg SID o 5 mg/kg BID

32. Ivermectina
    Sarna sarcóptica: 0.2–0.4 mg/kg PO o SQ c/semana x 4 semanas
    Heartworm preventivo: 0.006 mg/kg PO mensual
    CONTRAINDICADO en razas MDR1+ (Collies, Shelties, etc.)

33. Ketamina
    Dolor: 0.5 mg/kg SQ
    Sedación: 5–10 mg/kg. Combinar con benzodiacepinas o alfa-2.
    Cautela en cardiopatía e hipertensión.

34. Lactato de Ringer — dosis de mantenimiento:
    Gatos: 2–3 mL/kg/h
    Perros: 2–6 mL/kg/h

35. Lactulosа — encefalopatía hepática:
    Perros: 0.25–0.5 mL/kg PO c/6–8h hasta heces blandas

36. Hetastarch — shock hipovolémico:
    Bolos: 2–5 mL/kg c/10–20 min hasta respuesta. Máx 20 mL/kg/día.

37. Latanoprost
    Nota: precaución en glaucoma secundario a uveítis o luxación del cristalino

═══ DROGAS NUEVAS A AGREGAR ═════════════════════════════════════════

1. Pantoprazol
   Perros: 1 mg/kg IV
   Presentación: 4 mg/mL (vial 40 mg diluido en 10 cc NaCl → 4 mg/mL)
   Administrar IV lentamente
   Categoría: GI/PPI

2. Esomeprazol
   Perros: 0.5–1 mg/kg BID
   Nota: dosis >1 mg/kg puede causar vómitos y/o diarrea
   Categoría: GI/PPI

3. Bario (sulfato de bario) — para estudios de tránsito intestinal
   Perros <20 kg: 8–12 mL/kg; >20 kg: 5–7 mL/kg
   Gatos: 12–20 mL/kg
   Radiografías a los 5, 15, 30 min, 1h y 2h
   Categoría: imaging/diagnostic
   calcMode: fixed

4. Dorzolamida/Timolol (Cosopt)
   Uso: glaucoma
   PRECAUCIÓN: No usar Timolol en pacientes cardíacos (bradicardia)
   No usar en gatos asmáticos (crisis broncoespástica)
   Categoría: ophthalmic
   calcMode: fixed (gotas)

5. Diltiazem ER (Extended Release)
   Gatos: 30–60 mg/gato PO c/12–24h
   Perros: 1–4 mg/kg PO c/12h
   NO partir ni triturar comprimidos ER

Archivos a modificar: app.js (SEED_DRUGS — múltiples correcciones)

---


### MOD-015 — Rediseño arquitectura de tabletas/cápsulas (CRÍTICO)
⏳ PENDIENTE — Alta prioridad

Descripción: El campo "concentración del vial" no tiene sentido para tabletas
             y cápsulas. Se necesita un cambio de arquitectura completo en
             cómo la app maneja medicamentos sólidos.

PROBLEMA ACTUAL:
  - Carprofen: conc: 25 (usa el primer tamaño de pastilla como "conc")
  - Calcula: (2.2 mg/kg × 35.8 kg) / 25 = 3.15 comprimidos de 25 mg ← INCORRECTO
  - Resultado correcto: 1 comprimido de 75 mg BID

CAMBIOS REQUERIDOS:

A) Renombrar campo en el modal:
   - Para inyectables: "Concentración del vial (mg/mL)" ← igual que ahora
   - Para tabletas/cápsulas/líquido oral: "Presentaciones disponibles"
     con descripción: "Ej: 25, 75, 100 mg"
   - El campo 'conc' pasa a ser el PRIMER tamaño de tableta para cálculo base
   - El array 'tabSizes' contiene TODOS los tamaños disponibles

B) Nueva lógica de cálculo para tabletas:
   1. Calcular mg totales necesarios = dosePref × weightKg
   2. De los tabSizes disponibles, encontrar la opción MÁS PRÁCTICA:
      - Preferir 1 comprimido entero o 1 comprimido + ½
      - Nunca más de 2 comprimidos del mismo tamaño (a menos que no haya opción)
      - Nunca recomendar 3 o más pastillas pequeñas cuando hay una grande
      - Ejemplo: 78.7 mg → 75 mg (1 pastilla) es mejor que 25 mg × 3
   3. Mostrar hasta 2 opciones prácticas ordenadas por conveniencia

C) Agregar campo 'frequency' a cada droga:
   - Valores: 'SID','BID','TID','QID','q8h','q12h','q24h','q48h',
              'semanal','mensual','segun_indicacion'
   - Para drogas que varían (carprofen SID o BID): guardar ambas opciones
     y permitir seleccionar en el modal
   - Mostrar en el resultado: "1 comprimido 75 mg — BID (c/12h)"

D) Mostrar en resultados:
   Antes: "1 comprimido(s) 75 mg"
   Después: "1 comprimido 75 mg BID (c/12h)"
            "Total diario: 150 mg"

E) En el modal editar/agregar droga:
   - Campo: "Presentaciones disponibles (mg separados por coma)"
     → guarda en tabSizes automáticamente
   - Campo: "Frecuencia recomendada"
   - El campo 'conc' (para inyectables) cambia de nombre visualmente

Ejemplos CRÍTICOS que deben funcionar correctamente:
  Gabapentina 35 kg × 10 mg/kg = 350 mg
    INCORRECTO (actual): 3.5 cápsulas de 100 mg
    CORRECTO (v8):       1 cápsula 300 mg
                         Opción B: ½ cápsula 600 mg (si se puede abrir)

  Levetiracetam 35 kg × 20 mg/kg = 700 mg
    INCORRECTO (actual): 3 comprimidos de 250 mg
    CORRECTO (v8):       1 comprimido 750 mg

  Carprofen 35.8 kg × 2.2 mg/kg = 78.7 mg BID
    INCORRECTO (actual): 3.15 comprimidos de 25 mg
    CORRECTO (v8):       1 comprimido 75 mg BID

  Regla: SIEMPRE minimizar número de unidades.
         Preferir 1 unidad entera sobre múltiples pequeñas.
         El campo "conc" pasa a llamarse "Formulación" en tabletas/cápsulas.

Archivos a modificar:
  app.js — función smartTabletOptions (reescribir lógica de selección),
           función renderResult (agregar frecuencia),
           SEED_DRUGS (agregar campo frequency a todas las drogas)
  index.html — modal editar droga (renombrar campos, agregar frequency)

---


### MOD-016 — Mejorar presentación de drogas por tabla de peso
⏳ PENDIENTE

Descripción: Las drogas que se calculan por tabla de fabricante (Librela,
             Cytopoint, Solensia, etc.) deben mostrar el resultado de forma
             idéntica a las otras drogas — nombre comercial prominente y
             la recomendación en letra grande.

ANTES (actual):
  Header: "Bedinvetmab (tabla)"   |  "44.1–66 lb (20–30 kg) → 1 mL vial 15 mg SQ"
  (nombre genérico, resultado en texto pequeño a la derecha)

DESPUÉS (v8):
  Header: "Librela"               |  1 vial 15 mg SQ
          "Bedinvetmab"                (resultado en grande, mismo estilo que cc)
  Body: Vía: SQ | Fuente: tabla fabricante | Nota: ...

Regla general para todas las drogas de tabla:
  - result-generic = Nombre comercial (Librela, Cytopoint, Simparica, etc.)
  - result-trade = Nombre genérico
  - result-dose = La recomendación en GRANDE (mismo tamaño que el resto)
  - Eliminar el prefijo de rango de peso en el resultado
    (ej: quitar "44.1–66 lb →" y mostrar solo "1 mL vial 15 mg")

Archivos a modificar: app.js (función renderTableResult)

---


### MOD-017 — Presentaciones confirmadas para 6 drogas pendientes
⏳ PENDIENTE — Incluir en v8

1. Cardalis
   Presentaciones: Benazepril/Espironolactona 2.5/20 mg, 5/40 mg, 10/80 mg
   (Usar como tabla de peso igual que Cardalis_table ya existente)

2. Hidrocodona Bitartrato
   Forma: Jarabe (syrup) — 5 mg Hidrocodona + 1.5 mg Homatropina / 5 mL
   conc: 1 mg/mL (hidrocodona), formType: liquid_oral
   Nota: No es tableta — corregir formType en SEED_DRUGS

3. Lomustine (CCNU)
   Presentaciones: 5, 10, 40, 100 mg cápsulas
   tabSizes: [5, 10, 40, 100]
   Nota: No partir cápsulas (oncológico)

4. Melatonina
   Presentaciones: 1, 3, 5, 10 mg tabletas ✓
   Agregar en notas: "Verificar que el producto NO contiene Xylitol (tóxico para perros)"

5. Metocarbamol
   Presentaciones: 500, 750 mg tabletas ✓
   tabSizes: [500, 750]

6. Milbemicina Oxima — Interceptor Plus (Milbemicina/Praziquantel)
   Es un producto por tabla de peso — agregar a TABLE_DRUGS:
   2–8 lb      → 1 comprimido 2.3 mg / 22.8 mg
   8.1–25 lb   → 1 comprimido 5.75 mg / 57 mg
   25.1–50 lb  → 1 comprimido 11.5 mg / 114 mg
   50.1–100 lb → 1 comprimido 23 mg / 228 mg
   Nota: Mensual. Heartworm + tenias. Solo perros.

Archivos a modificar: app.js (SEED_DRUGS + TABLE_DRUGS)

---


### MOD-018 — Hoja de indicaciones para el cliente (compartir/imprimir)
⏳ PENDIENTE

Descripción: Cuando el veterinario toca "Compartir resultado" o "Imprimir",
             el formato debe ser una hoja de instrucciones clara y profesional
             que el cliente pueda leer y seguir en casa.

DISEÑO DE LA HOJA:
┌─────────────────────────────────────────────────────┐
│  🐾  INDICACIONES MÉDICAS — Toby                    │
│      Labrador · 32.5 lb (14.7 kg)                   │
│      Dr. [nombre clínica] · Fecha: 18/05/2026       │
├─────────────────────────────────────────────────────┤
│                                                     │
│  💊  CARPROFEN (Rimadyl)                            │
│      1 comprimido de 75 mg                          │
│      Cada 12 horas (BID) — con comida               │
│      ⚠ No dar si vomita o deja de comer            │
│                                                     │
│  💊  GABAPENTINA (Neurontin)                        │
│      1 cápsula de 300 mg                            │
│      Cada 8 horas (TID)                             │
│                                                     │
│  💉  MAROPITANT (Cerenia)                           │
│      1.47 cc subcutáneo                             │
│      Una vez al día (SID)                           │
│                                                     │
├─────────────────────────────────────────────────────┤
│  📞 Regrese o llame si nota: vómitos, letargia,    │
│     falta de apetito o cualquier cambio inusual.    │
└─────────────────────────────────────────────────────┘

ESPECIFICACIONES:
- Fuente grande (mínimo 14pt para el nombre del medicamento)
- Nombre comercial en negrita, genérico en gris debajo
- Dosis y frecuencia en texto grande y claro
- Ícono de forma: 💊 tableta/cápsula, 💉 inyectable, 🧪 líquido oral
- Notas clínicas importantes en amarillo/naranja (advertencias)
- Pie de página: nombre de la clínica y teléfono (configurable)
- Al imprimir: formato de página A4 o carta, márgenes amplios
- Para compartir por texto (WhatsApp/iMessage): formato limpio con
  separadores claros, sin emojis de difícil lectura

NUEVA FUNCIÓN: "Imprimir hoja de indicaciones"
  - Botón separado del actual "Imprimir"
  - Abre una vista de impresión optimizada para cliente
  - Permite agregar nombre del veterinario y clínica
  - Opción de agregar nota personalizada al final

CONFIGURACIÓN (nueva pantalla en ajustes):
  - Nombre de la clínica
  - Teléfono
  - Mensaje de pie de página personalizable

Archivos a modificar:
  app.js — nueva función printClientSheet(), shareFormatted()
  index.html — nueva vista de impresión CSS @media print mejorada,
               botón "Hoja de indicaciones para cliente"

---


### MOD-019 — Panel de dosis en tiempo real con selector presentación y frecuencia
⏳ PENDIENTE

Descripción: Al seleccionar una tableta/cápsula, aparece panel inline
             con toda la información y selectores interactivos.
             Todo se recalcula en tiempo real.

PANEL COMPLETO (ejemplo Carprofen, perro 22.7 kg):

  ┌────────────────────────────────────────────────────┐
  │ 🐕 22.7 kg                                         │
  │ Dosis Carprofen:  2.2–4.4 mg/kg                    │
  │ Rango paciente:   49.9–99.9 mg                     │
  │                                                    │
  │ Presentación:  ○ 25mg   ● 75mg ✓   ○ 100mg        │
  │ Frecuencia:    ○ SID    ● BID ✓    ○ TID           │
  │                                                    │
  │ Resultado:  1 tab. 75mg — BID                      │
  │ Dosis real: 75mg/dosis · 150mg/día ✓ en rango     │
  └────────────────────────────────────────────────────┘

  Si cambia a SID + 100mg:
  │ Resultado:  ½ tab. 100mg — SID                     │
  │ Dosis real: 50mg/dosis · 50mg/día ✓ en rango      │

INFORMACIÓN MOSTRADA:
  - Peso del paciente (tomado del campo ya ingresado)
  - Dosis de referencia en mg/kg (doseMin–doseMax)
  - Rango total en mg para ESE paciente (doseMin×kg – doseMax×kg)
  - Selector de presentación (radio buttons, solo si tabSizes ≥ 2)
  - Selector de frecuencia (solo si la droga tiene >1 frecuencia posible)
  - Resultado actualizado en tiempo real
  - Dosis real que recibirá (mg/dosis y mg/día)
  - Indicador verde ✓ si está en rango, naranja ⚠ si está fuera

LÓGICA "IDEAL" (pre-selección automática):
  Presentación: la que da resultado más cercano a 1 unidad entera
                dentro del rango de dosis
  Frecuencia:   la indicada en el campo 'frequency' de la droga
                (ej: Carprofen default = BID, pero puede ser SID)

CAMPO NUEVO EN CADA DROGA: 'frequencies'
  Ejemplos:
    Carprofen:    frequencies: ['SID','BID'], frequency: 'BID'
    Meloxicam:    frequencies: ['SID'],       frequency: 'SID'
    Fenobarbital: frequencies: ['BID','TID'], frequency: 'BID'
    Metronidazol: frequencies: ['BID','TID'], frequency: 'BID'
    Gabapentina:  frequencies: ['BID','TID'], frequency: 'TID'

  Si frequencies tiene solo 1 opción → no mostrar selector

SOLO APLICA CUANDO:
  - Droga es tablet o capsule con tabSizes ≥ 2 opciones
  - Paciente tiene peso ingresado (si no hay peso: mostrar panel
    vacío con mensaje "Ingresa el peso para ver opciones")

Estado guardado en:
  state.selectedPresentation[drugId] = mg del tab elegido
  state.selectedFrequency[drugId]    = 'SID'|'BID'|'TID' elegido

Archivos a modificar:
  app.js — toggleDrug(), renderDrugList(), calculate(),
           SEED_DRUGS (agregar campo frequencies a tabletas),
           state (agregar selectedPresentation, selectedFrequency)

---


### MOD-020 — Azitromicina: agregar presentaciones correctas
⏳ PENDIENTE

Corregir entrada existente de Azitromicina:
  Suspensión oral: 200 mg/5 mL = 40 mg/mL  (liquid_oral)
  Cápsulas: 50 mg  (capsule, tabSizes: [50])
  Agregar como 2 entradas separadas:
    1. Azitromicina suspensión — liquid_oral, conc: 40
    2. Azitromicina cápsulas   — capsule, conc: 50, tabSizes: [50]

Archivos a modificar: app.js (SEED_DRUGS)

---

### MOD-021 — Rediseño hoja de impresión para enfermería
⏳ PENDIENTE

Descripción: La hoja impresa debe ser una orden de medicamentos clara,
             compacta y funcional para que la enfermera administre
             los medicamentos directamente.

DISEÑO DE LA HOJA IMPRESA:
╔══════════════════════════════════════════════╗
║  ORDEN DE MEDICAMENTOS                       ║
║  Clínica: [nombre clínica]                   ║
╠══════════════════════════════════════════════╣
║  TOBY                    🐕 MACHO            ║
║  32.5 lb  (14.7 kg)      18/05/2026          ║
╠══════════════════════════════════════════════╣
║  CARPROFEN (Rimadyl)                         ║
║  1 tab. 75 mg — BID (cada 12h) con comida    ║
╠──────────────────────────────────────────────╣
║  MAROPITANT (Cerenia)                        ║
║  1.47 cc — SQ — una vez al día               ║
╠──────────────────────────────────────────────╣
║  ONDANSETRON (Zofran)                        ║
║  0.74 cc — IV LENTO (15-20 min) — c/8h      ║
╠══════════════════════════════════════════════╣
║  Veterinario: Dr. Villasmil-Ontiveros        ║
╚══════════════════════════════════════════════╝

ESPECIFICACIONES:
- Nombre del paciente: fuente MUY GRANDE (24-28pt), negrita
- Especie + sexo: con ícono 🐕/🐈 y M/H
- Peso en lb Y kg: visible claramente
- Fecha de emisión
- Por cada medicamento (formato compacto, 2 líneas máximo):
    Línea 1: NOMBRE GENÉRICO (Nombre comercial)
    Línea 2: cantidad + forma + vía + frecuencia + nota clave
- Solo advertencias críticas (IV lento, con comida, etc.)
- Sin fuente, sin rango Plumb, sin badge de validación
- Nombre del veterinario al pie
- Nombre de la clínica (configurable en ajustes)
- Formato: papel carta o A4, márgenes amplios, letra mínimo 12pt

NUEVA PANTALLA DE CONFIGURACIÓN (pestaña Ajustes):
  - Nombre de la clínica
  - Nombre del veterinario
  - Teléfono de contacto
  - Estas preferencias se guardan en localStorage

Archivos a modificar:
  app.js — nueva función printNurseSheet(), loadSettings(), saveSettings()
  index.html — nueva pestaña Ajustes, CSS @media print mejorado,
               botón "Imprimir orden" separado del botón "Imprimir" actual

---


### MOD-022 — Módulo de Fluidos (nueva pestaña independiente)
⏳ PENDIENTE

Descripción: Nueva pestaña "Fluidos" independiente del CRI. Calcula
             tasa de fluidos, déficit de deshidratación, bolo de
             reanimación, y proyección de volumen total. La app tendrá
             4 pestañas: [Calcular] [CRI] [Fluidos] [Drogas]

Respuestas del usuario:
  - CRI y Fluidos son módulos SEPARADOS (ambos se mantienen)
  - Fluidos incluir TODOS: LR, NaCl 0.9%, D5W, NaCl 0.45%, Normosol,
    PlasmaLyte, Hetastarch, Plasma, NaCl 7.2% hipertónico
  - Tiempo de reposición: la app pregunta al usuario (rápido 4-6h vs lento 12-24h)

CAMPOS DE ENTRADA:
  1. Peso (auto desde pantalla Calcular si ya ingresado)
  2. Especie: Perro / Gato
  3. Cardiopatía: Sí / No  (ajusta tasas máximas automáticamente)
  4. Tipo de fluido (selector con descripción de cada uno)
  5. % Deshidratación con descripción clínica:
       <5%    No detectable, pérdidas leves
       5-6%   Leve: MM ligeramente secas
       7-8%   Moderado: MM secas, pliegue lento, ojos hundidos
       10-12% Severo: MM muy secas, taquicardia, pliegue persiste
       >12%   Crítico: shock, extremidades frías
  6. Tiempo reposición déficit: Rápido (4-6h) / Lento (12-24h)
  7. Horas de hospitalización planificadas

RESULTADOS CALCULADOS:
  - Déficit en mL (% deshidratación × peso kg × 10)
  - Fase 1 — Reposición: tasa en mL/h + volumen total
  - Fase 2 — Mantenimiento: tasa en mL/h + volumen total
  - Volumen total en el período de hospitalización
  - Si bolo de emergencia necesario: mL y tiempo de administración

AJUSTES AUTOMÁTICOS:
  Cardiopatía:
    Tasa máxima: 2 mL/kg/h (perros), 1-2 mL/kg/h (gatos)
    Reposición: siempre lenta (24h), sin bolos rápidos
    Advertencia roja prominente

  Gatos: mantenimiento 2-3 mL/kg/h (vs 3-5 perros)
  NaCl 7.2%: solo bolo emergencia 3-5 mL/kg, NO mantenimiento
  Hetastarch: máx 20 mL/kg/día perros, 10 mL/kg/día gatos

TASAS DE REFERENCIA:
  Mantenimiento perros: 3-5 mL/kg/h
  Mantenimiento gatos:  2-3 mL/kg/h
  Bolo reanimación:     10-20 mL/kg en 15-20 min (perros)
                        10 mL/kg en 15 min (gatos)
  Shock severo (total): hasta 90 mL/kg/h perros (en alícuotas)
  Cardiópatas:          máx 2 mL/kg/h, sin bolos

OPCIONALES (si el usuario quiere):
  - Pérdidas por fiebre: +1 mL/kg/h por cada °C > 39.5
  - Pérdidas activas: vómito, diarrea, drenaje (campo libre en mL/h)

Archivos a modificar:
  index.html — 4 pestañas: Calcular, CRI, Fluidos, Drogas
  app.js — función calcFluidos(), datos de cada fluido

---


### MOD-023 — Rediseño del módulo CRI (basado en referencia clínica)
⏳ PENDIENTE

Descripción: Mejorar el módulo CRI actual basándose en la app de
             referencia (screenshots IMG_1740/1741), pero integrado
             con el estilo VetDose y usando las drogas de nuestra
             base de datos.

LO QUE TIENE LA APP DE REFERENCIA (útil):
  - Peso en lb Y kg simultáneamente (toggle o dual display)
  - Volumen del fluido portador (ej: 250 mL)
  - mL/kg/h deseado → calcula mL/h + gotas/min + duración
  - Selector de equipo: 10 ggt/mL, 15 ggt/mL, 20 ggt/mL, 60 ggt/mL
  - 4 sub-modos: Multiple Drug, Single Drug, Insulin, Quick CRI
  - Dosis de carga (loading) + dosis CRI separadas
  - "Agregar X mL a 250 mL de fluido" — instrucción clara de preparación

LO QUE VAMOS A MEJORAR EN VETDOSE:
  - Usar las drogas de nuestra base de datos (no lista fija)
  - Estilo visual VetDose (verde oscuro, elegante)
  - Integrado con el peso del paciente activo
  - Instrucción completa de preparación en español

ESTRUCTURA NUEVA DEL MÓDULO CRI:

  ┌─── DATOS BASE ──────────────────────────────────────┐
  │ Peso: [25 lb] / [11.34 kg]  Especie: 🐕 🐈         │
  │ Volumen del fluido portador: [250 mL]               │
  │ Equipo de goteo: ○10 ggt/mL ●15 ggt/mL ○20 ○60    │
  └─────────────────────────────────────────────────────┘

  ┌─── SUB-MODOS ───────────────────────────────────────┐
  │  [Droga única]  [Múltiple]  [Insulina]  [Rápido]   │
  └─────────────────────────────────────────────────────┘

  SUB-MODO: DROGA ÚNICA
    - Selector de droga (desde nuestra base de datos, filtrado
      por las que tienen CRI en su frequency)
    - Dosis de carga (loading): campo + resultado en mL → IV directo
    - Dosis CRI: mcg/kg/min o mg/kg/h → mL a agregar al fluido
    - Resultado: "Agregar X mL de [droga] a 250 mL LR"
    - Tasa de la bomba: X mL/h
    - Gotas/min según equipo seleccionado
    - Duración estimada del fluido

  SUB-MODO: MÚLTIPLE (hasta 3 drogas en mismo fluido)
    - Ej: Fentanilo + Lidocaína + Ketamina (MLK)
    - Calcula mL de cada droga a agregar
    - Verifica que el volumen total no exceda el fluido

  SUB-MODO: INSULINA
    - Protocolo estándar: Regular insulin CRI
    - Perros: 0.025-0.05 U/kg/h
    - Gatos: 0.025-0.05 U/kg/h
    - Ajuste según glucosa sanguínea
    - Concentración: 0.1 U/mL (1 U en 10 mL NaCl) o
                     1 U/mL (50 U en 50 mL NaCl)

  SUB-MODO: RÁPIDO (Quick CRI)
    - Lista de protocolos pre-definidos:
      MLK (Morfina+Lidocaína+Ketamina)
      FLK (Fentanilo+Lidocaína+Ketamina)
      Dopamina shock
      Dobutamina ICC
      Lidocaína antiarrítmico
    - Un toque → dosis estándar calculadas automáticamente

GOTAS/MIN:
  mL/h ÷ 60 × factor goteo = gotas/min
  Factor: 10 ggt/mL, 15 ggt/mL, 20 ggt/mL, 60 ggt/mL (microgotas)

RESULTADO COMPLETO (ejemplo Fentanilo 11.34 kg):
  ┌─────────────────────────────────────────────────────┐
  │ FENTANILO CRI — 11.34 kg                           │
  │ Loading: 0.01 mg/kg IV → 0.23 mL (directo)        │
  │                                                     │
  │ CRI: 0.003 mg/kg/h                                 │
  │ Agregar 1.7 mL de Fentanilo (50 mcg/mL)           │
  │ a 250 mL de LR                                     │
  │                                                     │
  │ Bomba: 11.3 mL/h                                   │
  │ Goteo: 2.8 gotas/min (equipo 15 ggt/mL)           │
  │ Duración: 22.1 horas                               │
  └─────────────────────────────────────────────────────┘

Drogas CRI más comunes a pre-configurar:
  Fentanilo, Lidocaína, Ketamina, Morfina, Hidromorfona,
  Dexmedetomidina, Dopamina, Dobutamina, Epinefrina,
  Norepinefrina, Metoclopramida, Maropitant IV,
  Insulina regular, Furosemida

Archivos a modificar:
  app.js — refactorizar updateCRI(), agregar sub-modos,
           agregar datos de goteo y protocolos rápidos
  index.html — rediseño pestaña CRI con sub-modos

---


### MOD-024 — Sincronización Excel maestro ↔ app.js + flujo Papich
⏳ PENDIENTE

ARCHIVOS DE REFERENCIA DISPONIBLES:
  Excel: VetDose_Drogas_Expandida.xlsx (140 drogas, formato maestro)
  PDF:   Papich Handbook of Veterinary Drugs, 5th Edition (1060 páginas)
  Hoja "Bibliografía" en Excel con fuentes verificadas por droga

ESTADO ACTUAL:
  App.js:  122 drogas
  Excel:   140 drogas
  54 drogas en Excel que NO están en app → agregar
  38 drogas en app que NO están en Excel → verificar si son correctas o duplicados

DROGAS EN EXCEL PENDIENTES DE AGREGAR A APP (54):
  acido_tranexamico, adequan, amoxicilina (sola), calcium_gluconato,
  cardalis, darbepoetina, desoxicorticosterona, dextros_50,
  difenidramina, dimenhidrinato (Dramamine), euthaphen,
  insulina_glargina, lactulos (duplicado lactulosa?), maropitant_gabap,
  melatonin, nacl_0_45_dex_2_5, naloxona, norepinefrina, numelvi (Atinvicitinib),
  oclacitinib (Apoquel), omeprazol, ondansetron, pantoprazole,
  pimobendan, plasma_fresco_congelado, pradofloxacino, praziquantel,
  prednisolona, prednisona, proin, propofol, prozinc, rivaroxaban,
  robenacoxib, ronidazole, selamectina, sevoflurano, sucralfato,
  tacrolimus_topico, telmisartan, temaril_p, tiletamina_zolazepam,
  tramadol, trazodone, trimetoprim_sulfa, ursodiol, vasopresina,
  vetsulin, vitamina_b12_cianocobalamina, vitamina_k1, warfarina,
  zenalpha, zenrelia, zonisamida

FLUJO DE TRABAJO ACORDADO:
  1. El Excel es la FUENTE DE VERDAD para drogas nuevas
  2. Para agregar nuevas drogas: buscar en Papich PDF → extraer datos →
     agregar al Excel con formato correcto → convertir a JSON → importar a VetDose
  3. Claude puede buscar en Papich, extraer dosis/presentaciones/notas
     y agregar al Excel directamente
  4. Lotes de 20-30 drogas a la vez para mantener control

COLUMNAS DEL EXCEL (formato a respetar):
  id | generic | trade | conc | doseMin | doseMax | dosePref | unit |
  route | category | formType | notes

CATEGORÍAS VÁLIDAS EN EXCEL:
  antiemetic | analgesic | sedation | fluid | emergency | antibiotic |
  cardiac | nsaid | gastrointestinal | urinary | dermatologic |
  ophthalmic | oncology | respiratory | euthanasia | other

FORMTYPES VÁLIDOS:
  injection | tablet | capsule | liquid_oral | topical | ophthalmic | other

NOTAS CORTAS (estilo Papich — máx 12 palabras):
  "No gatos >5 mg/kg" | "IV lento 15 min" | "Ajustar en renal"
  "No combinar NSAIDs" | "Retinotoxicidad felina" | "No Collies MDR1+"

PRÓXIMOS PASOS CUANDO SE IMPLEMENTE:
  1. Subir Excel actualizado + Papich PDF
  2. Claude busca cada droga en Papich
  3. Completa campos faltantes (tabSizes, frequencies, doseDog, doseCat)
  4. Genera app.js actualizado con todas las drogas sincronizadas

Archivos a modificar: app.js (SEED_DRUGS — agregar 54 drogas del Excel)

---


### MOD-025 — Lista de drogas solicitadas por el usuario
⏳ PENDIENTE

VERIFICACIÓN vs app actual:
  ✅ Ya existe — Pantoprazol (pantoprazol)
  ✅ Ya existe — Finasteride (finasteride)
  ✅ Ya existe — Lomustine/CCNU (lomustine)
  ✅ Ya existe — Amlodipino (amlodipino)

DROGAS NUEVAS A AGREGAR (buscar en Papich):
  1. Trazodone (Desyrel/genérico)
     Categoría: behavior/sedation
     Uso: ansiedad, fobias, pre-medicación. Perros y gatos.
     Pendiente: buscar dosis exacta en Papich

  2. Ondansetron (Zofran)
     Categoría: antiemetic
     Uso: náuseas, vómito. Perros y gatos.
     Presentaciones: 2 mg/mL inj, tabs 4, 8 mg
     Pendiente: confirmar dosis canina vs felina en Papich

  3. Fenofibrate (Tricor/genérico)
     Categoría: endocrine (hipertrigliceridemia)
     Uso: hiperlipidemia canina (Schnauzer, hipotiroidismo)
     Pendiente: dosis veterinaria en Papich — uso extra-label

  4. Pimobendan (Vetmedin)
     Categoría: cardiac
     Uso: ICC por DCM y MVD. Perros.
     Presentaciones: 1.25, 2.5, 5 mg tab masticables
     Pendiente: confirmar dosis exacta en Papich

  5. Telmisartan (Semintra)
     Categoría: cardiac/renal
     Uso: HTA y proteinuria en gatos. Aprobado para gatos.
     Presentación: solución oral 4 mg/mL
     Pendiente: confirmar dosis en Papich

  6. Robenacoxib (Onsior)
     Categoría: nsaid
     Uso: dolor/inflamación perioperatoria. Perros y gatos.
     Presentaciones: 6, 20 mg tab; 20 mg/mL inj
     Pendiente: confirmar dosis canina vs felina en Papich

  7. Bonqat (Pregabalina para gatos)

  8. Molidustat (Varenzin-CA1)
     Categoría: renal/hematologic
     Especie: SOLO GATOS
     Uso: anemia no regenerativa asociada a ERC (CKD)
     Mecanismo: inhibidor HIF-PH — estimula producción endógena de EPO
     Presentación: suspensión oral (liquid_oral)
     Dosis: 5 mg/kg PO SID (estudios clínicos confirmados)
     Protocolo: administrar directamente en boca con jeringa provista,
                NO mezclar con comida. Ciclos con pausa mínima 7 días
                entre ciclos según respuesta. Monitorear HCT semanal.
     Aprobación: FDA aprobación condicional 2024 (Elanco)
     Fuente: JVIM 2024; FDA label Varenzin-CA1
     Nota: NO indicado en perros. Agitar bien antes de cada dosis.
     Categoría: analgesic/behavior
     Uso: ansiedad en gatos (aprobado por FDA 2023 para gatos)
     Presentación: solución oral 75 mg/mL
     Nota: solo aprobado para gatos. Pregabalina = análogo gabapentina.
     Pendiente: buscar dosis en Papich o FDA label

  9. Panoquell-CA1 (Fuzapladib sódico)
     Categoría: GI (pancreatitis)
     Especie: SOLO PERROS
     Uso: manejo de signos clínicos de pancreatitis aguda canina
     Mecanismo: inmunomodulador — primer fármaco aprobado para pancreatitis canina
     Presentación: inyectable IV — polvo liofilizado reconstituido a 4 mg/mL
     Dosis: 0.4 mg/kg IV SID x 3 días consecutivos
     conc: 4 mg/mL
     Protocolo: solo hospitalización, IV directo, usar junto a cuidado de soporte
                (fluidos, antieméticos, analgesia, restricción dietaria)
     Aprobación: FDA aprobación condicional noviembre 2022 (Ceva/ISK Animal Health)
     CI: hipersensibilidad a fuzapladib, cardiopatía, falla hepática/renal,
         gestantes/lactantes/reproductores, cachorros <6 meses
     Fuente: FDA label NADA 141-567; AVMA 2022; Ceva product info

Archivos a modificar: app.js (SEED_DRUGS — agregar 9 nuevas entradas)
Fuente: Papich 5th Ed. + etiquetas FDA

---


### MOD-026 — Módulo de Nutrición y Energía
⏳ PENDIENTE

Descripción: Nueva pestaña o sección para calcular requerimiento calórico
             diario (RER y MER) en perros y gatos, y convertir a cantidad
             de alimento.

APP TENDRÁ 5 PESTAÑAS:
  [Calcular] [CRI] [Fluidos] [Nutrición] [Drogas]

FÓRMULAS:
  RER (Resting Energy Requirement):
    - Fórmula lineal (2-45 kg):  RER = (30 × peso_kg) + 70
    - Fórmula precisa (todos):   RER = 70 × (peso_kg ^ 0.75)
    - Mostrar ambas, usar la precisa por defecto

  MER (Metabolic Energy Requirement):
    MER = RER × factor

FACTORES PERROS:
  Adulto castrado:       1.6
  Adulto intacto:        1.8
  Pérdida de peso:       1.0
  Ganancia de peso:      1.2 – 1.8 (slider)
  Cachorro < 4 meses:    3.0
  Cachorro > 4 meses:    2.0
  Gestación (últimas 3 semanas): 3.0
  Lactancia:             4.0–5.0

FACTORES GATOS:
  Adulto castrado:       1.2
  Adulto intacto/activo: 1.4 – 1.6
  Pérdida de peso:       0.8 – 1.0
  Crecimiento/cachorro:  2.0 – 2.5

CAMPOS DE ENTRADA:
  1. Peso actual (kg/lb — auto desde pantalla Calcular)
  2. Especie: Perro / Gato
  3. Estado fisiológico (selector con los factores)
  4. ¿Paciente obeso? Sí/No
     → Si Sí: campo "Peso ideal (kg)" → usar peso ideal para cálculo
  5. kcal del alimento (por lata / por taza / por 100g)
     → Campo libre que el veterinario llena según el alimento específico
  6. Tipo de alimento: Lata / Croquetas / Mixto

RESULTADOS:
  ┌─────────────────────────────────────────────┐
  │ 🐕 Toby — 10 kg — Adulto castrado          │
  │                                             │
  │ RER:  370 kcal/día                         │
  │       (fórmula precisa: 374 kcal/día)      │
  │                                             │
  │ Factor MER: × 1.6 (adulto castrado)        │
  │ MER:  592 kcal/día                         │
  │                                             │
  │ Alimento: 328 kcal/lata                    │
  │ → 1.8 latas/día                            │
  │   (aprox. 1¾ lata)                         │
  │                                             │
  │ ℹ Punto de partida clínico.                │
  │   Ajustar según BCS y respuesta.           │
  └─────────────────────────────────────────────┘

NOTAS CLÍNICAS AUTOMÁTICAS:
  - Si obeso: "Usando peso ideal. No usar peso actual en obesidad."
  - Si cachorro <4 meses: "Requerimiento muy alto — dividir en 3-4 comidas/día"
  - Si pérdida de peso: "Factor restrictivo. Monitorear BCS mensualmente."
  - Siempre: "Resultado es punto de partida. Ajustar según BCS, masa
              muscular y respuesta clínica individual."

CONVERSIÓN PRÁCTICA:
  Mostrar resultado en:
  - Latas (si kcal/lata ingresado)
  - Tazas (si kcal/taza ingresado)
  - Gramos (si kcal/100g ingresado)
  Redondear a ¼ de unidad más cercana para practicidad

Archivos a modificar:
  index.html — 5ª pestaña Nutrición
  app.js — función calcNutricion(), factores MER

---


### MOD-027 — Drogas nuevas solicitadas
⏳ PENDIENTE

VERIFICACIÓN:
  ✅ Panoquell (fuzapladib) — ya existe
  ✅ Entyce (capromorelin perros) — ya existe
  ✅ Elura (capromorelin gatos) — ya existe
  ✅ Atipamezole — ya existe
  ❌ Naloxona — FALTA (es un reversor — categoría emergency/reversal)
  ❌ Flumazenil — FALTA (es un reversor — categoría emergency/reversal)

DROGAS A AGREGAR:

1. Colchicina
   Uso: pericarditis, amiloidosis hepatica, fibrosis hepatica
   Especie: perros principalmente
   Dosis: 0.01-0.03 mg/kg PO SID
   Presentación: tabletas 0.5, 0.6 mg
   Categoría: other (antiinflamatorio/antifibrótico)
   Fuente: Papich 5th Ed.

2. Guaifenesina (Guaifenesin)
   Uso: expectorante, relajante muscular (anestesia equina — poco uso SA)
   En small animal: expectorante para tos productiva
   Dosis: 3-5 mg/kg PO TID (expectorante)
   Presentación: tabs 200, 400 mg; syrup 100 mg/5 mL
   Categoría: respiratory
   Nota: En gatos NO — puede causar anemia Heinz body

3. Paracetamol (Acetaminofén)
   ⚠ SOLO PERROS — ALTAMENTE TÓXICO EN GATOS
   Uso: analgesia leve en perros (uso extra-label, no aprobado)
   Dosis perros: 10-15 mg/kg PO BID o TID (máx 15 mg/kg)
   Presentación: tabs 325, 500 mg
   Categoría: analgesic
   NOTA CRÍTICA: NUNCA EN GATOS — causa methemoglobinemia fatal
   Fuente: Papich 5th Ed.

4. Naloxona (Narcan)
   Uso: reversor de opioides (morfina, hidromorfona, fentanilo, etc.)
   Dosis: 0.01-0.04 mg/kg IV, IM, SQ; repetir c/20-40 min PRN
   Presentación: 0.4 mg/mL inyectable
   Categoría: emergency/reversal
   Nota: Duración corta — puede necesitar dosis repetidas
         Reversión parcial para preservar analgesia residual
   Fuente: Papich 5th Ed.

5. Flumazenil (Romazicon)
   Uso: reversor de benzodiacepinas (midazolam, diazepam)
   Dosis: 0.01 mg/kg IV lento; repetir PRN
   Presentación: 0.1 mg/mL inyectable
   Categoría: emergency/reversal
   Nota: Duración muy corta (30-60 min) — paciente puede re-sedarse
   Fuente: Papich 5th Ed.

6. Welactin (Omega-3 / Aceite de pescado)
   Uso: dermatología, articulaciones, cardiopatía, hipertrigliceridemia
   Presentación: cápsulas 1000 mg (EPA+DHA), líquido oral
   Dosis: variable según indicación y tamaño
         Típico: 20-55 mg/kg de EPA+DHA SID
   Categoría: dermatology (o nutraceutical)
   Nota: No es un fármaco en sentido estricto — suplemento nutricional

Archivos a modificar: app.js (SEED_DRUGS)
Fuente: Papich 5th Ed. + FDA labels

---


### MOD-028 — Rediseño UI: pestañas, header, settings y protocolos
⏳ PENDIENTE

═══ A) REDISEÑO DE PESTAÑAS ════════════════════════════════════════

Problema actual: pestañas planas y genéricas, demasiadas juntas
Solución: diseño más elegante + reorganización

NUEVA ESTRUCTURA DE PESTAÑAS (2 niveles):

  Nivel 1 — Barra principal (siempre visible):
  ┌──────────────────────────────────────────────┐
  │  🐾 VetDose    [Toby · 32.5 lb]    ⚙ [👤]  │
  └──────────────────────────────────────────────┘
  
  Nivel 2 — Pestañas de navegación:
  ┌─────────┬─────────┬──────────┬──────────────┐
  │Calcular │  CRI   │ Fluidos  │  Nutrición   │
  └─────────┴─────────┴──────────┴──────────────┘

  Pestaña Drogas → mover a ⚙ Settings (ícono engranaje)
  Settings también contiene:
    - Nombre del Dr./Clínica (personalizable)
    - Importar/Exportar drogas
    - CHANGELOG

DISEÑO PESTAÑAS:
  - Fondo: verde oscuro (#1a5c38) para la pestaña activa
  - Ícono pequeño + texto debajo
  - Borde inferior redondeado en activa
  - Transición suave al cambiar

═══ B) HEADER MEJORADO ═════════════════════════════════════════════

Actual: muestra "🐕 Toby · 32.5 lb (14.74 kg) · BSA 0.582 m²"
        → demasiada información mezclada

Nuevo header:
  - Mostrar solo nombre del paciente + peso EN LA UNIDAD ELEGIDA
    Si eligió lb: "🐕 Toby · 32.5 lb"
    Si eligió kg: "🐕 Toby · 14.7 kg"
  - Sin BSA en el header (es info secundaria)

En la pantalla de RESULTADOS:
  - Barra del paciente muestra: lb + kg + m² (las 3 — para referencia clínica)
  - El header solo muestra la unidad elegida

═══ C) PANTALLA DE SETTINGS ════════════════════════════════════════

Nueva pantalla accesible desde ícono ⚙ en el header:
  ┌─────────────────────────────────────────────┐
  │ ⚙ Ajustes                                  │
  ├─────────────────────────────────────────────┤
  │ 👤 Nombre del veterinario                   │
  │    [Dr. Yenen Villasmil-Ontiveros        ]  │
  │                                             │
  │ 🏥 Nombre de la clínica                     │
  │    [                                     ]  │
  │                                             │
  │ 📞 Teléfono                                 │
  │    [                                     ]  │
  ├─────────────────────────────────────────────┤
  │ 💊 Base de datos de drogas                  │
  │    [+ Agregar droga]                        │
  │    [📤 Exportar] [📥 Importar]              │
  ├─────────────────────────────────────────────┤
  │ 📋 VetDose 2.0 — Ver novedades              │
  └─────────────────────────────────────────────┘

Datos guardados en localStorage, aparecen en:
  - Hoja de indicaciones al cliente (pie de página)
  - Hoja de enfermería (encabezado)

═══ D) PESTAÑA PROTOCOLOS (nueva) ══════════════════════════════════

Concepto: filtros personalizados por patología/protocolo
          El veterinario crea sus propias listas de medicamentos
          para cada condición que maneja frecuentemente

FLUJO:
  1. Veterinario va a Protocolos → toca "+" → crea nuevo protocolo
  2. Escribe nombre: "Pancreatitis aguda"
  3. Selecciona medicamentos de la base de datos:
     ☑ Panoquell       ☑ Maropitant (Cerenia)
     ☑ Ondansetron     ☑ Pantoprazol
     ☑ Famotidina      ☑ Gabapentina
     ☑ Buprenorfina    ☑ Metronidazol
     ☑ Sucralfato
  4. Guarda el protocolo
  5. La próxima vez: toca "Pancreatitis aguda" →
     aparece la lista con SOLO esas drogas →
     selecciona las que quiere usar para este paciente →
     calcula dosis normalmente

PROTOCOLOS PRE-CARGADOS (ejemplos):
  - Pancreatitis aguda
  - Pre-anestésico perro sano
  - Pre-anestésico gato sano
  - Crisis epiléptica
  - Shock hipovolémico
  - Bloqueo urinario felino
  - ITU canina
  (el usuario puede editar o eliminar cualquiera)

INTERFAZ:
  Lista de protocolos con botón editar/borrar
  Cada protocolo: nombre + número de drogas
  Toque en protocolo → lista de drogas del protocolo
  Igual que la pantalla Calcular pero filtrada por el protocolo

ALMACENAMIENTO:
  localStorage: 'vetdose_protocols' = [{name, drugIds[]}]

NUEVA ESTRUCTURA DE PESTAÑAS FINAL:
  [Calcular] [CRI] [Fluidos] [Nutrición] [Protocolos]
  ⚙ Settings (modal desde header)

Archivos a modificar:
  index.html — nueva estructura header, pestañas, pantalla Settings,
               pantalla Protocolos
  app.js — funciones saveSettings(), loadSettings(),
           saveProtocol(), loadProtocols(), renderProtocols(),
           renderProtocolDrugs()

---

═══════════════════════════════════════════════════════════════
## NOTAS TÉCNICAS
═══════════════════════════════════════════════════════════════

Archivos de la app (7):
  app.js       — lógica, seed de 144 drogas, tablas de peso, antagonistas
  index.html   — UI completa, modal, filtros, tabs
  sw.js        — service worker offline (actualmente v5)
  manifest.json — configuración PWA
  icon-logo.png — logo VetDose
  icon-192.png  — ícono pantalla de inicio
  icon-512.png  — ícono alta resolución

URL de producción: spontaneous-cocada-7f1650.netlify.app
Hosting: Netlify (gratuito)
Almacenamiento: localStorage del iPhone (100% local, sin backend)
