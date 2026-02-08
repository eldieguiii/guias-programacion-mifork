<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

La **encapsulación** y la **ocultación de información** buscan separar claramente *lo que una clase hace* de *cómo lo hace internamente*. La encapsulación agrupa datos y operaciones relacionadas dentro de un mismo objeto, mientras que la ocultación impide que otros componentes accedan directamente a los detalles internos. De esta forma, se evita que el estado del objeto pueda ser manipulado de manera incorrecta o sin control, ya que solo puede modificarse mediante los métodos definidos por la propia clase.

Este mecanismo permite que la implementación interna pueda cambiar sin afectar al código que usa la clase. Por ejemplo, un atributo puede dejar de ser una variable y convertirse en un cálculo, o puede cambiar su estructura interna sin que el exterior lo perciba. Esto hace que el software sea más fácil de mantener, más modular y menos propenso a errores causados por dependencias innecesarias entre partes del programa.

Entre las ventajas principales de la ocultación de información destacan:

*   La reducción del acoplamiento entre componentes, favoreciendo diseños más limpios y mantenibles.
*   La protección del estado interno, evitando inconsistencias y facilitando el cumplimiento de invariantes de clase.
*   La posibilidad de modificar la implementación sin romper el código cliente, aumentando la flexibilidad para evolucionar el programa.

***



## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

La **interfaz pública** de una clase u objeto es el conjunto de métodos y constructores que pueden utilizarse desde fuera de la propia clase. Representa aquello que un usuario del objeto *está autorizado a ver y usar*: cómo se crea una instancia, qué operaciones se pueden realizar sobre ella y qué resultados ofrece. Esta interfaz actúa como un contrato que define el comportamiento observable sin revelar los detalles internos de implementación.

Su relación con la **ocultación de información** es directa, porque la interfaz pública establece el límite entre lo que se expone y lo que se mantiene oculto. Al ofrecer únicamente los métodos necesarios, se protege el estado interno y se evita que otros componentes puedan modificar datos sensibles de manera arbitraria. Gracias a esta separación, resulta posible cambiar la implementación interna sin afectar a quienes utilizan la clase, manteniendo la coherencia y facilitando la evolución del software.

***



## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

La **interfaz pública** debe diseñarse con cuidado porque constituye el contrato que la clase ofrece al exterior. Todo el código que utilice esa clase dependerá directamente de los métodos expuestos, sus nombres, sus parámetros y su comportamiento. Si la interfaz está mal definida —por ejemplo, si expone operaciones innecesarias o ambiguas— se dificulta su uso correcto y se incrementan las posibilidades de acoplamiento excesivo entre módulos.

Modificar la interfaz pública suele ser difícil porque cualquier cambio afecta a todas las partes del programa que la utilicen. Incluso una modificación pequeña, como cambiar un parámetro o el tipo devuelto, puede obligar a revisar y modificar numerosos componentes dependientes. Esto genera coste en tiempo, aumenta el riesgo de introducir errores y complica el mantenimiento del sistema.

Por ese motivo, es recomendable publicar solo lo estrictamente necesario y mantener ocultos todos los detalles internos que puedan cambiar en el futuro. Una interfaz pública estable facilita la evolución de la implementación interna sin romper el código que ya depende de ella, lo que contribuye a la robustez y la flexibilidad del diseño.

***



## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Las **invariantes de clase** son condiciones lógicas que deben cumplirse siempre para que los objetos de una clase se consideren válidos. Estas condiciones deben mantenerse después de ejecutar cualquier constructor y después de cada método público que pueda modificar el estado del objeto. Por ejemplo, en una clase que representa un intervalo numérico, una invariante podría ser que el límite inferior nunca sea mayor que el límite superior, algo que debe cumplirse en todo momento para evitar estados inconsistentes.

La **ocultación de información** ayuda a mantener estas invariantes porque impide que el código externo modifique directamente los atributos internos. Al obligar a que toda modificación del estado se realice a través de métodos controlados, la clase puede verificar y asegurar que cada cambio respeta las invariantes definidas. De esta forma, se reduce el riesgo de que el objeto entre en un estado inválido debido a alteraciones externas o manipulaciones no previstas.

Además, la ocultación permite reorganizar o mejorar la implementación interna sin comprometer la validez del objeto frente al exterior. La clase conserva el control total sobre cómo se mantienen y se verifican sus invariantes, lo que aporta robustez y coherencia al diseño orientado a objetos.

***



## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

Una implementación sencilla que encapsula las coordenadas sería:

```java
public class Punto {
    // Atributos ocultos
    private double x;
    private double y;

    // Constructor (parte de la interfaz pública)
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Método público (parte de la interfaz pública)
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }

    // (Opcional) getters controlados si se necesita exponer lectura
    public double getX() { return x; }
    public double getY() { return y; }
}
```

La **interfaz pública** de la clase `Punto` está formada por su *constructor* `Punto(double x, double y)`, el *método* `calcularDistanciaAOrigen()` y, si se incluyen, los *getters* `getX()` y `getY()`. Estos son los elementos que el código cliente puede invocar; todo lo demás permanece oculto. La representación interna (los atributos `x` e `y`) es un detalle de implementación que puede cambiar (por ejemplo, sustituirse por un array o por otra estructura) sin afectar a quien usa la clase, siempre que se mantenga la misma interfaz pública.

El modificador `**public**` indica que el miembro (clase, constructor o método) es accesible desde cualquier otra clase. El modificador `**private**` limita el acceso al interior de la propia clase, protegiendo el estado interno y evitando manipulaciones directas desde fuera. En comparación con C/C++, esto sería equivalente al uso de *access specifiers* (`public`/`private`) en C++ para controlar visibilidad, pero aquí se aplica en Java para reforzar la encapsulación y separar contrato (interfaz) de implementación (datos y detalles internos).

***



## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

Los modificadores `**public**` y `**private**` en Java pueden aplicarse a **clases**, **constructores**, **métodos** y **atributos**. En el caso de las clases, solo pueden ser `public` o tener visibilidad por defecto (package‑private) cuando son clases *top‑level*, es decir, clases que no están definidas dentro de otra clase. Las clases internas, en cambio, sí pueden ser también `private`, ya que forman parte de la estructura interna de otra clase.

En cuanto a **métodos, atributos y constructores**, pueden declararse tanto `public` como `private`. Esto permite controlar qué operaciones y datos pueden ser accesibles desde el exterior y cuáles deben permanecer ocultos para proteger la implementación interna. De esta manera se implementa la encapsulación: únicamente lo que la clase decide exponer mediante métodos públicos se convierte en parte de su interfaz, mientras que el resto queda reservado para uso interno.

Aplicar correctamente estos modificadores permite separar el contrato público de la clase de los detalles internos que pueden cambiar sin afectar a quien la utilice. Por ello, el uso combinando de `public` y `private` es esencial para mantener la integridad del diseño orientado a objetos y garantizar la consistencia del estado interno de cada objeto.

***



## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

Además de las visibilidades **pública** y **privada**, la Programación Orientada a Objetos suele ofrecer niveles intermedios que permiten un control más matizado del acceso. Estos niveles se utilizan para definir qué partes del programa pueden interactuar con ciertos elementos de una clase, equilibrando encapsulación y reutilización. Cada lenguaje establece sus propias reglas, pero la idea común es ofrecer opciones más flexibles que un simple “todo visible” o “nada visible”.

En **Java** existen cuatro niveles de visibilidad:

*   **public**: accesible desde cualquier parte.
*   **private**: accesible solo dentro de la propia clase.
*   **protected**: accesible desde la misma clase, el mismo paquete y desde clases derivadas, incluso si están en paquetes distintos.
*   **(default)** o *package‑private*: accesible solo dentro del mismo paquete, cuando no se especifica ningún modificador.

Esta variedad permite organizar el código en paquetes y controlar qué se expone realmente, evitando dependencias no deseadas entre módulos. Es especialmente útil en proyectos grandes, donde se desea que ciertos componentes sean visibles solo dentro de un mismo paquete, y no para cualquier otra parte del programa.

En otros lenguajes, los modelos de visibilidad varían. **C++**, por ejemplo, también ofrece `public`, `protected` y `private`, pero el significado de `protected` es algo diferente en cuanto a acceso desde el mismo módulo. **C#** amplía aún más las opciones, añadiendo niveles como `internal` (visible solo dentro del ensamblado) y `protected internal` (una combinación de ambos modelos). Esto demuestra que la visibilidad es un concepto común a la POO, pero cada lenguaje lo adapta a su filosofía y a su sistema de organización del código.

***



## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

Los **miembros privados** de instancia están ocultos para **otras clases** (opción a), **no** para **otras instancias de la misma clase**. En Java, la restricción de `private` se aplica **a nivel de clase**, no a nivel de objeto: cualquier método definido dentro de la clase puede acceder a los campos privados de *cualquier* instancia de esa misma clase, incluida la instancia pasada como parámetro. Esto permite implementar operaciones basadas en el estado interno de dos objetos del mismo tipo sin violar la encapsulación respecto al exterior.

Un ejemplo de método en `Punto` que calcula la distancia a otro `Punto` sería:

```java
public double calcularDistanciaAPunto(Punto otro) {
    // (opcional) comprobación defensiva
    if (otro == null) throw new IllegalArgumentException("El punto 'otro' no puede ser null");
    double dx = this.x - otro.x; // Acceso válido: ambos son campos privados de la misma clase
    double dy = this.y - otro.y;
    return Math.sqrt(dx * dx + dy * dy);
}
```

En este código, el acceso a `otro.x` y `otro.y` es legal porque el método se ejecuta **dentro de la clase `Punto`**, y por tanto tiene permiso para leer los campos privados de cualquier instancia de `Punto`. La ocultación de información sigue cumpliéndose: código externo a la clase no puede acceder directamente a `x` e `y`, pero los métodos de `Punto` sí pueden cooperar entre instancias para implementar comportamiento consistente y seguro.

***



## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

Los métodos **getter** y **setter** son operaciones públicas que permiten **acceder** y **modificar** atributos privados de un objeto de forma controlada. Un *getter* devuelve el valor de un atributo encapsulado, mientras que un *setter* permite cambiarlo verificando previamente, si es necesario, que el nuevo valor mantiene las invariantes de la clase. De este modo, la clase conserva el control sobre cómo se consulta y cómo se altera su estado interno.

En lenguajes orientados a objetos como Java, estos métodos sirven para mantener la **ocultación de información**, evitando exponer directamente los atributos. Gracias a ello, es posible validar datos, registrar cambios, impedir modificaciones no deseadas o incluso cambiar la implementación interna sin afectar a quienes utilizan la clase. Aunque en apariencia un getter y un setter parecen equivalentes a exponer un atributo público, en la práctica ofrecen una capa de control esencial para proteger la consistencia del objeto.

Por lo general, los getters siguen el patrón `getAtributo()` y los setters se nombran como `setAtributo(valor)`. Esta convención facilita la comprensión del código y permite que herramientas externas (por ejemplo, librerías de reflexión o frameworks) detecten fácilmente la interfaz de acceso a los datos del objeto.

***



## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

En este contexto, la palabra **“seguridad”** no se refiere a evitar que el programa pueda ser *hackeado* o atacado desde el exterior. La seguridad aquí tiene un sentido interno: significa proteger el estado del objeto frente a usos incorrectos dentro del propio programa. El objetivo es impedir que otras partes del código modifiquen directamente los atributos y provoquen estados inválidos o inconsistentes.

La **ocultación de información** permite que solo la propia clase pueda cambiar sus datos internos, y siempre a través de métodos que mantengan las invariantes. Esto reduce la probabilidad de errores, porque cada modificación pasa por un punto de control donde la clase decide si el cambio es válido. De este modo, se mejora la fiabilidad y estabilidad del software, independientemente de quién use la clase.

En resumen, la seguridad en este sentido es una seguridad **lógica**, no una seguridad contra atacantes externos. Protege la coherencia interna del programa, no su vulnerabilidad ante exploits o accesos no autorizados desde fuera.

***



## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

Un **miembro de instancia** es un atributo o método cuyo valor pertenece a **cada objeto individual**. Cada instancia de la clase mantiene su propia copia, de modo que modificar el valor en un objeto no afecta al resto. Esto se aplica a variables normales (no estáticas) y a los métodos que operan sobre ellas usando el estado particular de cada instancia.

Un **miembro de clase**, en cambio, pertenece a la **clase en sí**, no a cada objeto. En Java se declaran con el modificador `static`, lo que significa que existe una única copia compartida por todas las instancias. Estos miembros pueden representar información global relacionada con la clase o proporcionar utilidades que no dependen del estado de un objeto concreto.

Los miembros de clase **también pueden ocultarse**. Igual que con los miembros de instancia, pueden declararse `private` para impedir que otras clases accedan directamente a ellos. Esto permite mantener información compartida pero controlada, exponiendo solo métodos públicos si es necesario. El uso de `static` no elimina la posibilidad de aplicar encapsulación; simplemente determina el ámbito al que pertenece el miembro y cómo se comparte entre los objetos.

Por tanto, ambos tipos de miembros pueden gestionarse mediante los mismos principios de visibilidad. La encapsulación sigue siendo aplicable, ya sea protegiendo datos individuales de cada objeto o protegiendo datos compartidos por todos ellos.

***



## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, puede tener sentido que los **constructores sean privados** en determinadas situaciones. Un constructor privado impide que el código externo cree instancias directamente, lo que permite a la propia clase controlar completamente cómo y cuándo se generan los objetos. Esto resulta útil cuando se quiere imponer restricciones de creación o garantizar que las instancias cumplan ciertas condiciones desde el inicio.

Una razón habitual para usar constructores privados es el patrón **Singleton**, donde solo debe existir una única instancia en todo el programa. También se emplean cuando la clase ofrece **métodos factoría estáticos**, encargados de construir objetos de forma controlada, por ejemplo aplicando validaciones, reutilizando instancias existentes o devolviendo subtipos específicos.

Además, la ocultación del constructor ayuda a preservar invariantes y coherencia interna. Al centralizar la creación en métodos estáticos o en mecanismos internos, la clase asegura que no surjan objetos mal construidos o en estados inconsistentes. Por ello, aunque no es la opción más común, el uso de constructores privados es una herramienta valiosa en diseños que requieren control estricto sobre el proceso de construcción.

***



## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

En Java, los **miembros de clase** se indican con el modificador `static`. Esto hace que el miembro pertenezca a la **clase** y no a cada **instancia**, de modo que existe una única copia compartida por todos los objetos. El hecho de ser `static` no afecta a la visibilidad: también pueden declararse `public`, `private`, etc., aplicando los mismos principios de encapsulación que para los miembros de instancia.

A continuación se muestra un ejemplo de `Punto` que mantiene los máximos globales de `x` e `y` observados en **todas** las instancias creadas. Se utilizan **atributos estáticos privados** y **métodos estáticos públicos** para exponer la información sin permitir su modificación directa. La actualización se realiza en el constructor, que es el lugar natural para registrar el estado inicial de cada punto.

```java
public class Punto {
    // Estado de instancia (oculto)
    private double x;
    private double y;

    // Miembros de clase (compartidos por todas las instancias)
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;

    // Constructor: actualiza los máximos globales
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        if (x > maxX) maxX = x;
        if (y > maxY) maxY = y;
    }

    // Ejemplo de método de instancia cualquiera
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }

    // Interfaz pública para consultar los máximos globales (miembros de clase)
    public static double getMaxX() {
        return maxX;
    }

    public static double getMaxY() {
        return maxY;
    }
}
```

Con este diseño, `maxX` y `maxY` **pertenecen a la clase** y reflejan el mayor valor visto entre todos los `Punto` creados hasta el momento. La encapsulación se respeta porque los máximos no son modificables desde fuera (son `private`) y solo se exponen con *getters estáticos* (`public static`). En contextos concurrentes, podría considerarse sincronizar la actualización o usar clases atómicas, pero para un escenario básico secuencial esta solución es suficiente y clara.

***



## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

Un **método factoría** suele ser un método `static` porque crea objetos sin depender de una instancia previa. En este caso, debe recibir dos `double`, redondearlos y construir un `Punto` usando el constructor existente.

El método sería:

```java
public static Punto crearDesdeRedondeo(double x, double y) {
    return new Punto(Math.round(x), Math.round(y));
}
```

Sí, se utiliza `static` porque el método pertenece a la **clase**, no a un objeto concreto. De este modo, se puede invocar como `Punto.crearDesdeRedondeo(…)` sin necesidad de crear antes un `Punto`.

***



## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

Para cambiar la **representación interna** sin alterar la **interfaz pública**, se sustituye el par de atributos `x` e `y` por un **array privado** de dos posiciones. Desde fuera, todo sigue igual: se construye con dos `double` y se llama a `calcularDistanciaAOrigen()`. Este cambio demuestra cómo la ocultación de información permite evolucionar la implementación sin afectar al código cliente.

A continuación, una posible implementación manteniendo el mismo contrato público (incluyendo *getters* si se estaban usando). Obsérvese que el cálculo no cambia; solo cambia dónde se guardan los datos:

```java
public class Punto {
    // Representación interna: array de 2 posiciones [x, y]
    private final double[] coords = new double[2];

    // Interfaz pública (sin cambios): mismo constructor
    public Punto(double x, double y) {
        coords[0] = x; // x
        coords[1] = y; // y
    }

    // Interfaz pública (sin cambios): mismo método
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(coords[0] * coords[0] + coords[1] * coords[1]);
    }

    // (Opcional) getters si formaban parte de la interfaz pública previa
    public double getX() { return coords[0]; }
    public double getY() { return coords[1]; }
}
```

Este enfoque conserva la **interfaz pública estable**, lo que evita modificaciones en el código que ya usa `Punto`. Al mismo tiempo, permite cambiar la estructura interna (por ejemplo, de dos campos a un array) para futuras optimizaciones o extensiones, sin romper a los clientes que dependen de la clase.

***



## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

No es mejor declarar un atributo público aunque tenga un *getter* y un *setter*. Un atributo público expone directamente la representación interna, lo que impide a la clase controlar cómo se modifica su valor. En cambio, un *setter* permite validar datos, lanzar excepciones o mantener invariantes antes de aceptar un cambio. Por eso, incluso cuando exista un *setter*, la encapsulación sigue siendo necesaria para preservar la coherencia del objeto.

La convención más habitual en lenguajes orientados a objetos como Java es declarar **todos los atributos como `private`** y exponer solo los accesos estrictamente necesarios mediante métodos públicos. Este enfoque permite garantizar que cualquier lectura o modificación pase por puntos de control definidos, evitando que el estado interno pueda alterarse de manera arbitraria. Además, facilita cambiar la representación interna sin afectar al código cliente.

Esta práctica está estrechamente relacionada con las **invariantes de clase**, que son las condiciones que el objeto debe cumplir siempre. Si los atributos fuesen públicos, cualquier parte del programa podría modificar el estado sin respetar estas reglas, generando inconsistencias difíciles de detectar. Al mantener los atributos privados y canalizar los cambios mediante setters o métodos modificadores, la clase puede asegurarse de que sus invariantes se cumplen en todo momento y preservar la validez de cada instancia.

***



## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

Una clase es **inmutable** cuando, una vez creada una instancia, su estado no puede cambiar bajo ninguna circunstancia. Esto implica que todos sus atributos deben ser privados y no existir métodos que permitan modificar su contenido. Cualquier operación que "cambie" un dato debe devolver un **nuevo objeto**, manteniendo intacta la instancia original. Este tipo de diseño favorece que los objetos sean más fáciles de razonar, ya que su estado no varía con el tiempo.

Un **método modificador** es cualquier método que altera el estado interno del objeto, ya sea cambiando directamente un atributo o realizando transformaciones que afecten al contenido representado. Aunque muchos modificadores adoptan la forma de *setters*, no todos los modificadores son necesariamente setters: cualquier método que altere uno o varios atributos, incluso indirectamente, entra en esta categoría. Por esta razón, en una clase inmutable no deben existir métodos modificadores de ningún tipo.

La inmutabilidad ofrece varias ventajas. Reduce la posibilidad de errores relacionados con cambios inesperados del estado, facilita el *debugging* y permite compartir instancias de forma segura entre distintas partes del programa sin riesgo de interferencias. Además, en contextos concurrentes o multihilo, elimina la necesidad de sincronizar accesos a los datos, ya que no existe riesgo de modificación simultánea.

Por estas razones, muchos lenguajes y bibliotecas estándar —incluyendo Java con su clase `String`— emplean ampliamente objetos inmutables, ya que mejoran la robustez y la claridad del diseño.

***



## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

No es recomendable incluir métodos **setter** siempre ni convertirlos en una convención automática. Un setter expone la capacidad de modificar un atributo desde fuera de la clase, lo que aumenta el riesgo de romper invariantes, generar estados inválidos o permitir usos inapropiados del objeto. Incluir setters sin necesidad puede debilitar la encapsulación y hacer que el diseño sea menos robusto y más difícil de mantener.

La práctica más extendida en Programación Orientada a Objetos —especialmente en Java— es **crear setters solo cuando realmente hagan falta**. Si un atributo no necesita ser modificado desde fuera, lo correcto es no proporcionar un setter. Esto obliga a pensar con más cuidado en cuál es el comportamiento que la clase debe permitir y qué cambios deben estar controlados internamente.

Limitar los setters también favorece la posibilidad de crear **clases inmutables**, o al menos parcialmente inmutables, lo cual aporta ventajas en claridad, seguridad lógica y facilidad de razonamiento. Una clase con menos setters es una clase más estable, menos propensa a errores, y con una interfaz más pequeña y clara. En resumen, los setters deben ser una decisión de diseño explícita, no un elemento que se incluya por costumbre.

***



## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

La clase **`String`** en Java es **inmutable**, lo que significa que su contenido no puede modificarse una vez creada la instancia. Cada objeto `String` representa una secuencia fija de caracteres que no puede alterarse; cualquier operación que parezca modificarlo en realidad genera un objeto nuevo. Esta decisión de diseño reduce errores, facilita el razonamiento sobre el código y mejora la seguridad lógica del programa.

Al **concatenar dos cadenas**, Java no modifica ninguna de las cadenas originales. En su lugar, crea una **nueva instancia de `String`** con el resultado de la concatenación. Esto implica que expresiones como `a = a + b` generan objetos adicionales cada vez que se ejecutan. En contextos donde se concatenan pocas veces, el coste es irrelevante, pero cuando se concatena repetidamente dentro de un bucle el rendimiento puede degradarse notablemente debido a la creación de múltiples cadenas intermedias.

Cuando se necesita construir una cadena **incrementalmente** o **en muchas operaciones sucesivas**, la práctica recomendada es usar **`StringBuilder`** (o `StringBuffer` en entornos con múltiples hilos). Estas clases son **mutables** y permiten añadir, eliminar o modificar partes de la cadena sin crear nuevos objetos en cada paso. Su uso reduce la sobrecarga en tiempo y memoria, y es el estándar en operaciones de construcción extensiva de texto.

Por tanto, para concatenaciones repetidas, lo aconsejable es seguir un patrón como:

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append("Hola ");
}
String resultado = sb.toString();
```

Este enfoque aprovecha la mutabilidad de `StringBuilder` y evita la creación de cientos o miles de objetos `String` innecesarios, manteniendo la eficiencia y claridad del código.

***



## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

En POO, la comparación entre objetos puede hacerse según **identidad** o según **contenido**. La *identidad* indica si dos referencias apuntan al **mismo objeto en memoria**, mientras que el *contenido* verifica si ambos objetos representan la **misma información**, aunque sean instancias distintas. La elección depende del significado que tenga la clase: a veces importa si dos referencias son exactamente el mismo objeto, pero con mayor frecuencia interesa comparar si “son iguales” según los datos que contienen.

En Java, el método encargado de comparar por contenido es **`equals`**, que todas las clases heredan de `Object`. Su implementación por defecto compara la identidad, es decir, devuelve `true` solo si las dos referencias apuntan al mismo objeto. Para comparar por contenido, una clase debe **sobrescribir (`override`) `equals`** y definir qué cosas hacen que dos instancias se consideren equivalentes. Este diseño permite que cada clase defina su propia noción de igualdad sin depender de la comparación por direcciones de memoria.

En el caso concreto de las cadenas, la clase `String` **sobrescribe `equals`** para comparar carácter por carácter, por lo que dos cadenas con el mismo texto deben compararse usando cadena1.equals(cadena2)

Usar `==` en cadenas solo compara si las dos referencias apuntan al mismo objeto, lo cual no es adecuado en la mayoría de los casos y puede producir errores sutiles. En resumen, `equals` permite comparar contenido, mientras que `==` compara identidad, y la elección correcta depende de lo que se quiera expresar en el programa.

***



## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

En lenguajes orientados a objetos como Java, las **clases *wrapper*** son clases especiales cuyo propósito es **envolver** (*wrap*) un tipo primitivo para poder tratarlo como un objeto. Gracias a ellas, valores como `int`, `double` o `boolean` pueden almacenarse en colecciones genéricas, pasarse como objetos o beneficiarse de métodos adicionales. En Java, las clases wrapper estándar son `Integer`, `Double`, `Boolean`, entre otras, y cada una corresponde a un tipo primitivo.

La conversión entre un tipo primitivo y su wrapper puede hacerse de forma **explícita** (creando un objeto wrapper) o de forma **automática** gracias al mecanismo de *autoboxing* y *unboxing*. El autoboxing convierte un tipo primitivo en su wrapper cuando es necesario, mientras que el unboxing realiza la conversión inversa. Esto facilita el uso de estructuras de datos como `ArrayList<Integer>`, donde se necesitan objetos y no tipos primitivos, sin obligar al programador a crear las instancias manualmente en cada operación.

Entre sus ventajas principales está la posibilidad de almacenar valores primitivos en colecciones, utilizar métodos asociados a los wrappers (por ejemplo, `Integer.parseInt()`), y aprovechar la interoperabilidad con APIs orientadas a objetos. Además, los wrappers pueden representar valores nulos (`null`), lo que permite indicar la ausencia de valor, algo que los tipos primitivos no pueden expresar. Sin embargo, el uso excesivo de wrappers puede ser menos eficiente debido a la creación de objetos adicionales.

No todos los lenguajes orientados a objetos disponen de tipos primitivos separados de los objetos. Lenguajes como **Python** o **Ruby** tratan todos los valores como objetos, por lo que no necesitan clases wrapper. En cambio, lenguajes como **Java** o **C#** mantienen tipos primitivos por razones de eficiencia, y por ello requieren mecanismos equivalentes para integrarlos con estructuras y comportamientos orientados a objetos.

***



## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

Un **tipo de dato enumerado** es un tipo que define un conjunto finito y cerrado de valores posibles, cada uno representado por un nombre simbólico. Se utiliza para modelar conceptos que solo pueden tomar un número limitado de valores, como los meses del año, los días de la semana o los estados de un proceso. En POO, los enumerados ayudan a expresar ideas con mayor claridad, evitando valores numéricos arbitrarios y reduciendo errores al asegurar que solo pueden usarse los valores permitidos.

En **Java**, un `enum` es realmente una **clase especial**, aunque con sintaxis simplificada. Cada valor del enumerado es una **instancia única** (inmutable) de esa clase. Esto implica que un tipo enumerado puede tener atributos privados, métodos, constructores y comportamientos, igual que cualquier otra clase. De hecho, internamente los `enum` extienden de `java.lang.Enum`, lo que les proporciona funcionalidad adicional, como comparación segura, conversión a cadenas o métodos para recorrer los valores definidos.

En términos de **encapsulación**, los enumerados de Java presentan ventajas claras. Al ser clases completas, pueden mantener atributos privados asociados a cada valor del enumerado y exponer solo los métodos necesarios para acceder a ellos. Esto permite ocultar la representación interna y controlar cómo se usa cada constante, evitando que el código externo manipule datos sensibles. Además, los constructores de un `enum` son implícitamente **privados**, lo que evita la creación de valores no previstos y garantiza que el conjunto de instancias sea siempre el definido en el propio enumerado.

En conjunto, los `enum` de Java combinan claridad, seguridad y encapsulación, proporcionando una forma robusta de representar conjuntos limitados de valores con comportamiento asociado sin sacrificar las ventajas del diseño orientado a objetos.

***



## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado. Añade además cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

Se propone un `enum` como una clase especial con **doce instancias** (una por mes), que encapsula dos **atributos privados**: los **días** del mes y su **ordinal 1–12**. El constructor del enumerado (implícitamente privado) fija estos valores y se exponen **métodos públicos** (`getDias()` y `getOrdinal()`) que forman la interfaz, manteniendo la representación interna oculta. De este modo, se conserva la encapsulación y se evita crear instancias fuera del conjunto cerrado de valores.

Respecto a las **estaciones**, se implementan cuatro métodos que indican si un mes tiene días de **primavera, verano, otoño o invierno**, parametrizando el **hemisferio** con un booleano (`esHemisferioNorte`). Para el hemisferio norte se asume: primavera (mar–may), verano (jun–ago), otoño (sep–nov) e invierno (dic–feb); en el hemisferio sur se invierten. Como simplificación, **febrero** se modela con **28 días**; si se quisiese contemplar años bisiestos, podría añadirse lógica externa o un parámetro adicional.

```java
public enum Mes {
    ENERO(31, 1), FEBRERO(28, 2), MARZO(31, 3), ABRIL(30, 4),
    MAYO(31, 5), JUNIO(30, 6), JULIO(31, 7), AGOSTO(31, 8),
    SEPTIEMBRE(30, 9), OCTUBRE(31, 10), NOVIEMBRE(30, 11), DICIEMBRE(31, 12);

    private final int dias;
    private final int ordinal; // 1..12 (diferente de Enum.ordinal(), que es 0..11)

    private Mes(int dias, int ordinal) {
        this.dias = dias;
        this.ordinal = ordinal;
    }

    public int getDias() {
        return dias;
    }

    public int getOrdinal() {
        return ordinal;
    }

    public boolean esDePrimavera(boolean esHemisferioNorte) {
        return esHemisferioNorte
                ? (ordinal >= 3 && ordinal <= 5)   // mar, abr, may
                : (ordinal >= 9 && ordinal <= 11); // sep, oct, nov
    }

    public boolean esDeVerano(boolean esHemisferioNorte) {
        return esHemisferioNorte
                ? (ordinal >= 6 && ordinal <= 8)   // jun, jul, ago
                : (ordinal == 12 || ordinal <= 2); // dic, ene, feb
    }

    public boolean esDeOtoño(boolean esHemisferioNorte) {
        return esHemisferioNorte
                ? (ordinal >= 9 && ordinal <= 11)  // sep, oct, nov
                : (ordinal >= 3 && ordinal <= 5);  // mar, abr, may
    }

    public boolean esDeInvierno(boolean esHemisferioNorte) {
        return esHemisferioNorte
                ? (ordinal == 12 || ordinal <= 2)  // dic, ene, feb
                : (ordinal >= 6 && ordinal <= 8);  // jun, jul, ago
    }
}
```
