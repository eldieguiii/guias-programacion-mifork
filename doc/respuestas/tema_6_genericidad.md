<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

En lenguajes como C, donde no existe un sistema de tipos genéricos gestionado por el compilador, se suele recurrir a punteros genéricos (`void*`) para crear estructuras de datos capaces de almacenar cualquier tipo. La idea consiste en utilizar un array de punteros `void*`, de manera que cada posición pueda apuntar a una zona de memoria que contenga un dato de cualquier tipo concreto. La estructura no conoce el tipo real de los elementos, solo gestiona direcciones de memoria.

Este enfoque permite reutilizar la misma estructura de datos para almacenar enteros, reales o estructuras definidas por el usuario, pero traslada al programador la responsabilidad de recordar qué tipo hay en cada posición y cómo interpretarlo. El compilador no puede comprobar la corrección de los accesos, ya que toda la información de tipos se ha perdido.

Un ejemplo equivalente en Java consiste en utilizar un array de `Object`. Dado que todas las clases en Java heredan de `Object`, un array de este tipo puede almacenar instancias de cualquier clase. De nuevo, la estructura es flexible, pero no conoce el tipo concreto de los elementos que contiene.

```c
// Ejemplo en C
void* array[10];
int x = 5;
double y = 3.14;
array[0] = &x;
array[1] = &y;
```

```java
// Ejemplo en Java
Object[] array = new Object[10];
array[0] = Integer.valueOf(5);
array[1] = Double.valueOf(3.14);
```

***



## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

La programación genérica consiste en definir algoritmos y estructuras de datos de forma independiente del tipo concreto de los datos con los que trabajan. El objetivo principal es favorecer la reutilización del código sin duplicar implementaciones para cada tipo, manteniendo al mismo tiempo la seguridad de tipos y el soporte del compilador.

Mediante la programación genérica, un mismo contenedor, función o clase puede operar con distintos tipos, y es el usuario quien decide el tipo concreto en el momento de la instanciación. Esta abstracción se realiza de forma explícita, mediante parámetros de tipo, y el compilador puede verificar que los usos son coherentes.

Los ejemplos basados en `void*` en C o `Object` en Java pueden considerarse una forma muy básica y manual de genericidad, pero no constituyen programación genérica propiamente dicha. En ellos no existe un soporte real del lenguaje para los tipos genéricos, sino una simulación que elimina la información de tipos.

Por tanto, aunque el objetivo funcional se parece (reutilizar una estructura para distintos tipos), estos ejemplos no ofrecen las garantías ni las ventajas fundamentales de la programación genérica moderna.

***



## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

El principal problema del uso de `void*` en C es la pérdida total de información de tipos. El compilador no puede comprobar si el acceso a un elemento es correcto, ni si se está interpretando la memoria con el tipo adecuado. Esto puede provocar errores sutiles y difíciles de depurar, como accesos inválidos o resultados incorrectos.

En Java, aunque `Object` mantiene cierta información en tiempo de ejecución, el problema aparece al recuperar los elementos almacenados. Es necesario realizar un *downcasting* explícito para convertir un `Object` al tipo esperado, lo que puede derivar en excepciones `ClassCastException` si el tipo no coincide.

Además, ambos enfoques permiten mezclar libremente distintos tipos dentro de la misma estructura, sin ningún tipo de restricción. Esto hace que muchos errores solo se detecten en tiempo de ejecución, en lugar de ser detectados por el compilador durante la fase de desarrollo.

En consecuencia, se incrementa el riesgo de errores y se pierde una de las principales ventajas de los lenguajes tipados: la detección temprana de incoherencias en el uso de los tipos.

***



## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

Los parámetros de tipo son una forma de introducir variables de tipo en la definición de clases, interfaces o métodos. En lugar de especificar un tipo concreto, se define un parámetro simbólico (como `T`, `E` o `K`) que representará un tipo real elegido más adelante.

Estos parámetros se sustituyen por tipos concretos en el momento de la instanciación o de la llamada al método. De este modo, se consigue que una misma definición funcione con distintos tipos, sin perder información de tipos para el compilador.

Gracias a los parámetros de tipo, el compilador puede garantizar que todos los usos de un tipo genérico son coherentes. Por ejemplo, puede impedir que se inserte un elemento de tipo incorrecto en una colección o que se realicen conversiones inseguras.

En definitiva, los parámetros de tipo permiten combinar reutilización de código, expresividad y seguridad de tipos, resolviendo los problemas asociados al uso de `void*` o `Object`.

***



## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

En Java, las colecciones de la biblioteca estándar están parametrizadas mediante generics. Al instanciar una lista indicando `String` como parámetro de tipo, se fuerza que solo se puedan insertar objetos de ese tipo, y se evita cualquier conversión explícita al recuperar los elementos.

Durante el recorrido de la lista, el compilador garantiza que cada elemento es un `String`, lo que permite trabajar con él directamente sin riesgo de errores de tipo en tiempo de ejecución.

En C++, el mecanismo equivalente son las plantillas. La clase `std::vector` es una plantilla que se instancia con un tipo concreto, y el compilador genera una versión especializada del contenedor para ese tipo. De nuevo, se obtiene seguridad de tipos completa.

```java
// Java
import java.util.*;

List<String> lista = new ArrayList<>();
lista.add("uno");
lista.add("dos");

for (String s : lista) {
    System.out.println(s.toUpperCase());
}
```

```cpp
// C++
#include <vector>
#include <string>
#include <iostream>

std::vector<std::string> v;
v.push_back("uno");
v.push_back("dos");

for (const std::string& s : v) {
    std::cout << s << std::endl;
}
```

***



## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

En C++, cuando se instancia una plantilla con un tipo concreto, el compilador genera una versión específica de la clase o función para ese tipo. Este proceso se denomina instanciación de plantillas y ocurre completamente en tiempo de compilación. Cada tipo distinto produce código distinto.

En Java, el funcionamiento es diferente. El compilador utiliza los parámetros de tipo para realizar comprobaciones de tipos en tiempo de compilación, pero posteriormente elimina esa información mediante un proceso llamado *type erasure*. En tiempo de ejecución, los tipos genéricos se reemplazan por sus límites (normalmente `Object`).

Como consecuencia, en Java solo existe una versión del código genérico en tiempo de ejecución, mientras que en C++ pueden coexistir múltiples versiones especializadas. Esto explica por qué Java no permite ciertas operaciones con genéricos que sí son posibles en C++, como crear arrays de tipos genéricos.

En resumen, C++ prioriza la generación de código especializado, mientras que Java prioriza la compatibilidad con versiones anteriores de la JVM mediante el borrado de tipos.

***



## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

Una clase genérica con dos parámetros de tipo permite modelar estructuras que asocian dos valores heterogéneos sin perder información de tipos. Este patrón es común para representar pares clave–valor, resultados múltiples o estructuras auxiliares.

La clase `Par` puede definirse con dos parámetros de tipo, por ejemplo `A` y `B`, y almacenar internamente un valor de cada uno. El compilador garantizará que los accesos a cada elemento devuelven el tipo correcto.

Un caso de uso típico consiste en devolver más de un resultado desde un método, como la media y la desviación típica calculadas a partir de un conjunto de datos.

```java
public class Par<A, B> {
    private final A primero;
    private final B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero() {
        return primero;
    }

    public B getSegundo() {
        return segundo;
    }
}

// Ejemplo de uso
public static Par<Double, Double> estadisticas(double[] datos) {
    double media = ...;
    double desviacion = ...;
    return new Par<>(media, desviacion);
}
```

***



## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

En Java, los parámetros de tipo no están limitados al nivel de clase, sino que también pueden declararse a nivel de método. Esto es útil cuando la genericidad solo afecta a una operación concreta y no al estado de un objeto.

Un método definido con parámetros de tipo permite expresar relaciones entre sus argumentos y su valor de retorno. En el caso de `seleccionaUno`, se puede exigir que ambos objetos sean del mismo tipo y que el resultado sea exactamente ese tipo.

Frente a una versión basada en `Object`, la versión genérica evita el *downcasting* y detecta errores en tiempo de compilación, reforzando el chequeo de tipos y la claridad del código.

```java
// Versión con Object
Object seleccionaUno(Object a, Object b) { ... }

// Versión genérica
public static <T> T seleccionaUno(T a, T b) {
    return Math.random() < 0.5 ? a : b;
}
```

***



## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

Java permite restringir los parámetros de tipo mediante *bounds*, usando la palabra clave `extends`. Esto permite asegurar que el tipo genérico cumple ciertos requisitos, como heredar de una clase concreta o implementar una interfaz.

Una solución simple para un `Punto` consiste en usar `Number` como tipo de las coordenadas. Esto permite trabajar con cualquier tipo numérico, pero el tipo exacto se pierde y los métodos devuelven `Number`.

Una solución más precisa consiste en parametrizar la clase con `<T extends Number>`, lo que refuerza el chequeo de tipos y mantiene la coherencia entre las coordenadas.

Tras el *type erasure*, ambos enfoques se convierten internamente en el uso de `Number` como tipo base.

```java
// Sin generics
class Punto {
    private Number x, y;
}

// Con generics
class Punto<T extends Number> {
    private T x, y;
}
```

***



## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

La solución basada solo en `Number` permite crear puntos con coordenadas de distintos tipos numéricos, por ejemplo un entero y un real, ya que ambos son subtipos de `Number`. Sin embargo, esta flexibilidad puede ocultar incoherencias conceptuales.

Con la solución genérica, ambas coordenadas deben ser del mismo tipo concreto, ya que se utiliza un único parámetro de tipo `T`. Esto refuerza la coherencia interna del objeto y permite al compilador detectar usos incorrectos.

En cuanto a los métodos de acceso, en la versión sin generics `getX` devuelve un `Number`, mientras que en la versión genérica devuelve exactamente el tipo `T`. Esto evita conversiones posteriores y mejora la expresividad del código.

***



## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

***

Mediante el uso de genéricos autorreferenciados (*F-bounded polymorphism*), se puede asegurar que el método `distanciaA` solo opera con puntos del mismo tipo concreto. La interfaz se parametriza con su propio subtipo.

De este modo, cada implementación puede especificar su tipo exacto, y el compilador garantiza que no se mezclan puntos de distintas dimensiones. Se elimina la necesidad de `instanceof` y de conversiones explícitas.

Este enfoque refuerza el polimorfismo seguro y traslada los errores potenciales de ejecución al tiempo de compilación.

```java
public interface Punto<T extends Punto<T>> {
    double distanciaA(T p);
}

public class Punto2D implements Punto<Punto2D> {
    ...
    public double distanciaA(Punto2D p) { ... }
}
```

***



## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

Aunque `String` es subtipo de `Object`, `List<String>` no es subtipo de `List<Object>`. Los tipos genéricos en Java son invariantes, ya que permitir esa relación rompería la seguridad de tipos permitiendo insertar objetos incorrectos en la lista.

En cambio, los arrays en Java son covariantes, por lo que `String[]` sí es subtipo de `Object[]`. Esto provoca que ciertos errores solo se detecten en tiempo de ejecución, como una `ArrayStoreException`.

Un tipo es covariante si conserva la relación de subtipos, contravariante si la invierte, e invariante si no existe relación entre las instancias genéricas aunque exista entre los tipos base.

***



## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

Un *wildcard* (`?`) representa un tipo desconocido. Permite recuperar cierto grado de covarianza o contravarianza de forma controlada en los genéricos de Java, manteniendo la seguridad de tipos.

`? extends T` se utiliza cuando se quiere leer valores de una estructura y tratarlos como `T` o sus subtipos. En cambio, `? super T` se usa cuando se quiere escribir valores de tipo `T` en la estructura.

Estos mecanismos siguen el principio *PECS* (Producer Extends, Consumer Super).

```java
// Suma de números: solo lectura
double suma(List<? extends Number> lista) { ... }

// Añadir enteros: escritura
void añadeEnteros(List<? super Integer> lista) { ... }
```

Este enfoque permite recuperar flexibilidad sin renunciar al chequeo de tipos en tiempo de compilación.

***
