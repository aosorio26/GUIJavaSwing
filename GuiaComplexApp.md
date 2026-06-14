# Guía Paso a Paso: Proyecto ComplexApp en Java Swing

## Objetivo

Crear una aplicación llamada **ComplexApp** que permita realizar las cuatro operaciones fundamentales con números complejos:

* Suma
* Resta
* Multiplicación
* División

Además, se validarán los datos ingresados por el usuario utilizando una clase auxiliar llamada `SwingValidator`.

---

# 1. Crear el Proyecto

En NetBeans:

```text
File → New Project → Java with Ant → Java Application
```

Nombre del proyecto:

```text
opComplex
```

Desmarcar:

```text
Create Main Class
```

---

# 2. Crear los paquetes

Dentro de **Source Packages** crear:

```text
frames
modelo
vista
```

La estructura final debe verse así:

```text
Source Packages
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

# 3. Crear la clase Complex

Ubicación:

```text
modelo/Complex.java
```

Código:

```java
package modelo;

public class Complex {

    private float x, y;

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

        float divisor =
                n.x * n.x +
                n.y * n.y;

        Complex conjugado =
                new Complex(n.x, -n.y);

        Complex resultado =
                multiplicar(m, conjugado);

        resultado.x /= divisor;
        resultado.y /= divisor;

        return resultado;
    }
}
```

---

# Explicación de la Multiplicación

Si tenemos:

```text
m = a + bi
n = c + di
```

Entonces:

```text
(a + bi)(c + di)

= ac + adi + bci + bdi²

Como i² = -1

= ac - bd + (ad + bc)i
```

Por eso:

```java
new Complex(
    m.x * n.x - m.y * n.y,
    m.x * n.y + m.y * n.x
);
```

---

# Explicación de la División

Para dividir números complejos:

```text
m / n
```

Se multiplica arriba y abajo por el conjugado.

Ejemplo:

```text
(2 + 3i) / (1 + 4i)
```

Conjugado:

```text
1 - 4i
```

La fórmula es:

```text
(a+bi)/(c+di)

= (a+bi)(c-di)
  -------------
   c² + d²
```

Por eso el método:

```java
Complex conjugado =
        new Complex(n.x, -n.y);
```

y luego:

```java
float divisor =
        n.x*n.x + n.y*n.y;
```

---

# 4. Crear SwingValidator

Ubicación:

```text
vista/SwingValidator.java
```

Código:

```java
package vista;

import javax.swing.JOptionPane;
import javax.swing.text.JTextComponent;

public class SwingValidator {

    public boolean isPresent(
            JTextComponent c,
            String fieldName) {

        if (c.getText().trim().isEmpty()) {

            JOptionPane.showMessageDialog(
                    c,
                    fieldName + " es requerido.");

            c.requestFocusInWindow();

            return false;
        }

        return true;
    }

    public boolean isFloat(
            JTextComponent c,
            String fieldName) {

        try {

            Float.parseFloat(c.getText());

            return true;

        } catch (NumberFormatException e) {

            JOptionPane.showMessageDialog(
                    c,
                    fieldName + " debe ser numérico.");

            c.requestFocusInWindow();

            return false;
        }
    }
}
```

---

# ¿Qué hace SwingValidator?

Valida:

## Campo vacío

```java
sv.isPresent(mx, "mx");
```

Si el usuario no escribió nada:

```text
mx es requerido.
```

---

## Campo numérico

```java
sv.isFloat(mx, "mx");
```

Si el usuario escribe:

```text
hola
```

mostrará:

```text
mx debe ser numérico.
```

---

# 5. Crear el JFrame

En NetBeans:

```text
Click derecho en frames
→ New
→ JFrame Form
```

Nombre:

```text
ComplexApp
```

---

# 6. Diseñar la Interfaz

## Labels

```text
ml
nl
masl
menosl2
porl
divl
```

Textos:

```text
ml      = m
nl      = n

masl    = m + n:
menosl2 = m - n:
porl    = m * n:
divl    = m / n:
```

---

## Campos de Entrada

```text
mx
my

nx
ny
```

---

## Campos de Salida

```text
mas
menos
por
div
```

Configurar:

```text
editable = false
focusable = false
background = amarillo claro
```

---

## Botones

```text
calcular
limpiar
salir
```

Textos:

```text
Calcular
Limpiar
Salir
```

---

# 7. Código Inicial de ComplexApp

Imports:

```java
package frames;

import modelo.Complex;
import vista.SwingValidator;
import java.text.NumberFormat;
```

Atributos:

```java
Complex co;

NumberFormat numero =
        NumberFormat.getNumberInstance();
```

---

# 8. Método de Validación

Agregar:

```java
private boolean isValidData() {

    SwingValidator sv =
            new SwingValidator();

    return

        sv.isPresent(mx, "mx") &&
        sv.isFloat(mx, "mx") &&

        sv.isPresent(my, "my") &&
        sv.isFloat(my, "my") &&

        sv.isPresent(nx, "nx") &&
        sv.isFloat(nx, "nx") &&

        sv.isPresent(ny, "ny") &&
        sv.isFloat(ny, "ny");
}
```

---

# 9. Evento Calcular

```java
private void calcularActionPerformed(
        java.awt.event.ActionEvent evt) {

    if (isValidData()) {

        float mxx =
                Float.parseFloat(mx.getText());

        float myy =
                Float.parseFloat(my.getText());

        float nxx =
                Float.parseFloat(nx.getText());

        float nyy =
                Float.parseFloat(ny.getText());

        Complex m1 =
                new Complex(mxx, myy);

        Complex n1 =
                new Complex(nxx, nyy);

        co = new Complex();

        Complex r;

        r = co.sumar(m1, n1);

        mas.setText(
                numero.format(r.getX())
                + " + i"
                + numero.format(r.getY()));

        r = co.restar(m1, n1);

        menos.setText(
                numero.format(r.getX())
                + " + i"
                + numero.format(r.getY()));

        r = co.multiplicar(m1, n1);

        por.setText(
                numero.format(r.getX())
                + " + i"
                + numero.format(r.getY()));

        if (n1.getX() == 0 &&
            n1.getY() == 0) {

            div.setText(
                    "División entre 0");

        } else {

            r = co.dividir(m1, n1);

            div.setText(
                    numero.format(r.getX())
                    + " + i"
                    + numero.format(r.getY()));
        }

        limpiar.requestFocusInWindow();
    }
}
```

---

# 10. Evento Limpiar

```java
private void limpiarActionPerformed(
        java.awt.event.ActionEvent evt) {

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

# 11. Evento Salir

```java
private void salirActionPerformed(
        java.awt.event.ActionEvent evt) {

    System.exit(0);
}
```

---

# 12. Validar Teclas

Crear método auxiliar:

```java
private void validarTecla(
        java.awt.event.KeyEvent evt) {

    char c = evt.getKeyChar();

    if (!Character.isDigit(c)
            && c != '.'
            && c != '-'
            && c != '+'
            && c != '\n') {

        evt.consume();
    }
}
```

---

# Evento mx

```java
private void mxKeyTyped(
        java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        my.requestFocusInWindow();
    }
}
```

---

# Evento my

```java
private void myKeyTyped(
        java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        nx.requestFocusInWindow();
    }
}
```

---

# Evento nx

```java
private void nxKeyTyped(
        java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        ny.requestFocusInWindow();
    }
}
```

---

# Evento ny

```java
private void nyKeyTyped(
        java.awt.event.KeyEvent evt) {

    validarTecla(evt);

    if (evt.getKeyChar() == '\n') {
        calcular.requestFocusInWindow();
    }
}
```

---

# 13. Pruebas

Ingresar:

```text
mx = 2
my = 3

nx = 1
ny = 4
```

Representa:

```text
m = 2 + 3i
n = 1 + 4i
```

Resultados esperados:

```text
m + n = 3 + 7i

m - n = 1 - 1i

m * n = -10 + 11i
```

---


