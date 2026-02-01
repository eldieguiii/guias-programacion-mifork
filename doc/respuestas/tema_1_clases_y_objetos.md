<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Clases y Objetos". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: ninguno.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 1. Clases y objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

Las cuatro características fundamentales de la programación orientada a objetos son **encapsulación**, **abstracción**, **herencia** y **polimorfismo**. La **encapsulación** agrupa los datos y las funciones que los manipulan dentro de una misma entidad llamada *clase*. Esta característica permite restringir el acceso a los detalles internos mediante modificadores de acceso, como `private` o `public` en Java, protegiendo así el estado del objeto y evitando que sea modificado de manera incorrecta desde el exterior.

La **abstracción** se centra en mostrar únicamente la información esencial de un objeto, ocultando los detalles que no son relevantes para su uso. Mediante este principio, una clase define qué atributos y comportamientos son indispensables para representar una idea, sin necesidad de conocer cómo están implementados internamente. Esto simplifica el razonamiento sobre el código y facilita su mantenimiento.

La **herencia** permite crear nuevas clases a partir de otras ya existentes, heredando automáticamente atributos y métodos. Gracias a este mecanismo, una clase derivada puede reutilizar código sin necesidad de duplicarlo, además de poder añadir nuevas funcionalidades o modificar las heredadas. Es una forma de organizar jerarquías lógicas que favorecen la reutilización y la extensibilidad del software.

Finalmente, el **polimorfismo** posibilita que un mismo método pueda comportarse de manera diferente según el tipo del objeto que lo invoque. Esto se consigue principalmente mediante la sobrescritura de métodos en las subclases y el uso de referencias del tipo de la superclase. El resultado es un código más flexible, capaz de trabajar con distintos tipos concretos sin necesidad de modificar su estructura general.

***



## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

Entre los lenguajes más populares que soportan programación orientada a objetos se encuentran **Java**, **C++**, **Python** y **C#**. Java fue diseñado desde el inicio con la orientación a objetos como núcleo del lenguaje, lo que permite trabajar siempre con clases y objetos de manera estructurada. Este enfoque lo convierte en una opción habitual para aprender POO y para desarrollar aplicaciones empresariales, móviles y de servidor.

C++ combina la programación procedimental con la orientada a objetos, lo que facilita la transición para quienes provienen de C —como en tu caso—. Permite crear clases, herencia, polimorfismo y encapsulación, manteniendo al mismo tiempo un control detallado de la memoria. Python, por su parte, ofrece una sintaxis sencilla y una aproximación flexible, permitiendo la programación orientada a objetos sin obligar a utilizarla de forma estricta, pero proporcionando todas las características fundamentales.

Finalmente, C# es un lenguaje moderno desarrollado por Microsoft que integra de forma muy completa todos los principios de la programación orientada a objetos. Suele emplearse en desarrollo de aplicaciones de escritorio, videojuegos con Unity y entornos empresariales. Su diseño facilita el uso coherente de clases y objetos, lo que lo convierte en un lenguaje accesible y robusto para trabajar con POO.

***



## 3. Los paradigmas anteriores a la POO: ¿Qué es la **programación estructurada**? y, todavía mejor, ¿Qué es la **programación modular**?

La **programación estructurada** es un paradigma que surgió para mejorar la claridad y la fiabilidad del código, evitando el uso indiscriminado de saltos como `goto`. Este enfoque propone construir los programas utilizando únicamente tres tipos de estructuras: secuencias, decisiones (`if`, `switch`) y bucles (`while`, `for`). Gracias a estas restricciones, se obtiene un flujo de control más predecible y ordenado, lo que reduce errores y facilita la lectura del programa. En lenguajes como C, este paradigma se aplica de forma natural debido a su diseño orientado a bloques y funciones simples.

Por otro lado, la **programación modular** se basa en dividir un programa grande en partes más pequeñas, independientes y fáciles de gestionar, llamadas módulos. Cada módulo se encarga de una tarea concreta y posee una interfaz bien definida para interactuar con el resto del programa. En C, esto se observa claramente en la separación entre ficheros `.h` y `.c`, donde la interfaz se declara en el encabezado y la implementación se coloca en el código fuente. Esta división permite desarrollar, probar y mantener cada parte del software de forma aislada.

Además, la programación modular favorece la reutilización del código, ya que un módulo bien diseñado puede emplearse en distintos programas sin necesidad de modificar su interior. Esta técnica también mejora la organización del proyecto, permitiendo que varias personas trabajen en partes distintas del sistema sin interferir entre sí. Aunque no utiliza conceptos de clases u objetos, la modularidad se considera una base importante sobre la que posteriormente se construyó la programación orientada a objetos.

***



## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

Un objeto se define principalmente por **sus atributos**, que representan el estado interno del mismo. Estos atributos son variables que almacenan información relevante sobre el objeto, como por ejemplo la velocidad de un coche o el saldo de una cuenta bancaria. Cada instancia de una clase puede tener valores distintos en sus atributos, lo que permite modelar elementos del mundo real con características particulares.

El segundo elemento son los **métodos**, que representan el comportamiento del objeto. Los métodos son funciones asociadas a una clase que operan sobre los atributos para realizar acciones o modificar el estado interno. Este vínculo entre datos y funciones permite describir de manera coherente cómo debe actuar un objeto frente a determinadas situaciones, siguiendo el principio de encapsulación propio de la POO.

El tercer elemento es la **identidad**, que permite distinguir un objeto de otro aunque compartan los mismos atributos y métodos. La identidad garantiza que cada instancia sea única en memoria y pueda ser manipulada de forma independiente. Esto resulta fundamental para trabajar con múltiples objetos de una misma clase sin confundirlos, ya que cada uno mantiene su propio estado y trayectoria de ejecución.

***



## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

Una **clase** es una plantilla o modelo a partir del cual se crean objetos en programación orientada a objetos. En ella se definen los atributos que representan el estado y los métodos que determinan el comportamiento. Puede entenderse como la definición teórica de un tipo de entidad, en la que se especifica qué datos tendrá y qué operaciones podrá realizar. No contiene valores concretos, sino la estructura que usarán los objetos basados en ella.

Un **objeto**, en cambio, es una realización concreta de la clase, es decir, un elemento que posee valores específicos en sus atributos. Esto implica que la clase y el objeto no son lo mismo: la clase describe, mientras que el objeto existe realmente durante la ejecución del programa. Por ejemplo, si la clase es “Coche”, un objeto sería “un coche rojo con 120 km/h de velocidad”, que mantiene su propio estado independiente de otros objetos creados a partir de la misma clase.

El término **instancia** se utiliza para referirse precisamente a un objeto que ha sido creado a partir de una clase. Instanciar consiste en reservar memoria para ese objeto y asignar valores iniciales a sus atributos. Cada instancia es única, incluso cuando proviene de la misma clase que otras, porque mantiene su propio estado y su propia identidad.

No todos los lenguajes orientados a objetos utilizan el concepto de clase de la misma forma, e incluso algunos no lo utilizan en absoluto. Lenguajes como Java, C++ o C# basan su modelo de objetos estrictamente en clases, mientras que otros, como JavaScript, utilizan un sistema basado en prototipos, donde los objetos pueden crearse a partir de otros objetos sin necesidad de definir una clase formal. Esto demuestra que el paradigma orientado a objetos puede implementarse de diversas maneras según el diseño del lenguaje.

***



## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la **recolección de basura**? 

Los objetos suelen almacenarse en memoria **en el heap**, que es una zona destinada a la creación dinámica de datos cuyo tamaño y tiempo de vida no se conocen de antemano. Cuando se instancia un objeto, el lenguaje reserva espacio en el heap para sus atributos y estructura interna, mientras que la variable que lo referencia suele ubicarse en la pila (*stack*) si está definida dentro de una función. Esta separación permite que el objeto siga existiendo incluso cuando el ámbito donde se declaró la variable deja de estar activo, siempre que aún haya referencias apuntando hacia él.

No todos los lenguajes gestionan la memoria de la misma forma. En C++, por ejemplo, los objetos pueden crearse tanto en la pila como en el heap según cómo se declaren, lo que proporciona un control manual sobre su tiempo de vida. En cambio, lenguajes como Java o Python crean los objetos exclusivamente en el heap y proporcionan un sistema automático de gestión de memoria. Esta decisión de diseño afecta a cómo se controla la duración de los objetos y el modelo mental que el programador debe adoptar al trabajar con ellos.

La **recolección de basura** es un mecanismo que libera automáticamente la memoria ocupada por objetos que ya no se utilizan. Para ello, el sistema analiza qué objetos están todavía accesibles desde variables o estructuras activas; aquellos que no pueden alcanzarse se consideran “huérfanos” y se eliminan. Este proceso evita fugas de memoria y reduce la necesidad de gestionar manualmente la destrucción de objetos, aunque puede introducir pausas ocasionales debido al trabajo adicional que debe realizar el recolector.

***



## 7. ¿Qué es un método? ¿Qué es la **sobrecarga de métodos**? 

Un **método** es una función asociada a una clase que describe un comportamiento del objeto. Mientras que en programación estructurada las funciones se encuentran separadas de los datos, en programación orientada a objetos los métodos operan directamente sobre los atributos del objeto, permitiendo modificar su estado o realizar acciones relacionadas con él. De este modo, el método forma parte esencial de la definición de la clase, ya que determina cómo deben comportarse sus instancias ante determinadas operaciones.

La **sobrecarga de métodos** consiste en definir varios métodos con el mismo nombre dentro de una misma clase, pero diferenciados por el número o el tipo de sus parámetros. Gracias a esta característica, es posible ofrecer distintas versiones de una misma operación adaptadas a diferentes tipos de entrada, manteniendo un nombre común para facilitar su uso. El compilador selecciona automáticamente cuál de las versiones debe ejecutarse en función de los argumentos proporcionados, lo que permite escribir código más claro y flexible sin necesidad de inventar nombres distintos para operaciones conceptualmente similares.

***



## 8. Ejemplo mínimo de clase en Java, que se llame Punto, con dos atributos, x e y, con un método que se llame `calculaDistanciaAOrigen`, que calcule la distancia a la posición 0,0. Por sencillez, los atributos deben tener visibilidad por defecto. Crea además un ejemplo de uso con una instancia y uso del método

Una clase mínima en Java puede definirse especificando sus **atributos** y **métodos** dentro de una estructura `class`. Para ajustarse a la petición, los atributos `x` e `y` tendrán **visibilidad por defecto** (sin modificador), lo que implica acceso de *paquete* (package-private). El método `calculaDistanciaAOrigen` devolverá la distancia euclídea desde el punto hasta `(0, 0)`, aplicando la fórmula √(x² + y²). Se usará `Math.sqrt` y `Math.pow` para el cálculo.

A continuación se muestra la clase `Punto` y, después, un ejemplo de uso creando una instancia y llamando al método. En un proyecto real, convendría además añadir constructores y validaciones según necesidades, pero aquí se mantiene el ejemplo en su forma más simple para resaltar la estructura básica de clase, atributos y método.

```java 
// Archivo: Punto.java
class Punto {
    // Atributos con visibilidad por defecto (package-private)
    double x;
    double y;

    // Método que calcula la distancia al origen (0,0)
    double calculaDistanciaAOrigen() {
        return Math.sqrt(Math.pow(x, 2) + Math.pow(y, 2));
    }
}
```

***

```java
// Archivo: EjemploUso.java
public class EjemploUso {
    public static void main(String[] args) {
        Punto p = new Punto();
        p.x = 3.0;
        p.y = 4.0;

        double d = p.calculaDistanciaAOrigen();
        System.out.println("Distancia al origen: " + d); // Imprime 5.0
    }
}
```

***



## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es `static` y para qué vale? ¿Sólo se emplea para ese método `main`? ¿Para qué se combina con `final`?

El punto de entrada de un programa en Java es siempre el método `main`, cuya firma debe ser exactamente `public static void main(String[] args)`. Este método actúa como el inicio de la ejecución, de forma similar a `int main()` en C, pero adaptado al modelo orientado a objetos del lenguaje. Sin esta firma concreta, la JVM no reconoce dónde debe comenzar el programa, por lo que no es posible ejecutar la aplicación. Aunque un proyecto pueda tener muchas clases, solo aquellas que definan un método `main` con esa forma pueden servir como punto de inicio.

La palabra clave **`static`** indica que un elemento pertenece a la clase y no a una instancia concreta. En el caso del método `main`, esto permite que la JVM lo invoque sin necesidad de crear primero un objeto de la clase que lo contiene. Este mecanismo se utiliza también para atributos o métodos que deben compartirse entre todas las instancias, como contadores globales, constantes o utilidades generales. Por tanto, su uso no está limitado al método `main`, sino que forma parte habitual del diseño de estructuras que no dependen del estado individual de los objetos.

El modificador `static` puede combinarse con **`final`**, lo que suele emplearse para declarar constantes. Un atributo `static final` pertenece a la clase y, además, no puede modificarse una vez asignado. Esto se usa normalmente para valores inmutables que deben ser accesibles sin crear instancias, como `Math.PI` o configuraciones que no cambian durante la ejecución. Esta combinación favorece un código más seguro, ya que evita modificaciones accidentales y deja claro el propósito de esos valores dentro del programa.

***



## 10. Intenta ejecutar un poco de Java de forma básica, con los comandos `javac` y `java`. ¿Cómo podemos compilar el programa y ejecutarlo desde linea de comandos? ¿Java es compilado? ¿Qué es la **máquina virtual**? ¿Qué es el *byte-code* y los ficheros `.class`?

Para compilar un programa Java desde la línea de comandos se utiliza primero el comando `javac`, que traduce el código fuente del archivo `.java` a bytecode. Por ejemplo, si se tiene un archivo llamado `MiPrograma.java`, basta ejecutar `javac MiPrograma.java` para generar un fichero `MiPrograma.class`. Una vez compilado, el programa puede ejecutarse mediante el comando `java`, indicando el nombre de la clase que contiene el método `main`, sin la extensión: `java MiPrograma`. Este proceso es similar al flujo de compilación y ejecución en C, pero con una diferencia importante en el tipo de código generado y cómo se ejecuta posteriormente.

Java puede considerarse un lenguaje **compilado e interpretado a la vez**. El código fuente se compila a bytecode, un formato intermedio independiente del sistema operativo y del procesador. Ese bytecode no se ejecuta directamente por la CPU, sino que es interpretado o compilado en tiempo de ejecución por la **Máquina Virtual de Java (JVM)**. Esta máquina virtual actúa como una capa intermedia entre el programa y el sistema físico, ofreciendo portabilidad y seguridad. Gracias a ella, un mismo programa puede ejecutarse en diferentes plataformas sin necesidad de recompilarlo, siempre que exista una JVM instalada.

El **bytecode** es el conjunto de instrucciones intermedias que genera el compilador `javac`, y está almacenado en los archivos `.class`. Estos ficheros contienen la representación del programa en un formato entendido por la JVM, que luego lo interpreta o lo optimiza mediante técnicas como el *Just-In-Time compilation* (JIT). De esta manera, la ejecución mantiene un buen equilibrio entre portabilidad y rendimiento, evitando la necesidad de compilar el programa específicamente para cada tipo de máquina.

Este modelo de ejecución proporciona varias ventajas: la independencia de la plataforma, la posibilidad de aplicar optimizaciones en tiempo de ejecución y un sistema de seguridad integrado que controla el comportamiento del código. La presencia de bytecode y la intervención de la máquina virtual constituyen la base técnica que distingue a Java frente a lenguajes tradicionales puramente compilados, y explican el funcionamiento de los comandos `javac` y `java` dentro del proceso de desarrollo.

***



## 11. En el código anterior de la clase `Punto` ¿Qué es `new`? ¿Qué es un **constructor**? Pon un ejemplo de constructor en una clase `Empleado` que tenga DNI, nombre y apellidos

Un **`new`** en Java es el operador que **reserva memoria en el heap** y **crea una nueva instancia** de una clase. Al utilizar `new`, se invoca automáticamente un **constructor** de esa clase, que es un bloque especial de código encargado de **inicializar el objeto** (establecer valores iniciales, comprobar precondiciones, etc.). A diferencia de C/C++, donde puede haber asignación y construcción separadas, en Java toda instancia de objeto se crea siempre con `new` (salvo literales especiales como `String` o arreglos con sintaxis específica).

Un **constructor** es un método especial que **no tiene tipo de retorno** (ni siquiera `void`), y su **nombre coincide exactamente con el de la clase**. Puede haber varios constructores en la misma clase (sobrecarga) para permitir diferentes formas de inicialización. Si no se define ninguno, el compilador proporciona un **constructor por defecto** sin parámetros; en cuanto se define al menos uno, ese constructor por defecto deja de generarse automáticamente.

A continuación, un ejemplo de clase `Empleado` con **DNI, nombre y apellidos**, incluyendo un **constructor con parámetros** y, opcionalmente, un **constructor por defecto** que deja valores iniciales sencillos. Se muestra también el uso de `this` para diferenciar los atributos de los parámetros.

```java
// Archivo: Empleado.java
public class Empleado {
    private String dni;
    private String nombre;
    private String apellidos;

    // Constructor por defecto (opcional)
    public Empleado() {
        this.dni = "";
        this.nombre = "";
        this.apellidos = "";
    }

    // Constructor con parámetros
    public Empleado(String dni, String nombre, String apellidos) {
        this.dni = dni;
        this.nombre = nombre;
        this.apellidos = apellidos;
    }

    // Getters (opcionalmente setters si se requiere mutabilidad)
    public String getDni() { return dni; }
    public String getNombre() { return nombre; }
    public String getApellidos() { return apellidos; }
}
```

***

```java
// Ejemplo de uso
public class Demo {
    public static void main(String[] args) {
        Empleado e1 = new Empleado(); // usa el constructor por defecto
        Empleado e2 = new Empleado("12345678A", "Ana", "Pérez López"); // usa el constructor con parámetros
    }
}
```

***



## 12. ¿Qué es la referencia `this`? ¿Se llama igual en todos los lenguajes? Pon un ejemplo del uso de `this` en la clase `Punto`

La referencia **`this`** es un identificador especial que permite acceder a la **instancia actual** dentro del código de una clase. Se utiliza para referirse explícitamente a los **atributos** y **métodos** del objeto en el que se está ejecutando el código, y resulta especialmente útil cuando hay **sombras de nombre** (por ejemplo, parámetros de un constructor llamados igual que los atributos). En Java, `this` también puede usarse para **encadenar constructores** (`this(...)`) y para **pasar la propia instancia** como argumento a otros métodos.

No se llama igual en todos los lenguajes. En **Java, C++ y Kotlin** se utiliza `this`; en **Python** se emplea el parámetro explícito **`self`**; en **Swift** también se usa `self`; y en **JavaScript** existe `this`, pero su **enlace** depende del contexto de invocación (función, método, `call/apply/bind`, funciones flecha, etc.). Aunque varía el nombre y algunos detalles de comportamiento, la idea común es disponer de una forma de referirse al **objeto receptor** de la llamada.

Ejemplo de uso de `this` en la clase `Punto`, para distinguir atributos de parámetros y para llamar a otro método de la misma instancia:

```java
// Archivo: Punto.java
class Punto {
    double x; // visibilidad por defecto (package-private)
    double y;

    // Constructor: diferencia atributos y parámetros con 'this'
    Punto(double x, double y) {
        this.x = x; // 'this.x' es el atributo; 'x' es el parámetro
        this.y = y;
    }

    // Método de instancia que usa 'this' para claridad (opcional)
    double calculaDistanciaAOrigen() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }

    // Otro ejemplo: método que traslada el punto y devuelve la misma instancia
    Punto trasladar(double dx, double dy) {
        this.x += dx;
        this.y += dy;
        return this; // permite encadenar llamadas
    }
}
```

***



## 13. Añade ahora otro nuevo método que se llame `distanciaA`, que reciba un `Punto` como parámetro y calcule la distancia entre `this` y el punto proporcionado

La operación consiste en calcular la **distancia euclídea** entre dos puntos del plano. Para ello, se obtiene la diferencia en `x` y en `y` respecto a la instancia actual (`this`) y el punto recibido como parámetro, y se aplica la fórmula √((x₂ − x₁)² + (y₂ − y₁)²). En Java puede utilizarse `Math.hypot(dx, dy)` para mejorar la **estabilidad numérica** frente a posibles desbordamientos o pérdidas de precisión al elevar al cuadrado.

En el siguiente ejemplo se añade el método `distanciaA(Punto otro)` a la clase `Punto`. Se mantiene la **visibilidad por defecto** de los atributos como en el ejercicio previo y se incluye una comprobación simple de `null` para evitar errores en tiempo de ejecución:

```java
// Archivo: Punto.java
class Punto {
    double x; // visibilidad por defecto (package-private)
    double y;

    Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    double calculaDistanciaAOrigen() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }

    // Nuevo método: distancia entre 'this' y el punto 'otro'
    double distanciaA(Punto otro) {
        if (otro == null) {
            throw new IllegalArgumentException("El punto de destino no puede ser null");
        }
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        // Alternativa numéricamente estable:
        // return Math.hypot(dx, dy);
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

***

```java
// Ejemplo de uso
public class Demo {
    public static void main(String[] args) {
        Punto a = new Punto(3, 4);
        Punto b = new Punto(0, 0);
        System.out.println(a.distanciaA(b)); // 5.0
    }
}
```

***



## 14. El paso del `Punto` como parámetro a un método, es **por copia** o **por referencia**, es decir, si se cambia el valor de algún atributo del punto pasado como parámetro, dichos cambios afectan al objeto fuera del método? ¿Qué ocurre si en vez de un `Punto`, se recibiese un entero (`int`) y dicho entero se modificase dentro de la función? 

En Java, el paso de parámetros funciona siempre **por valor**, pero es importante matizar qué significa exactamente “valor” cuando se trabaja con objetos. En el caso de un objeto como `Punto`, lo que se pasa por valor es **la referencia** al objeto, no una copia del objeto completo. Esto implica que, dentro del método, la variable parámetro apunta al **mismo objeto** que la variable original. Por tanto, si se modifica algún **atributo** del `Punto` recibido, dichos cambios se reflejarán fuera del método, porque ambos nombres se refieren al mismo objeto en el heap.

Sin embargo, aunque la referencia se pasa por valor, no puede cambiarse desde fuera cuál es el objeto al que apunta la variable original. Si dentro del método se hace `otro = new Punto(...)`, ese cambio **no afecta** a la variable del exterior, porque solo se sustituye la copia local de la referencia. Lo único que sí se propaga son los cambios en los atributos del objeto referenciado. Esta distinción es fundamental para entender correctamente cómo se comportan los objetos al ser pasados como parámetros.

Si en lugar de un `Punto` se recibe un entero `int`, el comportamiento es distinto porque los **tipos primitivos** en Java no son objetos y su valor se pasa directamente **por copia**. Esto significa que, si dentro del método se modifica el valor del `int`, dicho cambio **no afecta** a la variable original fuera del método. El entero en la función es simplemente una copia independiente, del mismo modo que ocurre en C cuando se pasa un valor a una función sin usar punteros.

En resumen, los objetos permiten modificar su contenido desde dentro del método porque lo que se copia es la referencia, mientras que los tipos primitivos como `int` permanecen inmutables desde el exterior porque su valor se copia directamente. Esta diferencia entre pasar una referencia por valor y pasar un valor primitivo por valor es uno de los puntos esenciales para comprender el modelo de ejecución de Java.

***



## 15. ¿Qué es el método `toString()` en Java? ¿Existe en otros lenguajes? Pon un ejemplo de `toString()` en la clase `Punto` en Java

El método **`toString()`** en Java es una función heredada de `java.lang.Object` que devuelve una **representación textual** del objeto. Por defecto, esa representación incluye el nombre de la clase y un identificador basado en el **hash** (`ClassName@hexHash`), lo que suele ser poco informativo. Por ello, se **sobrescribe** (`@Override`) para devolver una cadena significativa con el estado del objeto (por ejemplo, sus atributos), lo que facilita la depuración, el registro de logs y la impresión directa en consola cuando se usa `System.out.println(obj)`.

Este concepto existe en otros lenguajes con nombres distintos pero propósito similar. En **C#** también se emplea `ToString()`. En **Python**, las funciones especiales **`__str__`** (para humanos) y **`__repr__`** (para desarrolladores) cumplen ese rol. En **C++**, no hay un método estándar, pero se suele **sobrecargar el operador de inserción** `operator<<` para `std::ostream` a fin de definir cómo se muestra un objeto. La idea común es proporcionar una forma controlada y legible de convertir un objeto a texto.

Ejemplo de `toString()` en la clase `Punto`, manteniendo atributos con visibilidad por defecto y mostrando un formato claro:

```java
// Archivo: Punto.java
class Punto {
    double x; // visibilidad por defecto (package-private)
    double y;

    Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    double calculaDistanciaAOrigen() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }

    double distanciaA(Punto otro) {
        if (otro == null) throw new IllegalArgumentException("El punto de destino no puede ser null");
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }

    @Override
    public String toString() {
        return "Punto(" + x + ", " + y + ")";
    }
}

// Ejemplo de uso
class Demo {
    public static void main(String[] args) {
        Punto p = new Punto(3, 4);
        System.out.println(p);            // Imprime: Punto(3.0, 4.0)
        System.out.println(p.toString()); // Equivalente
    }
}
```

***



## 16. Reflexiona: ¿una clase es como un `struct` en C? ¿Qué le falta al `struct` para ser como una clase y las variables de ese tipo ser instancias?

Una clase puede compararse parcialmente con un `struct` de C, pero solo en el sentido de que ambos **agrupan datos bajo un mismo nombre**. En un `struct`, se definen varios campos que pueden almacenarse juntos, y al crear una variable de ese tipo se obtiene una entidad con esos datos. En ese aspecto básico, una clase también define un conjunto de atributos que formarán parte de cada objeto creado a partir de ella. Esta similitud suele facilitar el primer contacto con la programación orientada a objetos para quienes vienen de C.

Sin embargo, al `struct` de C le falta algo esencial para comportarse como una clase: **comportamiento asociado**. Una clase no solo agrupa datos, sino que además **incluye métodos**, es decir, funciones que operan sobre esos datos y que forman parte del propio tipo. Esta unión de datos + comportamiento es la base de la orientación a objetos y permite expresar acciones vinculadas al estado del objeto. En C, las funciones deben definirse fuera del `struct`, sin relación directa con él, salvo por convención.

También falta en un `struct` el concepto de **encapsulación**, que en una clase permite controlar qué es accesible desde fuera mediante modificadores como `private`, `public` o `protected`. Un `struct` expone siempre todos sus campos sin posibilidad de ocultarlos. Del mismo modo, el `struct` carece de **constructores**, **this**, **sobrecarga de métodos**, **herencia**, **polimorfismo** y otros elementos que permiten que una clase represente un objeto con identidad y ciclo de vida propios. Por tanto, aunque comparten una idea superficial, un `struct` es solo un contenedor de datos, mientras que una clase es una unidad completa de datos + comportamiento + reglas de acceso.

*** 



## 17. Quitemos un poco de magia a todo esto: ¿Como se podría “emular”, con `struct` en C, la clase `Punto`, con su función para calcular la distancia al origen? ¿Qué ha pasado con `this`?

La clase `Punto` de Java puede “emularse” en C usando un `struct` para los datos y funciones externas para el comportamiento. Los campos `x` e `y` se agrupan en `struct Punto`, y la función que calcula la distancia al origen recibe un **puntero** a `Punto`. El cálculo replica la fórmula euclídea. La inicialización puede hacerse con un inicializador compuesto o con una función auxiliar que actúe como “constructor” informal.

```c
/* punto.h */
#ifndef PUNTO_H
#define PUNTO_H

typedef struct {
    double x;
    double y;
} Punto;

/* “Constructor” opcional (no existe como tal en C, es solo una función de ayuda) */
static inline Punto Punto_crear(double x, double y) {
    Punto p = { x, y };
    return p;
}

/* “Método” externo: calcula distancia al origen */
double Punto_calculaDistanciaAOrigen(const Punto *p);

#endif /* PUNTO_H */
```

***

```c
/* punto.c */
#include "punto.h"
#include <math.h>

double Punto_calculaDistanciaAOrigen(const Punto *p) {
    /* Alternativa estable: return hypot(p->x, p->y); */
    return sqrt(p->x * p->x + p->y * p->y);
}
```

***

```c
/* main.c */
#include <stdio.h>
#include "punto.h"

int main(void) {
    Punto a = Punto_crear(3.0, 4.0);
    double d = Punto_calculaDistanciaAOrigen(&a);
    printf("Distancia al origen: %.1f\n", d); /* 5.0 */
    return 0;
}
```

¿Qué ha pasado con `this`? En C no existe `this`; se **emula** pasando explícitamente un puntero al “receptor” como **primer parámetro** de cada función que actúa como método (`const Punto *p`). En otras palabras, lo que en Java sería `p.calculaDistanciaAOrigen()` se convierte en C en `Punto_calculaDistanciaAOrigen(&p)`. Con esta aproximación se pierde encapsulación, constructores reales, sobrecarga y control de accesos: un `struct` expone siempre sus campos y la disciplina de “datos + comportamiento” depende únicamente de convenciones de nombres y de la separación en módulos (`.h/.c`).

***