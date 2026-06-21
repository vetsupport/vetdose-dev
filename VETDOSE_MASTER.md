# VETDOSE MASTER — Fuente Única de Verdad
# Dr. Yenen Villasmil-Ontiveros, DVM — Pembroke Pines, Florida
# Última actualización: Junio 2026
# INSTRUCCIONES: Este archivo contiene TODO lo necesario para reconstruir
# VetDose 2.0 desde cero. Leer completo antes de tocar cualquier archivo.

═══════════════════════════════════════════════════════════════════════
## 1. DESCRIPCIÓN DEL PROYECTO
═══════════════════════════════════════════════════════════════════════

VetDose 2.0 es una PWA (Progressive Web App) de dosificación veterinaria
para pequeños animales (canino y felino). Funciona offline, instalable
en iPhone desde Safari. Sin backend, sin base de datos externa.
Todo el almacenamiento es localStorage del teléfono.

Autor: Dr. Yenen Villasmil-Ontiveros, DVM
Uso: clínica de pequeños animales, Florida
Colegas usuarios: varios veterinarios con acceso por contraseña compartida

URL producción: spontaneous-cocada-7f1650.netlify.app
Hosting: Netlify (gratuito) — conectado a GitHub repositorio 'vetdose'
URL desarrollo: vetsupport.github.io/vetdose-dev (para pruebas)

═══════════════════════════════════════════════════════════════════════
## 2. ARQUITECTURA — LOS 7 ARCHIVOS
═══════════════════════════════════════════════════════════════════════

app.js        — Toda la lógica. SEED_DRUGS (~140 drogas), TABLE_DRUGS
                (11 drogas por tabla de fabricante), funciones de cálculo,
                CRI, fluidos, nutrición, K⁺, protocolos, login, UI.
                ~3000 líneas. ES EL ARCHIVO MÁS IMPORTANTE.

index.html    — UI completa. Pantallas, modales, tabs, CSS embebido.
                ~800 líneas. Sin dependencias externas.

sw.js         — Service Worker. Cache offline. Versión actual: v30.
                CRÍTICO: incrementar número de versión en cada deploy
                o el teléfono sirve archivos viejos del caché.

manifest.json — Configuración PWA. Nombre, íconos, colores, display.

icon-logo.png — Logo VetDose (header de la app)
icon-192.png  — Ícono pantalla de inicio iPhone
icon-512.png  — Ícono alta resolución

REGLA DE ORO: Cada vez que se modifiquen archivos, incrementar el número
de versión en sw.js (ej: vetdose-v30 → vetdose-v31) para forzar
recarga en todos los dispositivos.

═══════════════════════════════════════════════════════════════════════
## 3. FLUJO DE TRABAJO (METODOLOGÍA)
═══════════════════════════════════════════════════════════════════════

REGLA ABSOLUTA: Nunca modificar producción directamente.
Siempre trabajar en vetdose-dev, probar ahí, y solo cuando el Dr.
Villasmil confirma que todo funciona → copiar a producción (vetdose).

Flujo de cambios:
  1. Dr. Villasmil sube el ZIP de producción actual
  2. Se hace UN SOLO cambio a la vez sobre ese archivo
  3. Se verifica sintaxis JS con: node -e "new Function(code)"
  4. Se verifica balance de divs en index.html con python3
  5. Se entrega ZIP al Dr. Villasmil
  6. Dr. Villasmil sube a vetdose-dev y prueba
  7. Si funciona → sube a vetdose (producción)
  8. Si hay error → Dr. Villasmil describe el error exacto y se corrige

VERIFICACIONES OBLIGATORIAS antes de entregar cualquier ZIP:
  - node -e "const fs=require('fs'); new Function(fs.readFileSync('app.js','utf8'))"
  - Balance divs: opens === closes en index.html
  - Grep de la corrección específica para confirmar que está en el archivo

═══════════════════════════════════════════════════════════════════════
## 4. SISTEMA DE LOGIN
═══════════════════════════════════════════════════════════════════════

Sistema de contraseña compartida. Todos los usuarios usan la misma
contraseña. Cada usuario tiene su propio nombre de usuario (login corto).

CONTRASEÑA ACTUAL: Vetsupport01
(Para cambiarla: modificar var VETDOSE_PWD en app.js, una sola línea)

FLUJO:
  Primera vez → pantalla de REGISTRO:
    - Título (Dr./Dra.)
    - Nombre completo (ej: Yenen Villasmil)
    - Credencial (ej: DVM)
    - Usuario corto (ej: yenenvil) — con aviso en naranja:
      "Su usuario NO es su correo electrónico"
    - Contraseña (Vetsupport01)
    → Guarda en localStorage, entra a la app
    → En la app aparece: "Dr. Yenen Villasmil DVM"

  Veces siguientes → pantalla de LOGIN:
    - Saluda: "Bienvenido, yenenvil"
    - Solo pide contraseña
    → Enter o botón Entrar

  Links entre pantallas:
    - Login → "¿Usuario nuevo? Crear cuenta"
    - Registro → "¿Ya tienes cuenta? Iniciar sesión"

ALMACENAMIENTO:
  localStorage 'vetdose_auth' = 'ok'
  localStorage 'vetdose_user' = JSON {username, displayName, title, nombre, credencial}

═══════════════════════════════════════════════════════════════════════
## 5. PESTAÑAS Y PANTALLAS
═══════════════════════════════════════════════════════════════════════

Pestañas actuales (barra inferior):
  💊 Calcular  |  💉 CRI  |  💧 Fluidos  |  🥗 Nutrición  |  ⚗️ K⁺  |  📋 Protocolos  |  🗄️ Drogas

Pantallas especiales (sin tab):
  screen-results → resultado del cálculo de dosis
  reg-overlay    → login/registro

ESTRUCTURA HTML CRÍTICA:
  Todas las pantallas .screen tienen display:none por defecto.
  Solo la .screen.active tiene display:block.
  El div #nut-bcs-mode debe estar DENTRO de #screen-nutricion.
  Si queda fuera, se muestra en K⁺ y Protocolos (bug estructural).

═══════════════════════════════════════════════════════════════════════
## 6. FUENTE DE VERDAD PARA DOSIS
═══════════════════════════════════════════════════════════════════════

FUENTE OFICIAL ÚNICA: Papich Handbook of Veterinary Drugs, 5th Edition
  (Mark G. Papich, 2020)

REGLA ABSOLUTA: Ninguna dosis, rango de dosis, ni valor farmacológico
puede existir en VetDose sin estar verificado en Papich 5th Ed.
Si no está en Papich, debe marcarse como:
  validationStatus: 'Pendiente validación — verificar antes de usar'

NUNCA inventar dosis. NUNCA atribuir "Papich" sin verificar.

═══════════════════════════════════════════════════════════════════════
## 7. ALGORITMO DE TABLETAS — REGLAS DEFINITIVAS
═══════════════════════════════════════════════════════════════════════

Función: smartTabletOptions() en app.js

REGLAS (no cambiar sin consultar al Dr. Villasmil):

1. Máximo 2 porciones en cualquier combinación
2. Mínimo mitad (0.5 tab) — NUNCA cuartos ni tercios
3. NO combinar medias de diferentes tamaños
   (sí: 0.5 tab 250mg; NO: 0.5 tab 62.5mg + 0.5 tab 250mg)
4. Combinaciones permitidas:
   - 1 tableta entera
   - 0.5 tableta (media)
   - 2 tabletas enteras (mismo tamaño)
   - 1.5 tabletas (mismo tamaño)
   - 1 entera + 1 entera (tamaños diferentes)
   - 1 entera + 0.5 (tamaños diferentes)
5. Orden de simplicidad (de mejor a peor):
   1° → 1 tableta entera
   2° → 0.5 tableta
   3° → 2 tabletas enteras
   4° → 1 entera + 0.5 diferente
   5° → 1.5 tabletas mismo tamaño
6. Botón por defecto = el MÁS SIMPLE dentro del rango (no el más cercano a la dosis preferida)
7. Máximo 5 opciones en pantalla
8. Solo mostrar combinaciones que caen dentro del rango [doseMin×kg, doseMax×kg]
9. Fallback: si ninguna combinación cae en el rango, mostrar la más cercana por encima del mínimo

HEADER DEL RESULTADO:
  Línea 1: Nombre comercial (Nombre genérico) — en grande
  Línea 2: Requerido: Xmg – Ymg (calculado del peso del paciente)
  Ejemplo: Clavamox (Amoxicilina + Ácido Clavulánico)
           Requerido: 187.5 – 375 mg

═══════════════════════════════════════════════════════════════════════
## 8. TABLAS DE DOSIFICACIÓN POR PESO (TABLE_DRUGS)
═══════════════════════════════════════════════════════════════════════

Estas drogas NO se calculan por mg/kg sino por tabla del fabricante.
El peso se convierte a lb antes de buscar la fila (redondeo a 1 decimal
para evitar falsos "fuera de rango" en límites exactos).

DROGAS POR TABLA (verificadas contra labels oficiales):

1. APOQUEL (Oclacitinib) — Zoetis label oficial
   6.6–8.8 lb    → 0.5 tab 3.6 mg
   10.0–14.9 lb  → 0.5 tab 5.4 mg
   15.0–19.9 lb  → 1 tab 3.6 mg
   20.0–29.9 lb  → 1 tab 5.4 mg
   30.0–44.9 lb  → 0.5 tab 16 mg (8 mg)
   45.0–59.9 lb  → 2 tab 5.4 mg (10.8 mg)
   60.0–89.9 lb  → 1 tab 16 mg
   90.0–129.9 lb → 1.5 tab 16 mg (24 mg)
   130.0–175.9 lb → 2 tab 16 mg
   Solo perros ≥12 meses. SID mantenimiento, BID x14 días agudo.

2. CYTOPOINT (Lokivetmab) — Zoetis label: 2 mg/kg mínimo
   Viales: 10, 20, 30, 40 mg. Combinar para alcanzar ≥2 mg/kg.
   <10 lb       → 1 vial 10 mg
   10–<20 lb    → 1 vial 20 mg
   20–<30 lb    → 1 vial 30 mg
   30–<40 lb    → 1 vial 40 mg
   40–<50 lb    → 1 vial 40 mg + 1 vial 10 mg
   50–<60 lb    → 1 vial 40 mg + 1 vial 20 mg
   60–<70 lb    → 1 vial 40 mg + 1 vial 30 mg
   70–<80 lb    → 2 viales 40 mg
   80–<90 lb    → 2 viales 40 mg + 1 vial 10 mg
   90–<100 lb   → 2 viales 40 mg + 1 vial 20 mg
   100–<110 lb  → 2 viales 40 mg + 1 vial 30 mg
   ≥110 lb      → 3 viales 40 mg
   Solo perros. SQ mensual.

3. SIMPARICA (Sarolaner) — Zoetis label
   Solo perros ≥6 meses y ≥2.8 kg (6.2 lb).
   6.2–11.0 lb  → Amarilla 5 mg
   11.1–22.0 lb → Naranja 10 mg (incluye 10kg=22.05lb → redondear a 22.1)
   22.1–44.0 lb → Morada 20 mg
   44.1–88.0 lb → Azul 40 mg
   88.1–132.0 lb → Verde 80 mg
   >132.0 lb    → Combinar tabletas apropiadas

4. SIMPARICA TRIO (Sarolaner+Milbemicina+Moxidectina) — Zoetis label
   Solo perros ≥8 semanas y ≥2.8 lb.
   2.8–5.5 lb   → 3/0.5/0.009 mg
   5.6–11.0 lb  → 6/1/0.018 mg
   11.1–22.0 lb → 12/2/0.036 mg
   22.1–44.0 lb → 24/4/0.072 mg
   44.1–88.0 lb → 48/8/0.144 mg
   88.1–132.0 lb → 96/16/0.288 mg

5. LIBRELA (Bedinvetmab) — Zoetis dosing chart oficial
   Solo perros. Anti-NGF. 0.5 mg/kg SQ mensual.
   <11 lb (<5 kg)       → 0.1 mL/kg del vial 5 mg/mL (NO vial completo)
   11.0–22.1 lb (5–10kg) → 1 mL vial 5 mg SQ
   22.2–44.1 lb (10.1–20kg) → 1 mL vial 10 mg SQ
   44.2–66.1 lb (20.1–30kg) → 1 mL vial 15 mg SQ
   66.2–88.2 lb (30.1–40kg) → 1 mL vial 20 mg SQ
   88.3–132.3 lb (40.1–60kg) → 1 mL vial 30 mg SQ
   132.4–176.4 lb (60.1–80kg) → 2 viales 30 mg SQ
   176.5–220.5 lb (80.1–100kg) → 1 vial 20mg + 1 vial 30mg SQ
   220.6–264.6 lb (100.1–120kg) → 2 viales 30 mg SQ

6. SOLENSIA (Frunevetmab) — Zoetis label
   Solo gatos. Anti-NGF felino. 1 mg/kg SQ mensual.
   Vial SIEMPRE 7 mg/mL (NO existen viales de 1/2/3/4 mg).
   5.5–15.4 lb (2.5–7 kg) → 1 vial 7 mg SQ (1 mL)
   ≥15.5 lb (>7 kg)       → 2 viales 7 mg SQ (2 mL, misma jeringa)

7. ZENRELIA (Ilunocitinib) — Elanco label oficial (DailyMed NADA 141-585)
   Solo perros ≥12 meses. JAK inhibidor. SID. Tabletas: 4.8/6.4/8.5/15 mg.
   (NO existen tabletas de 4/8/16 mg)
   6.6–8.8 lb   → 0.5 tab 4.8 mg
   8.9–11.8 lb  → 0.5 tab 6.4 mg
   11.9–14.3 lb → 0.5 tab 8.5 mg
   14.4–17.7 lb → 1 tab 4.8 mg
   17.8–23.6 lb → 1 tab 6.4 mg
   23.7–31.1 lb → 1 tab 8.5 mg
   31.2–35.4 lb → 1.5 tab 6.4 mg
   35.5–43.1 lb → 1.5 tab 8.5 mg
   43.2–55.0 lb → 1 tab 15 mg
   55.1–62.5 lb → 2 tab 8.5 mg
   62.6–83.3 lb → 1.5 tab 15 mg
   83.4–110.0 lb → 2 tab 15 mg
   110.1–137.5 lb → 2.5 tab 15 mg
   137.6–166.0 lb → 3 tab 15 mg

8. CERENIA TABS (Maropitant) — Zoetis label (2 mg/kg, vómito agudo)
   Perros: aprobado FDA. Gatos: off-label (2 mg/kg misma dosis).
   6.6–8.8 lb (3.0–4.0 kg)   → 0.5 tab 16 mg
   8.9–17.6 lb (4.1–8.0 kg)  → 1 tab 16 mg
   17.7–26.5 lb (8.1–12.0 kg) → 1 tab 24 mg
   26.6–52.9 lb (12.1–24.0 kg) → 2 tab 24 mg
   53.0–66.1 lb (24.1–30.0 kg) → 1 tab 60 mg
   66.2–132.3 lb (30.1–60.0 kg) → 2 tab 60 mg
   Dar con comida para reducir hipersalivación.

9. CARDALIS (Benazepril/Espironolactona) — Ceva label
   Solo perros. Insuficiencia cardíaca.
   5.5–11 lb  → Small 2.5/20 mg
   11.1–22 lb → Medium 5/40 mg
   22.1–44 lb → Large 10/80 mg
   44.1–88 lb → 2 tab Large
   88.1–132 lb → 3 tab Large

10. TRIFEXIS (Spinosad/Milbemicina) — Elanco label
    Solo perros ≥8 semanas y ≥5 lb.
    5–10 lb    → 140/2.3 mg (Naranja)
    10.1–20 lb → 270/4.5 mg (Verde)
    20.1–40 lb → 560/9.3 mg (Azul)
    40.1–60 lb → 810/13.5 mg (Amarillo)
    60.1–120 lb → 1620/27 mg (Rojo)
    Dar con comida para maximizar absorción.

11. INTERCEPTOR PLUS (Milbemicina/Praziquantel) — Elanco label
    Solo perros. Mensual. Heartworm + tenias.
    2–8 lb    → 2.3/22.8 mg
    8.1–25 lb → 5.75/57 mg
    25.1–50 lb → 11.5/114 mg
    50.1–100 lb → 23/228 mg

═══════════════════════════════════════════════════════════════════════
## 9. CORRECCIONES PAPICH APLICADAS (Junio 2026)
═══════════════════════════════════════════════════════════════════════

Verificadas contra Papich 5th Ed. y aplicadas en producción:

DROGA               CAMPO      ANTES    DESPUÉS   PAPICH REF
─────────────────────────────────────────────────────────────
acepromazina        dosePref   0.02     0.025     más común 0.025 mg/kg
alfaxalona          doseMin    1.0      1.5       inducción sin pre-anest 1.5-4.5
buprenorfina        doseMin    0.005    0.006     convencional 0.006-0.02
cefalexina          doseMax    30.0     25.0      22-25 mg/kg (30 solo equinos)
dexametasona        doseMin    0.1      0.07      anti-inflam 0.07-0.15
digoxina            doseMin    0.003    0.005     0.005-0.011 q12h
digoxina            doseMax    0.01     0.011     0.005-0.011 q12h
epinefrina          doseMin    0.01     0.005     anafilaxis 5-10 mcg/kg
epinefrina          doseMax    0.01     0.02      PCR hasta 20 mcg/kg
gentamicina         doseMin    6.0      9.0       9-14 mg/kg SID
gentamicina         doseMax    9.0      14.0      9-14 mg/kg SID
gentamicina         dosePref   6.0      9.0       iniciar en 9 mg/kg
hidromorfona        doseMax    0.2      0.22      IM/SQ hasta 0.22 mg/kg
ketamina            doseMax    10.0     22.0      5.5-22 mg/kg IV/IM
levotiroxina        doseMin    0.01     0.022     22 mcg/kg inicio
levotiroxina        doseMax    0.02     0.044     algunos hasta 44 mcg/kg
levotiroxina        dosePref   0.02     0.022     22 mcg/kg
metoclopramida      doseMin    0.2      0.5       0.5 mg/kg q8h
metoclopramida      dosePref   0.3      0.5       0.5 mg/kg
fenobarbital        doseMax    5.0      8.0       2-8 mg/kg q12h perros
pimobendan          doseMin    0.1      0.25*     0.25-0.3 mg/kg perros
tramadol            doseMin    2.0      5.0       5 mg/kg q6-8h
gabapentina         doseMax    20.0     40.0      hasta 40 mg/kg dolor neuropático
alprazolam          doseMin    0.01     0.025     0.025-0.05 mg/kg q8h
alprazolam          dosePref   0.02     0.025     0.025 mg/kg
clomipramina        doseMin    1.0      2.0       2-4 mg/kg q12h
amitriptilina       doseMax    4.0      2.0       1-2 mg/kg q12-24h
darbepoetina        doseMin    0.45     0.5       iniciar 0.5 mcg/kg/semana
diltiazem           doseMin    1.0      0.5       0.5-5 mg/kg según uso
diltiazem           doseMax    2.5      5.0       hasta 5 mg/kg FA
espironolactona     doseMin    1.0      2.0       2-4 mg/kg/día
flumazenil          dosePref   0.01     0.02      0.02 mg/kg IV
manitol             doseMax    1.0      2.0       0.25-2 g/kg
mirtazapina_oral    doseMin    1.0      0.5       0.5-1 mg/kg q12h

*Pimobendan: doseMin ajustado a 0.20 (no 0.25) para capturar 1 tab 1.25mg
en perros 5-7 kg. El label Vetmedin asigna 1 tab 1.25mg para hasta 11 lb
(5 kg), clínicamente aceptado para perros hasta ~7 kg (0.212 mg/kg).

═══════════════════════════════════════════════════════════════════════
## 10. PRESENTACIONES DE TABLETAS (tabSizes) VERIFICADAS
═══════════════════════════════════════════════════════════════════════

Estas presentaciones están en TABLET_SIZES en app.js:

Droga              tabSizes (mg)
────────────────────────────────────────────────────────
pimobendan         1.25, 2.5, 5, 10
amoxicilina_clavulanato  62.5, 125, 250, 375
cefalexina         250, 500, 1000
fenobarbital       16.2, 32.4, 64.8, 97.2 (Phenobarbital veterinario)
enalapril          2.5, 5, 10, 20
benazepril         5, 10, 20
amlodipino         2.5, 5, 10
levotiroxina       0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 1.0
alprazolam         0.25, 0.5, 1.0, 2.0
atenolol           25, 50, 100
diltiazem          30, 60, 90, 120
digoxina           0.0625, 0.125, 0.25
metronidazol       250, 500
doxiciclina        50, 100
fluconazol         50, 100, 150, 200
fludrocortisona    0.1 (única)
metimazol          2.5, 5, 10
levetiracetam      250, 500, 750
clopidogrel        75 (única)
grapiprant         20, 60, 100
deracoxib          12, 25, 75, 100
cefpodoxime        100, 200
marbofloxacino     25, 50, 100, 200
enrofloxacino      22.7, 68, 136
carprofen          25, 75, 100
melatonina         1, 3, 5, 10
meloxicam          1, 2.5, 7.5
gabapentina        100, 300, 400, 600
clindamicina       25, 75, 150 (cápsulas)
furosemida         12.5, 50
espironolactona    25, 50, 100
tramadol           50, 100
trazodona          50, 100, 150, 300
pregabalina        25, 50, 75, 100, 150, 200, 300
clomipramina       5, 20, 80 (Clomicalm, sabor carne)
metocarbamol       500, 750
colchicina         0.5, 0.6

═══════════════════════════════════════════════════════════════════════
## 11. MÓDULOS FUNCIONALES
═══════════════════════════════════════════════════════════════════════

### 11.1 CALCULAR DOSIS
- Ingreso de nombre de paciente, peso (kg o lb), especie
- Lista de drogas filtrable por especie, categoría y búsqueda de texto
- Selección múltiple de drogas
- Al calcular: muestra resultados en screen-results
- Drogas SEED_DRUGS: cálculo por mg/kg usando dosePref y conc
- Drogas TABLE_DRUGS: resultado de tabla según peso en lb (redondeo a 1 decimal)
- Antagonistas automáticos: atipamezole (dexmed/medetomidina), naloxona (opioides), flumazenil (benzodiacepinas)
- Ajuste de dosis post-cálculo dentro del rango Plumb

### 11.2 CRI (Constant Rate Infusion)
- Selección de droga CRI
- Peso del paciente (tomado del estado global)
- Tasa de dosis deseada (mcg/kg/min o mg/kg/h)
- Volumen de dilución y velocidad de bomba
- Calcula: mL del vial a agregar, concentración final, duración de bolsa
- Loading dose: cuando aplica, muestra mL desde el vial original (no de la bolsa)
  con explicación de administración (IV lento 1-5 min antes de iniciar CRI)
- Banner de instrucciones al inicio de la pantalla

### 11.3 FLUIDOS
- Plan AAHA 2024
- Inputs: peso, especie, cardiopatía (sí/no), tipo de fluido, % deshidratación
- Calcula: déficit, mantenimiento, fiebre, pérdidas activas
- Coloides (Hetastarch): bolus 3-5 mL/kg × 15 min, reevaluar
  Precaución coagulopatía y daño renal
- NaCl hipertónico: solo emergencia
- Ajuste automático en cardiopatía (tasas reducidas, sin bolos rápidos)

### 11.4 NUTRICIÓN
- Modo RER/MER: calcula calorías diarias con factores MER por especie/estado
- Selector de alimentos por especie (canino/felino separados, NO mezclados)
  Marcas: Hill's, Royal Canin, Purina — verificadas contra páginas oficiales
  Opción "Otro alimento — ingresar kcal manualmente"
- Modo BCS: estima peso ideal según BCS 1-9, plan de reducción de peso
  Proyecta semanas para llegar al peso meta (0.5-1% por semana)
  ALERTA: en gatos nunca restricción severa (lipidosis hepática)

### 11.5 POTASIO (K⁺)
- Corrección por tabla con dosis máxima 0.5 mEq/kg/h
- Pantalla separada, no afectada por el módulo de nutrición

### 11.6 PROTOCOLOS
- 6 protocolos predeterminados
- Personalizables (agregar/editar/eliminar)
- Guardados en localStorage 'vetdose_protocols'

### 11.7 BASE DE DATOS (DROGAS)
- Ver/editar/agregar/eliminar drogas
- Exportar/importar JSON
- Búsqueda y filtros

═══════════════════════════════════════════════════════════════════════
## 12. VALORES CALÓRICOS DE ALIMENTOS (VERIFICADOS)
═══════════════════════════════════════════════════════════════════════

Verificados contra páginas oficiales (hillspet.com, royalcanin.com,
purina.com, chewy.com, petco.com) — Junio 2026:

CANINOS — SECOS (kcal/taza):
  Hill's Science Diet Adult Canino       → 365 kcal/taza
  Hill's Prescription r/d Canino        → 245 kcal/taza
  Hill's Prescription w/d Canino        → 255 kcal/taza
  Hill's Prescription Metabolic Canino  → 291 kcal/taza
  RC Satiety Support Canino (Rx)        → 224 kcal/taza
  RC Medium Weight Care Canino          → 256 kcal/taza
  RC Medium Adult Canino                → 354 kcal/taza
  Pro Plan Weight Management Canino     → 330 kcal/taza
  Purina ONE Healthy Weight Canino      → 325 kcal/taza
  Pro Plan Adult Chicken & Rice Canino  → 369 kcal/taza

CANINOS — HÚMEDOS (kcal/lata):
  Hill's Prescription r/d Canino (156g) → 120 kcal/lata
  RC Satiety Support Canino (lata)       → 225 kcal/lata
  Purina Pro Plan Adult Canino (368g)    → 185 kcal/lata

FELINOS — SECOS (kcal/taza):
  Hill's Prescription r/d Felino        → 265 kcal/taza
  Hill's Prescription w/d Felino        → 280 kcal/taza
  Hill's Science Diet Light Felino       → 288 kcal/taza
  Hill's Science Diet Perfect Weight     → 300 kcal/taza
  RC Satiety Support Felino (Rx)        → 234 kcal/taza
  RC Indoor Adult Felino                → 325 kcal/taza
  RC Weight Care Felino                 → 254 kcal/taza
  Pro Plan Weight Management Felino     → 353 kcal/taza
  Purina ONE Ideal Weight Felino        → 427 kcal/taza

FELINOS — HÚMEDOS (kcal/lata):
  RC Satiety Support Felino (lata 5.8oz) → 82 kcal/lata
  Purina Pro Plan Adult Felino (156g)    → 185 kcal/lata

═══════════════════════════════════════════════════════════════════════
## 13. HISTORIAL DE VERSIONES
═══════════════════════════════════════════════════════════════════════

v1 — Versión inicial
  PWA instalable iPhone. 17 drogas seed. Calculadora cc/peso lb/kg.
  Calculadora diluciones. Base de datos editable. Exportar/importar JSON.

v2 — Mejoras visuales
  Fondo claro, letra grande, logo VetDose. Genérico primero, comercial segundo.
  Botón imprimir en resultados.

v3 — Base de datos expandida
  144 drogas. Campo fuente visible. Badge ⚠ pendientes. 14 colores por categoría.
  Modo dosis fija (mg/m², U/kg, gotas). Modal expandido. Orden alfabético.

v4 — Mejoras clínicas
  Selector especie (filtra lista). 10 drogas por tabla de peso.
  Barra búsqueda. 22 filtros categoría. Antagonistas automáticos.
  Ajuste dosis post-cálculo. CRI (reemplaza dilución). Cálculo BSA.
  Dosis canina/felina en modal. 50+ categorías.

v5 — Tablas de peso corregidas
  Simparica, Simparica Trio, Cytopoint, Librela, Solensia, Cardalis,
  Cerenia tabs, Zenrelia — todos corregidos con datos oficiales.

v6 — Auditoría tablas (sesión previa)
  Bug redondeo kg→lb en límites de tabla (22.0462 lb > 22.0 → fallaba).
  Apoquel corregido (daba 16mg a perros que necesitaban 5.4mg).
  Zenrelia tabletas correctas (4.8/6.4/8.5/15 mg, no 4/8/16 mg).
  Solensia vial real 7mg/mL (no 1/2/3/4 mg inventados).
  Cerenia rangos corregidos. Librela categoría <11lb agregada.

v7 — Audit Papich + fixes UX (sesión previa)
  33 correcciones de dosis verificadas contra Papich 5th Ed.
  Pimobendan doseMin corregido (0.1→0.25, el 0.1 era rango felino).
  Gentamicina 9-14 mg/kg SID. Fenobarbital hasta 8 mg/kg.
  Tramadol mínimo 5 mg/kg. Diltiazem 0.5-5 mg/kg.

v8 — Login + Nutrición + Algoritmo tabletas (sesión actual)
  Sistema login: registro (título, nombre, credencial, usuario, contraseña)
    + login (usuario guardado, solo contraseña).
    Contraseña compartida: Vetsupport01.
  Nutrición: selector de alimentos por especie (no mezclados).
    Valores calóricos verificados contra páginas oficiales fabricantes.
    Alimento personalizado con campo kcal manual.
  Algoritmo tabletas reescrito: combinaciones reales de presentaciones,
    orden de simplicidad correcto, sin combinar medias diferentes tamaños.
  Header resultado: Comercial (Genérico) / Requerido: Xmg–Ymg.
  CRI: banner instrucciones + explicación loading dose.
  Hetastarch: texto correcto (bolus 3-5 mL/kg × 15 min, reevaluar).
  Bug fix: wkg undefined en renderResult() → getWeightKg().
  SW v30 para forzar recarga en dispositivos.

═══════════════════════════════════════════════════════════════════════
## 14. PENDIENTES — PRÓXIMAS VERSIONES
═══════════════════════════════════════════════════════════════════════

### ALTA PRIORIDAD

MOD-004 — Dosis canina vs felina en cálculo
  Cuando especie=felina y la droga tiene doseCat definida, usar doseCat.
  Ejemplo crítico: Meloxicam → canino 0.2 mg/kg, felino 0.05 mg/kg.
  Actualmente campos doseDog/doseCat se guardan pero no se usan en cálculo.

MOD-009 — Presentaciones faltantes
  Clavamox: tabSizes [62.5, 125, 250, 375] + suspensión 62.5 mg/mL separada
  Clindamicina: separar en cápsulas [25, 75, 150] y suspensión 25 mg/mL
  Cytopoint: tabla de viales actualizada (rangos de 10 lb en 10 lb)

MOD-010 — Resultado recalcular a la derecha del botón (misma línea)

MOD-012 — Correcciones drogas (ver lista completa en sección abajo)

MOD-013 — Drogas nuevas: Amitriptilina, Clomipramina, Azitromicina,
  Carprofen inyectable/tabs, Cefalexina cápsulas 250mg

MOD-014 — Correcciones y drogas (lote mayo 2026, ver lista completa abajo)

### MEDIA PRIORIDAD

MOD-005 — Levetiracetam ER (Keppra XR) — 500, 750 mg, BID, no partir
MOD-016 — Mejor presentación visual de drogas por tabla de peso
MOD-018 — Hoja indicaciones para cliente (WhatsApp/imprimir)
MOD-019 — Panel dosis tiempo real con selector presentación y frecuencia
MOD-021 — Hoja enfermería (orden medicamentos para administrar)
MOD-025 — Drogas nuevas: Trazodone, Fenofibrate, Telmisartan, Robenacoxib,
  Bonqat (pregabalina gatos), Molidustat (Varenzin-CA1), Panoquell-CA1

### BAJA PRIORIDAD / FUTURO

MOD-002 — Pantalla "Acerca de / Versión" con changelog
MOD-007 — Rediseño UI minimalista (estilo Epocrates/MDCalc)
MOD-020 — Azitromicina: 2 entradas separadas (suspensión 40mg/mL + cáps 50mg)
MOD-022 — Módulo Fluidos ya implementado (verificar pendientes internos)
MOD-023 — Rediseño CRI (sub-modos: droga única, múltiple, insulina, rápido)
MOD-024 — Sincronizar Excel VetDose_Drogas_Expandida.xlsx → app.js (54 drogas pendientes)
MOD-028 — Rediseño pestañas (2 niveles, header mejorado, pantalla Settings)
MOD-027 — Drogas nuevas: Colchicina, Guaifenesina, Paracetamol (solo perros)

### IDEAS FUTURAS
  - Historial de pacientes con peso actualizable
  - Sincronización entre dispositivos vía iCloud
  - Modo oscuro
  - Agregar NexGard, Bravecto, HeartGard a TABLE_DRUGS
  - Compartir base de datos entre colegas

═══════════════════════════════════════════════════════════════════════
## 15. CORRECCIONES DE DROGAS PENDIENTES (detalle)
═══════════════════════════════════════════════════════════════════════

(Extraído de MOD-012 y MOD-014 — pendiente de implementar)

Acepromazina inyectable: presentaciones 1% (10 mg/mL) Y 0.2% (2 mg/mL)
Acepromazina tabletas: tabSizes [10, 12.5, 25]
Adequan: 2 mg/lb (4.4 mg/kg) IM, 2x/semana x4 sem, luego mensual
Amantadina: formType liquid_oral, conc 10 mg/mL (jarabe 50mg/5mL)
Amlodipino gatos: dosis fija 0.625 mg/gato (<6kg) o 1.25 mg/gato (>6kg)
Atenolol gatos: 1-2.5 mg/kg PO q12h o dosis fija 6.25-12.5 mg/gato
Atropina: presentaciones 0.54 mg/mL Y 0.4 mg/mL
Ciclosporina oral: cápsulas [10, 25, 50, 100] mg, congeladas = menos GI
Clopidogrel: loading 10 mg/kg PO, luego 2-3 mg/kg SID. Gatos: 18.75 mg/gato SID
Darbepoetina: viales 25 o 40 mcg/mL, no mezclar, refrigerar
Dexametasona: SP = 4 mg/mL, normal = 2 mg/mL (2 entradas separadas)
Dexmedetomidina perros: 5-10 mcg/kg IM/IV. Gatos: 7 mcg/kg IM o 3.5 IV
Diazepam GATOS: evitar vía oral — falla hepática idiosincrática
Digoxina gatos: dosis fija según peso (<3kg, 3-6kg, >6kg)
Diltiazem: agregar presentación ER separada
Dolasetron: 0.5-1 mg/kg (corregir doseMin a 0.5)
Doxiciclina gatos: dar con agua/comida (prevenir estenosis esofágica)
Dramamine (dimenhidrinato): gatos 12.5 mg/gato para cinetosis
Enrofloxacino gatos: RIESGO CEGUERA >5 mg/kg (reforzar nota)
Epinefrina: PCR 0.01 mg/kg, anafilaxis 0.01 IM. Gatos anafilaxis: 20 mcg/kg IV
Famciclovir: tabs 125 y 250 mg, BID o TID
Famotidina: tabs 10 y 20 mg, inyectable 10 mg/mL
Fenobarbital: tabSizes [12.5, 15, 16.2, 30, 32.4, 50, 60, 64.8, 97.2, 100]
Fentanilo: 0.01-0.04 mg/kg IV/IM/SQ. CRI: 0.003-0.006 mg/kg/h. 50 mcg/mL.
Furosemida: tabletas 12.5 y 50 mg (agregar tabSizes)
Gabapentina: líquida 50 mg/5 mL (10 mg/mL), refrigerar
GS-441524 (FIP): 15 mg/kg SID; neurológico/ocular 20 mg/kg SID. 42 días.
Hetastarch: bolos 2-5 mL/kg q10-20 min, máx 20 mL/kg/día
Hidromorfona: perros 0.05-0.22 mg/kg IV/IM/SQ c/2-4h; gatos 0.05-0.1 c/2-6h
Insulinas: Vetsulin U-40, ProZinc U-40, Glargina 100/300U, NPH, Detemir
Itraconazol: 5-10 mg/kg SID o 5 mg/kg BID
Ivermectina: sarna sarcóptica 0.2-0.4 mg/kg PO/SQ q semana x4
Ketamina: dolor 0.5 mg/kg SQ; cautela cardiopatía/HTA
Lactato Ringer: mantenimiento perros 2-6 mL/kg/h, gatos 2-3 mL/kg/h
Lactulose: 0.25-0.5 mL/kg PO q6-8h hasta heces blandas
Latanoprost: precaución glaucoma secundario uveítis o luxación cristalino
Ondansetron: corregir doseMin a 0.5 mg/kg; tabs 4, 8 mg

═══════════════════════════════════════════════════════════════════════
## 16. NOTAS TÉCNICAS IMPORTANTES
═══════════════════════════════════════════════════════════════════════

BUG HISTÓRICO (ya corregido en v8):
  wkg no estaba definido en renderResult() → causaba error silencioso
  al calcular drogas en tableta. Corregido: wkg → getWeightKg()

BUG DE REDONDEO (corregido en v6):
  10 kg = 22.0462 lb caía fuera de límite 22.0 en tablas de peso.
  Solución: redondear weightLb a 1 decimal antes de comparar con minLb/maxLb.

CACHÉ DEL SERVICE WORKER:
  Después de cada deploy, el teléfono puede seguir usando la versión vieja
  del caché hasta que el SW detecte el cambio (puede tardar hasta 24h).
  Para forzar: borrar caché del navegador en el teléfono.
  Solución definitiva: siempre incrementar CACHE = 'vetdose-vXX-fecha' en sw.js.

ALMACENAMIENTO LOCAL:
  vetdose_drugs       → base de datos de drogas (JSON array)
  vetdose_settings    → configuración (nombre Dr., clínica, etc.)
  vetdose_protocols   → protocolos guardados
  vetdose_auth        → 'ok' si autenticado
  vetdose_user        → datos del usuario registrado (JSON)
  vetdose_profiles    → perfiles múltiples (legacy)
  vetdose_freq_prefs  → preferencias de frecuencia por droga

HOSTING:
  Netlify conectado a GitHub. Push a main → deploy automático en ~1 min.
  URL: spontaneous-cocada-7f1650.netlify.app
  Dev: vetsupport.github.io/vetdose-dev (repositorio separado)

═══════════════════════════════════════════════════════════════════════
FIN DEL DOCUMENTO
Cualquier duda sobre el proyecto, leer este archivo primero.
═══════════════════════════════════════════════════════════════════════
