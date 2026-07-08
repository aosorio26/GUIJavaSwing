# Guía corregida para resolver el examen `sustiApp` en NetBeans con Maven

Esta guía explica cómo resolver el examen sustitutorio de POO implementando una aplicación Swing en NetBeans con Maven.

El examen pide programar el proyecto **`sustiApp`** con estas clases:

```text
sustiApp
└── Source Packages
    ├── pjFormularios
    │   ├── Alzeimer.java        JInternalFrame Form
    │   ├── Menu.java            JFrame Form
    │   ├── Operaciones.java     JInternalFrame Form
    │   └── Ppt.java             JInternalFrame Form
    │
    ├── pjmenu
    │
    └── vista
        └── SwingValidator.java  archivo dado por el examen
```

La idea general es:

- `Menu.java` será la ventana principal.
- `Menu.java` tendrá un `JDesktopPane`.
- Desde el menú superior se abrirán los formularios internos: `Alzeimer`, `Ppt` y `Operaciones`.
- `SwingValidator.java` se usará para validar campos vacíos, números enteros y números reales.

---

## 1. Crear el proyecto Maven en NetBeans

En NetBeans:

```text
File > New Project
```

Selecciona:

```text
Java with Maven > Java Application
```

Datos sugeridos:

```text
Project Name: sustiApp
Group Id: com.uni
Version: 1.0-SNAPSHOT
```

Luego crea los paquetes manualmente dentro de `Source Packages`.

---

## 2. Crear los paquetes correctos

Dentro de `Source Packages`, crea:

```text
pjFormularios
pjmenu
vista
```

La estructura final debe verse así:

```text
src/main/java
├── pjFormularios
│   ├── Alzeimer.java
│   ├── Menu.java
│   ├── Operaciones.java
│   └── Ppt.java
├── pjmenu
└── vista
    └── SwingValidator.java
```

No coloques los formularios dentro de `vista`. El paquete `vista` es solo para `SwingValidator.java`.

---

## 3. Colocar `SwingValidator.java`

El archivo dado por el examen debe ir en:

```text
src/main/java/vista/SwingValidator.java
```

Debe comenzar así:

```java
package vista;
```

El archivo contiene métodos como:

```java
isPresent(...)
isInteger(...)
isgrather0(...)
isDouble(...)
isRange4(...)
```

Importante: el método `isgrather0` está escrito así en el archivo original. Aunque el nombre tiene un error ortográfico, úsalo con ese mismo nombre para evitar errores.

---

## 4. Crear los formularios correctos

Dentro del paquete `pjFormularios`, crea:

| Clase | Tipo correcto en NetBeans |
|---|---|
| `Menu.java` | `JFrame Form` |
| `Alzeimer.java` | `JInternalFrame Form` |
| `Operaciones.java` | `JInternalFrame Form` |
| `Ppt.java` | `JInternalFrame Form` |

En NetBeans:

```text
Click derecho en pjFormularios > New > JFrame Form
```

para `Menu`.

Y para los demás:

```text
Click derecho en pjFormularios > New > JInternalFrame Form
```

para `Alzeimer`, `Operaciones` y `Ppt`.

---

# 5. Implementar `Menu.java`

`Menu.java` es el formulario principal. Debe extender de `JFrame`.

Debe iniciar así:

```java
package pjFormularios;

public class Menu extends javax.swing.JFrame {
    ...
}
```

## 5.1. Componentes del formulario `Menu`

Agrega un `JDesktopPane` y ponle este variable name:

```text
escritorio
```

Agrega un `JMenuBar` con dos menús:

| Texto visible | Variable name |
|---|---|
| Juegos | `juegos` |
| Cálculos | `calculos` |

Dentro del menú `Juegos`, agrega dos `JMenuItem`:

| Texto visible | Variable name |
|---|---|
| Alzeimer | `opAlzeimer` |
| Ppt | `opPpt` |

Dentro del menú `Cálculos`, agrega un `JMenuItem`:

| Texto visible | Variable name |
|---|---|
| 4Operaciones | `opCalculo` |

## 5.2. Constructor de `Menu`

Después de `initComponents();`, coloca:

```java
public Menu() {
    initComponents();
    setLocationRelativeTo(null);
    setTitle("Juegos y Cálculos");
}
```

## 5.3. Abrir `Alzeimer`

Doble clic en el menú `Alzeimer` y coloca:

```java
private void opAlzeimerActionPerformed(java.awt.event.ActionEvent evt) {
    Alzeimer f = new Alzeimer();
    escritorio.add(f);
    f.setVisible(true);
}
```

## 5.4. Abrir `Ppt`

Doble clic en el menú `Ppt` y coloca:

```java
private void opPptActionPerformed(java.awt.event.ActionEvent evt) {
    Ppt f = new Ppt();
    escritorio.add(f);
    f.setVisible(true);
}
```

## 5.5. Abrir `Operaciones`

Doble clic en el menú `4Operaciones` y coloca:

```java
private void opCalculoActionPerformed(java.awt.event.ActionEvent evt) {
    Operaciones f = new Operaciones();
    escritorio.add(f);
    f.setVisible(true);
}
```

## 5.6. Método `main`

Debe abrir `Menu`:

```java
public static void main(String args[]) {
    java.awt.EventQueue.invokeLater(new Runnable() {
        public void run() {
            new Menu().setVisible(true);
        }
    });
}
```

---

# 6. Implementar `Alzeimer.java`

`Alzeimer.java` debe ser un `JInternalFrame Form`.

Debe iniciar así:

```java
package pjFormularios;

public class Alzeimer extends javax.swing.JInternalFrame {
    ...
}
```

## 6.1. Componentes de `Alzeimer`

Agrega estos componentes:

| Componente | Texto visible | Variable name |
|---|---|---|
| `JLabel` | Jugador 1: | opcional |
| `JTextField` | vacío | `j1` |
| `JLabel` | Jugador 2: | opcional |
| `JTextField` | vacío | `j2` |
| `JLabel` | Num. juegos: | opcional |
| `JTextField` | vacío | `nJuegos` |
| `JButton` | Jugar | `jugar` |
| `JButton` | Limpiar | `limpiar` |
| `JTable` | tabla | `tabla` |
| `JTextField` | mensaje | `mensaje` |

La tabla debe estar dentro de un `JScrollPane`.

## 6.2. Importaciones

Arriba de la clase agrega:

```java
import javax.swing.table.DefaultTableModel;
import vista.SwingValidator;
```

## 6.3. Atributo validador

Dentro de la clase, antes del constructor:

```java
SwingValidator sv = new SwingValidator();
```

## 6.4. Constructor

```java
public Alzeimer() {
    initComponents();
    setTitle("Alzeimer");
    mensaje.setEditable(false);
}
```

## 6.5. Lógica esperada

El formulario pide dos jugadores y un número de juegos. Luego llena una tabla donde los jugadores cuentan de forma alternada del 1 al 3.

Ejemplo:

```text
Jugador 1: Yo
Jugador 2: Tu
Num. juegos: 6
```

Resultado:

```text
Juego | Yo | Tu
1     | 1  | 2
2     | 3  | 1
3     | 2  | 3
4     | 1  | 2
5     | 3  | 1
6     | 2  | 3
```

## 6.6. Botón `Jugar`

Doble clic en el botón `Jugar` y coloca:

```java
private void jugarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!sv.isPresent(j1, "Jugador 1")) return;
    if (!sv.isPresent(j2, "Jugador 2")) return;
    if (!sv.isPresent(nJuegos, "Número de juegos")) return;
    if (!sv.isInteger(nJuegos, "Número de juegos")) return;
    if (!sv.isgrather0(nJuegos, "Número de juegos")) return;

    int n = Integer.parseInt(nJuegos.getText());

    DefaultTableModel modelo = new DefaultTableModel();
    modelo.addColumn("Juego");
    modelo.addColumn(j1.getText());
    modelo.addColumn(j2.getText());

    int contador = 1;

    for (int i = 1; i <= n; i++) {
        int valorJ1 = contador;
        contador++;
        if (contador > 3) contador = 1;

        int valorJ2 = contador;
        contador++;
        if (contador > 3) contador = 1;

        modelo.addRow(new Object[]{i, valorJ1, valorJ2});
    }

    tabla.setModel(modelo);
    mensaje.setText("Felicitaciones a " + j1.getText() + " y " + j2.getText());
}
```

## 6.7. Botón `Limpiar`

Doble clic en el botón `Limpiar` y coloca:

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    j1.setText("");
    j2.setText("");
    nJuegos.setText("");
    mensaje.setText("");

    DefaultTableModel modelo = new DefaultTableModel();
    modelo.addColumn("Juego");
    modelo.addColumn("Jugador 1");
    modelo.addColumn("Jugador 2");
    tabla.setModel(modelo);

    j1.requestFocus();
}
```

---

# 7. Implementar `Ppt.java`

`Ppt.java` es el formulario de Piedra, Papel y Tijera.

Debe ser `JInternalFrame Form`.

```java
package pjFormularios;

public class Ppt extends javax.swing.JInternalFrame {
    ...
}
```

## 7.1. Componentes de `Ppt`

Agrega:

| Componente | Texto visible | Variable name |
|---|---|---|
| `JLabel` | Jugadores: | opcional |
| `JTextField` | jugador 1 | `j1` |
| `JTextField` | jugador 2 | `j2` |
| `JButton` | Jugar | `jugar` |
| `JTextField` | resultado jugador 1 | `r1` |
| `JTextField` | resultado jugador 2 | `r2` |
| `JLabel` | Ganador: | opcional |
| `JTextField` | ganador | `ganador` |
| `JButton` | Limpiar | `limpiar` |

## 7.2. Importaciones

```java
import java.util.Random;
import vista.SwingValidator;
```

## 7.3. Atributos

```java
SwingValidator sv = new SwingValidator();
String[] juegos = {"Piedra", "Papel", "Tijera"};
Random azar = new Random();
```

## 7.4. Constructor

```java
public Ppt() {
    initComponents();
    setTitle("Piedra, papel y tijera");
    r1.setEditable(false);
    r2.setEditable(false);
    ganador.setEditable(false);
}
```

## 7.5. Regla del juego

Usamos este arreglo:

```java
String[] juegos = {"Piedra", "Papel", "Tijera"};
```

Índices:

```text
0 = Piedra
1 = Papel
2 = Tijera
```

Regla:

```text
Piedra pierde contra Papel
Papel pierde contra Tijera
Tijera pierde contra Piedra
```

La condición compacta es:

```java
(rj1 + 1) % 3 == rj2
```

Si eso se cumple, gana el jugador 2.

## 7.6. Botón `Jugar`

```java
private void jugarActionPerformed(java.awt.event.ActionEvent evt) {
    if (!sv.isPresent(j1, "Jugador 1")) return;
    if (!sv.isPresent(j2, "Jugador 2")) return;

    int rj1 = azar.nextInt(3);
    int rj2 = azar.nextInt(3);

    r1.setText(juegos[rj1]);
    r2.setText(juegos[rj2]);

    if (rj1 == rj2) {
        ganador.setText("Empate");
    } else if ((rj1 + 1) % 3 == rj2) {
        ganador.setText(j2.getText());
    } else {
        ganador.setText(j1.getText());
    }
}
```

## 7.7. Botón `Limpiar`

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    j1.setText("");
    j2.setText("");
    r1.setText("");
    r2.setText("");
    ganador.setText("");
    j1.requestFocus();
}
```

---

# 8. Implementar `Operaciones.java`

`Operaciones.java` debe ser `JInternalFrame Form`.

```java
package pjFormularios;

public class Operaciones extends javax.swing.JInternalFrame {
    ...
}
```

## 8.1. Componentes de `Operaciones`

Agrega:

| Componente | Texto visible | Variable name |
|---|---|---|
| `JLabel` | N1 | opcional |
| `JTextField` | vacío | `n1` |
| `JLabel` | N2 | opcional |
| `JTextField` | vacío | `n2` |
| `JPanel` | 4 Operaciones | opcional |
| `JRadioButton` | Sumar | `sumar` |
| `JRadioButton` | Restar | `restar` |
| `JRadioButton` | Multiplicar | `multr` |
| `JRadioButton` | Dividir | `dividir` |
| `JLabel` | Resultado: | opcional |
| `JTextField` | resultado | `resultado` |
| `JButton` | Limpiar | `limpiar` |

El nombre `multr` se usa porque así aparece en el examen.

## 8.2. Crear un `ButtonGroup`

Agrega un `ButtonGroup` y ponle:

```text
grupoOperaciones
```

Asigna ese grupo a los radio buttons:

```text
sumar
restar
multr
dividir
```

Esto evita que se seleccionen varias operaciones al mismo tiempo.

## 8.3. Importación

```java
import vista.SwingValidator;
```

## 8.4. Atributo validador

```java
SwingValidator sv = new SwingValidator();
```

## 8.5. Constructor

```java
public Operaciones() {
    initComponents();
    setTitle("4 Operaciones");
    resultado.setEditable(false);
}
```

## 8.6. Método `calcular`

Dentro de la clase `Operaciones`, agrega este método:

```java
private void calcular() {
    if (!sv.isPresent(n1, "N1")) return;
    if (!sv.isPresent(n2, "N2")) return;
    if (!sv.isDouble(n1, "N1")) return;
    if (!sv.isDouble(n2, "N2")) return;

    double a = Double.parseDouble(n1.getText());
    double b = Double.parseDouble(n2.getText());
    double r;

    if (sumar.isSelected()) {
        r = a + b;
        resultado.setText(String.valueOf(r));
    } else if (restar.isSelected()) {
        r = a - b;
        resultado.setText(String.valueOf(r));
    } else if (multr.isSelected()) {
        r = a * b;
        resultado.setText(String.valueOf(r));
    } else if (dividir.isSelected()) {
        if (b == 0) {
            resultado.setText("No se puede dividir entre 0");
        } else {
            r = a / b;
            resultado.setText(String.valueOf(r));
        }
    }
}
```

## 8.7. Eventos de los radio buttons

En cada radio button llama a `calcular();`.

```java
private void sumarActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}
```

```java
private void restarActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}
```

```java
private void multrActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}
```

```java
private void dividirActionPerformed(java.awt.event.ActionEvent evt) {
    calcular();
}
```

## 8.8. Botón `Limpiar`

```java
private void limpiarActionPerformed(java.awt.event.ActionEvent evt) {
    n1.setText("");
    n2.setText("");
    resultado.setText("");
    grupoOperaciones.clearSelection();
    n1.requestFocus();
}
```

---

# 9. Configurar Maven para ejecutar `Menu`

Abre `pom.xml` y asegúrate de tener definida la clase principal.

Ejemplo simple:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.uni</groupId>
    <artifactId>sustiApp</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <exec.mainClass>pjFormularios.Menu</exec.mainClass>
    </properties>
</project>
```

Swing no necesita dependencias externas porque forma parte de Java.

---

# 10. Ejecutar el proyecto

En NetBeans:

```text
Click derecho al proyecto > Run
```

Si NetBeans no detecta la clase principal:

```text
Click derecho al proyecto > Properties > Run > Main Class
```

Selecciona:

```text
pjFormularios.Menu
```

---

# 11. Pruebas rápidas

## 11.1. Prueba de `Alzeimer`

Entrada:

```text
Jugador 1: Yo
Jugador 2: Tu
Num. juegos: 6
```

Salida esperada:

```text
Juego | Yo | Tu
1     | 1  | 2
2     | 3  | 1
3     | 2  | 3
4     | 1  | 2
5     | 3  | 1
6     | 2  | 3
```

Mensaje:

```text
Felicitaciones a Yo y Tu
```

## 11.2. Prueba de `Ppt`

Entrada:

```text
Jugador 1: Yo
Jugador 2: Tu
```

Al presionar `Jugar`, deben aparecer jugadas aleatorias:

```text
Piedra
Papel
Tijera
```

El ganador debe ser `Yo`, `Tu` o `Empate`.

## 11.3. Prueba de `Operaciones`

Entrada:

```text
N1: 5
N2: 0
Operación: Dividir
```

Salida esperada:

```text
No se puede dividir entre 0
```

Otra prueba:

```text
N1: 5
N2: 3
Operación: Sumar
```

Salida esperada:

```text
8.0
```

---

# 12. Exportar el proyecto a ZIP

El examen pide exportar el proyecto desde NetBeans:

```text
File > Export Project > To ZIP
```

Pasos:

1. Selecciona el proyecto.
2. Presiona `Browse`.
3. Elige tu carpeta de trabajo.
4. Escribe el nombre:

```text
ApellidoNombre.zip
```

5. Presiona `Export`.

---

# 13. Errores comunes

## `SwingValidator` no se reconoce

Agrega esta línea en el formulario:

```java
import vista.SwingValidator;
```

Y revisa que el archivo esté en:

```text
src/main/java/vista/SwingValidator.java
```

## `escritorio` no existe

Tu `JDesktopPane` tiene otro nombre. Cámbialo a:

```text
escritorio
```

## `grupoOperaciones` no existe

Debes agregar un `ButtonGroup` y llamarlo:

```text
grupoOperaciones
```

## `multr` no existe

Tu radio button de multiplicar debe tener variable name:

```text
multr
```

## Error con `isgrather0`

Primero valida que sea entero y luego mayor que cero:

```java
if (!sv.isInteger(nJuegos, "Número de juegos")) return;
if (!sv.isgrather0(nJuegos, "Número de juegos")) return;
```

---

# 14. Checklist final

Antes de entregar, revisa:

- [ ] El proyecto se llama `sustiApp`.
- [ ] `Menu.java` está en `pjFormularios`.
- [ ] `Menu.java` es `JFrame Form`.
- [ ] `Alzeimer.java` está en `pjFormularios`.
- [ ] `Alzeimer.java` es `JInternalFrame Form`.
- [ ] `Operaciones.java` está en `pjFormularios`.
- [ ] `Operaciones.java` es `JInternalFrame Form`.
- [ ] `Ppt.java` está en `pjFormularios`.
- [ ] `Ppt.java` es `JInternalFrame Form`.
- [ ] `SwingValidator.java` está en `vista`.
- [ ] El menú tiene `Juegos` y `Cálculos`.
- [ ] `Juegos` abre `Alzeimer` y `Ppt`.
- [ ] `Cálculos` abre `Operaciones`.
- [ ] Los campos obligatorios se validan.
- [ ] `Alzeimer` llena la tabla correctamente.
- [ ] `Ppt` genera jugadas aleatorias.
- [ ] `Operaciones` calcula suma, resta, multiplicación y división.
- [ ] División entre cero muestra `No se puede dividir entre 0`.
- [ ] El proyecto corre desde `pjFormularios.Menu`.
- [ ] El ZIP final se exporta desde NetBeans.
