# Guía paso a paso: App de Valor Futuro en Java Swing

Esta guía explica cómo crear desde cero una aplicación de **Valor Futuro** usando **Java Swing en NetBeans**.

La app permite ingresar:

- Pago mensual
- Tasa de interés anual
- Número de años

Y calcula el valor futuro acumulado.

---

## 1. ¿Qué tipo de interés usa esta app?

La aplicación usa **interés compuesto con capitalización mensual**.

Esto significa que cada mes ocurre lo siguiente:

1. Se suma el pago mensual al monto acumulado.
2. Luego se aplica el interés mensual al total.

El código principal es:

```java
for (int i = 1; i <= meses; i++) {
    valorFuturo = (valorFuturo + pagoMes) * (1 + tasaIntMensual);
}
```

La lógica es:

```text
Nuevo valor = (valor anterior + pago mensual) * (1 + tasa mensual)
```

Por ejemplo, si el pago mensual es 100 y la tasa mensual es 1%:

```text
Mes 1: (0 + 100) * 1.01 = 101
Mes 2: (101 + 100) * 1.01 = 203.01
Mes 3: (203.01 + 100) * 1.01 = 306.04
```

No es interés simple, porque el interés se aplica sobre el monto acumulado, no solo sobre el aporte inicial.

---

# 2. Crear el proyecto en NetBeans

Abre NetBeans y selecciona:

```text
File > New Project
```

Luego elige:

```text
Java with Ant > Java Application
```

También puede ser:

```text
Java with Maven > Java Application
```

Nombre sugerido del proyecto:

```text
futureValue
```

Desmarca la opción de crear clase principal si vas a usar un JFrame como ventana principal.

---

# 3. Crear los paquetes

En `Source Packages`, crea estos paquetes:

```text
frames
modelo
vista
```

Para crear un paquete:

```text
Clic derecho en Source Packages > New > Java Package
```

La estructura debe quedar así:

```text
Source Packages
├── frames
├── modelo
└── vista
```

---

# 4. Crear la clase del modelo

En el paquete `modelo`, crea una clase llamada:

```text
CalculosFinanieros
```

Código:

```java
package modelo;

public class CalculosFinanieros {

    public static final int MESES_EN_ANO = 12;

    public static double calcularValorFuturo(double pagoMes, double tasaIntAnual, int numAnos) {
        int meses = numAnos * MESES_EN_ANO;

        double tasaIntMensual = tasaIntAnual / MESES_EN_ANO / 100;

        double valorFuturo = 0;

        for (int i = 1; i <= meses; i++) {
            valorFuturo = (valorFuturo + pagoMes) * (1 + tasaIntMensual);
        }

        return valorFuturo;
    }
}
```

---

## Explicación de esta clase

Esta clase contiene la lógica financiera.

```java
public static final int MESES_EN_ANO = 12;
```

Representa que un año tiene 12 meses.

```java
int meses = numAnos * MESES_EN_ANO;
```

Convierte los años ingresados a meses.

Por ejemplo:

```text
5 años * 12 = 60 meses
```

```java
double tasaIntMensual = tasaIntAnual / MESES_EN_ANO / 100;
```

Convierte la tasa anual en tasa mensual decimal.

Por ejemplo, si la tasa anual es 12%:

```text
12 / 12 / 100 = 0.01
```

Eso equivale a 1% mensual.

---

# 5. Crear la clase de validación

En el paquete `vista`, crea una clase llamada:

```text
SwingValidator
```

Código:

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

    public boolean isDouble(JTextComponent c, String fieldName) {
        try {
            Double.parseDouble(c.getText());
            return true;
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(c, fieldName + " debe ser un número válido.");
            c.requestFocusInWindow();
            return false;
        }
    }

    public boolean isInteger(JTextComponent c, String fieldName) {
        try {
            Integer.parseInt(c.getText());
            return true;
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(c, fieldName + " debe ser un número entero.");
            c.requestFocusInWindow();
            return false;
        }
    }
}
```

---

## Explicación de esta clase

La clase `SwingValidator` sirve para validar los campos del formulario.

Tiene tres métodos importantes:

```java
isPresent()
```

Verifica que el campo no esté vacío.

```java
isDouble()
```

Verifica que el campo sea un número decimal válido.

```java
isInteger()
```

Verifica que el campo sea un número entero válido.

Por ejemplo:

- El pago mensual puede ser decimal.
- La tasa puede ser decimal.
- El número de años debe ser entero.

---

# 6. Crear el JFrame

En el paquete `frames`, crea un formulario:

```text
Clic derecho en frames > New > JFrame Form
```

Nombre del JFrame:

```text
ValorFuturo
```

NetBeans creará dos archivos:

```text
ValorFuturo.java
ValorFuturo.form
```

El archivo `.form` indica que la ventana fue creada con el diseñador visual de NetBeans.

---

# 7. Diseñar la ventana

En el diseñador visual de NetBeans, arrastra estos componentes desde la Palette.

## Labels

Agrega 4 `JLabel` con estos textos:

```text
Pago mensual:
Tasa interés anual:
Número de años:
Valor futuro:
```

## TextFields

Agrega 4 `JTextField`.

Cámbiales el nombre de variable a:

```text
pagoMes
tasaIntAnual
numAnos
valorFuturo
```

Para cambiar el nombre:

```text
Clic derecho sobre el componente > Change Variable Name
```

O también:

```text
Properties > Code > Variable Name
```

---

# 8. Configurar el campo valorFuturo

Selecciona el campo `valorFuturo`.

En Properties, busca estas opciones:

```text
editable
focusable
```

Desmarca ambas.

Debe quedar así:

```text
editable = false
focusable = false
```

Esto se hace porque el usuario no debe escribir el resultado. Ese campo solo debe mostrar el cálculo.

También puedes hacerlo por código en el constructor:

```java
public ValorFuturo() {
    initComponents();

    valorFuturo.setEditable(false);
    valorFuturo.setFocusable(false);
}
```

---

# 9. Agregar botones

Agrega 3 `JButton`.

Textos:

```text
Calcular
Limpiar
Salir
```

Nombres de variable:

```text
calcular
limpiar
salir
```

La ventana debería quedar parecida a esto:

```text
Pago mensual:          [______________]
Tasa interés anual:    [______________]
Número de años:        [______________]
Valor futuro:          [______________]

[Calcular] [Limpiar] [Salir]
```

---

# 10. Importar clases en el JFrame

Abre `ValorFuturo.java`.

Debajo de:

```java
package frames;
```

agrega:

```java
import java.text.NumberFormat;
import modelo.CalculosFinanieros;
import vista.SwingValidator;
```

Debe quedar parecido a esto:

```java
package frames;

import java.text.NumberFormat;
import modelo.CalculosFinanieros;
import vista.SwingValidator;
```

---

# 11. Programar el botón Calcular

En el diseñador visual, haz doble clic en el botón **Calcular**.

NetBeans creará un método parecido a este:

```java
private void calcularActionPerformed(java.awt.event.ActionEvent evt) {

}
```

Dentro coloca:

```java
private void calcularActionPerformed(java.awt.event.ActionEvent evt) {                                         

    if (isValidData()) {
        double pM = Double.parseDouble(pagoMes.getText());
        double tIA = Double.parseDouble(tasaIntAnual.getText());
        int nA = Integer.parseInt(numAnos.getText());

        double vF = CalculosFinanieros.calcularValorFuturo(pM, tIA, nA);

        NumberFormat currency = NumberFormat.getCurrencyInstance();

        valorFuturo.setText(currency.format(vF));
    }
}
```

---

## Explicación del botón Calcular

Primero valida los datos:

```java
if (isValidData()) {
```

Si los datos son correctos, convierte los textos a números:

```java
double pM = Double.parseDouble(pagoMes.getText());
double tIA = Double.parseDouble(tasaIntAnual.getText());
int nA = Integer.parseInt(numAnos.getText());
```

Luego llama al método del modelo:

```java
double vF = CalculosFinanieros.calcularValorFuturo(pM, tIA, nA);
```

Finalmente muestra el resultado con formato de moneda:

```java
NumberFormat currency = NumberFormat.getCurrencyInstance();
valorFuturo.setText(currency.format(vF));
```

---

# 12. Crear el método isValidData

Dentro de la clase `ValorFuturo`, pero fuera de los métodos de botones, agrega:

```java
private boolean isValidData() {
    SwingValidator sv = new SwingValidator();

    return sv.isPresent(pagoMes, "Pago mensual")
            && sv.isDouble(pagoMes, "Pago mensual")
            && sv.isPresent(tasaIntAnual, "Tasa interés anual")
            && sv.isDouble(tasaIntAnual, "Tasa interés anual")
            && sv.isPresent(numAnos, "Número de años")
            && sv.isInteger(numAnos, "Número de años");
}
```

Este método revisa que:

- El pago mensual no esté vacío.
- El pago mensual sea número decimal.
- La tasa no esté vacía.
- La tasa sea número decimal.
- El número de años no esté vacío.
- El número de años sea entero.

---

# 13. Programar el botón Limpiar

En el diseñador visual, haz doble clic en el botón **Limpiar**.

Coloca:

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {                                        

    pagoMes.setText("");
    tasaIntAnual.setText("");
    numAnos.setText("");
    valorFuturo.setText("");

    pagoMes.requestFocus();
}
```

Este botón borra todos los campos y vuelve a colocar el cursor en `pagoMes`.

---

# 14. Programar el botón Salir

En el diseñador visual, haz doble clic en el botón **Salir**.

Coloca:

```java
private void salirActionPerformed(java.awt.event.ActionEvent evt) {                                      

    System.exit(0);
}
```

Este botón cierra la aplicación.

---

# 15. Revisar el constructor del JFrame

Tu constructor debe verse parecido a esto:

```java
public ValorFuturo() {
    initComponents();

    valorFuturo.setEditable(false);
    valorFuturo.setFocusable(false);
}
```

`initComponents()` es generado automáticamente por NetBeans.

No se recomienda modificarlo manualmente.

---

# 16. Revisar el método main

Al final del JFrame, NetBeans genera un método `main`.

Debe tener algo parecido a esto:

```java
public static void main(String args[]) {
    java.awt.EventQueue.invokeLater(new Runnable() {
        public void run() {
            new ValorFuturo().setVisible(true);
        }
    });
}
```

También puede verse así:

```java
public static void main(String args[]) {
    java.awt.EventQueue.invokeLater(() -> {
        new ValorFuturo().setVisible(true);
    });
}
```

Ambas formas están bien.

---

# 17. Ejecutar la app

Haz clic derecho sobre `ValorFuturo.java` y selecciona:

```text
Run File
```

O presiona:

```text
Shift + F6
```

Prueba con estos datos:

```text
Pago mensual: 100
Tasa interés anual: 12
Número de años: 1
```

El programa debe mostrar un valor futuro aproximado a:

```text
$1,280.93
```

El símbolo de moneda puede variar según la configuración regional de tu computadora.

---

# 18. Exportar como ejecutable JAR

Para generar el `.jar`, usa:

```text
Clean and Build Project
```

En NetBeans puedes hacerlo con:

```text
Run > Clean and Build Project
```

O presionando:

```text
Shift + F11
```

Si tu proyecto es Maven, el `.jar` estará en:

```text
target/
```

Ejemplo:

```text
target/futureValue-1.0.jar
```

Si tu proyecto es Ant, el `.jar` estará en:

```text
dist/
```

Ejemplo:

```text
dist/futureValue.jar
```

Para ejecutarlo desde consola:

```bash
java -jar futureValue.jar
```

---

# 19. Estructura final del proyecto

La estructura debería quedar así:

```text
futureValue
└── Source Packages
    ├── frames
    │   ├── ValorFuturo.java
    │   └── ValorFuturo.form
    ├── modelo
    │   └── CalculosFinanieros.java
    └── vista
        └── SwingValidator.java
```

---

# 20. Resumen de responsabilidades

## `ValorFuturo.java`

Es la ventana principal.

Se encarga de:

- Mostrar los campos.
- Leer los datos ingresados.
- Ejecutar los eventos de botones.
- Mostrar el resultado.

## `CalculosFinanieros.java`

Es el modelo.

Se encarga de:

- Calcular el valor futuro.
- Aplicar interés compuesto mensual.

## `SwingValidator.java`

Es la clase de validación.

Se encarga de:

- Verificar campos vacíos.
- Verificar números decimales.
- Verificar números enteros.

---

# 21. Qué parte genera NetBeans y qué parte escribes tú

## Lo que genera NetBeans

NetBeans genera automáticamente:

```java
initComponents()
```

Dentro de ese método están:

- Los labels.
- Los text fields.
- Los botones.
- El layout.
- La posición de los componentes.

No es recomendable editar ese método manualmente.

---

## Lo que escribe el alumno

Tú escribes:

- La clase `CalculosFinanieros`.
- La clase `SwingValidator`.
- El código de los botones.
- El método `isValidData`.
- Los imports.

---

# 22. Flujo de funcionamiento

El flujo de la aplicación es:

```text
Usuario escribe datos
        ↓
Presiona Calcular
        ↓
Se validan los campos
        ↓
Se convierten los textos a números
        ↓
Se llama a CalculosFinanieros.calcularValorFuturo()
        ↓
Se calcula el valor futuro mes por mes
        ↓
Se muestra el resultado en valorFuturo
```

---

# 23. Código completo resumido

## CalculosFinanieros.java

```java
package modelo;

public class CalculosFinanieros {

    public static final int MESES_EN_ANO = 12;

    public static double calcularValorFuturo(double pagoMes, double tasaIntAnual, int numAnos) {
        int meses = numAnos * MESES_EN_ANO;
        double tasaIntMensual = tasaIntAnual / MESES_EN_ANO / 100;

        double valorFuturo = 0;

        for (int i = 1; i <= meses; i++) {
            valorFuturo = (valorFuturo + pagoMes) * (1 + tasaIntMensual);
        }

        return valorFuturo;
    }
}
```

## SwingValidator.java

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

    public boolean isDouble(JTextComponent c, String fieldName) {
        try {
            Double.parseDouble(c.getText());
            return true;
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(c, fieldName + " debe ser un número válido.");
            c.requestFocusInWindow();
            return false;
        }
    }

    public boolean isInteger(JTextComponent c, String fieldName) {
        try {
            Integer.parseInt(c.getText());
            return true;
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(c, fieldName + " debe ser un número entero.");
            c.requestFocusInWindow();
            return false;
        }
    }
}
```

## Métodos importantes en ValorFuturo.java

```java
private boolean isValidData() {
    SwingValidator sv = new SwingValidator();

    return sv.isPresent(pagoMes, "Pago mensual")
            && sv.isDouble(pagoMes, "Pago mensual")
            && sv.isPresent(tasaIntAnual, "Tasa interés anual")
            && sv.isDouble(tasaIntAnual, "Tasa interés anual")
            && sv.isPresent(numAnos, "Número de años")
            && sv.isInteger(numAnos, "Número de años");
}
```

```java
private void calcularActionPerformed(java.awt.event.ActionEvent evt) {                                         

    if (isValidData()) {
        double pM = Double.parseDouble(pagoMes.getText());
        double tIA = Double.parseDouble(tasaIntAnual.getText());
        int nA = Integer.parseInt(numAnos.getText());

        double vF = CalculosFinanieros.calcularValorFuturo(pM, tIA, nA);

        NumberFormat currency = NumberFormat.getCurrencyInstance();

        valorFuturo.setText(currency.format(vF));
    }
}
```

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {                                        

    pagoMes.setText("");
    tasaIntAnual.setText("");
    numAnos.setText("");
    valorFuturo.setText("");

    pagoMes.requestFocus();
}
```

```java
private void salirActionPerformed(java.awt.event.ActionEvent evt) {                                      

    System.exit(0);
}
```

---

