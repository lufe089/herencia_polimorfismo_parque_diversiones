# Guía de estudio sore herencia y polimorfismo

Esta guía de estudio busca aclarar conceptos sobre herencia, sobreescritura y polimorfismo. Estos son conceptos que pueden ser confusos y por ello esta guía utilizará un ejemplo de una granja. 

## Herencia - Creando la Clase Animal
*Concepto Clave*: La herencia es un mecanismo para crear una nueva clase (subclase) a partir de una existente (superclase). La nueva clase adquiere todas las propiedades y métodos de la clase base. Esto permite reutilizar código y organizarlo en una jerarquía lógica.

La Analogía: Pensemos en nuestra granja. Hay muchos animales, pero todos comparten un conjunto de características y comportamientos básicos. Todos tienen un nombre, todos comen. Podemos crear un plano general, una categoría Animal, que defina todo lo que es común a cualquier ser vivo en nuestra granja.

Piensa en esto: Antes de construir una casa, necesitas un plano. Un plano que dice "toda casa tiene paredes, un techo y una puerta". No dice si es de madera o de ladrillo, solo define lo esencial.

En nuestra granja, antes de tener vacas o gallinas, necesitamos un plano para lo más básico: un "Animal". ¿Qué tiene todo animal de nuestra granja? Pues tiene un nombre. Y hace varias cosas: come, se mueve y hace un ruido.

Ese plano, esa idea general de "Animal", es nuestra clase base. Y la acción de crear otros planos más específicos (como el plano de "Vaca") a partir de este plano general, se llama *Herencia*. La idea es simple: "una Vaca es un tipo de Animal". Por lo tanto, todo lo que define a un Animal, una Vaca también lo tendrá.

```cpp
Animal.h
#ifndef ANIMAL_H
#define ANIMAL_H

#include <string>

// Este es el plano general para cualquier animal.
class Animal {
public:
    // Constructor: la instrucción para crear un animal nuevo.
    Animal(std::string nombre);

    // Comportamientos que cualquier animal tendrá.
    void comer();
    void moverse(int distancia);
    void hacer_sonido();

protected:
    // Una característica interna que las clases que hereden podrán ver.
    std::string nombre;
};

#endif // ANIMAL_H
```

```cpp
Animal.cpp
#include "Animal.h"
#include <iostream>

// Así se construye un animal, guardando su nombre.
Animal::Animal(std::string nombre) {
    this->nombre = nombre;
}

// Así es como un animal genérico come.
void Animal::comer() {
    std::cout << nombre << " está comiendo pienso genérico." << std::endl;
}

// Así es como un animal genérico se mueve.
void Animal::moverse(int distancia) {
    std::cout << nombre << " se ha movido " << distancia << " metros." << std::endl;
}

// Así es como un animal genérico hace un ruido.
void Animal::hacer_sonido() {
    std::cout << nombre << " emite un sonido genérico." << std::endl;
}
```

### Análisis de código
La clase Animal tiene  un constructor y tres métodos.
El atributo nombre es protected para que las clases derivadas puedan acceder a él directamente.

##  La Adaptación del Comportamiento - Sobreescritura
Bien, ya tenemos nuestro plano de "Animal". Ahora vamos a crear el plano específico para una Vaca. Una vaca, como ya dijimos, es un tipo de Animal. Así que heredará todo lo de Animal.

Pero aquí hay varios caminos que podemos tomar con los comportamientos que heredamos:

*  La Reutilización: A veces, el comportamiento que heredamos esta perfecto. No hay que cambiar nada.
*  La Sobreescritura: Otras veces, el comportamiento heredado es demasiado general y necesitamos adaptarlo para nuestra subclase.
* La Extensión: A veces, el comportamiento del padre es un buen punto de partida, pero queremos hacer eso y algo más.
Vamos a ver los tres aplicados a nuestra Vaca

### El camino de la Reutilización
Piensa en el método `moverse(int distancia)`. Nuestro plano de Animal dice: `std::cout << nombre << " se ha movido " << distancia << " metros." << std::endl;`. ¿Una vaca se mueve? Sí. ¿La descripción "Lola se ha movido 5 metros" es válida para una vaca? Perfectamente. No necesitamos más detalle.

Así que, en nuestro plano de Vaca, simplemente no vamos a mencionar el método moverse. Al no definirlo, le estamos diciendo al compilador: "Oye, para el método moverse, usa la versión que ya tengo de mi clase Animal". Estamos reutilizando el código. 

### El Camino de la Sobreescritura: Cambiando lo que no nos sirve
Ahora, volvamos al método `hacer_sonido()`. Si le decimos a nuestra vaca que "haga un sonido", según el plano de Animal, dirá "emite un sonido genérico". Pero una vaca no hace eso Una vaca muge. "Muuu".

Necesitamos tomar el comportamiento hacer_sonido que heredamos de Animal y cambiarlo por completo. Reemplazarlo por una versión que sea específica para la vaca.

### El Camino de la Extensión: Haciendo lo del padre y algo más
Imagina que en nuestra granja tenemos un protocolo para cuando los animales comen. Primero comen, y luego, si es una vaca como parte del mismo acto, se registran en un "libro de registro de alimentación vacuna" para llevar un control.

El plano de Animal tiene un método comer() que solo dice que está comiendo. Pero para una Vaca, queremos que cuando coma, además de comer, se registre.

¿Cómo hacemos eso? No queremos reescribir desde cero la lógica de "comer". Queremos reutilizarla y luego añadirle algo.

En C++, para llamar a la versión del método de la clase padre desde una clase hija, usamos `NombreDeLaClasePadre::nombre_del_metodo()`.

Este es el código fuente
```cpp
Vaca.h
#ifndef VACA_H
#define VACA_H

#include "Animal.h"

class Vaca : public Animal {
public:
    Vaca(std::string nombre, int litros_leche);
    void producir_leche();

    // Vamos a SOBREESCRIBIR el comportamiento de hacer sonido.
    void hacer_sonido();

    // Vamos a EXTENDER el comportamiento de comer.
    void comer();

private:
    int litros_leche;
};

#endif // VACA_H
```

```cpp
Vaca.cpp
#include "Vaca.h"
#include <iostream>

Vaca::Vaca(std::string nombre, int litros_leche) : Animal(nombre) {
    this->litros_leche = litros_leche;
}

void Vaca::producir_leche() {
    std::cout << nombre << " ha producido " << litros_leche << " litros de leche." << std::endl;
}

// Aquí está la SOBREESCRITURA simple.
void Vaca::hacer_sonido() {
    std::cout << nombre << " muge: Muuuu" << std::endl;
}

// Aquí está la SOBREESCRITURA con EXTENSIÓN.
void Vaca::comer() {
    // Paso 1: Reutilizamos el comportamiento del padre.
    // Llamamos a la versión original del método.
    Animal::comer();

    // Paso 2: Añadimos la NUEVA funcionalidad, la extensión.
    std::cout << "  -> Registrando consumo de hierba para " << nombre << "." << std::endl;
}
```

### Análisis de código
* La línea class Vaca : public Animal establece la herencia.
* `Vaca::Vaca(...) : Animal(nombre)` es la forma en C++ de llamar al constructor de la clase base.
* Vaca añade su propio método `producir_leche`.
* Reutilización (implícita): No escribimos nada para moverse(), así que un objeto Vaca usará la implementación de Animal::moverse() tal cual.
* Sobreescritura: Hemos proporcionado una nueva implementación para hacer_sonido(). Ahora, una vaca siempre mugirá.
* Extensión: Dentro de nuestro nuevo método `comer()`, primero llamamos a `Animal::comer()` para que se ejecute el comportamiento original, y luego añadimos nuestro cout extra. Hacemos lo del padre y algo más.

## Clases Abstractas
Hemos estado trabajando con la idea de un "Animal" genérico que "hace un sonido genérico". Pero en la vida real, no puedes ir a una tienda y comprar "un animal". Compras un perro, un gato, un pez. Cada animal concreto hace un sonido concreto. La idea de "Animal" es solo eso, una idea, una categoría.

En programación, a veces tenemos clases que representan ideas tan generales que no tiene sentido crear un objeto directamente de ellas. Son planos para otros planos. Son lo que llamamos clases abstractas.

Una clase abstracta es una que no se puede instanciar, no se puede crear un objeto a partir de ella. Su único propósito es servir como clase base para que otras hereden de ella.

¿Y cómo marcamos una clase como abstracta en C++?
Haciendo que al menos uno de sus métodos sea "puro virtual". Un método puro virtual es un método que declaramos en la clase base pero que no implementamos. Simplemente ponemos un = 0; al final. Es como decirle a las clases hijas: "Oye, este método es tan importante que tú, sí o sí, tienes que proporcionar una implementación. Yo no te voy a dar una porque no sé cuál sería".

Vamos a convertir nuestro plano de Animal en un plano abstracto. Tiene  más sentido, porque el sonido de un animal es algo que DEBE ser definido por cada animal específico.

```cpp
#ifndef ANIMAL_H
#define ANIMAL_H

#include <string>

// Este ya no es un plano para un animal concreto, sino el plano para CUALQUIER animal.
class Animal {
public:
    Animal(std::string nombre);
    virtual ~Animal();

    void comer();
    void moverse(int distancia);

    // Método puro virtual
    // Estamos obligando a cualquier clase que herede de Animal a definir CÓMO hace su sonido.
    // La clase Animal ahora es abstracta. No puedes hacer 'new Animal("pepito")'.
    virtual void hacer_sonido() = 0;

protected:
    std::string nombre;
};

#endif // ANIMAL_H
```

```cpp
Animal.cpp
#include "Animal.h"
#include <iostream>

Animal::Animal(std::string nombre) {
    this->nombre = nombre;
}

Animal::~Animal() {
    // Destructor virtual.
}

void Animal::comer() {
    std::cout << nombre << " está comiendo pienso genérico." << std::endl;
}

void Animal::moverse(int distancia) {
    std::cout << nombre << " se ha movido " << distancia << " metros." << std::endl;
}

// Ya no hay implementación para hacer_sonido aquí.
// La borramos porque la declaramos como pura virtual (= 0).
```
### Análisis

* Claridad y Contratos: Ahora a clase Animal dice claramente: "Yo no sé cómo hacer un sonido, pero te aseguro que cualquier cosa que sea un Animal sí sabrá cómo hacerlo". Es un contrato. Si creas una clase Perro y no implementas hacer_sonido, el compilador te dará un error. Te está obligando a ser coherente.
  
* Diseño Robusto: Evita que existan "medias clases". Evita que alguien pueda crear un objeto Animal que no hace nada útil. Fuerzas a que todo el mundo trabaje con objetos concretos y bien definidos (Vaca, Gallina, etc.).

## Polimorfismo
Tenemos nuestro plano general Animal. Tenemos un plano específico Vaca que sobrescribe un comportamiento. Podríamos hacer lo mismo para una Gallina (que cacarea), un Cerdo (que gruñe), etc.

Ahora imagina que eres el granjero. Es por la mañana y tienes que despertar a todos. ¿Qué haces? ¿Te vas a la vaca y le dices "muge", luego a la gallina y le dices "cacarea"? Sería un trabajo terrible, sobre todo si tienes 100 animales.

No. Tú, como granjero inteligente, te paras en medio del corral y gritas una sola orden: "Animales, hagan su ruido".

No te diriges a cada uno por su tipo. Te diriges a todos como a lo que son: Animales. Y cada uno, al escuchar esa orden general, responde como sabe responder. La vaca muge, la gallina cacarea.

Eso, es el Polimorfismo. Es la capacidad de tratar a muchos objetos diferentes (vacas, gallinas) como si fueran de un mismo tipo (Animal), enviarles un mismo mensaje (hacer_sonido), y que cada uno responda a su manera.

Para que esto funcione en C++, ya hicimos el trabajo clave en el paso anterior: declaramos hacer_sonido como virtual. Eso le dijo al compilador: "para este método, no decidas nada ahora, espera a que el programa corra y mira qué tipo de animal es en realidad para llamar a la versión correcta".

Este sería la parte del programa del granjero
```cpp
#include <iostream>
#include <vector>
#include "Animal.h"
#include "Vaca.h"

// Necesitamos un plano para la gallina también.
class Gallina : public Animal {
public:
    Gallina(std::string nombre) : Animal(nombre) {}

    void hacer_sonido() {
        std::cout << nombre << " cacarea: Cocorocó" << std::endl;
    }

    void comer() {
        Animal::comer();
        std::cout << "  -> Registrando consumo de maíz para " << nombre << "." << std::endl;
    }
};

// Esta función es la del granjero.
// Recibe una lista de punteros a Animal. No sabe ni le importa si son vacas o gallinas.
void rutina_matutina(std::vector<Animal*>& lista_de_animales) {
    std::cout << "--- El granjero grita: Buenos días ---" << std::endl;
    for (int i = 0; i < lista_de_animales.size(); ++i) {
        // Llamada polimórfica
        // Se le dice al "animal" (sea lo que sea) que haga su sonido.
        lista_de_animales[i]->hacer_sonido();
    }
}

void rutina_alimentacion(std::vector<Animal*>& lista_de_animales) {
    std::cout << "\n--- Es la hora de comer ---" << std::endl;
    for (int i = 0; i < lista_de_animales.size(); ++i) {
        lista_de_animales[i]->comer();
    }
}

int main() {
    // En C++, para manejar objetos de diferentes tipos en una lista,
    // usamos punteros a la clase base.
    std::vector<Animal*> animales_de_la_granja;

    // Creamos los animales y los añadimos a la lista.
    animales_de_la_granja.push_back(new Vaca("Lola", 25));
    animales_de_la_granja.push_back(new Gallina("Clara"));
    animales_de_la_granja.push_back(new Vaca("Petra", 30));

    // El granjero hace sus trabajos.
    rutina_matutina(animales_de_la_granja);
    rutina_alimentacion(animales_de_la_granja);

    // Hay que liberar la memoria que usamos con 'new'.
    for (int i = 0; i < animales_de_la_granja.size(); ++i) {
        delete animales_de_la_granja[i];
    }

    return 0;
}
```
Salida esperada

```cpp
--- El granjero grita: Buenos días ---
Lola muge: Muuuu
Clara cacarea: Cocorocó
Petra muge: Muuuu

--- Es la hora de comer ---
Lola está comiendo pienso genérico.
  -> Registrando consumo de hierba para Lola.
Clara está comiendo pienso genérico.
  -> Registrando consumo de maíz para Clara.
Petra está comiendo pienso genérico.
  -> Registrando consumo de hierba para Petra.
```

### Análisis
Las funciones no tienen ni un if que pregunte "si es una vaca, haz esto, si es una gallina, haz lo otro".
Simplemente iteran y llaman a un método.
>Gracias a la palabra clave `virtual`, el programa se encarga de llamar a la versión correcta en cada caso, tanto para `hacer_sonido` como para `comer`.


## Ejercicio integrador Parque de Diversiones “Aventura Total”

### Contexto general

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

#### 1) Montaña Rusa

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

#### 2) Carrusel

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

#### 3) Casa del Terror

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

#### Resumen comparativo

| Atracción        | Base (COP) | Cargo por hora (COP) | Inspección | Recargo por hora extra (COP) | kWh/hora (estándar) | Variación por efectos |
|------------------|------------|----------------------|------------|------------------------------|---------------------|-----------------------|
| Montaña Rusa     | 50.000     | 8.000                | 10.000 si h>6 | 9.000                       | 20                  | —                     |
| Carrusel         | 20.000     | 3.000                | —          | 3.500                        | 12                  | —                     |
| Casa del Terror  | 30.000     | 5.000                | —          | 0                            | 15                  | +5 kWh/h con efectos  |

---

### Reglas de entrada y salida de datos (para orientar la consola)

- Entrada sugerida por atracción:
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


### Condiciones para el diseño

#### 1) Estructura conceptual obligatoria

El sistema debe incluir **una clase general** que represente una atracción del parque.  
A partir de ella, deben derivarse los tipos específicos de atracciones, cumpliendo las siguientes condiciones:

- Debe existir **al menos un método obligatorio** cuya definición sea diferente para cada tipo de atracción.  
  > (Esto implica que la clase general no puede ser instanciada directamente.)

- Debe existir **al menos un método con comportamiento por defecto** en la clase general, que se use de diferentes formas en las clases específicas:
  - Una atracción debe **heredar ese comportamiento sin modificarlo**.  
  - Otra debe **ampliarlo** (invocar el comportamiento general y **agregar una acción o ajuste adicional**).  
  - Otra debe **sustituirlo completamente** por un nuevo cálculo.

- Debe existir **un método con el mismo nombre pero con una lista de parámetros diferente**, que permita realizar cálculos en otro escenario (por ejemplo, cuando el parque amplía su horario de atención).

> Estas condiciones garantizan la existencia de una clase base no instanciable, la reutilización de comportamiento, y el uso de variaciones reales de comportamiento entre tipos.

---

#### 2) Evidencia explícita de comportamiento dinámico

Los cálculos globales del parque deben realizarse a partir de **una colección de elementos tratados de forma general** (todas las atracciones almacenadas como un mismo tipo base).

El programa debe invocar los métodos de cálculo a través de esta colección, y cada atracción debe responder con su propio comportamiento.

> No se permite usar `if`, `switch` o cualquier otro mecanismo que distinga manualmente el tipo de atracción.

El sistema debe demostrar que, al tratar los objetos como instancias del mismo tipo general, cada uno ejecuta su comportamiento particular según su clase.

---

#### 3) Reutilización y extensión

Dentro de los cálculos:

- Al menos **una atracción** debe **usar el comportamiento general y luego extenderlo**, agregando operaciones adicionales al resultado del cálculo común.  
  (Por ejemplo, sumar un recargo, aplicar una inspección adicional, o modificar un valor parcialmente.)

- Al menos **otra atracción** debe **reemplazar por completo el comportamiento general** con su propio cálculo independiente.

Estas diferencias deben ser **evidentes al ejecutar el programa**, mostrando resultados distintos bajo las mismas condiciones iniciales.

---
El programa debe:

1. Permitir crear distintos tipos de atracciones con sus datos básicos
2. Calcular el costo de funcionamiento para cada tipo de atracción, de acuerdo con sus características particulares.
3. Ofrecer una manera alternativa de calcular el costo cuando se trabaja más tiempo del normal.
4. Calcular el consumo energético aproximado de cada atracción según su tipo.
5. Mostrar por consola un resumen con la información individual y los totales del parque.

