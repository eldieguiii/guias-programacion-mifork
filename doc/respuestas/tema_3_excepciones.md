<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

### Opción A: **código de retorno (estado) + parámetro de salida (resultado)**

Una forma robusta en C es separar **estado** y **dato**: la función devuelve un **código de estado** (0 = ok, distinto de 0 = error) y escribe el resultado en un **parámetro de salida**. Así, desde fuera se puede comprobar el estado sin ambigüedades y decidir cómo informar al usuario. Esta estrategia evita valores centinela problemáticos (por ejemplo, `-1.0f` puede ser un valor válido en otras funciones). Es el patrón típico de la biblioteca C cuando una función necesita comunicar dos cosas: éxito/fracaso y un valor.

```c
#include <stdio.h>
#include <math.h>

int raiz(float x, float *out) {
    if (x < 0.0f || out == NULL) return 1; // 1 = argumento inválido
    *out = sqrtf(x);
    return 0; // éxito
}

int main(void) {
    float x = -9.0f, r;
    int rc = raiz(x, &r);
    if (rc != 0) {
        printf("Error: el argumento no puede ser negativo (x=%.2f)\n", x);
    } else {
        printf("√%.2f = %.4f\n", x, r);
    }
    return 0;
}
```

Este diseño es fácil de testear y componer: quien llama puede encadenar funciones verificando el **código de retorno**, y propagar o transformar el error según convenga (análogo a capturar y relanzar una excepción en Java, pero de forma explícita). También facilita extender el protocolo (p. ej., usar distintos códigos para “argumento inválido”, “overflow”, etc.) sin cambiar la firma del resultado principal.

***

### Opción B: **usar `errno` (estado global) y un valor especial de retorno**

Otra alternativa clásica en C es comunicar el fallo vía la variable global `errno`. La función puede **establecer `errno = EDOM`** (argumento fuera de dominio) y retornar un valor que el llamador no use para decidir, sino que consulte `errno` tras la llamada. Es el estilo de muchas funciones matemáticas históricas. Para evitar ambigüedades con el valor, se suele **limpiar `errno` antes** y **comprobarlo después**; también puede retornarse `NAN` si se trabaja con `float` y se desea propagar un “no número”.

```c
#include <stdio.h>
#include <math.h>
#include <errno.h>   // errno, EDOM
#include <fenv.h>    // opcional: banderas de coma flotante

float raiz(float x) {
    if (x < 0.0f) { errno = EDOM; return NAN; } // o return 0.0f;
    errno = 0;
    return sqrtf(x);
}

int main(void) {
    errno = 0;
    float x = -9.0f;
    float r = raiz(x);
    if (errno == EDOM) {
        printf("Error: dominio inválido; no existe raíz real para %.2f\n", x);
    } else {
        printf("√%.2f = %.4f\n", x, r);
    }
    return 0;
}
```

Este enfoque es **compatible con convenciones POSIX** y permite centralizar el manejo de errores en capas superiores. Sin embargo, `errno` es **estado global**: requiere disciplina (limpiarlo antes, leerlo justo después) y puede ser menos explícito que un código de retorno. Además, basarse en un **valor especial** (como `NAN`) exige que el llamador no confunda el caso normal con el excepcional; por ello, la comprobación de `errno` es la fuente de verdad. En términos de diseño, se acerca a “lanzar” una señal de error global, mientras que la opción A explicita el contrato en la firma, lo que se asemeja más al espíritu de las excepciones declaradas en Java.

***



## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una **excepción** es un evento anómalo que ocurre durante la ejecución de un programa y que interrumpe el flujo normal de instrucciones. En Java, una excepción es un **objeto** que representa ese fallo y que viaja por la pila de llamadas hasta encontrar un manejador (`catch`) adecuado; si no lo hay, el programa termina con un error. Conceptualmente, equivale a un mecanismo estructurado para señalizar errores y cambiar de ruta de ejecución sin tener que encadenar comprobaciones manuales tras cada llamada. Frente a C “puro” (sin excepciones), donde se usan códigos de retorno o `errno`, las excepciones separan la **lógica de éxito** de la **lógica de error** y evitan duplicar comprobaciones repetitivas.

El objetivo al **implementar** funciones es poder **señalar condiciones anómalas** con semántica clara y contexto (tipo de excepción, mensaje), sin mezclar el canal del resultado con el del error. Por ejemplo, una función puede lanzar `IllegalArgumentException` si recibe datos inválidos, o `IOException` si falla un acceso a disco; esto comunica al llamador qué ha ido mal y permite decisiones informadas. Al **llamar** a funciones, el objetivo es **manejar o propagar** de forma controlada: se captura lo que puede recuperarse (p. ej., pedir de nuevo una entrada), se limpia o registra en `catch`, y se garantiza liberación de recursos en `finally` o con *try-with-resources*. En paralelo con C, sería como comprobar un `rc` distinto de cero y saltar a una sección `cleanup`, pero con tipado, propagación automática y jerarquías que facilitan tratar clases de errores a distintos niveles.

***



## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

### Ejemplo en Java usando **excepciones** para señalar argumentos inválidos

En Java, lo idiomático es **lanzar una excepción** cuando la precondición no se cumple (p. ej., argumento negativo) y **separar** la lógica normal de la de error. El método `raiz` de `Calculadora` puede validar la entrada y, si es negativa, lanzar `IllegalArgumentException` con un mensaje claro; si es válida, devuelve la raíz cuadrada. Desde **fuera** (en `main`), se controla el error con un bloque `try/catch`, donde se decide cómo informar al usuario o recuperar.

```java
public class Calculadora {

    /**
     * Devuelve la raíz cuadrada de x si x >= 0.
     * @param x número de entrada (debe ser no negativo)
     * @return raíz cuadrada de x
     * @throws IllegalArgumentException si x < 0
     */
    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException("El argumento no puede ser negativo: " + x);
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        // Ejemplo 1: caso erróneo controlado desde fuera del método raiz
        double x1 = -9.0;
        try {
            double r1 = Calculadora.raiz(x1);
            System.out.printf("√%.2f = %.4f%n", x1, r1);
        } catch (IllegalArgumentException e) {
            System.out.println("Error al calcular la raíz: " + e.getMessage());
            // Aquí se podría pedir otro valor, registrar, etc.
        }

        // Ejemplo 2: caso correcto
        double x2 = 25.0;
        try {
            double r2 = Calculadora.raiz(x2);
            System.out.printf("√%.2f = %.4f%n", x2, r2);
        } catch (IllegalArgumentException e) {
            System.out.println("Error inesperado: " + e.getMessage());
        }
    }
}
```

Este diseño refleja la intención del contrato: **si la entrada es inválida, no hay resultado** y se comunica con una excepción tipada. El llamador decide si captura y maneja (`catch`) o si **propaga** más arriba (omitiendo el `catch`). En paralelo con el C “sin excepciones”, la verificación y el salto a manejo equivaldrían a chequear el código de retorno y saltar a `cleanup`, pero en Java el lenguaje proporciona **propagación automática** y una jerarquía de tipos de error que permite capturar por clase (`IllegalArgumentException`, `IOException`, etc.) o por superclase (`RuntimeException`) según el nivel de abstracción.

***



## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

**Lanzar** una excepción consiste en crear y emitir un objeto que representa una condición anómala para interrumpir el flujo normal. En Java se usa la palabra clave `throw` para lanzarla, por ejemplo `throw new IllegalArgumentException(...)`. Con ello, la ejecución del método actual se detiene en ese punto y se inicia la búsqueda de un bloque `catch` compatible en la **pila de llamadas**. **Capturar** (o **controlar**) una excepción es interceptarla con un `try/catch` cuyo tipo coincida (o sea supertipo) y decidir una acción: corregir, informar, registrar, convertir a otra excepción, o finalizar limpiamente (complementándolo con `finally` o *try-with-resources*).

**Propagar** una excepción significa que, si el método que la recibe **no** la captura, esta “sube” al llamador; si tampoco la captura, sigue ascendiendo, marco a marco, hasta encontrar un `catch` adecuado o, en último término, el hilo termina con un error no capturado. Durante la propagación ocurre el **desapilado** (**stack unwinding**): se van destruyendo los marcos de activación de los métodos que estaban en ejecución, ejecutándose cualquier `finally` pendiente en cada uno de ellos. En Java **no existe reanudación** tras el punto donde se lanzó la excepción: una vez lanzada, no se vuelve a la instrucción fallida; solo se continúa desde el `catch` que la maneje o el programa finaliza.

Aplicado al ejemplo de la raíz, el método `raiz` valida la precondición y **lanza** `IllegalArgumentException` si el argumento es negativo. Un método intermedio que llame a `raiz` puede **no** capturar y dejar que la excepción **se propague**; finalmente, `main` puede **capturar** para informar al usuario y continuar vivo. En cada salto, los métodos por los que pasa la excepción **no se reanudan** después del `throw`; sus `finally` (si los hay) sí se ejecutan, lo que garantiza la liberación de recursos.

```java
public class Calculadora {
    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException("El argumento no puede ser negativo: " + x);
        }
        return Math.sqrt(x);
    }

    // Capa intermedia que NO captura: deja que la excepción se propague
    static double calcularSeguro(double x) {
        // Un finally aquí se ejecutaría aunque raiz lance excepción
        try {
            return raiz(x);
        } finally {
            // p.ej., liberar recursos, cerrar logs, etc.
        }
    }

    public static void main(String[] args) {
        double a = -9.0;   // provocará excepción
        double b = 25.0;   // caso válido

        try {
            double ra = calcularSeguro(a); // aquí se lanza y PROPAGA
            System.out.println("√" + a + " = " + ra); // no se ejecuta
        } catch (IllegalArgumentException e) { // captura final
            System.out.println("Error al calcular: " + e.getMessage());
        }

        // El programa sigue vivo; se puede intentar otro cálculo
        try {
            double rb = calcularSeguro(b);
            System.out.println("√" + b + " = " + rb);
        } catch (IllegalArgumentException e) {
            System.out.println("Error inesperado: " + e.getMessage());
        }
    }
}
```

En este flujo, `raiz(-9)` **lanza**; `calcularSeguro` no la **captura**, pero sí ejecuta su `finally` al ser **desapilado**; `main` la **captura** y decide informar al usuario, manteniendo el programa operativo para un segundo intento. No existe reanudación en `raiz` ni en `calcularSeguro` tras el `throw`; la ejecución continúa únicamente desde el bloque `catch` que manejó la excepción o termina el hilo si nadie la maneja.

***



## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

La **propagación natural** de excepciones permite que un error viaje automáticamente hacia arriba por la pila de llamadas sin que cada función intermedia tenga que comprobar manualmente el estado devuelto. En C, cada función debe verificar explícitamente el código de retorno de la función que invoca; si olvida hacerlo en algún punto, el error puede quedar silenciado o producir comportamientos indeterminados. Con excepciones, el lenguaje garantiza que el fallo no se pierde: si nadie lo controla, continúa ascendiendo hasta encontrar un manejador adecuado o hasta finalizar el programa, haciendo el flujo de errores más seguro y menos propenso a omisiones.

Otra ventaja es que, durante la propagación, el sistema ejecuta automáticamente los bloques `finally` (o las cláusulas de *try-with-resources*), lo que garantiza que los recursos se liberen aunque ocurra un error en cualquier nivel. En C, este patrón debe implementarse manualmente usando saltos a etiquetas de limpieza (`goto cleanup`) y teniendo cuidado de cubrir todas las rutas de salida; basta un retorno prematuro mal gestionado para producir fugas. La propagación estructurada simplifica esta disciplina y mantiene el código más legible.

Además, el mecanismo permite separar la **lógica normal** de la **lógica de error**, lo cual reduce el ruido visual. En C, la verificación de cada llamada intermedia introduce código adicional que puede oscurecer la intención del algoritmo principal. Con excepciones, las funciones intermedias solo se ocupan de la parte que les corresponde y delegan la decisión de manejo a un nivel más adecuado, manteniendo cada función más concisa y centrada en su cometido.

Finalmente, esta propagación ofrece **información tipada** sobre la causa del problema. En C, los códigos de error suelen ser valores numéricos genéricos (`-1`, `NULL`, `errno`) que requieren interpretación adicional. En Java, el tipo de la excepción proporciona un contexto semántico inmediato —por ejemplo, distinguir entre `IllegalArgumentException`, `IOException` o `NumberFormatException`— permitiendo manejar clases de errores de forma diferenciada sin añadir complejidad al flujo de programa.

***



## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

Sí, en orientación a objetos las excepciones suelen ser **objetos** que heredan de una jerarquía común (en Java, de `Throwable`). Esto permite que cada excepción encapsule no solo un mensaje, sino también información adicional como la causa original, datos contextuales o métodos auxiliares. En lugar de limitarse a un simple código numérico como ocurre en C, un objeto-excepción puede transportar un estado interno más rico y coherente con los principios de encapsulación: cada tipo describe exactamente qué tipo de error representa y cómo debe interpretarse.

Este enfoque también mejora la claridad y la modularidad. Gracias al polimorfismo, un bloque `catch` puede decidir capturar una excepción concreta, una más general o incluso toda una categoría de errores. Es un mecanismo equivalente a tener funciones especializadas en C para cada código de error, pero integrado directamente en el lenguaje y con mayor expresividad. La encapsulación ayuda a que las capas superiores no dependan de cómo se implementa una excepción internamente; solo necesitan conocer su interfaz pública y su posición en la jerarquía.

Dado que las excepciones son objetos, es natural poder **crear excepciones personalizadas**. En Java basta con definir una clase que extienda `Exception` (para *checked exceptions*) o `RuntimeException` (para *unchecked exceptions*). Esto permite expresar errores de dominio específicos de forma tipada, lo que hace el código más legible y favorece que las funciones declaren exactamente qué condiciones excepcionales pueden producir. Una excepción propia también puede añadir atributos o métodos que aporten contexto adicional útil para el tratamiento del error.

Un ejemplo sencillo de una excepción personalizada podría ser:

```java
public class NumeroNegativoException extends RuntimeException {
    public NumeroNegativoException(double x) {
        super("No se puede calcular la raíz de un número negativo: " + x);
    }
}
```

Y su uso en el método `raiz`:

```java
public static double raiz(double x) {
    if (x < 0) {
        throw new NumeroNegativoException(x);
    }
    return Math.sqrt(x);
}
```

Gracias a esta posibilidad, la gestión de errores puede adaptarse al dominio del problema, separando de forma clara los tipos de fallo y facilitando que otros componentes los manejen de manera precisa y coherente.

***



## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

Un **objeto excepción** transporta siempre una **cantidad de información esencial** que resulta muy valiosa al llegar a un manejador (`catch`). A diferencia de C, donde normalmente solo se tiene un código de error o un valor centinela, una excepción en Java encapsula varios elementos que ayudan a diagnosticar y reconstruir lo sucedido sin necesidad de que las capas intermedias hagan trabajo manual. Esta encapsulación permite que la información fluya íntegra desde el punto donde se produjo el error hasta el lugar donde se decide cómo tratarlo.

La pieza más importante es el **mensaje descriptivo** (`getMessage()`), que indica qué ha fallado y por qué. Esto permite comunicar de forma clara la condición anómala sin depender de códigos numéricos ambiguos o de comentarios externos. Además del mensaje, toda excepción contiene una **traza de pila** (*stack trace*), que muestra la secuencia exacta de llamadas que llevó hasta el error. Esta información es crucial para comprender el contexto: no solo se sabe *qué* ha fallado, sino también *dónde* y *cómo se llegó allí*, lo que en C suele requerir imprimir información manualmente en cada función.

Otro elemento encapsulado es la **causa encadenada** (`getCause()`), que permite anidar excepciones: una función puede atrapar una excepción de bajo nivel y envolverla en otra más significativa para su capa, preservando el origen. Esta característica es muy útil en sistemas grandes, donde los errores atraviesan varias capas (acceso a datos, lógica de negocio, interfaz) y es importante no perder el detalle técnico que permitirá depurar el problema.

En conjunto, tener un mensaje, la traza de pila y la posible causa original permite que el manejador actúe con información completa: puede registrar adecuadamente, mostrar datos útiles al usuario, tomar decisiones condicionales según el tipo de excepción o re-lanzarla enriquecida. Comparado con C, esta encapsulación evita trabajo repetitivo, reduce errores de diagnóstico y mejora drásticamente la trazabilidad de fallos.

***



## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

Sí, en un bloque `try` pueden colocarse **varios bloques `catch`**, y cada uno puede capturar un **tipo distinto de excepción**. Esto permite manejar de forma diferenciada errores que pertenecen a categorías distintas: por ejemplo, validar argumentos, tratar errores de entrada/salida o gestionar excepciones del propio dominio del problema. La jerarquía de excepciones hace que los `catch` más específicos deban ir antes que los más generales; de lo contrario, los generales bloquearían a los específicos, ya que captarían todas las excepciones antes de que llegaran a su manejador adecuado.

En cuanto al número de bloques que se ejecutan, **solo se ejecuta uno**: el correspondiente al **primer `catch` cuyo tipo sea compatible** con la excepción lanzada. Una vez que ese bloque se ejecuta, los demás se omiten y el flujo continúa después del último `catch` o en un posible `finally`. Esto garantiza que un mismo error no se maneje más de una vez, manteniendo una semántica clara y predecible del control de errores. Además, simplifica la estructura del código, ya que no hay que preocuparse de evitar duplicidades o interacciones entre manejadores sucesivos.

Un ejemplo ilustrativo sería el siguiente:

```java
try {
    double r = Calculadora.raiz(x);  // Puede lanzar varias excepciones
    System.out.println("Resultado: " + r);
} catch (IllegalArgumentException e) {
    System.out.println("Argumento no válido: " + e.getMessage());
} catch (ArithmeticException e) {
    System.out.println("Error aritmético inesperado.");
} catch (Exception e) {
    System.out.println("Otro tipo de excepción: " + e.getMessage());
}
```

Aquí, si `raiz(x)` lanza `IllegalArgumentException`, se ejecutará exclusivamente el primer `catch`. Si lanza una excepción distinta pero aritmética, se ejecutará el segundo; y si se trata de cualquier otra excepción, actuará el tercero. Así se obtiene un manejo ordenado y claro, aprovechando la información encapsulada en cada tipo de excepción.

***



## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

El mecanismo estándar es usar un bloque `finally`, que **siempre se ejecuta** tras el `try` (haya o no excepción) y antes de que la excepción siga propagándose. De este modo se puede cerrar ficheros, liberar memoria nativa o desconectar sockets con garantía. Cuando además existe un `catch`, el `finally` se ejecuta **después** del manejador; cuando no hay `catch`, el `finally` se ejecuta igualmente y, tras él, la excepción continúa su ascenso por la pila. Es el patrón equivalente, en espíritu, a un bloque `cleanup` en C, pero con soporte del lenguaje.

**Con `catch` y `finally`** (se limpia y se sigue ejecutando el programa):

```java
import java.io.*;

public class DemoFinally {
    public static void main(String[] args) {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            String linea = br.readLine();           // Puede lanzar IOException
            double r = Calculadora.raiz(Double.parseDouble(linea)); // Puede lanzar IllegalArgumentException
            System.out.println("Resultado: " + r);
        } catch (IllegalArgumentException e) {
            System.out.println("Dato inválido: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("Error de E/S: " + e.getMessage());
        } finally {
            // Se ejecuta SIEMPRE: haya éxito, excepción capturada o no
            if (br != null) {
                try { br.close(); } catch (IOException ignore) {}
            }
            System.out.println("Recursos liberados (finally).");
        }
        System.out.println("El programa continúa.");
    }
}
```

***

**Sin `catch`, con `finally`** (se limpia y la excepción **se propaga**):

```java
import java.io.*;

public class DemoPropaga {
    public static void main(String[] args) throws IOException { // se declara propagación de IOException
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            // Si ocurre IOException aquí, no se captura en este método…
            String linea = br.readLine();
            // …pero el finally se ejecuta antes de salir por excepción.
            System.out.println("Primera línea: " + linea);
        } finally {
            if (br != null) {
                try { br.close(); } catch (IOException ignore) {}
            }
            System.out.println("Cierre garantizado en finally (aunque haya excepción).");
        }
        // Si hubo excepción, este punto no se alcanza.
    }
}
```

***

En ambos casos, `finally` garantiza la **liberación de recursos** incluso con rutas de salida abruptas por excepción. Cuando se trabaja con recursos que implementan `AutoCloseable`, se recomienda además el patrón **try-with-resources** (`try (var br = Files.newBufferedReader(...)) { ... }`), que automatiza el cierre sin necesidad de un `finally` explícito; no obstante, el concepto subyacente es el mismo: asegurar la limpieza antes de que la ejecución continúe o la excepción se propague.



## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

Sí, en Java el bloque `finally` puede aparecer **sin** `catch`, formando una construcción `try { ... } finally { ... }`. Este patrón se usa cuando no se desea manejar la excepción en ese punto, pero **sí** se quiere garantizar la liberación de recursos antes de que la excepción se propague a la función llamadora. Además, `finally` se ejecuta **tanto si ocurre como si no ocurre** una excepción en el `try`, con las únicas excepciones de terminaciones abruptas del proceso/hilo (p. ej., `System.exit(...)`, fallo de la JVM, parada forzada del proceso, o si el propio `finally` lanza otra excepción que interrumpe su ejecución).

Cuando hay un `return` dentro del `try`, el `finally` **también se ejecuta** antes de que el método retorne. El valor de retorno se evalúa primero y queda “pendiente” de devolver; a continuación se ejecuta el `finally`, y solo después se completa el retorno. Es importante notar que si el `finally` contiene su **propio `return`**, este **anula** el retorno anterior (y también silencia cualquier excepción previa), por lo que es una **mala práctica** incluir `return` dentro de `finally`. Se recomienda limitar `finally` a tareas de limpieza y registro.

Ejemplos breves:

```java
// try-finally SIN catch: limpia y deja que la excepción (si existe) se propague
void procesa() throws IOException {
    BufferedReader br = new BufferedReader(new FileReader("datos.txt"));
    try {
        System.out.println(br.readLine()); // Puede lanzar IOException
    } finally {
        br.close(); // Se ejecuta ocurra o no excepción, y también si hay return en el try
    }
}
```

```java
// El finally se ejecuta aunque haya return dentro del try
int f() {
    try {
        return 42;           // Valor calculado
    } finally {
        System.out.println("Limpieza antes de devolver"); // Siempre se ejecuta
        // return 7;  // Evitar: esto reemplazaría el 42 y es mala práctica
    }
}
```

***



## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

Las **excepciones controladas (checked)** son las que el compilador exige **declarar o capturar**; normalmente representan condiciones anómalas **previsibles y recuperables** (E/S, red, falta de recursos externos). En Java, son las subclases de `Exception` **que no** heredan de `RuntimeException`. Las **no controladas (unchecked)** son las subclases de `RuntimeException` (y también se suele mencionar `Error`, aunque estos son fallos del sistema/JVM): no requieren declaración ni captura y suelen indicar **errores de programación** o violación de precondiciones (p. ej., índices fuera de rango, nulos inesperados). `RuntimeException` actúa como **raíz** de las excepciones no controladas: todo lo que herede de ella se considera “unchecked” y, por tanto, se puede **propagar libremente** sin `throws`, dejando al programador decidir si lo quiere capturar en algún punto.

Como ejemplos típicos que podrían reutilizarse o definirse en código propio: **controladas** como `IOException`, `SQLException`, o una propia `UsuarioNoEncontradoException extends Exception` cuando la ausencia de un usuario forma parte del flujo normal a tratar. **No controladas** como `IllegalArgumentException`, `NullPointerException`, `IndexOutOfBoundsException`, o una propia `DominioInvalidoException extends RuntimeException` para señalar una violación de precondición. En código, podría expresarse así:

```java
// Controlada (checked): el compilador obliga a declarar/capturar
public class UsuarioNoEncontradoException extends Exception {
    public UsuarioNoEncontradoException(String id) {
        super("No existe el usuario con id: " + id);
    }
}
```

***

```java
// No controlada (unchecked): error de programación / precondición violada
public class DominioInvalidoException extends RuntimeException {
    public DominioInvalidoException(double x) {
        super("Dominio inválido para la operación con x=" + x);
    }
}
```

Criterios habituales de elección:
 - **Preferir excepción controlada (checked)** cuando: (1) la condición es **esperable** y se desea **forzar** al llamador a tratarla (p. ej., lectura de fichero que puede faltar), (2) existen **estrategias de recuperación** claras (reintentar, ruta alternativa, pedir datos al usuario), (3) el error forma parte del **contrato de la API** y debe quedar **documentado en la firma** (`throws`), (4) se trabaja en **capas de infraestructura** donde los fallos externos son la norma (E/S, red, BBDD). 
 - **Preferir excepción no controlada (unchecked)** cuando: (1) se viola una **precondición** o hay un **bug** (argumentos inválidos, estados imposibles), (2) capturar no aporta recuperación útil y solo ensucia el código, (3) se busca **simplicidad** en APIs de alto nivel, evitando listas extensas de `throws`, (4) el coste de obligar a capturar supera el beneficio (p. ej., utilidades puras que deben fallar rápido ante uso incorrecto).

***



## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

El uso de `throws` en Java forma parte del manejo de **excepciones controladas (checked)** y sirve para indicar que un método **puede lanzar** una o varias excepciones de ese tipo. En esencia, `throws` declara en la *firma* del método las condiciones excepcionales que pueden producirse, trasladando la responsabilidad del manejo a quien invoque dicho método. No captura la excepción ni la resuelve: simplemente anuncia que el método no la tratará internamente y que, si ocurre, se propagará hacia arriba por la pila de llamadas.

El papel de `throws` es importante porque las excepciones controladas exigen al compilador comprobar que son gestionadas de alguna manera. Si un método contiene código que puede lanzar una excepción controlada y no se rodea ese código con un `try-catch`, entonces es obligatorio añadir `throws` a la firma. Este mecanismo confiere mayor claridad al contrato del método: cualquier programador que lo utilice sabe, desde la declaración, qué errores debe prever y gestionar explícitamente.

`throws` actúa como **alternativa a capturar** una excepción controlada porque permite “delegar” su tratamiento a un nivel más adecuado. En muchas situaciones, el método actual no tiene suficiente contexto para decidir cómo recuperarse del error (por ejemplo, un fallo de E/S o una base de datos inaccesible). Declarar `throws` evita obligar al método a atrapar y manejar la excepción de forma artificial, permitiendo que la responsabilidad recaiga en un nivel superior que sí tenga la visión completa del flujo del programa. Es, por tanto, una forma estructurada de mantener limpio el código y respetar niveles de abstracción.

Un ejemplo sencillo:

```java
public void leerArchivo(String nombre) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(nombre));
    String linea = br.readLine();  // Puede lanzar IOException
    br.close();
}
```

El método no captura la `IOException`, pero la declara, de modo que quien lo llame deberá usar `try-catch` o, a su vez, volver a declarar `throws`. Gracias a esta flexibilidad, `throws` permite construir APIs más expresivas y mantener separada la lógica de negocio del manejo de errores externos.

***



## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

Una forma habitual es declarar en la **firma** que el método puede lanzar una excepción **controlada** (p. ej., `IOException`) y **no** manejarla localmente. Así, la responsabilidad de decidir qué hacer si el fichero no existe recae en el llamador. Aun cuando no se capture la excepción, es imprescindible asegurar la **liberación de recursos** con un bloque `finally` (o, en su defecto, *try-with-resources*). El `finally` se ejecutará tanto en el caso exitoso como si se lanza la excepción, garantizando el cierre del `Reader`.

En el ejemplo siguiente, el método `leerPrimeraLinea` abre un fichero y declara `throws IOException`. No captura la excepción (si el fichero no existe, se propagará), pero **cierra** el recurso en `finally`. Quien invoque este método deberá rodearlo con `try-catch` o, a su vez, declarar `throws` en su propia firma.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class Ficheros {

    /**
     * Abre el fichero y devuelve su primera línea.
     * No maneja localmente la excepción: se propaga hacia arriba.
     *
     * @param ruta Ruta del fichero a abrir.
     * @return La primera línea del fichero.
     * @throws IOException si el fichero no existe o hay un error de E/S.
     */
    public static String leerPrimeraLinea(String ruta) throws IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader(ruta)); // puede lanzar FileNotFoundException (subtipo de IOException)
            return br.readLine();                           // puede lanzar IOException
        } finally {
            if (br != null) {
                try { br.close(); } catch (IOException ignore) {}
            }
        }
    }
}
```

Para completar la idea, un llamador típico podría ser:

```java
public class App {
    public static void main(String[] args) {
        try {
            String linea = Ficheros.leerPrimeraLinea("datos.txt");
            System.out.println("Primera línea: " + linea);
        } catch (IOException e) {
            System.out.println("No se pudo leer el fichero: " + e.getMessage());
        }
    }
}
```

Este patrón mantiene el **contrato claro** (la firma comunica que puede fallar por E/S) y asegura la **limpieza de recursos** con `finally` incluso cuando la excepción se propaga. Como alternativa moderna, podría usarse *try-with-resources*; no obstante, aquí se muestra explícitamente `finally` como se solicita.

***



## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

Sí, en Java es perfectamente posible **incluir excepciones no controladas (unchecked)** en la cláusula `throws`, como `RuntimeException` o cualquiera de sus subclases. Sin embargo, no existe obligación de hacerlo: el compilador **no exige** declarar excepciones no controladas porque forman parte de errores de programación o violaciones de precondiciones. Añadirlas en `throws` es una decisión **documental**, no funcional; sirve para indicar explícitamente que un método puede lanzar ese tipo de error aunque no esté obligado a declararlo.

El método llamador **no está obligado** a rodear con `try-catch` una excepción no controlada, incluso si aparece en `throws`. El compilador no lo exige porque las excepciones de tipo `RuntimeException` se consideran fallos que normalmente **no se recuperan** en el punto inmediato, sino que deben propagarse hasta un lugar más adecuado (o terminar la ejecución). Forzar su captura solo aumentaría el ruido en el código sin aportar una estrategia significativa de recuperación. Por ello, en la práctica, capturar una excepción no controlada solo tiene sentido cuando se quiere registrar, transformar o abortar el programa de forma controlada.

El propósito de colocar una excepción no controlada en `throws` suele ser **documentar un contrato fuerte**: informar al usuario de la API de que, si viola ciertas precondiciones, puede recibir un error concreto. Esto se ve, por ejemplo, en métodos que quieren dejar claro que no admitirán argumentos nulos o valores fuera de rango y que lanzarán `NullPointerException` o `IllegalArgumentException`. La cláusula `throws` no cambia la semántica, pero sí aclara la intención y ayuda a herramientas de análisis o a otros programadores a prever los fallos potenciales, manteniendo un diseño más explícito y coherente.

***



## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

En Java suele adoptarse una regla orientada a la **naturaleza del problema**: usar **excepciones controladas (checked)** cuando la situación anómala es **externa, previsible y recuperable**, y usar **excepciones no controladas (unchecked)** cuando se trata de **errores de programación**, violación de **precondiciones** o estados que *no deberían ocurrir* si el código está bien escrito. Esta distinción mantiene claras las responsabilidades: las excepciones controladas obligan a *decidir* qué hacer, mientras que las no controladas permiten que el programa falle rápido si se usa mal una API o se viola un contrato.

Las excepciones controladas, como `IOException` o `SQLException`, se recomiendan cuando el error puede deberse a factores **fuera del control del programa**, como archivos inexistentes, redes caídas o recursos externos inaccesibles. Allí tiene sentido obligar al programador que llama a gestionar el problema, porque existe una estrategia razonable: reintentar, usar una ruta alternativa, avisar al usuario, o registrar el error y continuar. Las no controladas, como `IllegalArgumentException`, `NullPointerException` o `IndexOutOfBoundsException`, se emplean cuando el fallo indica que el **código se ha usado mal** o que se rompe una **precondición**, de modo que capturarlas suele carecer de utilidad, ya que lo adecuado es corregir el código.

No todos los lenguajes ofrecen ambas opciones. Java es una excepción notable, pues introduce explícitamente las dos categorías. Otros lenguajes como **C++, Python, JavaScript, C#, Kotlin o Go** no distinguen entre checked y unchecked: todas las excepciones (si el lenguaje las tiene) son equivalentes al modelo *unchecked*. Esto se debe a que muchos diseñadores de lenguajes y comunidades consideran que las checked exceptions complican la evolución de APIs, obligan a propagar demasiadas firmas `throws` y, en grandes proyectos, crean “ruido” en el código.

En los lenguajes donde solo existe una opción, la opción habitual equivale a las **no controladas** de Java: las excepciones pueden lanzarse y propagarse sin declaración obligatoria, confiando en que el desarrollador capture solo cuando exista una estrategia de recuperación real. Es el caso de Python (todas las excepciones son “unchecked”), C++ (todas las excepciones funcionan así pese a la antigua sintaxis `throw()`), C# o JavaScript. En estos entornos, es común diseñar APIs donde las excepciones representan fallos graves, y manejar de forma selectiva solo aquello que pueda resolverse o recuperarse de forma sensata.

***



## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

Lanzar excepciones dentro de un `catch` **sí tiene sentido** cuando, tras capturar una excepción, se necesita transformar el error, enriquecerlo o notificar a niveles superiores que no es posible recuperarse localmente. Esto suele hacerse cuando el método actual puede añadir contexto relevante (por ejemplo, indicando qué operación concreta falló), pero no puede resolver el problema por sí mismo. En estos casos, se captura una excepción de bajo nivel y se lanza una nueva que represente mejor el fallo dentro del dominio de la aplicación. Este patrón se conoce como *wrapping* o *exception translation*, y ayuda a mantener las capas bien aisladas semánticamente.

También es posible **relanzar la misma excepción capturada**, ya sea con `throw;` (en Java: `throw e;`). Esto resulta útil cuando se quiere realizar alguna acción local —como registrar el error, cerrar recursos específicos o añadir información al log— antes de que el fallo siga propagándose. Relanzar la misma excepción permite que la capa superior mantenga el contexto original (incluida la traza de pila), evitando perder información necesaria para debugar. Se aplica en situaciones en las que la función puede hacer “algo parcial” pero no puede dar solución completa al problema.

Ejemplo de **lanzar una nueva excepción** dentro del `catch` (traducción de excepción):

```java
try {
    double valor = Calculadora.raiz(x);
    System.out.println(valor);
} catch (NumberFormatException e) {
    throw new IllegalArgumentException("La entrada no es un número válido: " + x, e);
}
```

***

Ejemplo de **relanzar la misma excepción** tras realizar una acción local:

```java
try {
    double valor = Calculadora.raiz(x);
    System.out.println(valor);
} catch (IllegalArgumentException e) {
    System.err.println("Error detectado: " + e.getMessage()); // acción local
    throw e;  // se vuelve a lanzar la MISMA excepción
}
```

***

En ambos casos, el objetivo es permitir que la excepción llegue finalmente a un nivel más adecuado, donde sí exista el contexto necesario para decidir si el programa debe recuperarse, repetir la operación, comunicar el error al usuario o finalizar. De este modo, el manejo de errores se mantiene claro, coherente y bien distribuido entre capas.

***



## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

Una excepción puede ser la **causa** de otra cuando un error de bajo nivel se captura y se **encapsula** dentro de una excepción de nivel superior, preservando el rastro original. En Java, esta relación se expresa pasando la excepción original como **segunda** parámetro en el constructor (`new MiExcepcion(msg, cause)`) o con `initCause(cause)`. Este *encadenamiento de causas* permite que cada capa traduzca el error a su propio dominio semántico sin perder el **contexto técnico** (traza de pila y tipo original), facilitando el diagnóstico en producción y manteniendo el aislamiento entre capas.

Se considera una buena práctica capturar excepciones **técnicas** (p. ej., `IOException`, `SQLException`) en capas de infraestructura y **envolverlas** en excepciones del **dominio** o de la **aplicación** (p. ej., `RepositorioUsuarioException`, `PedidoServiceException`). De este modo, la capa superior no queda acoplada a detalles de implementación (como el sistema de ficheros o la base de datos), pero si necesita depurar, dispone de la causa original. Además, al imprimir la excepción, la JVM muestra el encadenamiento con la sección **“Caused by:”**, por lo que **sí se ve** la causa en pantalla junto con su traza, típicamente en `printStackTrace()` o en logs.

**Ejemplo de encapsulación en una excepción personalizada:**

```java
// Excepción de alto nivel (dominio/aplicación)
public class LecturaConfiguracionException extends Exception {
    public LecturaConfiguracionException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}
```

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class ConfigService {

    public String cargarPrimeraLinea(String ruta) throws LecturaConfiguracionException {
        try (BufferedReader br = new BufferedReader(new FileReader(ruta))) {
            return br.readLine(); // puede lanzar IOException
        } catch (IOException e) {
            // Se captura la excepción técnica y se traduce a una de negocio, preservando la causa
            throw new LecturaConfiguracionException("No se pudo leer la configuración de: " + ruta, e);
        }
    }

    public static void main(String[] args) {
        ConfigService svc = new ConfigService();
        try {
            String linea = svc.cargarPrimeraLinea("config.txt");
            System.out.println("Config: " + linea);
        } catch (LecturaConfiguracionException e) {
            // Al imprimir, se muestra la cadena de causas con "Caused by:"
            e.printStackTrace();
        }
    }
}
```

En la salida típica de `printStackTrace()`, se verá primero la traza de `LecturaConfiguracionException` y, a continuación, una sección **“Caused by: java.io.FileNotFoundException …”** (o la `IOException` concreta), con su propia traza de llamadas. Ese formato deja claro **qué falló en la capa superior** y **cuál fue el origen técnico** del fallo, ofreciendo un hilo completo de diagnóstico sin necesidad de que las capas intermedias agreguen impresiones manuales. Esta técnica combina encapsulación, claridad de contrato y trazabilidad efectiva.

***
