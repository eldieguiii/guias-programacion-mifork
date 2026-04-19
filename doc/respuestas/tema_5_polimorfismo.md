<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

El **polimorfismo** es un principio de la programación orientada a objetos que permite tratar objetos de distintas clases relacionadas mediante una clase común (normalmente una superclase) de forma uniforme. Esto significa que una referencia de un tipo base puede apuntar a objetos de diferentes subtipos y que, al invocar un método, el comportamiento que se ejecuta depende del tipo real del objeto y no del tipo de la referencia. Su utilidad principal es permitir escribir código más flexible, reutilizable y desacoplado de las clases concretas.

Desde el punto de vista práctico, el polimorfismo evita el uso de estructuras condicionales complejas (`if`, `switch`) basadas en tipos, delegando el comportamiento específico en cada subclase. Esto encaja con la filosofía de diseño orientado a objetos, en la que los objetos saben cómo comportarse y el código cliente solo conoce la interfaz común.

La **sobreescritura** de métodos consiste en redefinir, en una subclase, un método heredado de la clase base para proporcionar una implementación diferente o especializada. El método sobreescrito debe mantener la misma firma que el método original. La sobreescritura es el mecanismo técnico que hace posible el polimorfismo en tiempo de ejecución.

***



## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

La **ligadura dinámica** (o enlace tardío) es el mecanismo por el cual la selección del método que se va a ejecutar se determina en tiempo de ejecución, en función del tipo real del objeto al que apunta una referencia. A diferencia de la ligadura estática, donde la decisión se toma en tiempo de compilación, aquí el sistema decide dinámicamente qué implementación concreta debe usarse.

Este mecanismo está directamente relacionado con el polimorfismo, ya que permite que una misma llamada a un método tenga comportamientos distintos según el objeto concreto que reciba el mensaje. Sin ligadura dinámica no existiría el polimorfismo tal como se entiende en orientación a objetos, ya que siempre se ejecutaría la versión del método asociada al tipo de la referencia.

En **C++**, la ligadura dinámica debe indicarse explícitamente usando la palabra clave `virtual` en los métodos de la clase base. Si no se emplea, se utiliza ligadura estática por defecto. En **Java**, en cambio, todos los métodos no estáticos son virtuales por defecto, por lo que la ligadura dinámica se aplica automáticamente sin necesidad de indicarlo de forma explícita.

En **Python**, el enlace es siempre dinámico, ya que el lenguaje es interpretado y tipado dinámicamente. No existe una distinción explícita entre métodos virtuales y no virtuales, y el polimorfismo se apoya en la resolución dinámica de métodos y en el principio de *duck typing*.

***



## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

El siguiente ejemplo muestra una clase base `Soldado` con un método `saludar`, y dos subclases que redefinen dicho comportamiento. La clave del polimorfismo aparece al almacenar objetos de distintos tipos concretos en un array de tipo `Soldado` y recorrerlo sin conocer el subtipo real de cada elemento.

El código cliente trabaja únicamente con referencias de tipo `Soldado`, pero el método que se ejecuta en cada iteración depende del tipo real del objeto. Esto demuestra cómo el comportamiento correcto emerge dinámicamente sin necesidad de comprobar tipos de forma explícita.

```java
class Soldado {
    public void saludar() {
        System.out.println("Soldado saludando.");
    }
}

class Zapador extends Soldado {
    @Override
    public void saludar() {
        System.out.println("Zapador listo para la acción.");
    }
}

class Artillero extends Soldado {
    @Override
    public void saludar() {
        System.out.println("Artillero a punto.");
    }
}

public class Main {
    public static void main(String[] args) {
        Soldado[] ejercito = {
            new Zapador(),
            new Artillero(),
            new Zapador()
        };

        for (Soldado s : ejercito) {
            s.saludar();
        }
    }
}
```

***



## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

Al sobrescribir un método, es posible reutilizar el comportamiento de la clase base y ampliarlo o modificarlo ligeramente. Esto permite evitar duplicación de código y mantener una coherencia en el comportamiento común, añadiendo únicamente la funcionalidad específica de la subclase.

En Java, esta invocación se realiza mediante la palabra clave `super`, que permite llamar al método de la clase padre desde la subclase. El método base se ejecuta primero y, a continuación, se añade el comportamiento adicional definido en la subclase.

```java
class Zapador extends Soldado {
    @Override
    public void saludar() {
        super.saludar();
        System.out.println("ZAPADOR A SUS ÓRDENES");
    }
}
```

El uso de `super` es especialmente útil cuando la subclase quiere extender el comportamiento base en lugar de sustituirlo completamente.

***



## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

Al sobrescribir un método en Java, los tipos de los parámetros deben coincidir exactamente con los del método original. El tipo de retorno también debe ser el mismo, o bien un subtipo compatible (retorno covariante). Además, no se puede reducir la visibilidad del método (por ejemplo, pasar de `public` a `protected`).

La **sobreescritura** (*overriding*) redefine un método heredado manteniendo la misma firma, mientras que la **sobrecarga** (*overloading*) consiste en definir varios métodos con el mismo nombre pero con distinta lista de parámetros, normalmente dentro de la misma clase. La sobrecarga no está relacionada con el polimorfismo en tiempo de ejecución, sino con la resolución en tiempo de compilación.

La anotación `@Override` indica explícitamente que se está sobrescribiendo un método de la clase base. Su uso es recomendable porque permite al compilador detectar errores, como cambios accidentales en la firma del método, que impedirían una verdadera sobreescritura.

***



## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

En Java, el polimorfismo se utiliza desde etapas muy tempranas, incluso antes de estudiarlo formalmente. Cuando se sobrescriben métodos como `toString` o `equals`, ya se está aplicando polimorfismo, aunque no siempre se sea consciente de ello.

Por ejemplo, cuando un objeto se imprime por pantalla o se compara con otro, el método invocado depende del tipo real del objeto. Si una clase redefine `toString`, esa versión se ejecuta incluso cuando el objeto se trata como un `Object`, lo que es un caso claro de ligadura dinámica.

Esto demuestra que el polimorfismo no es un concepto aislado o avanzado, sino un mecanismo fundamental que se utiliza de forma habitual en el desarrollo con Java.

***



## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

Una **clase abstracta** es una clase que no puede instanciarse y que sirve como base para otras clases. Puede contener métodos normales y métodos abstractos. Un **método abstracto** es aquel que no tiene implementación y obliga a las subclases a proporcionar su propio comportamiento.

No es posible crear instancias directas de una clase abstracta, ya que su propósito es definir una interfaz común y un comportamiento parcial. Esto resulta especialmente útil cuando se desea garantizar que todas las subclases implementen determinados métodos.

```java
abstract class Soldado {
    public void saludar() {
        System.out.println("Soldado saludando.");
    }

    public abstract void atacar();
}

class Zapador extends Soldado {
    @Override
    public void atacar() {
        System.out.println("El zapador coloca explosivos.");
    }
}

class Artillero extends Soldado {
    @Override
    public void atacar() {
        System.out.println("El artillero dispara el cañón.");
    }
}
```

La palabra clave `abstract` debe colocarse tanto en la definición de la clase como en los métodos que no tengan implementación.

***



## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

La palabra clave `final` aplicada a un **método** impide que este sea sobrescrito por subclases. Esto equivale a bloquear el polimorfismo para ese método concreto. Aplicada a una **clase**, impide que la clase sea heredada, eliminando por completo la posibilidad de polimorfismo basado en herencia.

El uso de `final` suele justificarse por motivos de diseño, seguridad o eficiencia. En algunos casos, se desea garantizar que un comportamiento no pueda ser alterado por subclases, preservando invariantes importantes del sistema.

Un ejemplo conocido de clase `final` en la API estándar de Java es `String`. Al ser inmutable y final, se asegura que su comportamiento no pueda modificarse mediante herencia.

***



## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

Una **interfaz** en Java define un conjunto de métodos que una clase se compromete a implementar. No representa una relación “es un” basada en herencia de implementación, sino un contrato de comportamiento. A diferencia de las clases abstractas, las interfaces no contienen estado y sus métodos no tienen implementación (salvo métodos `default`).

Las interfaces son similares a las clases abstractas en el sentido de que no se pueden instanciar y se emplean para lograr polimorfismo. Sin embargo, se diferencian en que una clase puede **implementar múltiples interfaces**, mientras que solo puede heredar de una única clase.

Esto permite simular una forma controlada de herencia múltiple, centrada en comportamientos y no en implementación, lo que evita muchos de los problemas clásicos asociados a la herencia múltiple.

***



## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

Se puede definir una clase abstracta `Punto` con un método abstracto `calcularDistanciaA`. Cada subtipo (2D y 3D) implementa el cálculo correspondiente, comprobando primero que el punto recibido es compatible mediante `instanceof` y realizando *downcasting* cuando sea necesario.

Este diseño permite que otras clases, como `Linea`, trabajen únicamente con referencias de tipo `Punto`, sin conocer las dimensiones concretas de los puntos, apoyándose en el polimorfismo para obtener el comportamiento correcto.

```java
abstract class Punto {
    public abstract double calcularDistanciaA(Punto otro);
}

class Punto2D extends Punto {
    double x, y;

    public double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto2D) {
            Punto2D p = (Punto2D) otro;
            return Math.hypot(x - p.x, y - p.y);
        }
        throw new IllegalArgumentException();
    }
}

class Punto3D extends Punto {
    double x, y, z;

    public double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto3D) {
            Punto3D p = (Punto3D) otro;
            return Math.sqrt(Math.pow(x - p.x, 2) +
                             Math.pow(y - p.y, 2) +
                             Math.pow(z - p.z, 2));
        }
        throw new IllegalArgumentException();
    }
}

class Linea {
    private Punto a, b;

    public Linea(Punto a, Punto b) {
        this.a = a;
        this.b = b;
    }

    public double longitud() {
        return a.calcularDistanciaA(b);
    }
}
```

***



## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

La **herencia de interfaces** permite que una interfaz extienda a otra, heredando sus métodos y ampliando su contrato. En Java, sí existe **herencia múltiple de interfaces**, ya que una interfaz puede extender varias interfaces y una clase puede implementar múltiples interfaces sin ambigüedad de implementación.

Este mecanismo permite construir jerarquías de contratos bien estructuradas, donde cada nivel añade capacidades adicionales sin imponer una implementación concreta.

```java
interface Fichero {
    String leer();
}

interface FicheroEscribible extends Fichero {
    void escribir(String contenido);
    void eliminar();
}
```

Una clase que implemente `FicheroEscribible` estará obligada a proporcionar implementaciones para todos los métodos definidos en ambas interfaces, garantizando así un comportamiento completo y coherente.

***
