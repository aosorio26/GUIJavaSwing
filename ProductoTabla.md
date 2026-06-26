# Tutorial corregido para resolver `productoTabla` en NetBeans Maven

Este tutorial está pensado para resolver el examen `productoTabla` respetando lo que pide el PDF original: trabajar en NetBeans con Maven, completar principalmente `ProductoTabla.java`, usar las clases `Producto` y `SwingValidador`, mostrar `prodT1`, ordenar en `prodT2`, clonar y sumar un mes en `prodT3`, y buscar un valor según el radio button seleccionado.

La idea es hacerlo con el estilo que ya vimos en Java Swing: `DefaultTableModel`, eventos de botones/radio buttons, validación con `SwingValidator`, arreglos de objetos y fechas con `Date`, `GregorianCalendar`, `Calendar` y `DateFormat`.

---

## 1. Qué pide exactamente el examen

El proyecto se llama `productoTabla`.

La estructura esperada es esta:

```text
productoTabla
└── Source Packages
    ├── com.mycompany.productotest
    │   └── ProductoTabla.java      ← aquí se completa la programación
    ├── modelo
    │   └── Producto.java           ← ya programado o casi programado
    └── vista
        └── SwingValidator.java     ← ya programado
```

El PDF pide tres tablas:

```text
prodT1 → productos desordenados
prodT2 → productos ordenados
prodT3 → productos ordenados, clonados y con fecha + 1 mes
```

También aparecen estos elementos:

```text
fechaPromedio
codigo
nombre
fecha
mensaje
valor
resultado
```

En el examen sustitutorio, el paso 1 ya está programado y se deben completar los pasos 2 y 3. En el examen final anterior se ve la misma lógica completa: `prodT1` aleatorio, `prodT2` ordenado, `prodT3` clonado con +1 mes y búsqueda en la última fila.

---

## 2. Idea general de la solución

El programa debe manejar tres arreglos de productos:

```java
Producto[] pros1 = new Producto[nPro]; // productos originales
Producto[] pros2;                      // productos ordenados
Producto[] pros3;                      // productos clonados + 1 mes
```

La lógica es:

```text
1. Generar 6 productos aleatorios en pros1.
2. Mostrar pros1 en prodT1.
3. Calcular promedio de fechas y mostrarlo en fechaPromedio.
4. Cuando se elige Código, Nombre o Fecha:
   - Copiar pros1 hacia pros2.
   - Ordenar pros2 según el criterio.
   - Mostrar pros2 en prodT2.
   - Clonar pros2 hacia pros3.
   - Sumar un mes a la fecha de cada producto clonado.
   - Mostrar pros3 en prodT3.
   - Actualizar mensaje: Buscar CODIGO, Buscar NOMBRE o Buscar FECHA.
5. Cuando se escribe un valor en valor:
   - Validar según el criterio.
   - Buscar en pros3.
   - Si existe, mostrar la fila en resultado.
   - Si no existe, mostrar que no existe.
```

---

## 3. No usar `LocalDate` aquí

Para este examen conviene usar lo que aparece en el diagrama del profesor:

```java
import java.util.GregorianCalendar;
import java.util.Date;
import java.text.DateFormat;
import java.util.Calendar;
```

O sea, mejor usar `Date`, no `LocalDate`, porque el PDF del examen muestra explícitamente `Date`, `GregorianCalendar`, `Calendar` y `DateFormat`.

Por eso las fechas base se declaran así:

```java
GregorianCalendar[] gcs = {
    new GregorianCalendar(2024, 5, 10),
    new GregorianCalendar(2023, 4, 9),
    new GregorianCalendar(2022, 3, 8)
};

Date[] dates = {
    gcs[0].getTime(),
    gcs[1].getTime(),
    gcs[2].getTime()
};
```

Ojo: en `GregorianCalendar`, los meses empiezan en 0.

```text
0 = enero
1 = febrero
2 = marzo
3 = abril
4 = mayo
5 = junio
```

Entonces:

```java
new GregorianCalendar(2024, 5, 10)
```

representa:

```text
10/06/2024
```

---

## 4. Clase `Producto`

La clase `Producto` debe representar una fila de la tabla.

Debe tener:

```text
codigo
nombre
fecha
```

Una versión adecuada para este examen sería:

```java
package modelo;

import java.util.Date;

public class Producto implements Cloneable {
    private int codigo;
    private String nombre;
    private Date fecha;

    public Producto(int codigo, String nombre, Date fecha) {
        this.codigo = codigo;
        this.nombre = nombre;
        this.fecha = fecha;
    }

    public int getCodigo() {
        return codigo;
    }

    public void setCodigo(int codigo) {
        this.codigo = codigo;
    }

    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public Date getFecha() {
        return fecha;
    }

    public void setFecha(Date fecha) {
        this.fecha = fecha;
    }

    @Override
    public Producto clone() {
        return new Producto(codigo, nombre, (Date) fecha.clone());
    }
}
```

La parte importante es esta:

```java
(Date) fecha.clone()
```

Eso permite clonar la fecha para que `prodT3` no modifique accidentalmente la fecha original de `prodT2`.

---

## 5. Clase `SwingValidator`

En `DeporteApp` ya se usaba un validador así:

```java
if (!validator.isPresent(txtAlumno, "Alumno")) return;
if (!validator.isInteger(txtSalto1, "Salto 1")) return;
```

Para este examen, el validador puede mantenerse casi igual. Solo conviene agregar una validación de fecha, porque el examen permite buscar por FECHA.

Debe quedar más o menos así:

```java
package vista;

import javax.swing.*;
import javax.swing.text.JTextComponent;
import java.text.DateFormat;
import java.text.ParseException;

public class SwingValidator {

    public boolean isPresent(JTextComponent c, String fieldName) {
        if (c.getText().trim().length() == 0) {
            showMessage(c, fieldName + " es un campo requerido.");
            c.requestFocusInWindow();
            return false;
        }
        return true;
    }

    public boolean isInteger(JTextComponent c, String fieldName) {
        try {
            int i = Integer.parseInt(c.getText().trim());
            return true;
        } catch (NumberFormatException e) {
            showMessage(c, fieldName + " debe ser entero.");
            c.requestFocusInWindow();
            return false;
        }
    }

    public boolean isDate(JTextComponent c, String fieldName, DateFormat df) {
        try {
            df.setLenient(false);
            df.parse(c.getText().trim());
            return true;
        } catch (ParseException e) {
            showMessage(c, fieldName + " debe tener formato de fecha válido.");
            c.requestFocusInWindow();
            return false;
        }
    }

    private void showMessage(JTextComponent c, String message) {
        JOptionPane.showMessageDialog(c, message);
    }
}
```

No es necesario hacer un validador enorme. El examen solo necesita:

```text
presente
entero
fecha válida
```

---

## 6. Variables principales en `ProductoTabla.java`

En `ProductoTabla.java` se recomienda usar exactamente la idea del diagrama del examen:

```java
import javax.swing.table.DefaultTableModel;
import java.util.Random;
import java.util.Arrays;
import java.util.GregorianCalendar;
import java.util.Date;
import java.text.DateFormat;
import java.text.ParseException;
import java.util.Calendar;
import javax.swing.JTable;
import modelo.Producto;
import vista.SwingValidator;
```

Dentro de la clase:

```java
SwingValidator sv = new SwingValidator();
Random ran = new Random();

String[] nombres = {"Pa", "Pb", "Pc"};

GregorianCalendar[] gcs = {
    new GregorianCalendar(2024, 5, 10),
    new GregorianCalendar(2023, 4, 9),
    new GregorianCalendar(2022, 3, 8)
};

Date[] dates = {
    gcs[0].getTime(),
    gcs[1].getTime(),
    gcs[2].getTime()
};

int nPro = 6;

Producto[] pros1 = new Producto[nPro];
Producto[] pros2, pros3;

static DateFormat df = DateFormat.getDateInstance(DateFormat.SHORT);

String[] cols = {"Código", "Nombre", "Fecha"};

DefaultTableModel mod1 = new DefaultTableModel(cols, 0);
DefaultTableModel mod2 = new DefaultTableModel(cols, 0);
DefaultTableModel mod3 = new DefaultTableModel(cols, 0);
```

Esta forma respeta más el PDF que usar `ArrayList` o `LocalDate`.

---

## 7. Constructor de `ProductoTabla`

Después de `initComponents();`, se asignan los modelos a las tablas.

```java
public ProductoTabla() {
    initComponents();

    df.setLenient(false);

    prodT1.setModel(mod1);
    prodT2.setModel(mod2);
    prodT3.setModel(mod3);

    generarProductos();
    mostrarProductos(pros1, mod1);
    mostrarPromedioFechas();

    mensaje.setText("Buscar");
    resultado.setText("");
}
```

Aquí se asume que los nombres de tus componentes son los del PDF:

```text
prodT1
prodT2
prodT3
fechaPromedio
codigo
nombre
fecha
mensaje
valor
resultado
```

Si en NetBeans tus variables tienen otros nombres, debes adaptar el código.

---

## 8. Generar productos aleatorios para `prodT1`

El examen pide 6 filas generadas aleatoriamente.

```java
private void generarProductos() {
    for (int i = 0; i < nPro; i++) {
        int cod = ran.nextInt(4) + 1;
        String nom = nombres[ran.nextInt(nombres.length)];
        Date fec = dates[ran.nextInt(dates.length)];

        pros1[i] = new Producto(cod, nom, (Date) fec.clone());
    }
}
```

Explicación:

```java
ran.nextInt(4) + 1
```

genera códigos entre 1 y 4.

```java
nombres[ran.nextInt(nombres.length)]
```

escoge `Pa`, `Pb` o `Pc`.

```java
dates[ran.nextInt(dates.length)]
```

escoge una de las tres fechas base.

---

## 9. Mostrar productos en una tabla

Para no repetir código, se crea un método que reciba un arreglo y un modelo.

```java
private void mostrarProductos(Producto[] pros, DefaultTableModel mod) {
    mod.setRowCount(0);

    for (Producto p : pros) {
        Object[] fila = new Object[3];
        fila[0] = p.getCodigo();
        fila[1] = p.getNombre();
        fila[2] = df.format(p.getFecha());
        mod.addRow(fila);
    }
}
```

Esto es igual a la lógica que se usaba en `DeporteApp` con:

```java
modelo.addRow(fila);
```

La diferencia es que aquí se agregan productos en lugar de alumnos.

---

## 10. Calcular promedio de fechas

El examen muestra un texto como:

```text
Promedio de fechas: 4/03/23
```

Una forma simple es promediar los milisegundos de las fechas.

```java
private void mostrarPromedioFechas() {
    long suma = 0;

    for (Producto p : pros1) {
        suma += p.getFecha().getTime();
    }

    Date promedio = new Date(suma / pros1.length);
    fechaPromedio.setText("Promedio de fechas: " + df.format(promedio));
}
```

---

## 11. Paso 2: ordenar según radio button

Cuando el usuario selecciona `codigo`, `nombre` o `fecha`, debe ejecutarse el paso 2.

Puedes conectar el mismo método a los tres radio buttons.

```java
private void codigoActionPerformed(java.awt.event.ActionEvent evt) {
    ordenarYClonar();
}

private void nombreActionPerformed(java.awt.event.ActionEvent evt) {
    ordenarYClonar();
}

private void fechaActionPerformed(java.awt.event.ActionEvent evt) {
    ordenarYClonar();
}
```

El método principal sería:

```java
private void ordenarYClonar() {
    pros2 = clonarArreglo(pros1);

    if (codigo.isSelected()) {
        Arrays.sort(pros2, (a, b) -> Integer.compare(a.getCodigo(), b.getCodigo()));
        mensaje.setText("Buscar CODIGO");
    } else if (nombre.isSelected()) {
        Arrays.sort(pros2, (a, b) -> a.getNombre().compareToIgnoreCase(b.getNombre()));
        mensaje.setText("Buscar NOMBRE");
    } else if (fecha.isSelected()) {
        Arrays.sort(pros2, (a, b) -> a.getFecha().compareTo(b.getFecha()));
        mensaje.setText("Buscar FECHA");
    }

    mostrarProductos(pros2, mod2);

    pros3 = clonarArreglo(pros2);
    aumentarUnMes(pros3);
    mostrarProductos(pros3, mod3);

    valor.setText("");
    resultado.setText("");
    valor.requestFocusInWindow();
}
```

---

## 12. Clonar arreglo de productos

Como el examen pide clonar, no basta con hacer esto:

```java
pros3 = pros2;
```

Eso estaría mal porque ambos arreglos apuntarían a los mismos objetos.

Debe hacerse así:

```java
private Producto[] clonarArreglo(Producto[] origen) {
    Producto[] copia = new Producto[origen.length];

    for (int i = 0; i < origen.length; i++) {
        copia[i] = origen[i].clone();
    }

    return copia;
}
```

Así `pros2` y `pros3` son arreglos separados.

---

## 13. Sumar un mes a las fechas de `prodT3`

Como estamos usando `Date`, para sumar un mes se usa `Calendar`.

```java
private void aumentarUnMes(Producto[] pros) {
    Calendar cal = Calendar.getInstance();

    for (Producto p : pros) {
        cal.setTime(p.getFecha());
        cal.add(Calendar.MONTH, 1);
        p.setFecha(cal.getTime());
    }
}
```

Esto respeta el pedido:

```text
prodT3: clona a prodT2 y le suma 1 mes
```

---

## 14. Paso 3: buscar en `prodT3`

El examen dice que la última fila debe validar `valor` según el orden establecido por los radio buttons y buscar en `prodT3`.

Puedes hacer la búsqueda cuando se presione Enter en el campo `valor`.

En NetBeans, doble clic al `JTextField valor` y coloca:

```java
private void valorActionPerformed(java.awt.event.ActionEvent evt) {
    buscarEnProdT3();
}
```

También puedes hacerlo con un botón si tu interfaz lo tiene, pero en la imagen del examen parece que se trabaja con el campo `valor` directamente.

---

## 15. Validar antes de buscar

La validación debe depender del criterio.

```java
private boolean validarBusqueda() {
    if (!sv.isPresent(valor, "Valor")) return false;

    if (codigo.isSelected()) {
        return sv.isInteger(valor, "Código");
    }

    if (fecha.isSelected()) {
        return sv.isDate(valor, "Fecha", df);
    }

    return true;
}
```

Para nombre solo se valida que el campo no esté vacío.

---

## 16. Buscar producto

```java
private void buscarEnProdT3() {
    if (pros3 == null) {
        resultado.setText("Seleccione un orden primero");
        return;
    }

    if (!validarBusqueda()) return;

    String texto = valor.getText().trim();

    for (Producto p : pros3) {
        if (coincide(p, texto)) {
            resultado.setText(p.getCodigo() + " " + p.getNombre() + " " + df.format(p.getFecha()));
            return;
        }
    }

    resultado.setText("No existe");
}
```

La comparación se separa en otro método:

```java
private boolean coincide(Producto p, String texto) {
    if (codigo.isSelected()) {
        int cod = Integer.parseInt(texto);
        return p.getCodigo() == cod;
    }

    if (nombre.isSelected()) {
        return p.getNombre().equalsIgnoreCase(texto);
    }

    if (fecha.isSelected()) {
        try {
            Date buscada = df.parse(texto);
            return mismaFecha(p.getFecha(), buscada);
        } catch (ParseException e) {
            return false;
        }
    }

    return false;
}
```

---

## 17. Comparar fechas correctamente

No conviene comparar fechas con `==`.

Tampoco conviene comparar directamente con `equals` si hay horas internas distintas.

Mejor comparar año, mes y día:

```java
private boolean mismaFecha(Date a, Date b) {
    Calendar ca = Calendar.getInstance();
    Calendar cb = Calendar.getInstance();

    ca.setTime(a);
    cb.setTime(b);

    return ca.get(Calendar.YEAR) == cb.get(Calendar.YEAR)
            && ca.get(Calendar.MONTH) == cb.get(Calendar.MONTH)
            && ca.get(Calendar.DAY_OF_MONTH) == cb.get(Calendar.DAY_OF_MONTH);
}
```

---

## 18. Código completo de referencia para `ProductoTabla.java`

Este código es una guía. Debes adaptarlo a los nombres reales de tus componentes en NetBeans.

```java
package com.mycompany.productotest;

import javax.swing.table.DefaultTableModel;
import java.util.Random;
import java.util.Arrays;
import java.util.GregorianCalendar;
import java.util.Date;
import java.text.DateFormat;
import java.text.ParseException;
import java.util.Calendar;
import modelo.Producto;
import vista.SwingValidator;

public class ProductoTabla extends javax.swing.JFrame {

    SwingValidator sv = new SwingValidator();
    Random ran = new Random();

    String[] nombres = {"Pa", "Pb", "Pc"};

    GregorianCalendar[] gcs = {
        new GregorianCalendar(2024, 5, 10),
        new GregorianCalendar(2023, 4, 9),
        new GregorianCalendar(2022, 3, 8)
    };

    Date[] dates = {
        gcs[0].getTime(),
        gcs[1].getTime(),
        gcs[2].getTime()
    };

    int nPro = 6;
    Producto[] pros1 = new Producto[nPro];
    Producto[] pros2, pros3;

    static DateFormat df = DateFormat.getDateInstance(DateFormat.SHORT);

    String[] cols = {"Código", "Nombre", "Fecha"};
    DefaultTableModel mod1 = new DefaultTableModel(cols, 0);
    DefaultTableModel mod2 = new DefaultTableModel(cols, 0);
    DefaultTableModel mod3 = new DefaultTableModel(cols, 0);

    public ProductoTabla() {
        initComponents();

        df.setLenient(false);

        prodT1.setModel(mod1);
        prodT2.setModel(mod2);
        prodT3.setModel(mod3);

        generarProductos();
        mostrarProductos(pros1, mod1);
        mostrarPromedioFechas();

        mensaje.setText("Buscar");
        resultado.setText("");
    }

    private void generarProductos() {
        for (int i = 0; i < nPro; i++) {
            int cod = ran.nextInt(4) + 1;
            String nom = nombres[ran.nextInt(nombres.length)];
            Date fec = dates[ran.nextInt(dates.length)];
            pros1[i] = new Producto(cod, nom, (Date) fec.clone());
        }
    }

    private void mostrarProductos(Producto[] pros, DefaultTableModel mod) {
        mod.setRowCount(0);

        for (Producto p : pros) {
            Object[] fila = new Object[3];
            fila[0] = p.getCodigo();
            fila[1] = p.getNombre();
            fila[2] = df.format(p.getFecha());
            mod.addRow(fila);
        }
    }

    private void mostrarPromedioFechas() {
        long suma = 0;

        for (Producto p : pros1) {
            suma += p.getFecha().getTime();
        }

        Date promedio = new Date(suma / pros1.length);
        fechaPromedio.setText("Promedio de fechas: " + df.format(promedio));
    }

    private Producto[] clonarArreglo(Producto[] origen) {
        Producto[] copia = new Producto[origen.length];

        for (int i = 0; i < origen.length; i++) {
            copia[i] = origen[i].clone();
        }

        return copia;
    }

    private void ordenarYClonar() {
        pros2 = clonarArreglo(pros1);

        if (codigo.isSelected()) {
            Arrays.sort(pros2, (a, b) -> Integer.compare(a.getCodigo(), b.getCodigo()));
            mensaje.setText("Buscar CODIGO");
        } else if (nombre.isSelected()) {
            Arrays.sort(pros2, (a, b) -> a.getNombre().compareToIgnoreCase(b.getNombre()));
            mensaje.setText("Buscar NOMBRE");
        } else if (fecha.isSelected()) {
            Arrays.sort(pros2, (a, b) -> a.getFecha().compareTo(b.getFecha()));
            mensaje.setText("Buscar FECHA");
        }

        mostrarProductos(pros2, mod2);

        pros3 = clonarArreglo(pros2);
        aumentarUnMes(pros3);
        mostrarProductos(pros3, mod3);

        valor.setText("");
        resultado.setText("");
        valor.requestFocusInWindow();
    }

    private void aumentarUnMes(Producto[] pros) {
        Calendar cal = Calendar.getInstance();

        for (Producto p : pros) {
            cal.setTime(p.getFecha());
            cal.add(Calendar.MONTH, 1);
            p.setFecha(cal.getTime());
        }
    }

    private boolean validarBusqueda() {
        if (!sv.isPresent(valor, "Valor")) return false;

        if (codigo.isSelected()) {
            return sv.isInteger(valor, "Código");
        }

        if (fecha.isSelected()) {
            return sv.isDate(valor, "Fecha", df);
        }

        return true;
    }

    private void buscarEnProdT3() {
        if (pros3 == null) {
            resultado.setText("Seleccione un orden primero");
            return;
        }

        if (!validarBusqueda()) return;

        String texto = valor.getText().trim();

        for (Producto p : pros3) {
            if (coincide(p, texto)) {
                resultado.setText(p.getCodigo() + " " + p.getNombre() + " " + df.format(p.getFecha()));
                return;
            }
        }

        resultado.setText("No existe");
    }

    private boolean coincide(Producto p, String texto) {
        if (codigo.isSelected()) {
            int cod = Integer.parseInt(texto);
            return p.getCodigo() == cod;
        }

        if (nombre.isSelected()) {
            return p.getNombre().equalsIgnoreCase(texto);
        }

        if (fecha.isSelected()) {
            try {
                Date buscada = df.parse(texto);
                return mismaFecha(p.getFecha(), buscada);
            } catch (ParseException e) {
                return false;
            }
        }

        return false;
    }

    private boolean mismaFecha(Date a, Date b) {
        Calendar ca = Calendar.getInstance();
        Calendar cb = Calendar.getInstance();

        ca.setTime(a);
        cb.setTime(b);

        return ca.get(Calendar.YEAR) == cb.get(Calendar.YEAR)
                && ca.get(Calendar.MONTH) == cb.get(Calendar.MONTH)
                && ca.get(Calendar.DAY_OF_MONTH) == cb.get(Calendar.DAY_OF_MONTH);
    }

    private void codigoActionPerformed(java.awt.event.ActionEvent evt) {
        ordenarYClonar();
    }

    private void nombreActionPerformed(java.awt.event.ActionEvent evt) {
        ordenarYClonar();
    }

    private void fechaActionPerformed(java.awt.event.ActionEvent evt) {
        ordenarYClonar();
    }

    private void valorActionPerformed(java.awt.event.ActionEvent evt) {
        buscarEnProdT3();
    }
}
```

---

## 19. Qué partes van en eventos de NetBeans

No pegues todo dentro de un botón.

Lo recomendable es:

```text
Constructor ProductoTabla()
├── initComponents()
├── asignar modelos
├── generar productos
├── mostrar prodT1
└── mostrar promedio
```

Luego:

```text
Radio button código → ordenarYClonar()
Radio button nombre → ordenarYClonar()
Radio button fecha  → ordenarYClonar()
Campo valor Enter   → buscarEnProdT3()
```

Así el programa se parece más a lo que se hizo en `DeporteApp`: validar, calcular, agregar/mostrar y limpiar campos dentro de eventos.

---

## 20. Checklist para revisar antes de exportar

Antes de entregar, revisa esto:

```text
[ ] prodT1 muestra 6 filas aleatorias.
[ ] fechaPromedio muestra el promedio de fechas.
[ ] Al seleccionar Código, prodT2 se ordena por código.
[ ] Al seleccionar Nombre, prodT2 se ordena por nombre.
[ ] Al seleccionar Fecha, prodT2 se ordena por fecha.
[ ] prodT3 tiene los mismos datos de prodT2, pero con fecha + 1 mes.
[ ] Si busco por código, valida entero.
[ ] Si busco por nombre, valida campo no vacío.
[ ] Si busco por fecha, valida fecha.
[ ] Si existe el valor, resultado muestra la fila.
[ ] Si no existe, resultado muestra No existe.
[ ] El proyecto compila en NetBeans.
[ ] Se exporta como ApellidoNombre.zip.
```

---

## 21. Errores comunes

### Error 1: usar `LocalDate`

No es lo ideal aquí porque el PDF del profesor usa `Date`, `GregorianCalendar`, `Calendar` y `DateFormat`.

### Error 2: hacer `pros3 = pros2`

Eso no clona. Solo copia la referencia.

Debe usarse:

```java
pros3 = clonarArreglo(pros2);
```

### Error 3: sumar un mes a `pros2`

El mes se debe sumar a `pros3`, no a `pros2`.

```java
pros3 = clonarArreglo(pros2);
aumentarUnMes(pros3);
```

### Error 4: buscar en `prodT2`

El examen pide buscar en `prodT3`.

### Error 5: comparar fecha como texto sin validar

Mejor validar con `DateFormat` y comparar año, mes y día con `Calendar`.

---

## 22. Resumen final

La solución del examen se resume así:

```text
Producto = modelo de cada fila.
SwingValidator = valida campos de entrada.
ProductoTabla = controla la interfaz y la lógica.
prodT1 = arreglo original aleatorio.
prodT2 = copia ordenada.
prodT3 = copia clonada con fecha + 1 mes.
valor = campo de búsqueda.
resultado = fila encontrada o mensaje de no existe.
```

La clave para sacar los puntos es respetar los tres pasos del PDF:

```text
Paso 1: mostrar prodT1 con 6 productos.
Paso 2: ordenar en prodT2 y clonar + 1 mes en prodT3.
Paso 3: buscar en prodT3 según Código, Nombre o Fecha.
