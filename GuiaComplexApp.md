# Guía detallada: Crear **ComplexApp** en Java Swing con Maven (NetBeans)

## 0. ¿Qué vamos a construir?

Vamos a crear una aplicación de escritorio en **Java Swing** llamada **ComplexApp**.

La aplicación permitirá ingresar dos números complejos:

```text
m = mx + my·i
n = nx + ny·i
```

Y calculará:

```text
m + n
m - n
m * n
m / n
```

La aplicación tendrá:

- Una ventana gráfica hecha con `JFrame`.
- Una clase `Complex` para representar números complejos.
- Una clase `SwingValidator` para validar campos vacíos y campos numéricos.
- Un formulario `ComplexApp` para unir la interfaz con la lógica.

---

# 1. Crear el proyecto en NetBeans usando Maven

Abrir NetBeans y seguir esta ruta:

```text
File → New Project
```

Seleccionar:

```text
Java with Maven → Java Application
```

Luego presionar:

```text
Next
```

Configurar así:

```text
Project Name: ComplexAppMaven
Group Id: com.uni
Version: 1.0-SNAPSHOT
Package: com.uni
```

Luego presionar:

```text
Finish
```

NetBeans creará un proyecto Maven con una estructura similar a esta:

```text
ComplexAppMaven
│
├── pom.xml
│
└── Source Packages
    └── com.uni
```

---

# 2. Revisar el archivo `pom.xml`

El archivo `pom.xml` es el archivo de configuración de Maven.

Ahí se indica la versión de Java, dependencias y configuración del proyecto.

Abre el archivo:

```text
pom.xml
```

Y déjalo así:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.uni</groupId>
    <artifactId>ComplexAppMaven</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.release>21</maven.compiler.release>
        <exec.mainClass>frames.ComplexApp</exec.mainClass>
    </properties>

    <dependencies>
    </dependencies>

    <build>
        <plugins>

            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <mainClass>frames.ComplexApp</mainClass>
                </configuration>
            </plugin>

        </plugins>
    </build>

</project>
```

## Importante

La línea:

```xml
<exec.mainClass>frames.ComplexApp</exec.mainClass>
```

significa que la clase principal del programa será:

```text
frames.ComplexApp
```

Es decir:

```text
paquete frames
clase ComplexApp
```

---

# 3. Crear los paquetes del proyecto

Dentro de:

```text
Source Packages
```

crear estos paquetes:

```text
frames
modelo
vista
```

Para crear un paquete:

```text
Click derecho en Source Packages
→ New
→ Java Package
```

Crear uno por uno:

```text
frames
modelo
vista
```

La estructura debe quedar así:

```text
Source Packages
│
├── frames
│
├── modelo
│
└── vista
```

---

# 4. Crear la clase `Complex`

## 4.1. ¿Dónde se crea?

Crear la clase dentro del paquete:

```text
modelo
```

Ruta en NetBeans:

```text
Source Packages
→ modelo
→ Click derecho
→ New
→ Java Class
```

Nombre de la clase:

```text
Complex
```

Debe quedar así:

```text
Source Packages
└── modelo
    └── Complex.java
```

---

## 4.2. Código completo de `Complex.java`

Borra el contenido inicial y coloca este código:

```java
package modelo;

public class Complex {

    private float x;
    private float y;

    public Complex() {
    }

    public Complex(float x, float y) {
        this.x = x;
        this.y = y;
    }

    public float getX() {
        return x;
    }

    public float getY() {
        return y;
    }

    public Complex sumar(Complex m, Complex n) {
        return new Complex(
                m.x + n.x,
                m.y + n.y
        );
    }

    public Complex restar(Complex m, Complex n) {
        return new Complex(
                m.x - n.x,
                m.y - n.y
        );
    }

    public Complex multiplicar(Complex m, Complex n) {
        return new Complex(
                m.x * n.x - m.y * n.y,
                m.x * n.y + m.y * n.x
        );
    }

    public Complex dividir(Complex m, Complex n) {
        float divisor = n.x * n.x + n.y * n.y;

        Complex conjugado = new Complex(n.x, -n.y);

        Complex resultado = multiplicar(m, conjugado);

        resultado.x = resultado.x / divisor;
        resultado.y = resultado.y / divisor;

        return resultado;
    }
}
```

---

## 4.3. Explicación de la clase `Complex`

Esta clase representa un número complejo:

```text
x + yi
```

Donde:

```text
x = parte real
y = parte imaginaria
```

Por eso tenemos:

```java
private float x;
private float y;
```

Ejemplo:

```text
3 + 5i
```

Se representa así:

```java
Complex c = new Complex(3, 5);
```

---

## 4.4. ¿Por qué los atributos son `private`?

```java
private float x;
private float y;
```

Son privados para proteger los datos.

No se accede directamente así:

```java
c.x
```

Sino mediante métodos:

```java
c.getX();
c.getY();
```

Esto se llama **encapsulamiento**.

---

# 5. Crear la clase `SwingValidator`

## 5.1. ¿Dónde se crea?

Crear la clase dentro del paquete:

```text
vista
```

Ruta:

```text
Source Packages
→ vista
→ Click derecho
→ New
→ Java Class
```

Nombre:

```text
SwingValidator
```

Debe quedar así:

```text
Source Packages
└── vista
    └── SwingValidator.java
```

---

## 5.2. Código completo de `SwingValidator.java`

Borra el contenido inicial y coloca:

```java
package vista;

import javax.swing.JOptionPane;
import javax.swing.text.JTextComponent;

public class SwingValidator {

    public boolean isPresent(JTextComponent c, String fieldName) {

        if (c.getText().trim().isEmpty()) {

            JOptionPane.showMessageDialog(
                    c,
                    fieldName + " es requerido."
            );

            c.requestFocusInWindow();

            return false;
        }

        return true;
    }

    public boolean isFloat(JTextComponent c, String fieldName) {

        try {

            Float.parseFloat(c.getText());

            return true;

        } catch (NumberFormatException e) {

            JOptionPane.showMessageDialog(
                    c,
                    fieldName + " debe ser un número válido."
            );

            c.requestFocusInWindow();

            return false;
        }
    }
}
```

---

## 5.3. ¿Qué hace `SwingValidator`?

Esta clase valida los campos de texto de la ventana.

### Método `isPresent`

```java
public boolean isPresent(JTextComponent c, String fieldName)
```

Sirve para verificar si el campo está vacío.

Ejemplo:

```java
sv.isPresent(mx, "mx");
```

Si `mx` está vacío, muestra:

```text
mx es requerido.
```

---

### Método `isFloat`

```java
public boolean isFloat(JTextComponent c, String fieldName)
```

Sirve para verificar si el texto se puede convertir a `float`.

Ejemplo válido:

```text
12.5
```

Ejemplo inválido:

```text
hola
```

Si el campo no es numérico, muestra:

```text
mx debe ser un número válido.
```

---

# 6. Crear el formulario `ComplexApp`

## 6.1. ¿Dónde se crea?

Crear el formulario dentro del paquete:

```text
frames
```

Ruta:

```text
Source Packages
→ frames
→ Click derecho
→ New
→ JFrame Form
```

Nombre:

```text
ComplexApp
```

Debe quedar así:

```text
Source Packages
└── frames
    └── ComplexApp.java
```

---

# 7. Diseñar la ventana en modo Design

Abre:

```text
ComplexApp.java
```

Y entra a la pestaña:

```text
Design
```

Ahí se colocan los componentes visuales.

---

## 7.1. Componentes que debe tener la ventana

La ventana debe tener:

```text
4 JTextField de entrada
4 JTextField de salida
6 JLabel
3 JButton
```

---

# 8. Colocar los `JLabel`

Arrastra desde la paleta:

```text
Swing Controls → Label
```

Coloca 6 etiquetas.

Luego cambia sus nombres de variable.

Para cambiar el nombre de variable:

```text
Click derecho sobre el componente
→ Change Variable Name
```

Configurar así:

| Variable | Texto visible |
|---|---|
| `ml` | `m` |
| `nl` | `n` |
| `masl` | `m + n:` |
| `menosl2` | `m - n:` |
| `porl` | `m * n:` |
| `divl` | `m / n:` |

---

# 9. Colocar los campos de entrada

Arrastra 4 campos:

```text
Swing Controls → Text Field
```

Cambia sus nombres de variable así:

| Variable | Uso |
|---|---|
| `mx` | Parte real del complejo m |
| `my` | Parte imaginaria del complejo m |
| `nx` | Parte real del complejo n |
| `ny` | Parte imaginaria del complejo n |

La idea es:

```text
m = mx + my·i
n = nx + ny·i
```

Ejemplo:

```text
mx = 2
my = 3
```

Representa:

```text
m = 2 + 3i
```

---

# 10. Colocar los campos de salida

Arrastra 4 campos más:

```text
Swing Controls → Text Field
```

Cambia sus nombres de variable así:

| Variable | Resultado |
|---|---|
| `mas` | Resultado de la suma |
| `menos` | Resultado de la resta |
| `por` | Resultado de la multiplicación |
| `div` | Resultado de la división |

---

## 10.1. Configurar los campos de salida

Selecciona cada campo:

```text
mas
menos
por
div
```

Y en la ventana de propiedades coloca:

```text
editable = false
focusable = false
```

También cambia el fondo a amarillo claro:

```text
background = 255, 255, 204
```

Esto indica que esos campos son solo para mostrar resultados.

El alumno no debe escribir en ellos.

---

# 11. Colocar los botones

Arrastra 3 botones:

```text
Swing Controls → Button
```

Configura así:

| Variable | Texto visible |
|---|---|
| `calcular` | `Calcular` |
| `limpiar` | `Limpiar` |
| `salir` | `Salir` |

---

# 12. Configurar la ventana principal

Haz click en una zona vacía del formulario.

En propiedades puedes configurar:

```text
title = ComplexApp: 4 Operaciones
defaultCloseOperation = EXIT_ON_CLOSE
```

Esto hace que la ventana tenga título y que el programa termine cuando se presione la X.

---

# 13. Agregar imports y atributos en `ComplexApp`

Ahora abre la pestaña:

```text
Source
```

Al inicio del archivo debe aparecer:

```java
package frames;
```

Debajo coloca estos imports:

```java
import modelo.Complex;
import vista.SwingValidator;
import java.text.NumberFormat;
```

Luego, dentro de la clase `ComplexApp`, antes del constructor, coloca:

```java
Complex co;
NumberFormat numero = NumberFormat.getNumberInstance();
```

Debe quedar más o menos así:

```java
package frames;

import modelo.Complex;
import vista.SwingValidator;
import java.text.NumberFormat;

public class ComplexApp extends javax.swing.JFrame {

    Complex co;
    NumberFormat numero = NumberFormat.getNumberInstance();

    public ComplexApp() {
        initComponents();
    }

    // resto del código generado por NetBeans
}
```

---

# 14. Crear el método `isValidData`

## 14.1. ¿Dónde se coloca?

En `ComplexApp.java`, baja casi hasta el final del archivo.

Busca una zona antes del método `main`, o antes de:

```java
// Variables declaration - do not modify
```

Coloca ahí este método:

```java
private boolean isValidData() {

    SwingValidator sv = new SwingValidator();

    return sv.isPresent(mx, "mx")
            && sv.isFloat(mx, "mx")
            && sv.isPresent(my, "my")
            && sv.isFloat(my, "my")
            && sv.isPresent(nx, "nx")
            && sv.isFloat(nx, "nx")
            && sv.isPresent(ny, "ny")
            && sv.isFloat(ny, "ny");
}
```

---

## 14.2. ¿Qué hace este método?

Valida los cuatro campos de entrada:

```text
mx
my
nx
ny
```

Primero revisa que no estén vacíos.

Luego revisa que sean números `float`.

Si todo está bien, devuelve:

```java
true
```

Si algo está mal, devuelve:

```java
false
```

---

# 15. Crear el método `validarTecla`

## 15.1. ¿Dónde se coloca?

En el mismo archivo:

```text
ComplexApp.java
```

Colócalo debajo de `isValidData`, antes de `main`.

Código:

```java
private void validarTecla(java.awt.event.KeyEvent evt) {

    char c = evt.getKeyChar();

    if (!Character.isDigit(c)
            && c != '.'
            && c != '+'
            && c != '-'
            && c != '\n') {

        evt.consume();
    }
}
```

---

## 15.2. ¿Qué hace?

Este método impide escribir letras.

Permite:

```text
números
punto decimal
signo +
signo -
tecla Enter
```

Ejemplos permitidos:

```text
12
-5
+7
3.14
```

Ejemplos no permitidos:

```text
hola
abc
@
```

---

# 16. Programar el botón `Calcular`

## 16.1. ¿Cómo crear el evento?

Ir a la pestaña:

```text
Design
```

Hacer doble click en el botón:

```text
Calcular
```

NetBeans creará automáticamente un método parecido a este:

```java
private void calcularActionPerformed(java.awt.event.ActionEvent evt) {

}
```

Dentro de ese método coloca este código:

```java
private void calcularActionPerformed(java.awt.event.ActionEvent evt) {

    if (isValidData()) {

        float mxx = Float.parseFloat(mx.getText());
        float myy = Float.parseFloat(my.getText());
        float nxx = Float.parseFloat(nx.getText());
        float nyy = Float.parseFloat(ny.getText());

        Complex m1 = new Complex(mxx, myy);
        Complex n1 = new Complex(nxx, nyy);

        co = new Complex();

        Complex resultado;

        resultado = co.sumar(m1, n1);

        mas.setText(
                numero.format(resultado.getX())
                + " + i"
                + numero.format(resultado.getY())
        );

        resultado = co.restar(m1, n1);

        menos.setText(
                numero.format(resultado.getX())
                + " + i"
                + numero.format(resultado.getY())
        );

        resultado = co.multiplicar(m1, n1);

        por.setText(
                numero.format(resultado.getX())
                + " + i"
                + numero.format(resultado.getY())
        );

        if (n1.getX() == 0 && n1.getY() == 0) {

            div.setText("División entre 0");

        } else {

            resultado = co.dividir(m1, n1);

            div.setText(
                    numero.format(resultado.getX())
                    + " + i"
                    + numero.format(resultado.getY())
            );
        }

        limpiar.requestFocusInWindow();
    }
}
```

---

## 16.2. Explicación del botón Calcular

Primero valida:

```java
if (isValidData()) {
```

Si los datos son correctos, recién calcula.

Luego lee los textos:

```java
float mxx = Float.parseFloat(mx.getText());
float myy = Float.parseFloat(my.getText());
float nxx = Float.parseFloat(nx.getText());
float nyy = Float.parseFloat(ny.getText());
```

Después crea los objetos:

```java
Complex m1 = new Complex(mxx, myy);
Complex n1 = new Complex(nxx, nyy);
```

Por ejemplo, si el usuario escribe:

```text
mx = 2
my = 3
```

Entonces se crea:

```java
Complex m1 = new Complex(2, 3);
```

Que representa:

```text
2 + 3i
```

Luego se calculan los resultados y se muestran en los campos:

```java
mas.setText(...);
menos.setText(...);
por.setText(...);
div.setText(...);
```

---

# 17. Programar el botón `Limpiar`

## 17.1. Crear el evento

Ir a:

```text
Design
```

Hacer doble click sobre el botón:

```text
Limpiar
```

NetBeans creará:

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {

}
```

Coloca dentro:

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {

    mx.setText("");
    my.setText("");
    nx.setText("");
    ny.setText("");

    mas.setText("");
    menos.setText("");
    por.setText("");
    div.setText("");

    mx.requestFocusInWindow();
}
```

---

## 17.2. ¿Qué hace?

Borra todos los campos de entrada:

```java
mx.setText("");
my.setText("");
nx.setText("");
ny.setText("");
```

Borra todos los resultados:

```java
mas.setText("");
menos.setText("");
por.setText("");
div.setText("");
```

Y devuelve el cursor al primer campo:

```java
mx.requestFocusInWindow();
```

---

# 18. Programar el botón `Salir`

## 18.1. Crear el evento

Ir a:

```text
Design
```

Hacer doble click sobre el botón:

```text
Salir
```

NetBeans creará:

```java
private void salirActionPerformed(java.awt.event.ActionEvent evt) {

}
```

Coloca dentro:

```java
private void salirActionPerformed(java.awt.event.ActionEvent evt) {
    System.exit(0);
}
```

---

## 18.2. ¿Qué hace?

Cierra completamente la aplicación.

---

# 19. Programar los eventos `KeyTyped`

Estos eventos sirven para controlar lo que el usuario escribe.

Se aplicarán a:

```text
mx
my
nx
ny
```

---

## 19.1. Evento KeyTyped de `mx`

Ir a:

```text
Design
```

Seleccionar el campo:

```text
mx
```

Luego:

```text
Properties
→ Events
→ Key
→ keyTyped
```

Presiona los tres puntos o escribe el nombre:

```text
mxKeyTyped
```

NetBeans creará:

```java
private void mxKeyTyped(java.awt.event.KeyEvent evt) {

}
```

Coloca dentro:

```java
private void mxKeyTyped(java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        my.requestFocusInWindow();
    }
}
```

---

## 19.2. Evento KeyTyped de `my`

Seleccionar:

```text
my
```

Ir a:

```text
Properties → Events → Key → keyTyped
```

Crear:

```text
myKeyTyped
```

Código:

```java
private void myKeyTyped(java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        nx.requestFocusInWindow();
    }
}
```

---

## 19.3. Evento KeyTyped de `nx`

Seleccionar:

```text
nx
```

Ir a:

```text
Properties → Events → Key → keyTyped
```

Crear:

```text
nxKeyTyped
```

Código:

```java
private void nxKeyTyped(java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        ny.requestFocusInWindow();
    }
}
```

---

## 19.4. Evento KeyTyped de `ny`

Seleccionar:

```text
ny
```

Ir a:

```text
Properties → Events → Key → keyTyped
```

Crear:

```text
nyKeyTyped
```

Código:

```java
private void nyKeyTyped(java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        calcular.requestFocusInWindow();
    }
}
```

---

# 20. Verificar que los eventos estén conectados

En el código generado por NetBeans debería aparecer algo parecido:

```java
mx.addKeyListener(new java.awt.event.KeyAdapter() {
    public void keyTyped(java.awt.event.KeyEvent evt) {
        mxKeyTyped(evt);
    }
});
```

También para:

```text
my
nx
ny
```

Y para los botones debe aparecer algo como:

```java
calcular.addActionListener(new java.awt.event.ActionListener() {
    public void actionPerformed(java.awt.event.ActionEvent evt) {
        calcularActionPerformed(evt);
    }
});
```

Si esto aparece, significa que los eventos están bien conectados.

---

# 21. Método `main`

NetBeans genera automáticamente el método `main` dentro de `ComplexApp`.

Debe verse parecido a esto:

```java
public static void main(String args[]) {

    java.awt.EventQueue.invokeLater(new Runnable() {
        public void run() {
            new ComplexApp().setVisible(true);
        }
    });
}
```

Este método es importante porque inicia la ventana.

No lo borres.

---

# 22. Ejecutar el proyecto

Como es Maven, hay varias formas.

## Opción 1: Desde NetBeans

Click derecho sobre el proyecto:

```text
Run
```

Si pregunta cuál es la clase principal, elegir:

```text
frames.ComplexApp
```

---

## Opción 2: Desde consola

Entrar a la carpeta del proyecto, donde está `pom.xml`.

Ejecutar:

```bash
mvn clean compile
```

Luego:

```bash
mvn exec:java
```

---

# 23. Probar la aplicación

Escribir:

```text
mx = 2
my = 3
nx = 1
ny = 4
```

Eso representa:

```text
m = 2 + 3i
n = 1 + 4i
```

Presionar:

```text
Calcular
```

Resultados esperados:

```text
m + n = 3 + i7
m - n = 1 + i-1
m * n = -10 + i11
```

Para la división, el resultado aproximado será:

```text
m / n = 0.8235 + i-0.2941
```

---

# 24. Probar división entre cero

Escribir:

```text
nx = 0
ny = 0
```

Presionar:

```text
Calcular
```

Debe mostrar:

```text
División entre 0
```

La división entre cero no se puede realizar porque el número complejo:

```text
0 + 0i
```

no tiene inverso.

---

# 25. Errores comunes

## Error 1: No reconoce `Complex`

Si sale error en:

```java
Complex m1 = new Complex(mxx, myy);
```

Revisar que arriba esté importado:

```java
import modelo.Complex;
```

---

## Error 2: No reconoce `SwingValidator`

Si sale error en:

```java
SwingValidator sv = new SwingValidator();
```

Revisar que arriba esté:

```java
import vista.SwingValidator;
```

---

## Error 3: El proyecto no inicia

Revisar en `pom.xml`:

```xml
<exec.mainClass>frames.ComplexApp</exec.mainClass>
```

Y también:

```xml
<mainClass>frames.ComplexApp</mainClass>
```

---

## Error 4: El evento no funciona

Si al presionar un botón no pasa nada, probablemente el evento no está conectado.

Solución:

```text
Design
→ doble click nuevamente sobre el botón
```

NetBeans debe llevarte al método correcto.

---

## Error 5: No deja escribir números negativos

Revisar que `validarTecla` tenga permitido:

```java
c != '-'
```

---

# 26. Resumen de archivos finales

Al terminar, el proyecto debe tener:

```text
ComplexAppMaven
│
├── pom.xml
│
└── Source Packages
    │
    ├── frames
    │   └── ComplexApp.java
    │
    ├── modelo
    │   └── Complex.java
    │
    └── vista
        └── SwingValidator.java
```

---

# 27. Resumen del flujo del programa

```text
El usuario escribe mx, my, nx, ny
              │
              ▼
Presiona Calcular
              │
              ▼
ComplexApp valida los campos
              │
              ▼
SwingValidator revisa vacío y número
              │
              ▼
ComplexApp crea objetos Complex
              │
              ▼
Complex calcula suma, resta, multiplicación y división
              │
              ▼
ComplexApp muestra los resultados
```

---

# 28. Qué aprende el alumno con este proyecto

Con este proyecto el alumno practica:

- Creación de proyectos Java con Maven.
- Organización por paquetes.
- Creación de clases.
- Encapsulamiento.
- Constructores.
- Métodos.
- Objetos.
- Formularios `JFrame`.
- Eventos de botones.
- Eventos de teclado.
- Validación de datos.
- Separación entre interfaz y lógica.
- Operaciones matemáticas con números complejos.

---

# 29. Código final resumido por archivos

## `modelo/Complex.java`

```java
package modelo;

public class Complex {

    private float x;
    private float y;

    public Complex() {
    }

    public Complex(float x, float y) {
        this.x = x;
        this.y = y;
    }

    public float getX() {
        return x;
    }

    public float getY() {
        return y;
    }

    public Complex sumar(Complex m, Complex n) {
        return new Complex(m.x + n.x, m.y + n.y);
    }

    public Complex restar(Complex m, Complex n) {
        return new Complex(m.x - n.x, m.y - n.y);
    }

    public Complex multiplicar(Complex m, Complex n) {
        return new Complex(
                m.x * n.x - m.y * n.y,
                m.x * n.y + m.y * n.x
        );
    }

    public Complex dividir(Complex m, Complex n) {
        float divisor = n.x * n.x + n.y * n.y;
        Complex conjugado = new Complex(n.x, -n.y);
        Complex resultado = multiplicar(m, conjugado);

        resultado.x = resultado.x / divisor;
        resultado.y = resultado.y / divisor;

        return resultado;
    }
}
```

---

## `vista/SwingValidator.java`

```java
package vista;

import javax.swing.JOptionPane;
import javax.swing.text.JTextComponent;

public class SwingValidator {

    public boolean isPresent(JTextComponent c, String fieldName) {
        if (c.getText().trim().isEmpty()) {
            JOptionPane.showMessageDialog(c, fieldName + " es requerido.");
            c.requestFocusInWindow();
            return false;
        }
        return true;
    }

    public boolean isFloat(JTextComponent c, String fieldName) {
        try {
            Float.parseFloat(c.getText());
            return true;
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(c, fieldName + " debe ser un número válido.");
            c.requestFocusInWindow();
            return false;
        }
    }
}
```

---

## Partes principales de `frames/ComplexApp.java`

```java
package frames;

import modelo.Complex;
import vista.SwingValidator;
import java.text.NumberFormat;

public class ComplexApp extends javax.swing.JFrame {

    Complex co;
    NumberFormat numero = NumberFormat.getNumberInstance();

    public ComplexApp() {
        initComponents();
    }

    private boolean isValidData() {

        SwingValidator sv = new SwingValidator();

        return sv.isPresent(mx, "mx")
                && sv.isFloat(mx, "mx")
                && sv.isPresent(my, "my")
                && sv.isFloat(my, "my")
                && sv.isPresent(nx, "nx")
                && sv.isFloat(nx, "nx")
                && sv.isPresent(ny, "ny")
                && sv.isFloat(ny, "ny");
    }

    private void validarTecla(java.awt.event.KeyEvent evt) {

        char c = evt.getKeyChar();

        if (!Character.isDigit(c)
                && c != '.'
                && c != '+'
                && c != '-'
                && c != '\n') {

            evt.consume();
        }
    }

    private void calcularActionPerformed(java.awt.event.ActionEvent evt) {

        if (isValidData()) {

            float mxx = Float.parseFloat(mx.getText());
            float myy = Float.parseFloat(my.getText());
            float nxx = Float.parseFloat(nx.getText());
            float nyy = Float.parseFloat(ny.getText());

            Complex m1 = new Complex(mxx, myy);
            Complex n1 = new Complex(nxx, nyy);

            co = new Complex();

            Complex resultado;

            resultado = co.sumar(m1, n1);
            mas.setText(numero.format(resultado.getX()) + " + i" + numero.format(resultado.getY()));

            resultado = co.restar(m1, n1);
            menos.setText(numero.format(resultado.getX()) + " + i" + numero.format(resultado.getY()));

            resultado = co.multiplicar(m1, n1);
            por.setText(numero.format(resultado.getX()) + " + i" + numero.format(resultado.getY()));

            if (n1.getX() == 0 && n1.getY() == 0) {
                div.setText("División entre 0");
            } else {
                resultado = co.dividir(m1, n1);
                div.setText(numero.format(resultado.getX()) + " + i" + numero.format(resultado.getY()));
            }

            limpiar.requestFocusInWindow();
        }
    }

    private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {

        mx.setText("");
        my.setText("");
        nx.setText("");
        ny.setText("");

        mas.setText("");
        menos.setText("");
        por.setText("");
        div.setText("");

        mx.requestFocusInWindow();
    }

    private void salirActionPerformed(java.awt.event.ActionEvent evt) {
        System.exit(0);
    }

    private void mxKeyTyped(java.awt.event.KeyEvent evt) {
        validarTecla(evt);

        if (evt.getKeyChar() == '\n') {
            my.requestFocusInWindow();
        }
    }

    private void myKeyTyped(java.awt.event.KeyEvent evt) {
        validarTecla(evt);

        if (evt.getKeyChar() == '\n') {
            nx.requestFocusInWindow();
        }
    }

    private void nxKeyTyped(java.awt.event.KeyEvent evt) {
        validarTecla(evt);

        if (evt.getKeyChar() == '\n') {
            ny.requestFocusInWindow();
        }
    }

    private void nyKeyTyped(java.awt.event.KeyEvent evt) {
        validarTecla(evt);

        if (evt.getKeyChar() == '\n') {
            calcular.requestFocusInWindow();
        }
    }
}
```

---

# 30. Nota final para el alumno

En este proyecto, la interfaz gráfica está separada de la lógica matemática.

La clase:

```text
ComplexApp
```

se encarga de la ventana.

La clase:

```text
Complex
```

se encarga de las operaciones matemáticas.

La clase:

```text
SwingValidator
```

se encarga de validar los datos.

Esta separación hace que el programa esté mejor organizado y sea más fácil de mantener.
