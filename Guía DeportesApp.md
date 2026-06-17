# Tutorial: implementar DeporteApp en NetBeans paso a paso

Este tutorial implementa la aplicación **DeporteApp** para controlar una competencia de **salto largo**. La interfaz usa los nombres pedidos en el enunciado: `txtAlumno`, `txtSalto1`, `txtSalto2`, `txtSalto3`, `btnRegistrar`, `taCompetencia`, `btnEstadisticas` y `taEstadistica`.

---

## 1. Crear el proyecto en NetBeans

1. Abrir **NetBeans**.
2. Ir a **File > New Project**.
3. Elegir:
   - **Java with Maven**
   - **Java Application**
4. En **Project Name**, escribir:

```text
DeporteApp
```

5. Desmarcar la opción:

```text
Create Main Class
```

6. Presionar **Finish**.

---

## 2. Crear los paquetes

Dentro de `Source Packages`, crear estos paquetes:

```text
presentacion
utility
```

Para crearlos:

1. Clic derecho en **Source Packages**.
2. Elegir **New > Java Package**.
3. Crear primero `presentacion`.
4. Repetir y crear `utility`.

---

## 3. Crear la clase SwingValidator

Dentro del paquete `utility`:

1. Clic derecho sobre `utility`.
2. Elegir **New > Java Class**.
3. Nombre de clase:

```text
SwingValidator
```

4. Pegar este código:

```java
package utility;

import javax.swing.JOptionPane;
import javax.swing.text.JTextComponent;

public class SwingValidator {

    public boolean isPresent(JTextComponent c, String fieldName) {
        if (c.getText().length() == 0) {
            showMessage(c, fieldName + " es un campo requerido.");
            c.requestFocusInWindow();
            return false;
        }
        return true;
    }

    public boolean isInteger(JTextComponent c, String fieldName) {
        try {
            int i = Integer.parseInt(c.getText());
            return true;
        } catch (NumberFormatException e) {
            showMessage(c, fieldName + " debe ser entero.");
            c.requestFocusInWindow();
            return false;
        }
    }

    public boolean isDouble(JTextComponent c, String fieldName) {
        try {
            double d = Double.parseDouble(c.getText());
            return true;
        } catch (NumberFormatException e) {
            showMessage(c, fieldName + " debe ser un número válido.");
            c.requestFocusInWindow();
            return false;
        }
    }

    private void showMessage(JTextComponent c, String message) {
        JOptionPane.showMessageDialog(c, message);
    }
}
```

---

## 4. Crear el JFrame Form

Dentro del paquete `presentacion`:

1. Clic derecho sobre `presentacion`.
2. Elegir **New > JFrame Form**.
3. Nombre de clase:

```text
DeporteApp
```

4. Presionar **Finish**.

---

## 5. Diseñar la ventana

En la pestaña **Design**, colocar los siguientes componentes:

### Labels

Colocar etiquetas con estos textos:

```text
Alumno
Salto 1
Salto 2
Salto 3
Competencia
```

### Text Fields

Agregar cuatro `JTextField` y cambiar sus nombres de variable:

| Componente | Variable name |
|---|---|
| Campo del alumno | `txtAlumno` |
| Campo Salto 1 | `txtSalto1` |
| Campo Salto 2 | `txtSalto2` |
| Campo Salto 3 | `txtSalto3` |

Para cambiar el nombre:

1. Clic derecho sobre el componente.
2. Elegir **Change Variable Name**.
3. Escribir el nombre correspondiente.

### Botón Registrar

Agregar un `JButton`:

| Propiedad | Valor |
|---|---|
| Text | `Registrar` |
| Variable name | `btnRegistrar` |

### Tabla de competencia

Agregar un `JTable` dentro de un `JScrollPane`.

Cambiar el nombre de variable de la tabla a:

```text
taCompetencia
```

La tabla debe tener estas columnas:

```text
Nombre
Intento 1
Intento 2
Intento 3
Final
```

Para editar las columnas:

1. Clic derecho sobre la tabla.
2. Ir a **Table Contents**.
3. Cambiar los títulos de las columnas.
4. Dejar la tabla sin filas iniciales, o borrar las filas de ejemplo.

### Botón Estadísticas

Agregar otro `JButton`:

| Propiedad | Valor |
|---|---|
| Text | `Estadísticas` |
| Variable name | `btnEstadisticas` |

### Área de estadísticas

Agregar un `JTextArea` dentro de un `JScrollPane`.

Cambiar su nombre de variable a:

```text
taEstadistica
```

En sus propiedades colocar:

```text
lineWrap = true
```

---

## 6. Importar las clases necesarias

Abrir la pestaña **Source** de `DeporteApp` y, arriba del archivo, debajo del `package`, agregar:

```java
package presentacion;

import javax.swing.table.DefaultTableModel;
import utility.SwingValidator;
```

---

## 7. Crear el modelo de la tabla

Dentro de la clase `DeporteApp`, antes del constructor, declarar:

```java
DefaultTableModel modelo;
SwingValidator validator = new SwingValidator();
```

Luego, dentro del constructor `public DeporteApp()`, después de `initComponents();`, colocar:

```java
modelo = new DefaultTableModel();
modelo.addColumn("Nombre");
modelo.addColumn("Intento 1");
modelo.addColumn("Intento 2");
modelo.addColumn("Intento 3");
modelo.addColumn("Final");
taCompetencia.setModel(modelo);
```

Debe quedar parecido a esto:

```java
public DeporteApp() {
    initComponents();

    modelo = new DefaultTableModel();
    modelo.addColumn("Nombre");
    modelo.addColumn("Intento 1");
    modelo.addColumn("Intento 2");
    modelo.addColumn("Intento 3");
    modelo.addColumn("Final");
    taCompetencia.setModel(modelo);
}
```

---

## 8. Programar el botón Registrar

Volver a **Design**.

1. Doble clic sobre el botón **Registrar**.
2. NetBeans creará el método `btnRegistrarActionPerformed`.
3. Dentro de ese método, pegar:

```java
private void btnRegistrarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!validator.isPresent(txtAlumno, "Alumno")) return;
    if (!validator.isPresent(txtSalto1, "Salto 1")) return;
    if (!validator.isPresent(txtSalto2, "Salto 2")) return;
    if (!validator.isPresent(txtSalto3, "Salto 3")) return;

    if (!validator.isInteger(txtSalto1, "Salto 1")) return;
    if (!validator.isInteger(txtSalto2, "Salto 2")) return;
    if (!validator.isInteger(txtSalto3, "Salto 3")) return;

    String alumno = txtAlumno.getText();
    int salto1 = Integer.parseInt(txtSalto1.getText());
    int salto2 = Integer.parseInt(txtSalto2.getText());
    int salto3 = Integer.parseInt(txtSalto3.getText());

    int mayor = salto1;

    if (salto2 > mayor) {
        mayor = salto2;
    }

    if (salto3 > mayor) {
        mayor = salto3;
    }

    Object[] fila = new Object[5];
    fila[0] = alumno;
    fila[1] = salto1;
    fila[2] = salto2;
    fila[3] = salto3;
    fila[4] = mayor;

    modelo.addRow(fila);

    txtAlumno.setText("");
    txtSalto1.setText("");
    txtSalto2.setText("");
    txtSalto3.setText("");
    txtAlumno.requestFocusInWindow();
}
```

Este botón hace lo siguiente:

- Verifica que los campos no estén vacíos.
- Verifica que los saltos sean enteros.
- Calcula el mejor salto del alumno.
- Agrega una fila a la tabla.
- Limpia los campos.

---

## 9. Programar el botón Estadísticas

Volver a **Design**.

1. Doble clic sobre el botón **Estadísticas**.
2. NetBeans creará el método `btnEstadisticasActionPerformed`.
3. Dentro de ese método, pegar:

```java
private void btnEstadisticasActionPerformed(java.awt.event.ActionEvent evt) {
    int totalAlumnos = modelo.getRowCount();

    if (totalAlumnos == 0) {
        taEstadistica.setText("No hay alumnos registrados.");
        return;
    }

    int maximo = Integer.parseInt(modelo.getValueAt(0, 4).toString());
    String ganador = modelo.getValueAt(0, 0).toString();

    for (int i = 1; i < totalAlumnos; i++) {
        int saltoFinal = Integer.parseInt(modelo.getValueAt(i, 4).toString());

        if (saltoFinal > maximo) {
            maximo = saltoFinal;
            ganador = modelo.getValueAt(i, 0).toString();
        }
    }

    taEstadistica.setText("Total de alumnos: " + totalAlumnos + "\n"
            + "El máximo puntaje es: " + maximo + "\n"
            + "El ganador es: " + ganador);
}
```

Este botón calcula:

- Total de alumnos registrados.
- Mayor salto final.
- Nombre del ganador.

---

## 10. Revisar el método main

Al final de la clase `DeporteApp`, NetBeans debe haber creado el método `main`.

Debe abrir la ventana así:

```java
public static void main(String args[]) {
    java.awt.EventQueue.invokeLater(new Runnable() {
        public void run() {
            new DeporteApp().setVisible(true);
        }
    });
}
```

Si no existe, se puede agregar manualmente dentro de la clase.

---

## 11. Probar el programa

Ejecutar el proyecto con:

```text
Shift + F6
```

O clic derecho sobre `DeporteApp.java` y elegir:

```text
Run File
```

Probar con estos datos:

```text
Alumno: AA
Salto 1: 100
Salto 2: 110
Salto 3: 120
```

Al registrar, debe aparecer una fila parecida a:

```text
AA | 100 | 110 | 120 | 120
```

Luego probar otro alumno:

```text
Alumno: BB
Salto 1: 90
Salto 2: 80
Salto 3: 70
```

Al presionar **Estadísticas**, debería aparecer algo parecido a:

```text
Total de alumnos: 2
El máximo puntaje es: 120
El ganador es: AA
```

---

## 12. Probar mensajes de error

Para probar los errores, escribir por ejemplo:

```text
Salto 1: cien
```

Debe salir un mensaje como:

```text
Salto 1 debe ser entero.
```

También se puede dejar un campo vacío para probar:

```text
Alumno es un campo requerido.
```

---

## 13. Exportar el proyecto a ZIP

Cuando el proyecto ya funcione:

1. Ir a la barra de menú.
2. Elegir:

```text
File > Export Project > To ZIP...
```

3. Seleccionar el proyecto `DeporteApp`.
4. Elegir la carpeta donde se guardará.
5. Colocar el nombre pedido, por ejemplo:

```text
ApellidoNombre.zip
```

6. Presionar **Export**.

---

## 14. Estructura final esperada

El proyecto debe quedar aproximadamente así:

```text
DeporteApp
└── Source Packages
    ├── presentacion
    │   └── DeporteApp.java
    └── utility
        └── SwingValidator.java
```

---

## 15. Resumen de la lógica

La aplicación trabaja así:

1. El usuario escribe el nombre del alumno y sus tres saltos.
2. El programa valida que todos los campos estén completos.
3. El programa valida que los saltos sean números enteros.
4. Se calcula el mayor de los tres saltos.
5. Se agrega el alumno a la tabla.
6. El botón **Estadísticas** recorre la tabla y busca al alumno con mayor salto final.
