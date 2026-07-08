# Guía detallada para resolver el examen SustiApp en NetBeans con Maven

Esta guía explica cómo resolver el examen sustitutorio de Programación Orientada a Objetos usando **Java Swing**, **NetBeans** y **Maven**. Está pensada para que puedas partir del proyecto `sustiApp`, copiar el archivo `SwingValidator.java`, completar las vistas pedidas y exportar el proyecto final como `.zip`.

El examen pide programar el proyecto **SustiApp** con estas clases principales:

| Clase | Tipo | Puntaje aproximado |
|---|---|---:|
| `Menu.java` / `SustiApp.java` | `JFrame Form` | 5 pts |
| `Alzeimer.java` o `Alzheimer.java` | `JInternalFrame Form` | 5 pts |
| `Ppt.java` | `JInternalFrame Form` | 5 pts |
| `Operaciones.java` | `JInternalFrame Form` | 5 pts |

> Nota importante: en el proyecto base puede aparecer como `SustiApp.java` en vez de `Menu.java`, y como `Alzeimer.java` en vez de `Alzheimer.java`. Lo mejor es **no cambiar nombres de clases si ya existen**, porque NetBeans asocia cada formulario `.java` con su `.form`. Si el proyecto base trae `Alzeimer.java`, usa `Alzeimer`; si trae `Alzheimer.java`, usa `Alzheimer`.

---

## 1. Objetivo general del examen

El programa debe mostrar una ventana principal con menú:

- Menú **Juegos**
  - Opción **Alzeimer / Alzheimer**
  - Opción **Ppt**
- Menú **Cálculos**
  - Opción **4 Operaciones**

Cada opción del menú debe abrir un formulario interno dentro de un `JDesktopPane`.

La ventana principal es un `JFrame`; los tres ejercicios son `JInternalFrame`.

La idea visual es esta:

```text
SustiApp / Menu JFrame
│
├── JMenuBar
│   ├── JMenu juegos
│   │   ├── JMenuItem opAlzeimer / opAlzheimer
│   │   └── JMenuItem opPpt
│   │
│   └── JMenu calculos
│       └── JMenuItem opCalculo
│
└── JDesktopPane escritorio
    ├── Alzeimer / Alzheimer JInternalFrame
    ├── Ppt JInternalFrame
    └── Operaciones JInternalFrame
```

---

## 2. Preparar el proyecto en NetBeans

### 2.1. Crear carpeta de trabajo

Según el examen, la solución debe guardarse en el Escritorio en una carpeta con el formato:

```text
ApellidoNombreCodigo
```

Ejemplo:

```text
DelgadoRomero20235009B
```

Dentro de esa carpeta coloca:

```text
ApellidoNombreCodigo/
├── Preguntas.pdf
├── SwingValidator.java
└── sustiApp/
```

---

### 2.2. Abrir el proyecto Maven

En NetBeans:

1. Abre NetBeans.
2. Ve a **File > Open Project**.
3. Busca la carpeta `sustiApp`.
4. Selecciona el proyecto Maven.
5. Clic en **Open Project**.

La estructura esperada es:

```text
sustiApp/
├── pom.xml
└── src/
    └── main/
        └── java/
            ├── com/
            │   └── mycompany/
            │       └── sustiapp/
            │           ├── SustiApp.java
            │           ├── SustiApp.form
            │           ├── Alzeimer.java
            │           ├── Alzeimer.form
            │           ├── Ppt.java
            │           ├── Ppt.form
            │           ├── Operaciones.java
            │           └── Operaciones.form
            └── vista/
                └── SwingValidator.java
```

Si la carpeta `vista` no existe, créala dentro de `src/main/java`.

---

## 3. Revisar el archivo `pom.xml`

Abre `pom.xml`. Debe verse parecido a esto:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.mycompany</groupId>
    <artifactId>sustiApp</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.release>22</maven.compiler.release>
        <exec.mainClass>com.mycompany.sustiapp.SustiApp</exec.mainClass>
    </properties>
</project>
```

### Si tu JDK no es 22

Si tienes JDK 21, cambia:

```xml
<maven.compiler.release>22</maven.compiler.release>
```

por:

```xml
<maven.compiler.release>21</maven.compiler.release>
```

Si tienes JDK 17:

```xml
<maven.compiler.release>17</maven.compiler.release>
```

Lo importante es que el número coincida con el JDK que NetBeans está usando.

---

## 4. Copiar `SwingValidator.java`

El examen indica que este archivo ya viene dado. Debe ir en:

```text
src/main/java/vista/SwingValidator.java
```

El contenido debe empezar así:

```java
package vista;

import javax.swing.*;
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
        JOptionPane.showMessageDialog(c, message, "Dato no válido", JOptionPane.ERROR_MESSAGE);
    }
}
```

En tus formularios podrás usarlo así:

```java
import vista.SwingValidator;
```

Y luego:

```java
SwingValidator validator = new SwingValidator();
```

---

## 5. Formulario principal: `SustiApp.java` o `Menu.java`

Este formulario vale 5 puntos. Debe ser un **JFrame Form**.

### 5.1. Componentes que debe tener

En el diseñador de NetBeans agrega:

| Componente | Variable name | Texto visible |
|---|---|---|
| `JDesktopPane` | `escritorio` | No aplica |
| `JMenuBar` | automático | No aplica |
| `JMenu` | `juegos` | `Juegos` |
| `JMenu` | `calculos` | `Cálculos` |
| `JMenuItem` | `opAlzeimer` o `opAlzheimer` | `Alzeimer` o `Alzheimer` |
| `JMenuItem` | `opPpt` | `Ppt` |
| `JMenuItem` | `opCalculo` | `4 Operaciones` |

El examen marca en rojo los nombres de variable. Eso significa que no basta con que el texto visible diga “Juegos”; también conviene que el **Variable Name** sea el correcto.

---

### 5.2. Configurar aceleradores de teclado

Puedes configurar shortcuts:

| Opción | Shortcut recomendado |
|---|---|
| `opAlzeimer` | `Alt + A` |
| `opPpt` | `Alt + P` |
| `opCalculo` | `Alt + 4` |

En NetBeans:

1. Selecciona el `JMenuItem`.
2. Ve a **Properties**.
3. Busca **accelerator**.
4. Coloca la combinación correspondiente.

---

### 5.3. Programar apertura de formularios internos

Haz doble clic sobre cada opción del menú y coloca el código.

#### Opción Alzheimer / Alzeimer

Si tu clase se llama `Alzeimer`:

```java
private void opAlzeimerActionPerformed(java.awt.event.ActionEvent evt) {
    Alzeimer al = new Alzeimer();
    escritorio.add(al);
    al.setVisible(true);
}
```

Si tu clase se llama `Alzheimer`:

```java
private void opAlzheimerActionPerformed(java.awt.event.ActionEvent evt) {
    Alzheimer al = new Alzheimer();
    escritorio.add(al);
    al.setVisible(true);
}
```

#### Opción Ppt

```java
private void opPptActionPerformed(java.awt.event.ActionEvent evt) {
    Ppt pp = new Ppt();
    escritorio.add(pp);
    pp.setVisible(true);
}
```

#### Opción 4 Operaciones

```java
private void opCalculoActionPerformed(java.awt.event.ActionEvent evt) {
    Operaciones op = new Operaciones();
    escritorio.add(op);
    op.setVisible(true);
}
```

> También puede funcionar `show()`, pero es más correcto usar `setVisible(true)`.

---

## 6. Formulario Alzheimer / Alzeimer

Este formulario vale 5 puntos. Debe ser un **JInternalFrame Form**.

El ejercicio dice que dos amigos juegan varias veces y cuentan `1`, `2`, `3` de forma alternada como ejercicio contra el Alzheimer.

Ejemplo:

```text
Jugador 1: Yo
Jugador 2: Tu
Num. juegos: 6
```

Resultado esperado:

| Juego | Yo | Tu |
|---:|---:|---:|
| 1 | 1 | 2 |
| 2 | 3 | 1 |
| 3 | 2 | 3 |
| 4 | 1 | 2 |
| 5 | 3 | 1 |
| 6 | 2 | 3 |

Mensaje:

```text
Felicitaciones a Yo y Tu
```

---

### 6.1. Componentes del formulario

Agrega estos componentes:

| Componente | Variable name | Texto / función |
|---|---|---|
| `JTextField` | `j1` | Nombre jugador 1 |
| `JTextField` | `j2` | Nombre jugador 2 |
| `JTextField` | `nJuegos` | Cantidad de juegos |
| `JButton` | `jugar` | `Jugar` |
| `JButton` | `limpiar` | `Limpiar` |
| `JTable` | `tabla` | Tabla de resultados |
| `JTextField` | `mensaje` | Mensaje final |

Recomendaciones visuales:

- El formulario debe ser `closable`, `iconifiable`, `maximizable` y `resizable`.
- El título puede ser `Alzeimer` o `Alzheimer`.
- El campo `mensaje` puede tener fondo amarillo claro y `editable = false`.
- La tabla puede ir dentro de un `JScrollPane` con borde titulado `Juegos`.

---

### 6.2. Importaciones necesarias

Arriba del archivo agrega:

```java
import javax.swing.table.DefaultTableModel;
import vista.SwingValidator;
```

---

### 6.3. Crear el método de validación

Dentro de la clase, fuera de los eventos, agrega:

```java
private boolean isValidData() {
    SwingValidator sv = new SwingValidator();

    return sv.isPresent(j1, "Jugador 1") &&
           sv.isPresent(j2, "Jugador 2") &&
           sv.isPresent(nJuegos, "Número de juegos") &&
           sv.isInteger(nJuegos, "Número de juegos");
}
```

Si quieres exigir que el número de juegos sea mayor que cero:

```java
private boolean isValidData() {
    SwingValidator sv = new SwingValidator();

    if (!sv.isPresent(j1, "Jugador 1")) return false;
    if (!sv.isPresent(j2, "Jugador 2")) return false;
    if (!sv.isPresent(nJuegos, "Número de juegos")) return false;
    if (!sv.isInteger(nJuegos, "Número de juegos")) return false;

    int n = Integer.parseInt(nJuegos.getText().trim());
    if (n <= 0) {
        javax.swing.JOptionPane.showMessageDialog(this,
                "Número de juegos debe ser mayor que 0.",
                "Dato no válido",
                javax.swing.JOptionPane.ERROR_MESSAGE);
        nJuegos.requestFocusInWindow();
        return false;
    }

    return true;
}
```

---

### 6.4. Programar botón `Jugar`

Haz doble clic sobre el botón `jugar` y coloca:

```java
private void jugarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) {
        return;
    }

    String jugador1 = j1.getText().trim();
    String jugador2 = j2.getText().trim();
    int numeroJuegos = Integer.parseInt(nJuegos.getText().trim());

    String[] columnas = {"Juego", jugador1, jugador2};
    DefaultTableModel modelo = new DefaultTableModel(columnas, 0);

    int contador = 1;

    for (int i = 1; i <= numeroJuegos; i++) {
        int valorJ1 = contador;
        contador++;
        if (contador > 3) contador = 1;

        int valorJ2 = contador;
        contador++;
        if (contador > 3) contador = 1;

        Object[] fila = {i, valorJ1, valorJ2};
        modelo.addRow(fila);
    }

    tabla.setModel(modelo);
    mensaje.setText("Felicitaciones a " + jugador1 + " y " + jugador2);
}
```

Este algoritmo genera la secuencia:

```text
1, 2, 3, 1, 2, 3, ...
```

pero la reparte entre los dos jugadores en cada fila.

---

### 6.5. Programar botón `Limpiar`

Haz doble clic sobre el botón `limpiar` y coloca:

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    j1.setText("");
    j2.setText("");
    nJuegos.setText("");
    mensaje.setText("");

    DefaultTableModel modelo = new DefaultTableModel(new String[]{"Juego", "", ""}, 0);
    tabla.setModel(modelo);

    j1.requestFocusInWindow();
}
```

---

### 6.6. Errores comunes en este formulario

| Error | Consecuencia | Corrección |
|---|---|---|
| No cambiar los encabezados de tabla | La tabla sale con columnas vacías | Usar `new DefaultTableModel(columnas, 0)` |
| No validar `nJuegos` | Puede fallar con texto | Usar `SwingValidator.isInteger` |
| No reiniciar tabla al limpiar | Quedan filas antiguas | Asignar un modelo nuevo vacío |
| Confundir `Alzeimer` con `Alzheimer` | Error de compilación | Usar exactamente el nombre real de la clase |

---

## 7. Formulario Ppt: Piedra, papel y tijera

Este formulario vale 5 puntos. Debe ser un **JInternalFrame Form**.

Dos amigos juegan piedra, papel o tijera. El programa elige aleatoriamente la jugada de cada jugador, muestra ambas jugadas y muestra el ganador.

---

### 7.1. Componentes del formulario

| Componente | Variable name | Función |
|---|---|---|
| `JTextField` | `j1` | Nombre jugador 1 |
| `JTextField` | `j2` | Nombre jugador 2 |
| `JButton` | `jugar` | Ejecutar juego |
| `JTextField` | `r1` | Resultado aleatorio jugador 1 |
| `JTextField` | `r2` | Resultado aleatorio jugador 2 |
| `JTextField` | `ganador` | Nombre del ganador o empate |
| `JButton` | `limpiar` | Limpiar campos |

Recomendaciones:

- `r1`, `r2` y `ganador` deben estar en `editable = false` o `focusable = false`.
- Puedes ponerles fondo amarillo claro para que se vean como resultados.

---

### 7.2. Importaciones necesarias

```java
import vista.SwingValidator;
```

No necesitas importar `Random` si usas `Math.random()`.

---

### 7.3. Atributo para las jugadas

Dentro de la clase `Ppt`, antes del constructor, declara:

```java
String[] juegos = {"Piedra", "Papel", "Tijera"};
```

Ejemplo:

```java
public class Ppt extends javax.swing.JInternalFrame {

    String[] juegos = {"Piedra", "Papel", "Tijera"};

    public Ppt() {
        initComponents();
    }
}
```

---

### 7.4. Programar botón `Jugar`

Haz doble clic en `jugar` y coloca:

```java
private void jugarActionPerformed(java.awt.event.ActionEvent evt) {
    SwingValidator sv = new SwingValidator();

    if (!sv.isPresent(j1, "Jugador 1") ||
        !sv.isPresent(j2, "Jugador 2")) {
        return;
    }

    int rj1 = (int) (Math.random() * 3); // 0, 1 o 2
    int rj2 = (int) (Math.random() * 3); // 0, 1 o 2

    r1.setText(juegos[rj1]);
    r2.setText(juegos[rj2]);

    if (rj1 == rj2) {
        ganador.setText("Empate");
    } else if ((rj1 + 1) % 3 == rj2) {
        ganador.setText(j2.getText().trim());
    } else {
        ganador.setText(j1.getText().trim());
    }
}
```

---

### 7.5. Entender la fórmula `(rj1 + 1) % 3 == rj2`

Codificamos las jugadas así:

| Número | Jugada |
|---:|---|
| 0 | Piedra |
| 1 | Papel |
| 2 | Tijera |

La regla queda circular:

```text
Piedra pierde contra Papel
Papel pierde contra Tijera
Tijera pierde contra Piedra
```

Con la fórmula:

```java
(rj1 + 1) % 3 == rj2
```

se verifica si la jugada del jugador 2 vence a la del jugador 1.

Ejemplos:

| `rj1` | `rj2` | Interpretación | ¿Gana j2? |
|---:|---:|---|---|
| 0 Piedra | 1 Papel | Papel vence piedra | Sí |
| 1 Papel | 2 Tijera | Tijera vence papel | Sí |
| 2 Tijera | 0 Piedra | Piedra vence tijera | Sí |

Si no hay empate y no gana j2, entonces gana j1.

---

### 7.6. Programar botón `Limpiar`

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    j1.setText("");
    j2.setText("");
    r1.setText("");
    r2.setText("");
    ganador.setText("");

    j1.requestFocusInWindow();
}
```

---

### 7.7. Errores comunes en Ppt

| Error | Consecuencia | Corrección |
|---|---|---|
| Usar `Math.random() * 2` | Nunca sale una de las jugadas | Usar `Math.random() * 3` |
| Comparar textos con `==` | Comparación incorrecta | Comparar índices enteros |
| No validar nombres | Puede jugar con campos vacíos | Usar `SwingValidator` |
| Escribir `Piedra`, `Papel`, `Tijera` varias veces | Código repetido | Usar arreglo `String[] juegos` |

---

## 8. Formulario Operaciones

Este formulario vale 5 puntos. Debe ser un **JInternalFrame Form**.

El usuario ingresa dos números enteros y selecciona una operación:

- Sumar
- Restar
- Multiplicar
- Dividir

El resultado se muestra en un campo de texto.

---

### 8.1. Componentes del formulario

| Componente | Variable name | Función |
|---|---|---|
| `JTextField` | `n1` | Primer número |
| `JTextField` | `n2` | Segundo número |
| `JRadioButton` | `sumar` | Sumar |
| `JRadioButton` | `restar` | Restar |
| `JRadioButton` | `multr` | Multiplicar |
| `JRadioButton` | `dividir` | Dividir |
| `JTextField` | `resultado` | Mostrar resultado |
| `JButton` | `limpiar` | Limpiar campos |
| `ButtonGroup` | por ejemplo `grupoOperaciones` | Agrupar radio buttons |

Muy importante: los `JRadioButton` deben estar dentro de un `ButtonGroup`. Si no haces esto, el usuario podría seleccionar varias operaciones a la vez.

---

### 8.2. Crear el `ButtonGroup`

En el diseñador de NetBeans:

1. Desde la paleta, agrega un **ButtonGroup**.
2. Cambia su variable name a `grupoOperaciones`.
3. Selecciona cada radio button.
4. En la propiedad **buttonGroup**, elige `grupoOperaciones`.

---

### 8.3. Importación necesaria

```java
import vista.SwingValidator;
```

---

### 8.4. Método de validación

Dentro de la clase `Operaciones`, agrega:

```java
private boolean isValidData() {
    SwingValidator sv = new SwingValidator();

    return sv.isPresent(n1, "N1") &&
           sv.isInteger(n1, "N1") &&
           sv.isPresent(n2, "N2") &&
           sv.isInteger(n2, "N2");
}
```

---

### 8.5. Opción 1: programar cada radio button por separado

Esta opción se parece mucho a lo que se suele hacer en clase.

#### Botón `sumar`

```java
private void sumarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());

    int suma = numero1 + numero2;
    resultado.setText(String.valueOf(suma));
}
```

#### Botón `restar`

```java
private void restarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());

    int resta = numero1 - numero2;
    resultado.setText(String.valueOf(resta));
}
```

#### Botón `multr`

```java
private void multrActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());

    int producto = numero1 * numero2;
    resultado.setText(String.valueOf(producto));
}
```

#### Botón `dividir`

```java
private void dividirActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());

    if (numero2 == 0) {
        resultado.setText("No se puede dividir entre 0");
        return;
    }

    double division = (double) numero1 / numero2;
    resultado.setText(String.valueOf(division));
}
```

---

### 8.6. Opción 2: usar un solo método para calcular

Esta opción es más ordenada. Cada radio button llama al mismo método.

Primero crea este método:

```java
private void calcular() {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());

    if (sumar.isSelected()) {
        resultado.setText(String.valueOf(numero1 + numero2));
    } else if (restar.isSelected()) {
        resultado.setText(String.valueOf(numero1 - numero2));
    } else if (multr.isSelected()) {
        resultado.setText(String.valueOf(numero1 * numero2));
    } else if (dividir.isSelected()) {
        if (numero2 == 0) {
            resultado.setText("No se puede dividir entre 0");
        } else {
            resultado.setText(String.valueOf((double) numero1 / numero2));
        }
    }
}
```

Luego, en cada radio button:

```java
private void sumarActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}

private void restarActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}

private void multrActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}

private void dividirActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}
```

Cualquiera de las dos opciones es válida. Para examen, la primera suele ser más directa; para código limpio, la segunda es mejor.

---

### 8.7. Programar botón `Limpiar`

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    n1.setText("");
    n2.setText("");
    resultado.setText("");

    grupoOperaciones.clearSelection();
    n1.requestFocusInWindow();
}
```

Si tu `ButtonGroup` tiene otro nombre, cambia `grupoOperaciones` por el nombre real.

Si no tienes `ButtonGroup`, al menos limpia manualmente:

```java
sumar.setSelected(false);
restar.setSelected(false);
multr.setSelected(false);
dividir.setSelected(false);
```

---

### 8.8. Errores comunes en Operaciones

| Error | Consecuencia | Corrección |
|---|---|---|
| Dividir enteros sin casting | `5 / 2` da `2` en vez de `2.5` | Usar `(double) numero1 / numero2` |
| No controlar división entre cero | Resultado incorrecto o excepción | Validar `numero2 == 0` |
| No usar `ButtonGroup` | Se pueden marcar varias operaciones | Crear `ButtonGroup` |
| No validar entradas | Falla si se escribe texto | Usar `SwingValidator` |

---

## 9. Validación de datos en todos los formularios

El examen exige validar los datos de entrada para mostrar mensajes como:

```text
Dato no válido
Jugador 1 es un campo requerido.
N1 debe ser entero.
```

Por eso debes usar `SwingValidator` en:

- `Alzeimer / Alzheimer`: validar `j1`, `j2`, `nJuegos`.
- `Ppt`: validar `j1`, `j2`.
- `Operaciones`: validar `n1`, `n2`.

Una validación típica es:

```java
SwingValidator sv = new SwingValidator();

if (!sv.isPresent(n1, "N1") || !sv.isInteger(n1, "N1")) {
    return;
}
```

Pero es mejor recordar que, si llamas primero a `isInteger` con un campo vacío, también te puede decir “debe ser entero”. Por eso conviene usar este orden:

```java
sv.isPresent(campo, "Campo") && sv.isInteger(campo, "Campo")
```

Primero comprueba que exista texto y luego que sea entero.

---

## 10. Navegación con Enter entre campos

Esto no siempre es obligatorio, pero ayuda a que el programa se vea más completo.

Ejemplo en `Alzeimer`:

```java
private void j1KeyTyped(java.awt.event.KeyEvent evt) {
    char c = evt.getKeyChar();
    if (c == '\n') {
        j2.requestFocusInWindow();
    }
}

private void j2KeyTyped(java.awt.event.KeyEvent evt) {
    char c = evt.getKeyChar();
    if (c == '\n') {
        nJuegos.requestFocusInWindow();
    }
}

private void nJuegosKeyTyped(java.awt.event.KeyEvent evt) {
    char c = evt.getKeyChar();
    if (c == '\n') {
        jugar.requestFocusInWindow();
    }
}
```

En NetBeans se agrega desde:

```text
Events > Key > keyTyped
```

---

## 11. Probar el proyecto

### 11.1. Ejecutar desde NetBeans

1. Clic derecho sobre el proyecto `sustiApp`.
2. Clic en **Clean and Build**.
3. Luego clic en **Run**.

Debe abrirse la ventana principal.

---

### 11.2. Pruebas para el menú

| Acción | Resultado esperado |
|---|---|
| Clic en `Juegos > Alzeimer` | Se abre formulario interno Alzheimer |
| Clic en `Juegos > Ppt` | Se abre formulario interno Ppt |
| Clic en `Cálculos > 4 Operaciones` | Se abre formulario interno Operaciones |
| Alt + A | Abre Alzheimer |
| Alt + P | Abre Ppt |
| Alt + 4 | Abre Operaciones |

---

### 11.3. Pruebas para Alzheimer

Caso correcto:

```text
Jugador 1: Yo
Jugador 2: Tu
Num. juegos: 6
```

Debe producir una tabla con 6 filas y mensaje:

```text
Felicitaciones a Yo y Tu
```

Casos de validación:

| Entrada | Resultado esperado |
|---|---|
| `Jugador 1` vacío | Mensaje de campo requerido |
| `Jugador 2` vacío | Mensaje de campo requerido |
| `nJuegos` vacío | Mensaje de campo requerido |
| `nJuegos = abc` | Mensaje “debe ser entero” |
| `nJuegos = 0` si validaste mayor que cero | Mensaje “debe ser mayor que 0” |

---

### 11.4. Pruebas para Ppt

Caso correcto:

```text
Jugador 1: Yo
Jugador 2: Tu
```

Al presionar `Jugar`, debe aparecer algo como:

```text
r1: Tijera
r2: Piedra
Ganador: Tu
```

Casos de validación:

| Entrada | Resultado esperado |
|---|---|
| `j1` vacío | Mensaje de campo requerido |
| `j2` vacío | Mensaje de campo requerido |
| Presionar limpiar | Se vacían `j1`, `j2`, `r1`, `r2`, `ganador` |

---

### 11.5. Pruebas para Operaciones

| N1 | N2 | Operación | Resultado esperado |
|---:|---:|---|---|
| 5 | 3 | Sumar | 8 |
| 5 | 3 | Restar | 2 |
| 5 | 3 | Multiplicar | 15 |
| 5 | 2 | Dividir | 2.5 |
| 5 | 0 | Dividir | No se puede dividir entre 0 |

Casos de validación:

| Entrada | Resultado esperado |
|---|---|
| `n1` vacío | Mensaje de campo requerido |
| `n2` vacío | Mensaje de campo requerido |
| `n1 = hola` | Mensaje “N1 debe ser entero” |
| `n2 = x` | Mensaje “N2 debe ser entero” |

---

## 12. Limpieza del proyecto antes de exportar

Antes de exportar:

1. Ejecuta **Clean and Build**.
2. Verifica que no haya errores en Output.
3. Ejecuta el programa una vez.
4. Prueba los tres formularios.
5. Cierra NetBeans si quieres evitar archivos bloqueados.

No borres los archivos `.form`, porque NetBeans los usa para el diseñador visual.

---

## 13. Exportar el proyecto como ZIP desde NetBeans

El examen pide exportar desde NetBeans:

```text
File > Export Project > To ZIP...
```

Pasos:

1. En NetBeans, ve a **File**.
2. Selecciona **Export Project**.
3. Selecciona **To ZIP...**.
4. En **Root Project**, selecciona `sustiApp`.
5. En **Build ZIP**, clic en **Browse**.
6. Navega a tu carpeta de trabajo `ApellidoNombreCodigo`.
7. Nombre del archivo:

```text
ApellidoNombre.zip
```

Ejemplo:

```text
DelgadoRomero.zip
```

8. Clic en **Export**.
9. Sube el `.zip` al enlace indicado.

---

## 14. Checklist final de entrega

Antes de subir, revisa esto:

- [ ] El proyecto abre en NetBeans.
- [ ] El proyecto compila con Maven.
- [ ] `pom.xml` tiene el `exec.mainClass` correcto.
- [ ] `SwingValidator.java` está en el paquete `vista`.
- [ ] `SustiApp.java` o `Menu.java` es `JFrame Form`.
- [ ] El formulario principal tiene `JDesktopPane escritorio`.
- [ ] El menú tiene `Juegos` y `Cálculos`.
- [ ] `opAlzeimer / opAlzheimer` abre el formulario Alzheimer.
- [ ] `opPpt` abre el formulario Ppt.
- [ ] `opCalculo` abre el formulario Operaciones.
- [ ] Alzheimer valida entradas y genera tabla.
- [ ] Ppt valida nombres, genera jugadas aleatorias y ganador.
- [ ] Operaciones valida enteros y controla división entre cero.
- [ ] Los botones `Limpiar` vuelven a la vista inicial.
- [ ] El `.zip` final tiene el nombre pedido.

---

## 15. Código resumen de los eventos principales

Esta sección es útil si ya tienes las interfaces diseñadas y solo necesitas completar los eventos.

### 15.1. Menú principal

```java
private void opAlzeimerActionPerformed(java.awt.event.ActionEvent evt) {
    Alzeimer al = new Alzeimer();
    escritorio.add(al);
    al.setVisible(true);
}

private void opPptActionPerformed(java.awt.event.ActionEvent evt) {
    Ppt pp = new Ppt();
    escritorio.add(pp);
    pp.setVisible(true);
}

private void opCalculoActionPerformed(java.awt.event.ActionEvent evt) {
    Operaciones op = new Operaciones();
    escritorio.add(op);
    op.setVisible(true);
}
```

Si tu clase se llama `Alzheimer`, cambia `Alzeimer` por `Alzheimer`.

---

### 15.2. Alzheimer completo: lógica principal

```java
private boolean isValidData() {
    SwingValidator sv = new SwingValidator();

    return sv.isPresent(j1, "Jugador 1") &&
           sv.isPresent(j2, "Jugador 2") &&
           sv.isPresent(nJuegos, "Número de juegos") &&
           sv.isInteger(nJuegos, "Número de juegos");
}

private void jugarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    String jugador1 = j1.getText().trim();
    String jugador2 = j2.getText().trim();
    int numeroJuegos = Integer.parseInt(nJuegos.getText().trim());

    String[] columnas = {"Juego", jugador1, jugador2};
    DefaultTableModel modelo = new DefaultTableModel(columnas, 0);

    int contador = 1;

    for (int i = 1; i <= numeroJuegos; i++) {
        int valorJ1 = contador;
        contador++;
        if (contador > 3) contador = 1;

        int valorJ2 = contador;
        contador++;
        if (contador > 3) contador = 1;

        modelo.addRow(new Object[]{i, valorJ1, valorJ2});
    }

    tabla.setModel(modelo);
    mensaje.setText("Felicitaciones a " + jugador1 + " y " + jugador2);
}

private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    j1.setText("");
    j2.setText("");
    nJuegos.setText("");
    mensaje.setText("");
    tabla.setModel(new DefaultTableModel(new String[]{"Juego", "", ""}, 0));
    j1.requestFocusInWindow();
}
```

---

### 15.3. Ppt completo: lógica principal

```java
String[] juegos = {"Piedra", "Papel", "Tijera"};

private void jugarActionPerformed(java.awt.event.ActionEvent evt) {
    SwingValidator sv = new SwingValidator();

    if (!sv.isPresent(j1, "Jugador 1") ||
        !sv.isPresent(j2, "Jugador 2")) {
        return;
    }

    int rj1 = (int) (Math.random() * 3);
    int rj2 = (int) (Math.random() * 3);

    r1.setText(juegos[rj1]);
    r2.setText(juegos[rj2]);

    if (rj1 == rj2) {
        ganador.setText("Empate");
    } else if ((rj1 + 1) % 3 == rj2) {
        ganador.setText(j2.getText().trim());
    } else {
        ganador.setText(j1.getText().trim());
    }
}

private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    j1.setText("");
    j2.setText("");
    r1.setText("");
    r2.setText("");
    ganador.setText("");
    j1.requestFocusInWindow();
}
```

---

### 15.4. Operaciones completo: lógica principal

```java
private boolean isValidData() {
    SwingValidator sv = new SwingValidator();

    return sv.isPresent(n1, "N1") &&
           sv.isInteger(n1, "N1") &&
           sv.isPresent(n2, "N2") &&
           sv.isInteger(n2, "N2");
}

private void sumarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());
    resultado.setText(String.valueOf(numero1 + numero2));
}

private void restarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());
    resultado.setText(String.valueOf(numero1 - numero2));
}

private void multrActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());
    resultado.setText(String.valueOf(numero1 * numero2));
}

private void dividirActionPerformed(java.awt.event.ActionEvent evt) {
    if (!isValidData()) return;

    int numero1 = Integer.parseInt(n1.getText().trim());
    int numero2 = Integer.parseInt(n2.getText().trim());

    if (numero2 == 0) {
        resultado.setText("No se puede dividir entre 0");
        return;
    }

    resultado.setText(String.valueOf((double) numero1 / numero2));
}

private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    n1.setText("");
    n2.setText("");
    resultado.setText("");

    sumar.setSelected(false);
    restar.setSelected(false);
    multr.setSelected(false);
    dividir.setSelected(false);

    n1.requestFocusInWindow();
}
```

---

## 16. Qué hacer si NetBeans no deja editar partes del código

En los formularios `.form`, NetBeans genera automáticamente el método:

```java
initComponents()
```

Esa parte aparece bloqueada y dice algo como:

```java
// Variables declaration - do not modify
```

No edites esa zona manualmente. Para cambiar componentes:

- Usa el diseñador visual.
- Cambia propiedades desde la ventana **Properties**.
- Cambia nombres desde **Code > Variable Name**.
- Agrega eventos desde **Events** o doble clic sobre botones.

El código que sí debes escribir va en:

- Eventos `ActionPerformed`.
- Métodos propios como `isValidData()` o `calcular()`.
- Atributos declarados fuera de `initComponents()`.

---

## 17. Posibles errores de compilación y solución

### Error: `cannot find symbol class SwingValidator`

Causa probable: el archivo no está en el paquete correcto.

Solución:

1. Verifica que esté aquí:

```text
src/main/java/vista/SwingValidator.java
```

2. Verifica que arriba diga:

```java
package vista;
```

3. En tus formularios importa:

```java
import vista.SwingValidator;
```

---

### Error: `cannot find symbol class Alzeimer`

Causa probable: tu clase se llama `Alzheimer`, no `Alzeimer`, o al revés.

Solución:

- Abre el archivo del formulario.
- Mira el nombre real:

```java
public class Alzeimer extends javax.swing.JInternalFrame
```

O:

```java
public class Alzheimer extends javax.swing.JInternalFrame
```

Usa exactamente ese nombre en el menú.

---

### Error: `release version 22 not supported`

Causa probable: tu Maven está configurado para JDK 22, pero NetBeans está usando JDK 21 o 17.

Solución: cambia en `pom.xml`:

```xml
<maven.compiler.release>22</maven.compiler.release>
```

por tu versión real:

```xml
<maven.compiler.release>21</maven.compiler.release>
```

O:

```xml
<maven.compiler.release>17</maven.compiler.release>
```

---

### Error: la ventana abre pero el menú no muestra nada

Causa probable: olvidaste agregar el formulario interno al `JDesktopPane`.

Debe ser:

```java
Ppt pp = new Ppt();
escritorio.add(pp);
pp.setVisible(true);
```

No basta con:

```java
Ppt pp = new Ppt();
pp.setVisible(true);
```

porque el `JInternalFrame` necesita estar dentro del `JDesktopPane`.

---

### Error: la tabla no actualiza columnas con los nombres de jugadores

Causa probable: estás agregando filas a un modelo viejo.

Solución: crea un modelo nuevo cada vez que presionas `Jugar`:

```java
String[] columnas = {"Juego", jugador1, jugador2};
DefaultTableModel modelo = new DefaultTableModel(columnas, 0);
tabla.setModel(modelo);
```

---

## 18. Recomendación final para sacar el mayor puntaje

Para maximizar el puntaje, prioriza en este orden:

1. Que el proyecto compile.
2. Que el menú abra los tres formularios.
3. Que cada formulario tenga los nombres de variable pedidos.
4. Que los botones `Jugar`, operaciones y `Limpiar` funcionen.
5. Que uses `SwingValidator` para validar campos vacíos y enteros.
6. Que la división entre cero muestre mensaje y no se caiga.
7. Que el `.zip` final tenga el nombre pedido.

Si te queda poco tiempo en examen, no pierdas tiempo decorando demasiado la interfaz. Primero haz que funcione.

