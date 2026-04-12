<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### 1. En orientación a objetos, ¿qué es la **herencia** y su relación con “A es-un B”?

En orientación a objetos, la **herencia** es un mecanismo mediante el cual una clase puede definirse a partir de otra, reutilizando y extendiendo su definición. Cuando se afirma que *A es-un B*, se expresa una relación de especialización: un objeto de tipo A puede considerarse también un objeto de tipo B. Esta relación no es simplemente conceptual, sino que tiene consecuencias directas en el sistema de tipos y en cómo se estructura la memoria de los objetos.

La primera implicación es la **compatibilidad de tipos**. Si una clase `Artillero` hereda de `Soldado`, cualquier instancia de `Artillero` puede tratarse como un `Soldado`. Esto permite escribir código que trabaje con referencias al tipo base sin conocer los detalles concretos del subtipo, de forma similar a cómo, en C, se puede trabajar con punteros genéricos pero de manera segura y verificada por el compilador.

La segunda implicación es la **herencia de estado y comportamiento**. Las subclases heredan los atributos y métodos accesibles de la superclase. Esto significa que un `Artillero` o un `Zapador` contiene internamente el estado definido en `Soldado` (como el nombre) y puede reutilizar su comportamiento (por ejemplo, el método `saludar()`), añadiendo además funcionalidades específicas.

```java
class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soy el soldado " + nombre);
    }
}

class Artillero extends Soldado {
    private int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
    }

    public int getCohetes() {
        return cohetes;
    }
}

class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() {
        return minas;
    }
}

// Uso de la compatibilidad de tipos
Soldado[] soldados = new Soldado[] {
    new Artillero("Luis", 5),
    new Zapador("Ana", 3),
    new Artillero("Carlos", 2)
};

for (Soldado s : soldados) {
    s.saludar();
}
```

***



## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

### 2. Constructores, orden de ejecución y uso de `super`

Al crear un objeto de una subclase, no se ejecuta únicamente el constructor de dicha subclase, sino también el de todas sus superclases. En una jerarquía simple como `Soldado → Artillero`, se ejecutan **dos constructores**: primero el de `Soldado` y después el de `Artillero`. Este orden garantiza que la parte base del objeto esté correctamente inicializada antes de añadir el comportamiento específico del subtipo.

La palabra clave `super` dentro de un constructor se utiliza para invocar explícitamente al constructor de la superclase. Conceptualmente, es equivalente a indicar que parte de la inicialización del objeto debe delegarse en la clase base. En el ejemplo anterior, `super(nombre)` permite inicializar el atributo `nombre`, que pertenece a `Soldado`.

Si la clase base **no tiene un constructor sin parámetros accesible**, es obligatorio llamar explícitamente a `super` con los argumentos adecuados. De lo contrario, el compilador generará un error. Esto fuerza a que las subclases respeten las reglas de inicialización impuestas por la superclase, reforzando la coherencia del modelo.

***



## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### 3. Atributos privados de la superclase y memoria de la subclase

Desde el punto de vista de memoria, una instancia de una subclase **sí contiene** los atributos definidos en la superclase, incluso si estos son privados. Un objeto `Artillero` incluye internamente la parte correspondiente a `Soldado`, con su atributo `nombre`, además de sus propios atributos como `cohetes`.

Sin embargo, el hecho de que esos atributos formen parte del objeto **no implica que puedan usarse directamente desde el código de la subclase**. En Java, el modificador `private` limita el acceso únicamente a la propia clase donde se declara el atributo. Por tanto, aunque `Artillero` hereda el estado de `Soldado`, no puede acceder directamente a `nombre`.

Esto refuerza la encapsulación: la subclase debe interactuar con el estado heredado a través de métodos públicos o protegidos definidos por la superclase. En el ejemplo, el método `saludar()` pertenece a `Soldado` y puede usar `nombre` internamente, mientras que `Artillero` reutiliza ese comportamiento sin violar las restricciones de acceso.

***



## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### 4. Compatibilidad de tipos y extensibilidad del código

La compatibilidad de tipos aporta una ventaja clave en términos de **extensibilidad**. El código que trabaja con el tipo base no necesita modificarse cuando se añaden nuevos subtipos. Esto permite que un sistema crezca añadiendo comportamientos nuevos sin reescribir lógica existente, siguiendo el principio de *abierto/cerrado*.

Si se añade un nuevo tipo de soldado, por ejemplo un `Medico`, basta con que herede de `Soldado` para que sea compatible con cualquier código que ya opere sobre arrays o colecciones de `Soldado`. El bucle que solicita el saludo a todos los soldados funciona sin cambios, ya que solo depende de la interfaz común.

```java
class Medico extends Soldado {
    public Medico(String nombre) {
        super(nombre);
    }

    public void curar() {
        System.out.println("Curando heridos");
    }
}

// El código existente no se modifica
Soldado[] soldados = new Soldado[] {
    new Artillero("Luis", 5),
    new Zapador("Ana", 3),
    new Medico("Elena")
};

for (Soldado s : soldados) {
    s.saludar();
}
```

***



## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### 5. Referencias, *casting* e `instanceof`

En Java, una referencia del **supertipo puede apuntar a objetos reales de un subtipo**. Esto es una consecuencia directa de la compatibilidad de tipos: una variable de tipo `Soldado` puede referenciar un objeto `Artillero` o `Zapador`. A través de esa referencia solo se pueden invocar métodos definidos en el tipo estático (`Soldado`), aunque el objeto real sea más específico.

El **upcasting** consiste en tratar un objeto de un subtipo como si fuera del supertipo, y es implícito y seguro. El **downcasting** es la operación inversa: convertir una referencia del supertipo a una del subtipo concreto. Esta operación no es segura por defecto y debe comprobarse, ya que podría provocar errores en tiempo de ejecución.

El operador `instanceof` permite comprobar el tipo real del objeto antes de realizar un *downcasting*. De esta forma se puede acceder al comportamiento específico del subtipo solo cuando sea seguro hacerlo.

```java
for (Soldado s : soldados) {
    s.saludar();
    if (s instanceof Artillero) {
        Artillero a = (Artillero) s;
        System.out.println("Cohetes: " + a.getCohetes());
    }
}
```

***



## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### 6. Acceso protegido y herencia

El acceso **protegido** (`protected`) es un nivel intermedio de visibilidad que permite el acceso desde la propia clase, desde las subclases y desde otras clases del mismo paquete. Se utiliza cuando se desea permitir que las subclases interactúen con ciertos detalles internos sin hacerlos públicos.

En Java, se implementa utilizando la palabra clave `protected` al declarar atributos o métodos. Esto es habitual en jerarquías de herencia, donde la superclase quiere ofrecer a sus subclases un acceso controlado a parte de su estado interno.

```java
class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }
}

class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }

    public void ponerMinas() {
        System.out.println(nombre + " está poniendo minas");
    }
}
```

***



## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### 7. Clase base común de los objetos

En muchos lenguajes orientados a objetos existe el concepto de una **clase raíz** de la que derivan todas las demás, pero esto no ocurre en todos los lenguajes. En C++, por ejemplo, no existe una clase base implícita común a todos los objetos.

En Java, **todas las clases heredan directa o indirectamente de `java.lang.Object`**. Esto implica que cualquier objeto dispone de métodos comunes como `toString()`, `equals()` o `hashCode()`, incluso si no se hereda explícitamente de ninguna clase.

Esta decisión de diseño proporciona una base común para todos los objetos y permite que ciertas operaciones genéricas funcionen de manera uniforme en todo el lenguaje.

***



## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### 8. Herencia múltiple

La **herencia múltiple** consiste en permitir que una clase herede directamente de más de una superclase. Este mecanismo puede aumentar la flexibilidad, pero introduce problemas de ambigüedad, como el conocido *problema del diamante*, cuando varias superclases definen el mismo comportamiento.

Java **no permite herencia múltiple de clases**. Una clase solo puede extender de otra clase concreta. Esta restricción simplifica el modelo de herencia y evita conflictos complejos en la resolución de métodos y atributos.

No obstante, Java ofrece una alternativa mediante **interfaces**, que permiten herencia múltiple de comportamiento abstracto, manteniendo un modelo más controlado y seguro.

***



## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### 9. Excepciones personalizadas y herencia

En Java, las excepciones son objetos y forman parte de una jerarquía basada en la clase `Throwable`. Crear una excepción personalizada implica heredar de una de sus subclases. Para que una excepción sea **no controlada**, debe heredar de `RuntimeException`.

Además, una excepción puede estar **compuesta** con otros objetos para incluir información contextual. En este caso, la excepción contiene un `Usuario` que causó el problema y permite opcionalmente encadenar la causa subyacente.

```java
class Usuario {
    private String nombre;

    public Usuario(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}

class UsuarioNoEncontradoException extends RuntimeException {
    private Usuario usuario;

    public UsuarioNoEncontradoException(Usuario usuario) {
        this.usuario = usuario;
    }

    public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
        super(causa);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario;
    }
}
```

***



## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### 10. Herencia vs. reutilización de código

La herencia no debe utilizarse únicamente como un mecanismo de reutilización de código porque implica una **relación semántica fuerte** del tipo *es-un*. Si esa relación no existe realmente, el diseño resultante puede ser confuso y frágil.

Reutilizar código mediante herencia acopla fuertemente las clases, de modo que cambios en la superclase pueden afectar de forma inesperada a todas las subclases. Esto dificulta el mantenimiento y reduce la capacidad de evolución del sistema.

Por ello, la herencia debe emplearse cuando se desea modelar una jerarquía conceptual clara, no simplemente para evitar duplicar código.

***



## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### 11. Favorecer la composición frente a la herencia

Favorecer la **composición** significa construir clases a partir de otras, delegando responsabilidades, en lugar de heredar comportamiento. La composición describe relaciones del tipo *tiene-un*, que suelen ser más flexibles que *es-un*.

La principal ventaja es que reduce el acoplamiento entre clases y permite cambiar comportamientos de forma más sencilla. Además, evita muchos de los problemas derivados de dependencias rígidas en jerarquías profundas de herencia.

Este enfoque facilita el mantenimiento y la extensión del código, especialmente en sistemas grandes o en evolución constante.

***



## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### 12. “La herencia rompe la encapsulación”

Se afirma que la herencia rompe la encapsulación porque las subclases pueden depender de **detalles internos** de la superclase. Si esos detalles cambian, las subclases pueden verse afectadas incluso aunque la interfaz pública no se haya modificado.

Cuando una subclase accede a miembros protegidos, queda implícitamente acoplada a la implementación de la superclase. Esto debilita la separación entre interfaz e implementación, que es uno de los pilares de la encapsulación.

La composición evita este problema al obligar a interactuar únicamente a través de interfaces bien definidas.

***



## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### 13. Modelado por herencia y por composición

En el enfoque por **herencia**, se define una superclase `Persona` que contiene los datos comunes (`dni` y `nombre`). Las clases `Estudiante` y `Trabajador` heredan de ella, expresando que ambos *son* personas.

```java
class Persona {
    protected String dni;
    protected String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }
}

class Estudiante extends Persona {
    public Estudiante(String dni, String nombre) {
        super(dni, nombre);
    }
}

class Trabajador extends Persona {
    public Trabajador(String dni, String nombre) {
        super(dni, nombre);
    }
}
```

***

En el enfoque por **composición**, los datos comunes se agrupan en una clase `DatosPersonales`, que es utilizada por `Estudiante` y `Trabajador`. Estas clases *tienen* datos personales, pero no heredan de una jerarquía común.

```java
class DatosPersonales {
    private String dni;
    private String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }
}

class Estudiante {
    private DatosPersonales datos;

    public Estudiante(DatosPersonales datos) {
        this.datos = datos;
    }
}

class Trabajador {
    private DatosPersonales datos;

    public Trabajador(DatosPersonales datos) {
        this.datos = datos;
    }
}
```

***
