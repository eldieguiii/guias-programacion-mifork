<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Composición con `struct` en C: puntos y líneas

En C, la composición “tiene-un” puede modelarse definiendo `struct` que contienen otros `struct`. A continuación se muestra una estructura `Point` con coordenadas `x` e `y`, y una estructura `Line` compuesta por dos puntos, `a` y `b`. Se implementa una función `distance_between_points` para calcular la distancia euclídea entre dos puntos, y otra `line_length` que reutiliza la anterior para obtener la longitud de una línea. Se emplea `double` para mayor precisión y `const` para dejar claro que no se modifican los argumentos.

```c
#include <stdio.h>
#include <math.h>   // sqrt, pow

typedef struct {
    double x;
    double y;
} Point;

typedef struct {
    Point a;
    Point b;
} Line;

double distance_between_points(const Point *p, const Point *q) {
    double dx = q->x - p->x;
    double dy = q->y - p->y;
    return sqrt(dx * dx + dy * dy);
}

double line_length(const Line *l) {
    return distance_between_points(&l->a, &l->b);
}

int main(void) {
    Point p1 = {0.0, 0.0};
    Point p2 = {3.0, 4.0};
    Line  seg = {p1, p2};

    printf("Distancia P1-P2: %.2f\n", distance_between_points(&p1, &p2));
    printf("Longitud de la línea: %.2f\n", line_length(&seg));
    return 0;
}
```

La función `distance_between_points` implementa directamente la norma euclídea en 2D, `sqrt((Δx)^2 + (Δy)^2)`, evitando copias innecesarias al recibir punteros constantes. La función `line_length` ilustra la reutilización: una línea “tiene-dos” puntos y su longitud no es más que la distancia entre ellos. De esta forma, la operación sobre el agregado (`Line`) se expresa en términos de la operación ya definida para sus componentes (`Point`).

Esta solución es extensible: si se quisiera soportar líneas poligonales o “tiene-varios puntos”, bastaría con definir un `struct Polyline` con un puntero a `Point` y un tamaño, y acumular las distancias consecutivas. Asimismo, si se trabaja en entornos sin FPU o con requisitos de rendimiento específicos, puede valorarse evitar `pow` (ya se ha evitado) y mantener el cálculo en términos de multiplicaciones, o incluso comparar distancias al cuadrado cuando no se requiera la raíz.

***



## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

### Composición e inmutabilidad en Java: `Punto` y `Linea`

En orientación a objetos, la composición “tiene-un” se expresa haciendo que una clase contenga instancias de otras. A continuación se definen `Punto` (inmutable) y `Linea` (inmutable) de forma que se oculte el estado interno mediante campos `private` y `final`, sin exponer *setters*, y asegurando que, una vez creadas, sus referencias no cambian. La distancia entre puntos se implementa como método de instancia en `Punto`, y la longitud de la línea reutiliza esa operación. La inmutabilidad se refuerza validando entradas en el constructor y evitando devolver referencias mutables (no es necesario “copia defensiva” porque `Punto` también es inmutable).

```java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    /** Distancia euclídea a otro punto. */
    public double distanciaA(Punto otro) {
        if (otro == null) throw new IllegalArgumentException("Punto nulo");
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.hypot(dx, dy); // mayor estabilidad numérica que sqrt(dx*dx + dy*dy)
    }

    @Override
    public String toString() {
        return "Punto(" + x + ", " + y + ")";
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Punto)) return false;
        Punto p = (Punto) o;
        return Double.compare(x, p.x) == 0 && Double.compare(y, p.y) == 0;
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(x, y);
    }
}
```

***

```java
public final class Linea {
    private final Punto a;
    private final Punto b;

    public Linea(Punto a, Punto b) {
        if (a == null || b == null) throw new IllegalArgumentException("Extremos nulos");
        if (a.equals(b)) throw new IllegalArgumentException("Una línea no puede tener extremos coincidentes");
        // No hace falta copia defensiva: Punto es inmutable
        this.a = a;
        this.b = b;
    }

    public Punto getA() { return a; }
    public Punto getB() { return b; }

    /** Longitud de la línea: distancia entre sus extremos. */
    public double longitud() {
        return a.distanciaA(b);
    }

    @Override
    public String toString() {
        return "Linea[" + a + " -> " + b + "]";
    }
}
```

```java
public class Demo {
    public static void main(String[] args) {
        Punto p1 = new Punto(0.0, 0.0);
        Punto p2 = new Punto(3.0, 4.0);
        Linea seg = new Linea(p1, p2);

        System.out.printf("Distancia P1-P2: %.2f%n", p1.distanciaA(p2));
        System.out.printf("Longitud de la línea: %.2f%n", seg.longitud());
    }
}
```

La encapsulación se asegura marcando los campos como `private` y eliminando la posibilidad de modificación posterior mediante `final` y ausencia de métodos modificadores. Al ser `Punto` inmutable, `Linea` no necesita realizar copias defensivas para proteger su invariantes; en caso de contener objetos mutables, sí sería necesario. También puede observarse cómo `Linea` reutiliza el método de `Punto`, lo que reduce duplicación y mantiene la lógica localizada donde corresponde. De este modo, se logra una composición clara, con inmutabilidad y ocultación de información que superan las restricciones del ejemplo equivalente en C.

***



## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

La **multiplicidad** en composición indica cuántas instancias de una clase pueden estar relacionadas con una instancia de otra clase cuando se establece una relación “tiene‑un”. Se usa para precisar si un objeto contiene exactamente un componente, varios, un número variable, o incluso ninguno, aunque en composición estricta suele haber siempre al menos uno. Esta información se expresa con rangos numéricos como `1`, `0..1`, `1..*`, `*`, etc., y permite describir formalmente la estructura interna de los objetos en el diseño orientado a objetos.

En el ejemplo anterior, una `Linea` está formada **exactamente por dos** puntos: un punto inicial y uno final. Esto implica que la multiplicidad desde `Linea` hacia `Punto` es **2**, lo que en notación UML se escribiría como `Linea → Punto: 2`. Esta relación expresa que cada línea debe contener dos puntos y no puede existir con menos ni más, ya que su definición geométrica depende precisamente de esos dos extremos.

En sentido inverso, cada `Punto` no está obligado a pertenecer a ninguna línea concreta. Un `Punto` puede existir por sí mismo sin formar parte de una línea, o puede ser usado por varias líneas distintas si así se decide en el programa. Por ello, la multiplicidad desde `Punto` hacia `Linea` es `0..*`, indicando que un punto puede estar en cero, una o varias líneas: `Punto → Linea: 0..*`. Esto refleja que la composición está del lado de `Linea`, no del lado de `Punto`.

Esta diferencia en ambas direcciones muestra por qué la multiplicidad es importante: permite describir con precisión qué clase “posee” a cuál, qué objetos dependen de otros y cómo se estructura el modelo de datos en orientación a objetos.

***



## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

La **composición fuerte** describe una relación en la que un objeto “contenedor” es dueño exclusivo de los objetos que lo componen. En este caso, las partes no tienen sentido de manera independiente y su ciclo de vida está completamente ligado al del todo. Si el objeto contenedor deja de existir, necesariamente también desaparecen sus componentes. Esta relación es la que se considera **composición en sentido estricto**, y suele representarse en UML con un rombo negro. Un ejemplo típico sería una `Linea` que crea internamente y posee sus dos `Punto`: si la línea se destruye, sus puntos dejan de existir porque no se conciben fuera de esa estructura.

Por el contrario, la **composición débil** implica que el objeto “contenedor” utiliza o agrupa otros objetos, pero no es su propietario exclusivo. Las partes pueden existir fuera del conjunto, pertenecer a varios contenedores o sobrevivir independientemente del ciclo de vida del todo. En este caso, si el objeto contenedor desaparece, los objetos relacionados pueden seguir existiendo sin problema. Esta relación suele denominarse **asociación** o **agregación**, y se representa en UML con un rombo blanco. Por ejemplo, si una `Linea` recibe como parámetros dos `Punto` ya existentes y no los crea ni controla su ciclo de vida, entonces esos puntos pueden seguir existiendo incluso si la línea se elimina.

La principal consecuencia de distinguir entre composición fuerte y débil recae, por tanto, en la **responsabilidad sobre el ciclo de vida de los objetos**. En una composición fuerte, el contenedor controla creación, mantenimiento y destrucción de las partes. En una composición débil, sin embargo, el contenedor solo utiliza esas partes, sin dictar su existencia. Esto también influye en cómo se diseñan los constructores, la gestión de memoria y las reglas de inmutabilidad y encapsulación en un programa orientado a objetos.

En resumen, solemos llamar **asociación/agregación** a la composición débil, donde las partes viven de manera independiente, y llamamos **composición** propiamente dicha a la composición fuerte, donde las partes dependen completamente del objeto que las contiene.

***



## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

Cuando una clase utiliza a otra solamente **recibiéndola como parámetro**, **devolviéndola como resultado**, **creándola dentro de un método** mediante `new`, o **empleándola como variable local**, no se habla de composición, sino de **dependencia**. Esta relación expresa que un método necesita temporalmente otra clase para llevar a cabo una operación, pero sin que exista una relación estructural duradera entre los objetos. La dependencia es, por tanto, una relación más débil y efímera, que no afecta al ciclo de vida del objeto que se usa de manera puntual.

A diferencia de la composición, la dependencia **no implica propiedad** ni responsabilidad sobre el objeto dependiente. Cuando una clase depende de otra, no se está diciendo que “tenga” instancias de esa clase, sino simplemente que “las usa” durante la ejecución de un método. El objeto dependiente puede existir antes y después de dicha llamada, o incluso ser creado y destruido dentro del propio método, sin establecer una relación persistente entre ambas clases.

En diseño orientado a objetos, esta distinción permite separar relaciones estructurales (como composición o agregación) de relaciones operativas (dependencias). Mientras que la composición forma parte de la definición estable de una clase —qué objetos la componen internamente—, la dependencia solo indica que una clase requiere los servicios de otra para completar un comportamiento concreto. Por ello, se considera la forma más débil y transitoria de relación entre clases.

En resumen, recibir, devolver o crear objetos dentro de métodos **no constituye composición**, sino **dependencia**, porque no se integra ningún objeto en la estructura interna de la clase ni se asume responsabilidad sobre su ciclo de vida.

***



## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Dos diseños: composición fuerte vs. composición débil

En **composición fuerte**, el objeto contenedor crea y es dueño exclusivo de sus partes. Para modelarlo, conviene hacer que `Linea` **cree internamente** sus `Punto` a partir de coordenadas (o, si recibe `Punto`, que los **copie**), y que **no exponga** sus instancias internas; en su lugar, se pueden devolver **copias**. De este modo, no hay alias externos a los extremos de la línea y, cuando la `Linea` deja de existir, sus `Punto` quedan inalcanzables y son recolectados por el GC. Se mantiene además la inmutabilidad para simplificar las invariantes.

En **composición débil** (agregación/asociación), la `Linea` **no posee** los puntos, sino que **referencia** instancias creadas y gestionadas externamente. La línea puede compartir puntos con otras líneas, y los puntos pueden vivir más allá del ciclo de vida de la `Linea`. Si los puntos fueran mutables, una modificación externa se reflejaría en la longitud; si son inmutables, igualmente queda claro que el ciclo de vida **no** está ligado al de la línea. A continuación se incluyen ambas variantes y una pequeña demo.

***

#### Versión 1: **Composición fuerte** (la línea posee y encapsula sus puntos)

```java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    public double distanciaA(Punto otro) {
        if (otro == null) throw new IllegalArgumentException("Punto nulo");
        return Math.hypot(otro.x - this.x, otro.y - this.y);
    }

    @Override
    public String toString() {
        return "Punto(" + x + ", " + y + ")";
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Punto)) return false;
        Punto p = (Punto) o;
        return Double.compare(x, p.x) == 0 && Double.compare(y, p.y) == 0;
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(x, y);
    }
}
```

***

```java
/** Composición fuerte: la línea crea y encapsula sus puntos. */
public final class LineaFuerte {
    private final Punto a;
    private final Punto b;

    /** Se crean internamente los extremos; no se aceptan referencias externas. */
    public LineaFuerte(double ax, double ay, double bx, double by) {
        Punto pa = new Punto(ax, ay);
        Punto pb = new Punto(bx, by);
        if (pa.equals(pb)) throw new IllegalArgumentException("Extremos coincidentes");
        this.a = pa;
        this.b = pb;
    }

    /** Se devuelven COPIAS para no exponer las instancias internas. */
    public Punto getA() { return new Punto(a.getX(), a.getY()); }
    public Punto getB() { return new Punto(b.getX(), b.getY()); }

    public double longitud() { return a.distanciaA(b); }

    @Override
    public String toString() {
        return "LineaFuerte[" + a + " -> " + b + "]";
    }
}
```

***

#### Versión 2: **Composición débil / Agregación** (la línea referencia puntos externos)

```java
/** Composición débil: la línea usa puntos proporcionados desde fuera. */
public final class LineaDebil {
    private final Punto a;
    private final Punto b;

    /** Se aceptan referencias externas; la línea NO es dueña de los puntos. */
    public LineaDebil(Punto a, Punto b) {
        if (a == null || b == null) throw new IllegalArgumentException("Extremos nulos");
        if (a.equals(b)) throw new IllegalArgumentException("Extremos coincidentes");
        this.a = a;
        this.b = b;
    }

    /** Se exponen las mismas referencias (seguras aquí por ser Punto inmutable). */
    public Punto getA() { return a; }
    public Punto getB() { return b; }

    public double longitud() { return a.distanciaA(b); }

    @Override
    public String toString() {
        return "LineaDebil[" + a + " -> " + b + "]";
    }
}
```

***

#### Demo mínima

```java
public class Demo {
    public static void main(String[] args) {
        // Composición fuerte: Linea crea y encapsula sus puntos
        LineaFuerte lf = new LineaFuerte(0, 0, 3, 4);
        System.out.println(lf);
        System.out.println("Longitud (fuerte): " + lf.longitud());

        // Composición débil: Linea referencia puntos creados externamente
        Punto p1 = new Punto(0, 0);
        Punto p2 = new Punto(3, 4);
        LineaDebil ld = new LineaDebil(p1, p2);
        System.out.println(ld);
        System.out.println("Longitud (débil): " + ld.longitud());

        // Nota: En la débil, p1 y p2 pueden reutilizarse en otras líneas y
        // seguir existiendo aunque 'ld' deje de usarse.
    }
}
```

En la variante fuerte, la `LineaFuerte` controla la creación de sus extremos y **no** permite que existan alias externos hacia esos `Punto` internos; incluso los *getters* devuelven **copias**. Así, la pertenencia y el ciclo de vida quedan claramente ligados a la línea. En la variante débil, la `LineaDebil` recibe referencias externas y las expone, lo que deja claro que **no** posee los puntos; si se reutilizan en otras líneas o sobreviven a la `LineaDebil`, el modelo refleja correctamente que el ciclo de vida está desacoplado.

***



## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

En una **composición fuerte** en Java, el contenedor no destruye explícitamente a sus componentes porque el lenguaje **no ofrece destrucción manual de objetos**. La gestión de memoria en Java está delegada al **garbage collector (GC)**, que se encarga automáticamente de liberar los objetos cuando ya no existen referencias accesibles hacia ellos. Esto significa que, cuando un objeto como `Linea` deja de ser alcanzable, también lo dejan de ser los `Punto` que contiene —si nadie más tiene referencias hacia ellos—, y el GC podrá reclamarlos en algún ciclo futuro.

El motivo fundamental por el que no se observa destrucción explícita es que Java **no implementa destructores** como en C++. En su lugar, adopta un modelo de memoria seguro y automático. No es necesario (ni posible) invocar `delete`, ni liberar manualmente los objetos compuestos. Esto evita errores clásicos como *double free*, *use‑after‑free* o fugas de memoria causadas por olvidos al liberar recursos.

La consecuencia práctica es que, en composición fuerte, el ciclo de vida queda implícito: cuando el objeto contenedor cae en desuso, sus componentes también se vuelven inalcanzables. El programador solo debe asegurarse de que no existen referencias externas (alias) hacia esos componentes si desea que realmente dependan del ciclo de vida del contenedor. En este sentido, la inmutabilidad y la encapsulación ayudan a garantizar que los objetos internos no escapan y que su vida depende exclusivamente de la del objeto compuesto.

En resumen, la destrucción en composición fuerte ocurre **de manera automática e indirecta**: el contenedor deja de ser accesible, sus componentes dejan de ser accesibles, y el GC libera los recursos en un momento indeterminado posterior. Por eso no aparece ningún código de destrucción explícita en clases como `Linea`.

***



## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Composición débil con invariantes: `Departamento` ↔ `Profesor`

Se modela **agregación (composición débil)** haciendo que `Departamento` *use* objetos `Profesor` existentes sin apropiarse de su ciclo de vida. Se mantiene la **invariante**: siempre existe un director y dicho director **pertenece** a la lista del departamento. Para no romper la encapsulación, se emplea internamente `Profesor[]` con tope `50`, pero la API pública no expone el array: se ofrecen métodos para añadir al final, eliminar por posición, consultar el número de profesores y obtener un profesor por posición. Se lanza excepción si se intenta violar la invariante (p. ej., eliminar al director sin antes cambiarlo).

Se incluyen dos relaciones simultáneas: (1) el departamento **agrega** una colección de profesores; y (2) el departamento **agrega** a su director (que además **está en** la colección). El constructor exige un director inicial y lo inserta en la lista; `cambiarDirector` solo admite un profesor que ya figure en el departamento; `eliminarProfesorEn` impide eliminar al director (debe cambiarse primero). La clase `Profesor` se define inmutable y con identidad por `id`, lo que facilita evitar duplicados y razona mejor sobre la pertenencia.

```java
// Clase simple e inmutable para agregación débil.
public final class Profesor {
    private final String id;     // Identificador único (ej., DNI o código interno)
    private final String nombre;

    public Profesor(String id, String nombre) {
        if (id == null || id.isBlank()) throw new IllegalArgumentException("id inválido");
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("nombre inválido");
        this.id = id;
        this.nombre = nombre;
    }

    public String getId() { return id; }
    public String getNombre() { return nombre; }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Profesor)) return false;
        Profesor p = (Profesor) o;
        return id.equals(p.id); // identidad por id
    }

    @Override
    public int hashCode() { return id.hashCode(); }

    @Override
    public String toString() { return "Profesor{id='" + id + "', nombre='" + nombre + "'}"; }
}
```

***

```java
import java.util.Arrays;

/**
 * Agregación (composición débil) de Profesores.
 * Invariante de clase:
 *   - Siempre hay director (director != null).
 *   - El director SIEMPRE está en la lista de profesores (0 <= indexOf(director) < size).
 *   - size en [1..MAX] (nunca 0 porque al menos está el director).
 */
public final class Departamento {
    private static final int MAX = 50;

    private final Profesor[] profesores = new Profesor[MAX]; // detalle encapsulado
    private int size = 0;
    private Profesor director;

    /** Crea un departamento con director inicial, que se añade a la lista. */
    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) throw new IllegalArgumentException("director inicial nulo");
        this.director = directorInicial;
        this.profesores[size++] = directorInicial; // garantiza pertenencia desde el inicio
        verificarInvariante();
    }

    /** Variante con lista inicial adicional; el director siempre queda incluido. */
    public Departamento(Profesor directorInicial, Profesor... iniciales) {
        if (directorInicial == null) throw new IllegalArgumentException("director inicial nulo");
        this.director = directorInicial;
        // Añadir director primero
        this.profesores[size++] = directorInicial;
        // Añadir el resto evitando duplicados y respetando capacidad
        if (iniciales != null) {
            for (Profesor p : iniciales) {
                if (p == null) continue;
                if (size >= MAX) throw new IllegalStateException("Capacidad máxima alcanzada");
                if (indexOf(p) == -1) {
                    profesores[size++] = p;
                }
            }
        }
        verificarInvariante();
    }

    /** Número de profesores del departamento. */
    public int numeroProfesores() { return size; }

    /** Obtiene el profesor por posición [0..size-1]. No se expone el array. */
    public Profesor profesorEn(int posicion) {
        rango(posicion);
        return profesores[posicion];
    }

    /** Devuelve el director actual. */
    public Profesor getDirector() { return director; }

    /** Añade al final; no admite nulos ni duplicados; respeta tope MAX. */
    public void anadirProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException("profesor nulo");
        if (size >= MAX) throw new IllegalStateException("Capacidad máxima alcanzada");
        if (indexOf(p) != -1) throw new IllegalArgumentException("el profesor ya está en el departamento");
        profesores[size++] = p;
        verificarInvariante();
    }

    /**
     * Elimina el profesor en la posición dada y devuelve el eliminado.
     * Prohíbe eliminar al director para no violar la invariante (cámbiese antes).
     */
    public Profesor eliminarProfesorEn(int posicion) {
        rango(posicion);
        Profesor eliminado = profesores[posicion];
        if (eliminado.equals(director)) {
            throw new IllegalStateException("No se puede eliminar al director; cámbielo antes");
        }
        // Compactar el array desplazando hacia la izquierda
        int elementosAMover = size - posicion - 1;
        if (elementosAMover > 0) {
            System.arraycopy(profesores, posicion + 1, profesores, posicion, elementosAMover);
        }
        profesores[--size] = null;
        verificarInvariante();
        return eliminado;
    }

    /**
     * Cambia el director por otro profesor que ya pertenezca al departamento.
     * No admite nulos ni profesores ajenos a la lista.
     */
    public void cambiarDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) throw new IllegalArgumentException("nuevo director nulo");
        if (indexOf(nuevoDirector) == -1) {
            throw new IllegalArgumentException("el nuevo director debe pertenecer al departamento");
        }
        this.director = nuevoDirector;
        verificarInvariante();
    }

    // --- Utilidades privadas ---

    private int indexOf(Profesor p) {
        for (int i = 0; i < size; i++) {
            if (profesores[i].equals(p)) return i;
        }
        return -1;
    }

    private void rango(int posicion) {
        if (posicion < 0 || posicion >= size) {
            throw new IndexOutOfBoundsException("posición fuera de rango: " + posicion);
        }
    }

    private void verificarInvariante() {
        if (director == null) throw new IllegalStateException("invariante rota: director nulo");
        int idx = indexOf(director);
        if (idx == -1) throw new IllegalStateException("invariante rota: director no está en la lista");
        if (size < 1 || size > MAX) throw new IllegalStateException("invariante rota: tamaño inválido");
    }

    @Override
    public String toString() {
        return "Departamento{director=" + director +
               ", profesores=" + Arrays.asList(Arrays.copyOf(profesores, size)) + "}";
    }
}
```

***

```java
// Ejemplo de uso
public class Demo {
    public static void main(String[] args) {
        Profesor a = new Profesor("P01", "Ana");
        Profesor b = new Profesor("P02", "Bruno");
        Profesor c = new Profesor("P03", "Carmen");

        Departamento dpto = new Departamento(a, b); // Ana es directora y ya está en la lista
        dpto.anadirProfesor(c);
        System.out.println("Nº profesores: " + dpto.numeroProfesores()); // 3

        dpto.cambiarDirector(c); // director pasa a ser Carmen (ya pertenecía)
        System.out.println("Director: " + dpto.getDirector());

        // Eliminación segura: no se puede eliminar al director actual
        Profesor eliminado = dpto.eliminarProfesorEn(1); // elimina a Bruno si no es director
        System.out.println("Eliminado: " + eliminado);
    }
}
```

Este diseño cumple la agregación solicitada, garantiza la invariante en todo momento y mantiene la encapsulación del almacenamiento interno (no se expone el array ni se permite su modificación externa). La política de duplicados se gestiona por identidad (`id`) en `Profesor`, se respeta el límite de capacidad y las operaciones fallan de forma explícita con excepciones cuando una acción podría dejar al objeto en un estado inválido.

***



## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Versión con `List`: código y diferencias clave

Al emplear `List` (con `ArrayList`) se simplifica la gestión interna respecto a los arrays primitivos. Desaparecen la contabilidad manual del tamaño, el manejo de huecos nulos y el desplazamiento con `System.arraycopy` para eliminar. Además, `indexOf`, `contains` y `remove(int)` proporcionan operaciones de búsqueda y borrado más expresivas. Se mantiene el tope de capacidad `MAX = 50` como invariante de dominio, no como limitación estructural de la colección. Se conserva la agregación débil: el departamento **usa** instancias de `Profesor` sin ser su propietario exclusivo, y se impone la invariante de clase: siempre hay director y el director pertenece a la lista.

```java
// Igual que antes: inmutable, identidad por id
public final class Profesor {
    private final String id;
    private final String nombre;

    public Profesor(String id, String nombre) {
        if (id == null || id.isBlank()) throw new IllegalArgumentException("id inválido");
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("nombre inválido");
        this.id = id;
        this.nombre = nombre;
    }

    public String getId() { return id; }
    public String getNombre() { return nombre; }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Profesor)) return false;
        return id.equals(((Profesor) o).id);
    }

    @Override
    public int hashCode() { return id.hashCode(); }

    @Override
    public String toString() { return "Profesor{id='" + id + "', nombre='" + nombre + "'}"; }
}
```

***

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

/**
 * Agregación débil con List.
 * Invariante:
 *  - director != null
 *  - director ∈ profesores
 *  - 1 <= profesores.size() <= MAX
 */
public final class Departamento {
    private static final int MAX = 50;

    private final List<Profesor> profesores = new ArrayList<>();
    private Profesor director;

    /** Siempre arranca con director, que se añade a la lista. */
    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) throw new IllegalArgumentException("director inicial nulo");
        this.director = directorInicial;
        profesores.add(directorInicial);
        verificarInvariante();
    }

    /** Variante con lista inicial adicional; evita duplicados y respeta capacidad. */
    public Departamento(Profesor directorInicial, List<Profesor> iniciales) {
        if (directorInicial == null) throw new IllegalArgumentException("director inicial nulo");
        this.director = directorInicial;
        profesores.add(directorInicial);
        if (iniciales != null) {
            for (Profesor p : iniciales) {
                if (p == null) continue;
                if (profesores.size() >= MAX) throw new IllegalStateException("Capacidad máxima alcanzada");
                if (!profesores.contains(p)) profesores.add(p);
            }
        }
        verificarInvariante();
    }

    public int numeroProfesores() { return profesores.size(); }

    public Profesor profesorEn(int pos) { return profesores.get(pos); } // delega validación a List

    public Profesor getDirector() { return director; }

    /** Añade al final; no admite nulos ni duplicados; respeta MAX. */
    public void anadirProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException("profesor nulo");
        if (profesores.size() >= MAX) throw new IllegalStateException("Capacidad máxima alcanzada");
        if (profesores.contains(p)) throw new IllegalArgumentException("el profesor ya pertenece al departamento");
        profesores.add(p);
        verificarInvariante();
    }

    /**
     * Elimina el profesor en la posición dada.
     * Prohíbe eliminar al director; cámbiese antes.
     */
    public Profesor eliminarProfesorEn(int pos) {
        Profesor candidato = profesores.get(pos);
        if (candidato.equals(director)) {
            throw new IllegalStateException("No se puede eliminar al director; cámbielo antes");
        }
        Profesor eliminado = profesores.remove(pos);
        verificarInvariante();
        return eliminado;
    }

    /** Cambia el director a otro miembro ya existente. */
    public void cambiarDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) throw new IllegalArgumentException("nuevo director nulo");
        if (!profesores.contains(nuevoDirector)) {
            throw new IllegalArgumentException("el nuevo director debe pertenecer al departamento");
        }
        this.director = nuevoDirector;
        verificarInvariante();
    }

    /** Lista inmodificable de lectura (no expone la estructura interna). */
    public List<Profesor> getProfesores() {
        return Collections.unmodifiableList(profesores);
    }

    private void verificarInvariante() {
        if (director == null) throw new IllegalStateException("invariante rota: director nulo");
        if (!profesores.contains(director)) {
            throw new IllegalStateException("invariante rota: el director no está en la lista");
        }
        if (profesores.isEmpty() || profesores.size() > MAX) {
            throw new IllegalStateException("invariante rota: tamaño inválido");
        }
    }

    @Override
    public String toString() {
        return "Departamento{director=" + director + ", profesores=" + profesores + "}";
    }
}
```

***

### ¿Qué se ha ahorrado y cómo exponer “todos los profesores” sin romper la encapsulación?

Con `List` se ha eliminado la **contabilidad manual** del tamaño (`size`), el **relleno y anulación de huecos** del array, y el **desplazamiento** con `System.arraycopy` al eliminar por posición. También se aprovechan utilidades como `contains`, `indexOf`, `remove(int)` y las excepciones estándar de rango que lanza `List#get`. En suma, se reduce código “ceremonial” y propenso a errores, manteniendo exactamente las mismas invariantes de dominio (capacidad máxima, unicidad y pertenencia del director).

Si existiera un método que devolviera *todos* los profesores, devolver la **lista interna** directamente permitiría a clientes **modificar el estado** del departamento por fuera de sus reglas (por ejemplo, quitar al director o añadir más de 50), violando encapsulación e invariantes. Para evitarlo, se devuelve una **vista no modificable** (`Collections.unmodifiableList(profesores)` o `List.copyOf(profesores)`), que permite leer pero no alterar. Si los elementos fueran mutables, además de la vista inmodificable se requerirían **copias defensivas** (o exponer DTOs) para impedir que cambios sobre cada `Profesor` afecten indirectamente al departamento; en este diseño, `Profesor` es inmutable, por lo que la vista inmodificable es suficiente.

***



## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Composición recursiva inmutable: `Persona` con referencia a su madre

Una **composición recursiva** aparece cuando una clase contiene una referencia a otra instancia de su **misma** clase. En el ejemplo, `Persona` es inmutable y contiene una referencia a su `madre`, que también es una `Persona`. Se garantiza la inmutabilidad declarando los campos como `private final`, sin exponer *setters*, y ofreciendo solo métodos de lectura. Se permite que la madre sea `null` cuando se desconoce o cuando se alcanza el “tope” de la genealogía, como en el caso de una abuela sin registro de su madre, lo que hace el modelo práctico para construir líneas ascendentes de parentesco.

A continuación se muestra una implementación sencilla, con `toString` y un método de utilidad `lineaMaterna()` para recorrer la cadena madre‑de‑madre. El *main* construye una familia lineal “nieto → madre → abuela” y ejemplifica tanto la creación como la consulta de la línea materna. La inmutabilidad garantiza que, una vez creada la genealogía, no se puede alterar accidentalmente y se mantiene coherente a lo largo del programa.

```java
public final class Persona {
    private final String nombre;
    private final Persona madre; // puede ser null en la raíz de la cadena

    public Persona(String nombre, Persona madre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("nombre inválido");
        }
        this.nombre = nombre;
        this.madre = madre; // null permitido para la raíz
    }

    public String getNombre() { return nombre; }
    public Persona getMadre() { return madre; }

    /** Devuelve la cadena de ancestros maternos desde esta persona hacia arriba (incluye this). */
    public java.util.List<Persona> lineaMaterna() {
        java.util.ArrayList<Persona> linea = new java.util.ArrayList<>();
        Persona actual = this;
        while (actual != null) {
            linea.add(actual);
            actual = actual.madre;
        }
        return java.util.Collections.unmodifiableList(linea);
    }

    @Override
    public String toString() {
        return "Persona{" + nombre + (madre != null ? ", madre=" + madre.nombre : ", madre=null") + "}";
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Persona)) return false;
        Persona p = (Persona) o;
        // Para ejemplo: igualdad por identidad nominal + estructura materna (simplificada)
        return nombre.equals(p.nombre) && 
               ((madre == null && p.madre == null) || (madre != null && madre.equals(p.madre)));
    }

    @Override
    public int hashCode() {
        return java.util.Objects.hash(nombre, madre);
    }
}

public class Demo {
    public static void main(String[] args) {
        Persona abuela = new Persona("Lola", null);
        Persona madre  = new Persona("Ana", abuela);
        Persona nieto  = new Persona("Diego", madre);

        System.out.println("Nieto: " + nieto.getNombre());
        System.out.println("Madre del nieto: " + nieto.getMadre().getNombre());
        System.out.println("Abuela del nieto: " + nieto.getMadre().getMadre().getNombre());

        System.out.println("\nLínea materna de " + nieto.getNombre() + ":");
        for (Persona p : nieto.lineaMaterna()) {
            System.out.println(" - " + p.getNombre());
        }
    }
}
```

***

Otros ejemplos clásicos de **composiciones recursivas** incluyen: listas enlazadas (cada nodo referencia al siguiente), árboles binarios o *tries* (cada nodo contiene referencias a hijos del mismo tipo), directorios de un sistema de archivos (un directorio contiene subdirectorios y archivos; los subdirectorios son a su vez directorios), el DOM/AST en compiladores o navegadores (nodos contienen otros nodos), organizaciones jerárquicas (empleado con “manager” que también es empleado), y, como se mencionó, la cadena de **causas** en las excepciones de Java (`Throwable` con su `cause`) que modela un encadenamiento potencialmente arbitrario del mismo tipo. Estos diseños permiten recorrer estructuras de tamaño y profundidad variables con patrones uniformes.

***



## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Qué es una composición bidireccional

Una **composición (o agregación) bidireccional** es una relación en la que **cada extremo mantiene una referencia al otro** y ambas vistas se mantienen **consistentes**: el contenedor conoce a sus partes y cada parte conoce a su contenedor. En términos de diseño, esto obliga a definir **puntos únicos de actualización** (métodos de enlace/desenlace) para evitar estados incoherentes y asegurar las **invariantes** (por ejemplo, que el director del departamento pertenezca a su lista). También conviene **ocultar** los *setters* del lado “parte” para que solo el contenedor pueda establecer la relación, y evitar que el cliente rompa la bidireccionalidad.

En el ejemplo `Departamento`–`Profesor`, implementar una relación bidireccional implica: (1) añadir en `Profesor` un campo `departamento` con un método **no público** (p. ej., *package‑private* o `protected`) para fijarlo; (2) centralizar en `Departamento` las operaciones `añadir/eliminar/cambiarDirector`, que además de actualizar su `List` **sincronicen** la contrarreferencia en `Profesor`; y (3) gestionar **traslados** entre departamentos, anulando la referencia previa si el profesor ya pertenecía a otro. Al devolver colecciones, se seguirá ofreciendo una **vista inmodificable** para no saltarse las reglas, y se seguirá validando la invariante de que el director siempre esté en la lista.

A continuación se muestra un esquema mínimo. Nótese que `Profesor#setDepartamento` **no es público**, de modo que solo el propio modelo puede mantener la simetría; `Departamento` es el **único** punto autorizado para mutar la asociación (añadir/quitar/trasladar), y cada operación actualiza **ambos lados** de la relación (lista ↔ campo `departamento`), preservando las invariantes (capacidad, unicidad, director ∈ lista).

```java
// Lado "parte": conoce a su contenedor, pero no expone setter público.
public final class Profesor {
    private final String id;
    private final String nombre;
    private Departamento departamento; // back-reference

    public Profesor(String id, String nombre) {
        if (id == null || id.isBlank()) throw new IllegalArgumentException("id inválido");
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("nombre inválido");
        this.id = id;
        this.nombre = nombre;
    }

    public String getId() { return id; }
    public String getNombre() { return nombre; }
    public Departamento getDepartamento() { return departamento; }

    // Setter restringido: visibilidad de paquete (o protected) para evitar uso externo.
    void setDepartamento(Departamento dpto) { this.departamento = dpto; }

    @Override public boolean equals(Object o) {
        if (!(o instanceof Profesor)) return false;
        return id.equals(((Profesor)o).id);
    }
    @Override public int hashCode() { return id.hashCode(); }
    @Override public String toString() {
        return "Profesor{id='" + id + "', nombre='" + nombre + "'}";
    }
}
```

***

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public final class Departamento {
    private static final int MAX = 50;

    private final List<Profesor> profesores = new ArrayList<>();
    private Profesor director;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) throw new IllegalArgumentException("director inicial nulo");
        anadirProfesor(directorInicial);   // sincroniza ambos lados
        this.director = directorInicial;
        verificarInvariante();
    }

    public int numeroProfesores() { return profesores.size(); }
    public Profesor profesorEn(int i) { return profesores.get(i); }
    public Profesor getDirector() { return director; }
    public List<Profesor> getProfesores() { return Collections.unmodifiableList(profesores); }

    /** Añade y sincroniza la contrarreferencia; gestiona traslado si venía de otro depto. */
    public void anadirProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException("profesor nulo");
        if (profesores.size() >= MAX) throw new IllegalStateException("capacidad máxima alcanzada");
        if (profesores.contains(p)) throw new IllegalArgumentException("duplicado");
        Departamento anterior = p.getDepartamento();
        if (anterior != null && anterior != this) {
            anterior.eliminarProfesor(p); // desenlaza del anterior (ambos lados)
        }
        profesores.add(p);
        p.setDepartamento(this);
        verificarInvariante();
    }

    /** Elimina por posición preservando la bidireccionalidad (no permite eliminar director). */
    public Profesor eliminarProfesorEn(int pos) {
        Profesor objetivo = profesores.get(pos);
        if (objetivo.equals(director)) {
            throw new IllegalStateException("no se puede eliminar al director; cámbielo antes");
        }
        profesores.remove(pos);
        objetivo.setDepartamento(null);
        verificarInvariante();
        return objetivo;
    }

    /** Elimina por identidad (usado en traslados). */
    private void eliminarProfesor(Profesor p) {
        int idx = profesores.indexOf(p);
        if (idx == -1) return;
        if (p.equals(director)) throw new IllegalStateException("no se puede eliminar al director");
        profesores.remove(idx);
        p.setDepartamento(null);
    }

    /** Cambia el director a un profesor que ya pertenezca a este departamento. */
    public void cambiarDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) throw new IllegalArgumentException("nuevo director nulo");
        if (!profesores.contains(nuevoDirector))
            throw new IllegalArgumentException("el nuevo director debe pertenecer al departamento");
        this.director = nuevoDirector;
        verificarInvariante();
    }

    private void verificarInvariante() {
        if (director == null) throw new IllegalStateException("invariante: director nulo");
        if (!profesores.contains(director))
            throw new IllegalStateException("invariante: el director debe estar en la lista");
        if (profesores.isEmpty() || profesores.size() > MAX)
            throw new IllegalStateException("invariante: tamaño inválido");
    }

    @Override public String toString() {
        return "Departamento{director=" + director + ", profesores=" + profesores + "}";
    }
}
```

***

> Puntos de diseño clave: centralizar las mutaciones en `Departamento` para **mantener sincronía**, restringir el *setter* del back‑reference en `Profesor`, devolver **vistas inmodificables** en operaciones de lectura y **verificar invariantes** después de cada actualización. Con ello se obtiene una relación bidireccional coherente, segura y fácil de razonar.

***
