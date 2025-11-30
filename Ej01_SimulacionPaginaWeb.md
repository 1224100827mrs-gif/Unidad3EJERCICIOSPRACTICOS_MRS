# EJERCICIO PRACTICO 1 SWING 

### NodoDOM
```java
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
/*
 * Autor: Marisol Rincón Solís
 * Correo: 1224100827.mrsq@gmail.com
 * Descripción:
 * Esta clase representa un nodo dentro del árbol DOM construido manualmente.
 * Este nodo sirve como unidad básica para representar la estructura de un
 * documento HTML dentro del simulador. El árbol completo se forma enlazando
 * nodos DOM entre sí.
 */

package PRACTICASWING.domModelo;

import java.util.ArrayList;
import java.util.List;

public class NodoDOM {

    /**
     * Etiqueta del nodo (nombre de la etiqueta HTML).
     * Ejemplos: "html", "body", "div", "p".
     */
    private String etiqueta;

    /**
     * Contenido textual dentro del nodo.
     * Es opcional y puede estar vacío si el nodo solo sirve como contenedor.
     */
    private String contenido;

    /**
     * Referencia al nodo padre.
     * Es null únicamente cuando el nodo es la raíz del árbol.
     */
    private NodoDOM padre;

    /**
     * Lista de nodos hijos. Representa los elementos anidados dentro del nodo.
     */
    private List<NodoDOM> hijos;

    /**
     * Constructor principal del nodo DOM.
     *
     * @param etiqueta  Nombre de la etiqueta HTML asociada.
     * @param contenido Texto opcional dentro del nodo.
     */
    public NodoDOM(String etiqueta, String contenido) {
        this.etiqueta = etiqueta;
        this.contenido = contenido;
        this.hijos = new ArrayList<>();
    }

    /**
     * Agrega un nodo hijo a este nodo.
     *
     * @param hijo Nodo hijo que será agregado a la lista.
     */
    public void agregarHijo(NodoDOM hijo) {
        hijo.setPadre(this);
        hijos.add(hijo);
    }

    /**
     * Elimina un nodo hijo.
     *
     * @param hijo Nodo hijo que se desea eliminar.
     */
    public void eliminarHijo(NodoDOM hijo) {
        hijos.remove(hijo);
        hijo.setPadre(null);
    }

    /**
     * Obtiene la etiqueta HTML del nodo.
     *
     * @return Etiqueta HTML.
     */
    public String getEtiqueta() {
        return etiqueta;
    }

    /**
     * Obtiene el contenido textual del nodo.
     *
     * @return Contenido del nodo.
     */
    public String getContenido() {
        return contenido;
    }

    /**
     * Devuelve el nodo padre.
     *
     * @return Nodo padre, o null si es la raíz.
     */
    public NodoDOM getPadre() {
        return padre;
    }

    /**
     * Asigna un nuevo padre al nodo.
     *
     * @param padre Nodo padre.
     */
    public void setPadre(NodoDOM padre) {
        this.padre = padre;
    }

    /**
     * Devuelve la lista de nodos hijos.
     *
     * @return Lista de hijos.
     */
    public List<NodoDOM> getHijos() {
        return hijos;
    }

    /**
     * Representación del nodo como texto.
     * Generalmente útil para depuración.
     *
     * @return La etiqueta HTML del nodo.
     */
    @Override
    public String toString() {
        return etiqueta;
    }
}

```
### ArbolDOM
```java
/*
 * ArbolDOM.java
 *
 * Autor: Marisol Rincón Solís
 * Correo: 1224100827.mrsq@gmail.com
 *
 * Descripción:
 * Esta clase administra la estructura del árbol DOM completo.
 * Contiene la raíz del árbol ("html") y métodos para agregar, eliminar
 * y generar el código HTML final a partir de la estructura del árbol.
 */

package PRACTICASWING.domModelo;

public class ArbolDOM {

    /** Nodo raíz del árbol DOM (siempre es la etiqueta <html>). */
    private NodoDOM raiz;

    /**
     * Constructor del árbol.
     * Inicializa la raíz con un nodo HTML básico.
     */
    public ArbolDOM() {
        raiz = new NodoDOM("html", "");
    }

    /**
     * Devuelve la raíz del árbol.
     *
     * @return Nodo raíz del DOM.
     */
    public NodoDOM getRaiz() {
        return raiz;
    }

    /**
     * Agrega un nodo hijo debajo de un nodo padre.
     *
     * @param padre Nodo donde se insertará el nuevo hijo.
     * @param hijo Nuevo nodo DOM a insertar.
     */
    public void agregarNodo(NodoDOM padre, NodoDOM hijo) {
        padre.agregarHijo(hijo);
    }

    /**
     * Elimina un nodo del árbol (siempre y cuando no sea la raíz).
     *
     * @param nodo Nodo a eliminar.
     */
    public void eliminarNodo(NodoDOM nodo) {
        if (nodo == raiz) return;

        NodoDOM padre = nodo.getPadre();
        if (padre != null) {
            padre.eliminarHijo(nodo);
        }
    }

    /**
     * Genera el código HTML completo recorriendo el árbol desde la raíz.
     *
     * @return Código HTML generado.
     */
    public String generarHTML() {
        return generarHTMLRecursivo(raiz, 0);
    }

    /**
     * Método recursivo que convierte el árbol en texto HTML con sangría.
     *
     * @param nodo Nodo actual.
     * @param nivel Nivel de sangría.
     * @return Sección HTML generada para ese nodo.
     */
    private String generarHTMLRecursivo(NodoDOM nodo, int nivel) {

        String sangria = "  ".repeat(nivel);
        StringBuilder sb = new StringBuilder();

        sb.append(sangria).append("<").append(nodo.getEtiqueta()).append(">\n");

        if (!nodo.getContenido().isEmpty()) {
            sb.append(sangria).append("  ").append(nodo.getContenido()).append("\n");
        }

        for (NodoDOM hijo : nodo.getHijos()) {
            sb.append(generarHTMLRecursivo(hijo, nivel + 1));
        }

        sb.append(sangria).append("</").append(nodo.getEtiqueta()).append(">\n");

        return sb.toString();
    }
}

```
```java
/*
 * NodoVisual.java
 *
 * Autor: Marisol Rincón Solís
 * Correo: 1224100827.mrsq@gmail.com
 *
 * Descripción:
 * Representa gráficamente un nodo DOM en el panel de árbol.
 * Contiene su posición, tamaño, color y métodos de dibujo.
 */

package PRACTICASWING.domVista;

import PRACTICASWING.domModelo.NodoDOM;
import java.awt.*;

public class NodoVisual {

    /** Referencia al nodo lógico del DOM. */
    private NodoDOM nodoDOM;

    /** Coordenada X del nodo en pantalla. */
    private int x;

    /** Coordenada Y del nodo en pantalla. */
    private int y;

    /** Ancho del rectángulo dibujado. */
    private int ancho = 110;

    /** Alto del rectángulo dibujado. */
    private int alto = 35;

    /** Indica si el nodo está seleccionado por el usuario. */
    private boolean seleccionado;

    /**
     * Constructor de un nodo visual.
     *
     * @param nodoDOM Nodo lógico que se representará.
     */
    public NodoVisual(NodoDOM nodoDOM) {
        this.nodoDOM = nodoDOM;
    }

    /**
     * Dibuja el nodo visual en el panel.
     *
     * @param g Objeto Graphics para realizar el dibujo.
     */
    public void dibujar(Graphics g) {
        g.setColor(seleccionado ? Color.LIGHT_GRAY : Color.WHITE);
        g.fillRect(x, y, ancho, alto);

        g.setColor(Color.BLACK);
        g.drawRect(x, y, ancho, alto);
        g.drawString(nodoDOM.getEtiqueta(), x + 10, y + 20);
    }

    /**
     * Verifica si un punto está dentro del nodo.
     *
     * @param px Coordenada X del punto.
     * @param py Coordenada Y del punto.
     * @return true si el punto cae dentro del nodo.
     */
    public boolean contienePunto(int px, int py) {
        return px >= x && px <= x + ancho && py >= y && py <= y + alto;
    }

    /** GETTERS y SETTERS */

    public NodoDOM getNodoDOM() { return nodoDOM; }

    public void setX(int x) { this.x = x; }

    public void setY(int y) { this.y = y; }

    public int getAncho() { return ancho; }

    public int getAlto() { return alto; }

    public void setSeleccionado(boolean seleccionado) { this.seleccionado = seleccionado; }

    /**
     * Devuelve el punto inferior del nodo, usado para dibujar líneas hacia los hijos.
     *
     * @return Punto base del nodo.
     */
    public Point getBase() {
        return new Point(x + ancho / 2, y + alto);
    }

    /**
     * Devuelve el punto central del nodo.
     *
     * @return Punto central.
     */
    public Point getCentro() {
        return new Point(x + ancho / 2, y + alto / 2);
    }
}

```
### PanelArbolPersonalizado
```java
/*
 * Autor: Marisol Rincón Solís
 * Descripción:
 * Este panel muestra visualmente el árbol DOM usando nodos dibujados de forma personalizada.
 * Permite seleccionar nodos con el mouse, mostrando su relación jerárquica mediante líneas.
 * Además, notifica a otros componentes (como el controlador) cuando un nodo es seleccionado.
 */

package PRACTICASWING.domVista;

import PRACTICASWING.domModelo.ArbolDOM;
import PRACTICASWING.domModelo.NodoDOM;
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.beans.PropertyChangeListener;
import java.util.*;

public class PanelArbolPersonalizado extends JPanel {

    /** Árbol DOM lógico que se representará visualmente. */
    private ArbolDOM arbolDOM;

    /** Mapa que asocia cada NodoDOM con su representación visual NodoVisual. */
    private Map<NodoDOM, NodoVisual> nodosVisuales;

    /** Nodo actualmente seleccionado por el usuario. */
    private NodoVisual nodoSeleccionado;

    /** Tamaño preferido del área de dibujo. */
    private int anchoPanel = 800;
    private int altoPanel = 600;

    /** Panel interno que se encarga del dibujo completo. */
    private JPanel panelDibujo;

    /**
     * Constructor del panel de árbol personalizado.
     *
     * @param arbolDOM Árbol DOM que se representará.
     */
    public PanelArbolPersonalizado(ArbolDOM arbolDOM) {
        this.arbolDOM = arbolDOM;
        this.nodosVisuales = new HashMap<>();
        this.nodoSeleccionado = null;

        inicializarComponentes();
        actualizarArbol();
    }

    /**
     * Inicializa el panel, el área de dibujo y los eventos del mouse.
     */
    private void inicializarComponentes() {
        setLayout(new BorderLayout());
        setBorder(BorderFactory.createTitledBorder("Estructura DOM - Árbol Personalizado"));
        setPreferredSize(new Dimension(anchoPanel, altoPanel));

        panelDibujo = new JPanel() {

            /** Dibuja todo el árbol DOM en pantalla. */
            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                dibujarArbolCompleto(g);
            }

            @Override
            public Dimension getPreferredSize() {
                return new Dimension(anchoPanel, altoPanel);
            }
        };

        panelDibujo.setBackground(Color.WHITE);

        // Evento de clic en los nodos
        panelDibujo.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                manejarClicRaton(e.getX(), e.getY());
            }
        });

        panelDibujo.setToolTipText("Haz clic en cualquier nodo para seleccionarlo");
        add(new JScrollPane(panelDibujo), BorderLayout.CENTER);
    }

    /**
     * Detecta clics en el área gráfica y selecciona nodos.
     *
     * @param x coordenada X del clic
     * @param y coordenada Y del clic
     */
    private void manejarClicRaton(int x, int y) {
        System.out.println("Clic en coordenadas: " + x + ", " + y);

        // Buscar si el clic coincide con algún nodo
        for (NodoVisual nodoVisual : nodosVisuales.values()) {
            if (nodoVisual.contienePunto(x, y)) {

                if (nodoSeleccionado != null) {
                    nodoSeleccionado.setSeleccionado(false);
                }

                nodoSeleccionado = nodoVisual;
                nodoVisual.setSeleccionado(true);

                panelDibujo.repaint();

                firePropertyChange("nodoSeleccionado", null, nodoVisual.getNodoDOM());
                return;
            }
        }

        // Si se clickeó un área vacía, se deselecciona
        if (nodoSeleccionado != null) {
            nodoSeleccionado.setSeleccionado(false);
            nodoSeleccionado = null;
            panelDibujo.repaint();
            firePropertyChange("nodoSeleccionado", null, null);
        }
    }

    /**
     * Actualiza el árbol visual recalculando posiciones y redibujando.
     */
    public void actualizarArbol() {
        calcularPosiciones();
        if (panelDibujo != null) panelDibujo.repaint();
    }

    /**
     * Recorre el árbol DOM y asigna una posición gráfica a cada nodo.
     */
    private void calcularPosiciones() {
        nodosVisuales.clear();

        if (arbolDOM.getRaiz() != null) {
            calcularPosicionesRecursivo(arbolDOM.getRaiz(), anchoPanel / 2, 50, anchoPanel / 3);
        }
    }

    /**
     * Algoritmo recursivo para distribuir visualmente los nodos del árbol.
     */
    private void calcularPosicionesRecursivo(NodoDOM nodo, int x, int y, int espacioHorizontal) {

        NodoVisual nodoVisual = new NodoVisual(nodo);
        nodoVisual.setX(x - nodoVisual.getAncho() / 2);
        nodoVisual.setY(y);
        nodosVisuales.put(nodo, nodoVisual);

        java.util.List<NodoDOM> hijos = nodo.getHijos();
        if (hijos.isEmpty()) return;

        int espacioTotal = espacioHorizontal * (hijos.size() - 1);
        int inicioX = x - espacioTotal / 2;
        int nuevaY = y + 80;
        int nuevoEspacio = Math.max(espacioHorizontal / 2, 100);

        for (int i = 0; i < hijos.size(); i++) {
            int hijoX = inicioX + i * espacioHorizontal;
            calcularPosicionesRecursivo(hijos.get(i), hijoX, nuevaY, nuevoEspacio);
        }
    }

    /**
     * Dibuja todos los nodos y conexiones del árbol.
     */
    private void dibujarArbolCompleto(Graphics g) {
        Graphics2D g2d = (Graphics2D) g;

        g2d.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

        dibujarLineas(g2d);

        for (NodoVisual nodoVisual : nodosVisuales.values()) {
            nodoVisual.dibujar(g2d);
        }

        if (nodosVisuales.size() <= 1) {
            g2d.setColor(Color.GRAY);
            g2d.drawString("💡 Haz clic en la raíz 'html' y agrega nodos hijos.", 50, 120);
        }
    }

    /**
     * Dibuja las líneas que conectan padre → hijos.
     */
    private void dibujarLineas(Graphics2D g2d) {

        g2d.setColor(Color.GRAY);
        g2d.setStroke(new BasicStroke(2));

        for (NodoVisual nodoVisual : nodosVisuales.values()) {

            NodoDOM nodoDOM = nodoVisual.getNodoDOM();
            Point puntoPadre = nodoVisual.getBase();

            for (NodoDOM hijo : nodoDOM.getHijos()) {

                NodoVisual hijoVisual = nodosVisuales.get(hijo);
                if (hijoVisual != null) {
                    Point puntoHijo = hijoVisual.getCentro();

                    g2d.drawLine(
                            puntoPadre.x,
                            puntoPadre.y,
                            puntoHijo.x,
                            puntoHijo.y - hijoVisual.getAlto() / 2
                    );
                }
            }
        }
    }

    /** Devuelve el nodo DOM actualmente seleccionado. */
    public NodoDOM getNodoSeleccionado() {
        return nodoSeleccionado != null ? nodoSeleccionado.getNodoDOM() : null;
    }

    /** Permite registrar un listener para cambios de selección. */
    public void agregarPropertyChangeListener(PropertyChangeListener listener) {
        addPropertyChangeListener(listener);
    }

    /**
     * Muestra en consola todos los nodos visibles.
     * Se utiliza para depuración.
     */
    public void mostrarNodosParaDebug() {
        System.out.println("=== NODOS VISIBLES EN EL ÁRBOL ===");

        for (NodoVisual nv : nodosVisuales.values()) {
            System.out.println("Nodo: " + nv.getNodoDOM() +
                               " en posición: " + nv.getAncho() + ", " + nv.getAlto());
        }

        System.out.println("Total nodos: " + nodosVisuales.size());
    }
}

```
### PanelControles
```java
/*
 * PanelControles.java
 *
 * Autor: Marisol Rincón Solís
 * Correo: 1224100827.mrsq@gmail.com
 *
 * Descripción:
 * Panel con los campos de texto y botones que permiten agregar o eliminar nodos.
 */

package PRACTICASWING.domVista;

import javax.swing.*;
import java.awt.*;

/**
 * Panel que contiene los controles para manipular el árbol DOM:
 * - Campo de etiqueta
 * - Campo de contenido
 * - Botón agregar
 * - Botón eliminar
 */
public class PanelControles extends JPanel {

    /** Campo para escribir la etiqueta del nodo. */
    private JTextField etiqueta;

    /** Campo para escribir el contenido del nodo. */
    private JTextField contenido;

    /** Botón para agregar un nodo nuevo. */
    private JButton agregar;

    /** Botón para eliminar el nodo seleccionado. */
    private JButton eliminar;

    /** Mensaje de estado para el usuario. */
    private JLabel estado;

    /**
     * Constructor del panel de controles.
     */
    public PanelControles() {

        setLayout(new BorderLayout());
        setBorder(BorderFactory.createTitledBorder("Controles"));

        JPanel p = new JPanel(new GridLayout(3, 2));

        p.add(new JLabel("Etiqueta:"));
        etiqueta = new JTextField();
        p.add(etiqueta);

        p.add(new JLabel("Contenido:"));
        contenido = new JTextField();
        p.add(contenido);

        agregar = new JButton("Agregar");
        p.add(agregar);

        eliminar = new JButton("Eliminar");
        p.add(eliminar);

        add(p, BorderLayout.CENTER);
        estado = new JLabel("Seleccione un nodo.");
        add(estado, BorderLayout.SOUTH);
    }

    /** Getters para el controlador */

    public JTextField getCampoEtiqueta() { return etiqueta; }

    public JTextField getCampoContenido() { return contenido; }

    public JButton getBtnAgregar() { return agregar; }

    public JButton getBtnEliminar() { return eliminar; }

    public JLabel getLblEstado() { return estado; }
}

```
### PanelHTML
```java
/*
 * PanelHTML.java
 *
 * Autor: Marisol Rincón Solís
 * Correo: 1224100827.mrsq@gmail.com
 *
 * Descripción:
 * Panel que muestra el HTML generado a partir del árbol DOM.
 */

package PRACTICASWING.domVista;

import PRACTICASWING.domModelo.ArbolDOM;
import javax.swing.*;
import java.awt.*;
public class PanelHTML extends JPanel {

    /** Área de texto donde se muestra el HTML. */
    private JTextArea area;

    /** Referencia al árbol DOM para obtener el HTML. */
    private ArbolDOM arbolDOM;

    /**
     * Constructor del panel HTML.
     *
     * @param arbolDOM Árbol desde el cual se generará el HTML.
     */
    public PanelHTML(ArbolDOM arbolDOM) {
        this.arbolDOM = arbolDOM;

        setLayout(new BorderLayout());
        setBorder(BorderFactory.createTitledBorder("HTML"));

        area = new JTextArea();
        area.setEditable(false);
        area.setFont(new Font("Monospaced", Font.PLAIN, 13));

        add(new JScrollPane(area), BorderLayout.CENTER);
    }

    /**
     * Actualiza el panel mostrando el HTML generado.
     */
    public void actualizarHTML() {
        area.setText(arbolDOM.generarHTML());
    }
}

```
### SimuladorDOM
```java

/*
 * Descripción:
 * Programa principal que crea la ventana. Muestra el árbol DOM y el HTML generado.
 * Usa un árbol hecho manualmente
 *
 * Autor: Marisol Rincón Solís
 * Correo: 1224100827.mrsq@gmail.com
 */

package PRACTICASWING.dom;

import PRACTICASWING.domContralador.ControladorDOM;
import PRACTICASWING.domModelo.ArbolDOM;
import PRACTICASWING.domVista.PanelArbolPersonalizado;
import PRACTICASWING.domVista.PanelControles;
import PRACTICASWING.domVista.PanelHTML;
import javax.swing.*;
import java.awt.*;

public class SimuladorDOM {

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> crearYMostrarGUI());
    }

    private static void crearYMostrarGUI() {

        ArbolDOM arbolDOM = new ArbolDOM();

        PanelArbolPersonalizado panelArbol = new PanelArbolPersonalizado(arbolDOM);
        PanelHTML panelHTML = new PanelHTML(arbolDOM);
        PanelControles panelControles = new PanelControles();

        new ControladorDOM(arbolDOM, panelArbol, panelHTML, panelControles);

        JFrame frame = new JFrame("Simulador DOM");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new BorderLayout());

        JPanel panelIzquierdo = new JPanel(new BorderLayout());
        panelIzquierdo.add(panelArbol, BorderLayout.CENTER);
        panelIzquierdo.add(panelControles, BorderLayout.SOUTH);

        JSplitPane split = new JSplitPane(
                JSplitPane.HORIZONTAL_SPLIT,
                panelIzquierdo,
                panelHTML
        );
        split.setDividerLocation(550);

        frame.add(split, BorderLayout.CENTER);
        frame.setSize(1100, 600);
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);

        JOptionPane.showMessageDialog(frame,
                "Bienvenido.\nSeleccione un nodo para agregar o eliminar.",
                "Información",
                JOptionPane.INFORMATION_MESSAGE);
    }
}

```
