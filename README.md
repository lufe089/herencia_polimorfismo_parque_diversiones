# Guía de estudio sore herencia y polimorfismo

## Parque de Diversiones “Aventura Total”

## Contexto general

El Parque de Diversiones **Aventura Total** está renovando su sistema de control operativo.  
La administración necesita una aplicación que permita registrar las atracciones del parque, calcular el costo de funcionamiento de cada una y estimar el consumo de energía según su tipo.  
El sistema debe poder adaptarse a las diferencias entre atracciones, ya que no todas funcionan de la misma forma ni tienen los mismos requerimientos.

El nuevo software debe permitir **manejar distintos tipos de atracciones** de manera flexible, sin que el código principal dependa de los detalles de cada una pues se espera construir nuevas atracciones en el futuro.  
Algunas atracciones compartirán los mismos cálculos, otras los modificarán o ampliarán, y otras tendrán reglas completamente distintas.

-- 
### Situación del parque

En **Aventura Total** hay por ahora tres atracciones principales:

1. 🎢 **Montaña Rusa**: genera altos costos por mantenimiento y gran consumo eléctrico.  
2. 🎠 **Carrusel**: tradicional, de bajo costo y consumo moderado.  
3. 👻 **Casa del Terror**: con efectos especiales que aumentan su gasto de energía.

Todas se registran bajo una estructura común, pero cada una debe calcular:
- Su costo de operación diaria.  
- Su consumo energético estimado.  
- El costo adicional cuando se extienden las horas de funcionamiento más allá de lo habitual.


### Detalle operativo de las atracciones

A continuación se precisan **reglas numéricas y condiciones** para cada atracción del parque. 

> **Convenciones generales**
> - **Horas de operación diarias** (`h`): entero entre 1 y 12.
> - **Horas adicionales** (`x`): entero ≥ 0 (horario extendido).
> - **Costo diario en jornada normal:** se calcula con una **tarifa base** más un **componente por hora**.
> - **Costo en jornada extendida:** se toma el costo de la jornada normal y se **suma un recargo por cada hora adicional**, cuando aplique.
> - **Consumo energético estimado:** valor por **kilovatio-hora** (kWh) en función del tiempo de operación y factores propios de la atracción.
> - Los valores monetarios están en **pesos colombianos (COP)**.

---

### 1) Montaña Rusa

Atracción de alta velocidad, con inspecciones frecuentes y mayor desgaste.

**Parámetros y validaciones**
- Horas diarias permitidas: `1 ≤ h ≤ 10` (por seguridad).
- Si `h > 6`, se cobra una **inspección adicional**.

**Cálculo de costos**
- **Tarifa base diaria:** 50.000
- **Cargo por hora de operación:** 8.000 × `h`
- **Inspección adicional (si h > 6):** 10.000 (única vez)
- **Costo jornada normal:** `50.000 + 8.000*h + (h>6 ? 10.000 : 0)`

**Horario extendido**
- **Recargo por hora adicional:** 9.000 × `x`
- **Costo con horario extendido:** `Costo jornada normal + 9.000*x`
- **Límite de extensión recomendado:** `x ≤ 3`

**Consumo energético**
- **Consumo por hora:** 20 kWh
- **Consumo total:** `20 * (h + x)`

**Ejemplos numéricos**
- Caso A: `h=5, x=0` → Costo: `50.000 + 8.000*5 = 90.000` | Consumo: `20*5 = 100 kWh`
- Caso B: `h=7, x=2` → Costo: `50.000 + 8.000*7 + 10.000 + 9.000*2 = 50.000 + 56.000 + 10.000 + 18.000 = 134.000` | Consumo: `20*(7+2)=180 kWh`

---

### 2) Carrusel

Atracción tradicional, estable y de bajo consumo. Requiere poca supervisión.

**Parámetros y validaciones**
- Horas diarias permitidas: `1 ≤ h ≤ 12`
- No requiere inspección adicional por horas altas.

**Cálculo de costos**
- **Tarifa base diaria:** 20.000
- **Cargo por hora de operación:** 3.000 × `h`
- **Costo jornada normal:** `20.000 + 3.000*h`

**Horario extendido**
- **Recargo por hora adicional:** 3.500 × `x`
- **Costo con horario extendido:** `Costo jornada normal + 3.500*x`

**Consumo energético**
- **Consumo por hora:** 12 kWh
- **Consumo total:** `12 * (h + x)`

**Ejemplos numéricos**
- Caso A: `h=6, x=0` → Costo: `20.000 + 3.000*6 = 38.000` | Consumo: `12*6 = 72 kWh`
- Caso B: `h=6, x=2` → Costo: `38.000 + 3.500*2 = 45.000` | Consumo: `12*8 = 96 kWh`

---

### 3) Casa del Terror

Atracción con iluminación y sonido especializados. El gasto eléctrico puede variar según los efectos activos.

**Parámetros y validaciones**
- Horas diarias permitidas: `1 ≤ h ≤ 10`
- Puede activarse un **paquete de efectos** para temporadas altas.

**Cálculo de costos**
- **Tarifa base diaria:** 30.000
- **Cargo por hora de operación:** 5.000 × `h`
- **Costo jornada normal:** `30.000 + 5.000*h`

**Horario extendido**
- **Recargo por hora adicional:** *no aplica por defecto* (`0 × x`)
- **Costo con horario extendido:** `Costo jornada normal` (aunque aumente el consumo)
- *Nota:* La administración puede definir un recargo futuro si se considera necesario.

**Consumo energético**
- **Consumo por hora (estándar):** 15 kWh
- **Efectos especiales activados:** +5 kWh por hora
- **Consumo total sin efectos:** `15 * (h + x)`
- **Consumo total con efectos:** `(15 + 5) * (h + x) = 20 * (h + x)`

**Ejemplos numéricos**
- Caso A (sin efectos): `h=4, x=0` → Costo: `30.000 + 5.000*4 = 50.000` | Consumo: `15*4 = 60 kWh`
- Caso B (con efectos): `h=4, x=2` → Costo: `50.000` (no hay recargo por extensión) | Consumo: `20*(4+2) = 120 kWh`

---

## Resumen comparativo

| Atracción        | Base (COP) | Cargo por hora (COP) | Inspección | Recargo por hora extra (COP) | kWh/hora (estándar) | Variación por efectos |
|------------------|------------|----------------------|------------|------------------------------|---------------------|-----------------------|
| Montaña Rusa     | 50.000     | 8.000                | 10.000 si h>6 | 9.000                       | 20                  | —                     |
| Carrusel         | 20.000     | 3.000                | —          | 3.500                        | 12                  | —                     |
| Casa del Terror  | 30.000     | 5.000                | —          | 0                            | 15                  | +5 kWh/h con efectos  |

---

## Reglas de entrada y salida de datos (para orientar la consola)

- Entrada mínima sugerida por atracción:
  - Nombre (texto)
  - Horas de operación `h` (entero)
  - Horas adicionales `x` (entero)
  - Indicador de efectos (solo para Casa del Terror): “sí/no”
- Salida por atracción:
  - Nombre, horas, costo en jornada normal, costo en jornada extendida (si procede), consumo estimado.
- Totales del parque:
  - Suma de costos en jornada normal
  - Suma de costos en jornada extendida
  - Suma de consumo energético

## Lo que se espera del sistema

El programa debe:

1. Permitir crear distintos tipos de atracciones con sus datos básicos
2. Calcular el costo de funcionamiento para cada tipo de atracción, de acuerdo con sus características particulares.
3. Ofrecer una manera alternativa de calcular el costo cuando se trabaja más tiempo del normal.
4. Calcular el consumo energético aproximado de cada atracción según su tipo.
5. Mostrar por consola un resumen con la información individual y los totales del parque.

--

Cada atracción comparte una base común, pero el comportamiento no será idéntico en todos los casos:

- Algunas atracciones **mantienen el mismo comportamiento** que se usa de forma general.  
- Otras **ajustan parcialmente** el cálculo del costo o del consumo.  
- Algunas **lo sustituyen completamente** por su propia lógica.  
- En ciertos casos, la clase general no define el comportamiento y obliga a las atracciones a establecer el suyo.  
- Debe existir además un cálculo de costo que permita pasar distintos valores y obtener resultados diferentes.

El diseño debe permitir que el sistema principal trate a todas las atracciones de manera similar, aunque cada una responda de acuerdo con sus propias reglas.

---
