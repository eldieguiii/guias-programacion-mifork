<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

Un puntero a una función es una variable que almacena la dirección de memoria de una función. Igual que ocurre con los punteros a datos en C, permite indirectamente invocar una función sin conocer su implementación concreta en tiempo de compilación. Este mecanismo resulta especialmente útil para implementar callbacks, pasar comportamiento como argumento o desacoplar la llamada de la función concreta que se ejecutará.

Desde el punto de vista sintáctico, un puntero a función debe declarar el tipo de retorno y los tipos de sus parámetros. La llamada se realiza usando el propio puntero como si fuera el nombre de la función, lo que da lugar a un estilo de programación más flexible, aunque también más propenso a errores si no se maneja con cuidado.

En C, los punteros a funciones no capturan contexto ni estado adicional: únicamente hacen referencia a código ejecutable. Por tanto, cualquier dato adicional debe pasarse explícitamente como parámetro.

```c
#include <stdio.h>
#include <ctype.h>

char* aMayusculasFunc(char* cadena) {
    for (int i = 0; cadena[i] != '\0'; i++) {
        cadena[i] = toupper(cadena[i]);
    }
    return cadena;
}

int main() {
    char texto[] = "hola mundo";
    char* (*aMayusculas)(char*) = aMayusculasFunc;

    printf("%s\n", aMayusculas(texto));
    return 0;
}
```

***



## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

Una función lambda es una función anónima que puede definirse y usarse directamente como un valor. Este tipo de funciones permite expresar comportamientos de forma concisa, sin necesidad de declarar una función con nombre. Son habituales en lenguajes que favorecen estilos funcionales y resultan muy útiles para operaciones cortas y expresivas.

En JavaScript, las funciones son ciudadanos de primera clase, por lo que las lambdas (funciones flecha) pueden asignarse a variables, pasarse como parámetros o devolverse desde otras funciones sin restricciones especiales. La sintaxis es ligera y favorece la legibilidad cuando se trabajan transformaciones simples.

En Java, las funciones lambda aparecen a partir de Java 8 y están fuertemente tipadas. Una lambda siempre se asigna a una referencia cuyo tipo es una interfaz funcional, como `Function<String, String>`. Esto permite integrar el estilo funcional sin abandonar el sistema de tipos estático del lenguaje.

```javascript
// JavaScript
let aMayusculas = s => s.toUpperCase();
console.log(aMayusculas("hola mundo"));
```

```java
// Java
Function<String, String> aMayusculas = s -> s.toUpperCase();
System.out.println(aMayusculas.apply("hola mundo"));
```

***



## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

El paradigma funcional es un estilo de programación que se centra en el uso de funciones puras, la inmutabilidad de los datos y la composición de funciones. En este paradigma, el énfasis no se pone en el estado mutable o en los objetos, sino en la transformación de datos mediante funciones. Esto favorece programas más predecibles y fáciles de razonar.

Lenguajes inicialmente orientados a objetos, como Java a partir de la versión 8, incorporan características del paradigma funcional. Por esta razón se les denomina lenguajes multi‑paradigma, ya que permiten combinar programación orientada a objetos, imperativa y funcional dentro del mismo código.

Decir que las funciones son ciudadanos de primera clase implica que se tratan igual que cualquier otro valor. Esto significa que pueden almacenarse en variables, pasarse como argumentos a métodos y devolverse como resultado. Esta propiedad es fundamental para poder programar de manera funcional.

***



## 4. Explica la sintaxis básica de una función lambda en Java.

La sintaxis básica de una función lambda en Java consta de tres partes: la lista de parámetros, el operador `->` y el cuerpo de la función. Los parámetros pueden ir entre paréntesis o no, dependiendo de si hay uno solo, y su tipo suele inferirse automáticamente a partir del contexto.

El cuerpo de la lambda puede ser una sola expresión o un bloque de instrucciones encerrado entre llaves. Si es una expresión, su valor se devuelve implícitamente; si es un bloque, se debe usar la palabra clave `return` cuando el tipo de retorno no es `void`.

Las lambdas en Java no existen de forma aislada, sino que siempre se asignan a una interfaz funcional concreta. El compilador usa el método abstracto de dicha interfaz para verificar que la lambda tiene la firma correcta.

```java
Function<String, String> aMayusculas = (String s) -> {
    return s.toUpperCase();
};
```

***



## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

Recibir una función como parámetro permite desacoplar el algoritmo principal del comportamiento concreto que se desea aplicar. El método `transformar` recibe una cadena y una función transformadora, y delega en esta última la operación específica a realizar sobre el texto.

En JavaScript, esta técnica es muy natural, ya que las funciones forman parte del núcleo del lenguaje. En Java, se consigue un efecto similar usando interfaces funcionales, lo que mantiene la seguridad de tipos característica del lenguaje.

Este enfoque mejora la reutilización y la extensibilidad del código, ya que el método `transformar` no necesita modificarse para aplicar nuevas transformaciones.

```javascript
function transformar(cadena, aMayusculas) {
    return aMayusculas(cadena);
}
```

```java
static String transformar(String cadena, Function<String, String> aMayusculas) {
    return aMayusculas.apply(cadena);
}
```

***



## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

Una de las ventajas principales de las funciones lambda es que permiten definir el comportamiento justo en el punto donde se necesita. En lugar de declarar la función previamente, se puede escribir la lambda directamente en la llamada al método.

Esto reduce la cantidad de código adicional y hace que la lógica sea más local y expresiva. Es especialmente útil cuando la función es corta y específica para un único uso.

El ejemplo siguiente muestra cómo invertir una cadena pasando la lambda directamente al método `transformar`, sin necesidad de declarar una variable intermedia.

```java
String resultado = transformar("hola", s -> new StringBuilder(s).reverse().toString());
```

***



## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

Un cierre, o closure, se produce cuando una función lambda captura variables del contexto donde ha sido definida. Esto permite que la función recuerde y utilice valores externos incluso después de que el ámbito original haya terminado su ejecución.

En Java, las variables locales capturadas por una lambda deben ser efectivamente finales, lo que garantiza que no cambien y evita incoherencias. Aun así, la lambda puede acceder a ellas libremente para realizar su lógica interna.

El siguiente ejemplo muestra cómo una lambda concatena una cadena externa al texto de entrada, cerrando sobre una variable local definida fuera de la lambda.

```java
String sufijo = "!!!";
Function<String, String> transformar = s -> s + sufijo;
```

***



## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

Un puntero a función en C es simplemente una dirección de memoria sin información adicional. No captura contexto ni estado, por lo que cualquier dato necesario debe pasarse explícitamente como argumento en cada llamada.

Una función lambda, en cambio, puede cerrar sobre variables de su entorno, lo que permite asociar comportamiento y datos de forma natural. Esto hace que las lambdas sean más expresivas y cercanas al concepto de objeto con estado inmutable.

Además, en lenguajes como Java, las lambdas están integradas en el sistema de tipos y en bibliotecas estándar, lo que mejora la seguridad y la capacidad de abstracción respecto a los punteros a funciones tradicionales.

***



## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

Devolver funciones permite construir comportamientos personalizados de forma dinámica. La función `crearDescuento` devuelve una función que aplica un porcentaje de descuento previamente fijado, encapsulando dicho porcentaje en un closure.

Cada llamada a `crearDescuento` genera una nueva función con su propio porcentaje capturado. Esto permite crear múltiples descuentos independientes sin duplicar código.

La closure se produce porque la función devuelta recuerda el valor de `porcentaje` incluso después de que `crearDescuento` haya terminado su ejecución.

```java
static Function<Double, Double> crearDescuento(double porcentaje) {
    return cantidad -> cantidad * (1 - porcentaje);
}
```

***



## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

Una interfaz funcional es una interfaz que define exactamente un método abstracto. Este método representa la firma que deben cumplir las funciones lambda asociadas a dicha interfaz.

Java permite que las interfaces funcionales tengan métodos `default` o `static`, siempre que exista un único método abstracto. Esto facilita añadir funcionalidad sin romper compatibilidad.

Las interfaces funcionales son la base del soporte de lambdas en Java y permiten una integración segura con el sistema de tipos estático del lenguaje.

***



## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

Definir una interfaz funcional propia permite adaptar las lambdas a dominios concretos del problema. La interfaz `Transformador` representa una transformación de una cadena en otra.

Este tipo de interfaz mejora la legibilidad cuando el significado semántico es más relevante que usar directamente `Function<String, String>`.

```java
@FunctionalInterface
interface Transformador {
    String transformar(String texto);
}
```

***



## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

Al hacer la interfaz funcional genérica, se aumenta su reutilización y flexibilidad. Un `Transformador<T, R>` puede representar cualquier conversión de un tipo a otro.

Esto resulta especialmente útil cuando se trabaja con distintos tipos de datos sin duplicar interfaces. El siguiente ejemplo muestra un transformador que redondea un `Double` a `Integer`.

```java
interface Transformador<T, R> {
    R transformar(T valor);
}

Transformador<Double, Integer> redondear = d -> d.intValue();
```

***



## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

(Esta pregunta está mejor en los apuntes de la libreta)

Java proporciona un amplio conjunto de interfaces funcionales en el paquete `java.util.function`. Entre las más comunes se encuentran `Function<T, R>`, `Consumer<T>`, `Supplier<T>` y `Predicate<T>`.

Existen también variantes especializadas para tipos primitivos, como `IntFunction`, `DoubleConsumer` o `LongPredicate`, que evitan el coste del autoboxing.

Estas interfaces cubren la mayoría de casos habituales y reducen la necesidad de definir interfaces funcionales propias.

***



## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

El método `forEach` permite recorrer colecciones usando un enfoque funcional. En lugar de un bucle explícito, se pasa una función que se ejecuta para cada elemento.

Este estilo hace el código más declarativo, ya que se expresa qué hacer con cada elemento, no cómo recorrer la colección. Resulta especialmente claro para operaciones simples.

```java
List<Integer> numeros = List.of(-2, 3, 0, 5);
numeros.forEach(n -> {
    if (n > 0) {
        System.out.println(n + " es positivo");
    }
});
```

***



## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

PECS significa *Producer Extends, Consumer Super*. Es una regla que indica que si un genérico produce valores, debe usar `extends`, y si consume valores, debe usar `super`.

En el caso de `forEach`, el `Consumer` consume elementos de tipo `T`, por lo que se usa `Consumer<? super T>`. Esto permite mayor flexibilidad al aceptar consumidores que trabajen con superclases de `T`.

Aplicando este principio al método `transformar`, se podría permitir que la función transformadora consuma subtipos de `T` o produzca supertipos de `R`, aumentando la reutilización del método.

***



## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

Las referencias a métodos permiten usar métodos existentes como si fueran lambdas, mejorando la legibilidad cuando no es necesario escribir la lambda explícitamente.

En JavaScript, se puede guardar una referencia a un método siempre que se tenga en cuenta el contexto de `this`. En Java, las referencias a métodos forman parte del lenguaje desde Java 8.

Este mecanismo es especialmente útil para reutilizar métodos ya definidos sin crear código redundante.

```javascript
class Persona {
    constructor(nombre) {
        this.nombre = nombre;
    }
    saludar() {
        console.log("Hola, soy " + this.nombre);
    }
}

let p = new Persona("Ana");
let saludo = p.saludar.bind(p);
saludo();
```

```java
class Persona {
    String nombre;
    void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

Persona p = new Persona("Ana");
Runnable saludo = p::saludar;
saludo.run();
```

***



## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

Java permite referencias a métodos estáticos, a constructores, a métodos de instancia de un objeto concreto y a métodos de instancia sobre cualquier objeto de una clase.

Cada tipo se usa en contextos diferentes, pero todos siguen la misma idea de reutilizar código existente como una función.

Estas referencias hacen el código más conciso cuando la intención ya queda clara con el nombre del método referenciado.

```java
// Método estático
Function<String, Integer> f1 = Integer::parseInt;

// Constructor
Supplier<List<String>> f2 = ArrayList::new;

// Método de instancia de una instancia concreta
Persona p = new Persona("Luis");
Runnable f3 = p::saludar;

// Método de instancia sobre cualquier instancia
Function<String, String> f4 = String::toUpperCase;
```

***



## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

La ordenación con expresiones lambda permite definir comparadores de forma concisa y legible. En el primer caso, se implementa manualmente la comparación, controlando explícitamente todas las condiciones.

La segunda versión aprovecha los métodos auxiliares de `Comparator`, que facilitan la composición y reducen el riesgo de errores. Esto da lugar a un código más declarativo.

Ambas versiones muestran cómo el enfoque funcional mejora la expresividad al trabajar con colecciones complejas.

```java
Collections.sort(personas, (p1, p2) -> {
    int cmp = Integer.compare(p1.getEdad(), p2.getEdad());
    return (cmp != 0) ? cmp : p1.getNombre().compareTo(p2.getNombre());
});
```

```java
Collections.sort(
    personas,
    Comparator.comparing(Persona::getEdad)
              .thenComparing(Persona::getNombre)
);
```

***
