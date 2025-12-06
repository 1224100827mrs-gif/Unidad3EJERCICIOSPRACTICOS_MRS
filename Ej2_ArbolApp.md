# Ejercicio 2

### Clase Nodo
 ``` java

package ArbolBinarioApp;
/**
 * Clase que representa un nodo dentro de un Árbol Binario de Búsqueda.
 * Cada nodo almacena un valor entero y referencias a sus hijos izquierdo y derecho.
 * Se utiliza como elemento básico para la estructura del árbol.
 * 
 * @author Marisol Rincón Solís
 * @author 1224100827..
 */
public class Nodo {

    /** Valor entero almacenado en el nodo. */
    public int valor;

    /** Referencia al hijo izquierdo del nodo. */
    public Nodo izquierdo;

    /** Referencia al hijo derecho del nodo. */
    public Nodo derecho;

    /**
     * Constructor que inicializa un nodo con el valor indicado.
     * Los hijos izquierdo y derecho se inicializan como null.
     * 
     * @param valor valor que almacenará el nodo.
     */
    public Nodo(int valor) {
        this.valor = valor;
        this.izquierdo = null;
        this.derecho = null;
    }

    /**
     * Convierte el valor del nodo en una cadena de texto.
     * 
     * @return representación en texto del valor del nodo.
     */
    @Override
    public String toString() {
        return String.valueOf(valor);
    }
}
```
### Clase ArbolBinarioBusqueda
 ``` java

package ArbolBinarioApp;

/**
 * Implementación de un Árbol Binario de Búsqueda (ABB),
 * que permite insertar, buscar, eliminar nodos, realizar recorridos
 * y limpiar la estructura completa.
 * Cada nodo almacena un valor entero único (no se permiten duplicados).
 * 
 * Esta versión incluye la capacidad de identificar visualmente
 * cuál fue el último nodo encontrado durante una búsqueda, permitiendo
 * que la interfaz gráfica lo resalte en color rojo.
 * 
 * @author 
 *      Marisol Rincón Solís
 */
import java.util.ArrayList;
import java.util.List;

public class ArbolBinarioBusqueda {

    /** Nodo raíz del árbol. */
    private Nodo raiz;

    /** Lista auxiliar para almacenar los recorridos. */
    private List<Integer> recorridoActual;

    /** Nodo temporal para almacenar resultados de búsqueda. */
    private Nodo nodoEncontrado;

    /** 
     * Nodo encontrado en la búsqueda más reciente.
     * Se usa únicamente para resaltarlo visualmente en la GUI.
     */
    private Nodo nodoBuscadoVisual;

    /**
     * Constructor: inicializa el árbol vacío.
     */
    public ArbolBinarioBusqueda() {
        this.raiz = null;
        this.recorridoActual = new ArrayList<>();
        this.nodoEncontrado = null;
        this.nodoBuscadoVisual = null;
    }

    /**
     * Inserta un valor en el árbol.
     * Si el árbol está vacío, crea la raíz.
     * 
     * @param valor valor entero a insertar.
     * @return true si la inserción fue exitosa, false si el valor ya existe.
     */
    public boolean insertar(int valor) {
        if (raiz == null) {
            raiz = new Nodo(valor);
            return true;
        }
        return insertarRecursivo(raiz, valor);
    }

    /**
     * Inserta un nodo de forma recursiva respetando las reglas del ABB.
     */
    private boolean insertarRecursivo(Nodo actual, int valor) {
        if (valor < actual.valor) {

            if (actual.izquierdo == null) {
                actual.izquierdo = new Nodo(valor);
                return true;
            }
            return insertarRecursivo(actual.izquierdo, valor);

        } else if (valor > actual.valor) {

            if (actual.derecho == null) {
                actual.derecho = new Nodo(valor);
                return true;
            }
            return insertarRecursivo(actual.derecho, valor);

        } else {
            return false; // no se permiten duplicados
        }
    }

    /**
     * Busca un valor dentro del árbol.
     * 
     * Además de devolver el nodo encontrado, esta versión también
     * registra dicho nodo en la variable nodoBuscadoVisual para que
     * la interfaz gráfica pueda resaltarlo visualmente.
     * 
     * @param valor valor a buscar.
     * @return nodo encontrado o null si no existe.
     */
    public Nodo buscar(int valor) {
        nodoEncontrado = null;
        buscarRecursivo(raiz, valor);

        // <<< NUEVO: guardar el nodo para resaltarlo en rojo >>>
        nodoBuscadoVisual = nodoEncontrado;

        return nodoEncontrado;
    }

    /**
     * Realiza la búsqueda de forma recursiva.
     */
    private void buscarRecursivo(Nodo actual, int valor) {
        if (actual == null) return;

        if (valor == actual.valor) {
            nodoEncontrado = actual;

        } else if (valor < actual.valor) {
            buscarRecursivo(actual.izquierdo, valor);

        } else {
            buscarRecursivo(actual.derecho, valor);
        }
    }

    /**
     * Devuelve el nodo que debe mostrarse resaltado en la interfaz gráfica.
     * 
     * @return nodo recientemente encontrado o null si no existe.
     */
    public Nodo getNodoBuscadoVisual() {
        return nodoBuscadoVisual;
    }

    /**
     * Elimina un nodo con el valor indicado.
     */
    public boolean eliminar(int valor) {
        if (raiz == null) return false;

        raiz = eliminarRecursivo(raiz, valor);

        // <<< NUEVO: si el nodo eliminado era el resaltado, se limpia >>>
        if (nodoBuscadoVisual != null && nodoBuscadoVisual.valor == valor) {
            nodoBuscadoVisual = null;
        }

        return true;
    }

    /**
     * Elimina un nodo del árbol aplicando los 3 casos de eliminación.
     */
    private Nodo eliminarRecursivo(Nodo actual, int valor) {
        if (actual == null) return null;

        if (valor < actual.valor) {
            actual.izquierdo = eliminarRecursivo(actual.izquierdo, valor);

        } else if (valor > actual.valor) {
            actual.derecho = eliminarRecursivo(actual.derecho, valor);

        } else { 
            // Caso 1: sin hijos
            if (actual.izquierdo == null && actual.derecho == null) {
                return null;

            // Caso 2: un hijo
            } else if (actual.izquierdo == null) {
                return actual.derecho;

            } else if (actual.derecho == null) {
                return actual.izquierdo;

            } else {
                // Caso 3: dos hijos (sucesor)
                Nodo sucesor = encontrarMinimo(actual.derecho);
                actual.valor = sucesor.valor;
                actual.derecho = eliminarRecursivo(actual.derecho, sucesor.valor);
            }
        }
        return actual;
    }

    /**
     * Encuentra el valor mínimo dentro de un subárbol.
     */
    private Nodo encontrarMinimo(Nodo nodo) {
        while (nodo.izquierdo != null) {
            nodo = nodo.izquierdo;
        }
        return nodo;
    }

    /** Recorridos del árbol */
    public List<Integer> inOrden() { recorridoActual.clear(); inOrdenRecursivo(raiz); return new ArrayList<>(recorridoActual); }
    private void inOrdenRecursivo(Nodo actual) { if (actual!=null){ inOrdenRecursivo(actual.izquierdo); recorridoActual.add(actual.valor); inOrdenRecursivo(actual.derecho);} }

    public List<Integer> preOrden() { recorridoActual.clear(); preOrdenRecursivo(raiz); return new ArrayList<>(recorridoActual); }
    private void preOrdenRecursivo(Nodo actual){ if(actual!=null){ recorridoActual.add(actual.valor); preOrdenRecursivo(actual.izquierdo); preOrdenRecursivo(actual.derecho);} }

    public List<Integer> postOrden(){ recorridoActual.clear(); postOrdenRecursivo(raiz); return new ArrayList<>(recorridoActual); }
    private void postOrdenRecursivo(Nodo actual){ if(actual!=null){ postOrdenRecursivo(actual.izquierdo); postOrdenRecursivo(actual.derecho); recorridoActual.add(actual.valor);} }

    /**
     * Limpia completamente el árbol y elimina cualquier nodo resaltado.
     */
    public void limpiar() {
        raiz = null;
        recorridoActual.clear();
        nodoEncontrado = null;

        // <<< NUEVO: eliminar nodo resaltado >>>
        nodoBuscadoVisual = null;
    }

    /** Devuelve la raíz actual del árbol. */
    public Nodo getRaiz() { return raiz; }

    /** Verifica si el árbol está vacío. */
    public boolean estaVacio() { return raiz == null; }
}


```
### Clase ArbolGUI
 ``` java

package ArbolBinarioApp;

/**
 * Interfaz gráfica para visualizar e interactuar con un Árbol Binario de Búsqueda.
 * Permite insertar, eliminar, buscar nodos, ver recorridos y mostrar el árbol dibujado.
 * Ahora también permite resaltar en color rojo el nodo encontrado mediante una búsqueda.
 * 
 * @author Marisol Rincón Solís
 */
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.List;

public class ArbolGUI extends JFrame {

    /** Árbol binario de búsqueda utilizado en la interfaz. */
    private ArbolBinarioBusqueda arbol;

    /** Campo para ingresar valores. */
    private JTextField txtValor;

    /** Área donde se muestran los recorridos. */
    private JTextArea txtRecorrido;

    /** Panel donde se dibuja el árbol. */
    private JPanel panelArbol;

    /** Etiqueta de mensajes. */
    private JLabel lblMensaje;

    /**
     * Constructor: inicializa la interfaz gráfica y el árbol.
     */
    public ArbolGUI() {
        arbol = new ArbolBinarioBusqueda();
        inicializarGUI();
    }

    /**
     * Configura todos los elementos gráficos, paneles, botones y listeners.
     */
    private void inicializarGUI() {
        setTitle("Árbol Binario de Búsqueda - Visualizador");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // Panel superior
        JPanel panelSuperior = new JPanel(new FlowLayout());
        panelSuperior.add(new JLabel("Valor:"));
        txtValor = new JTextField(5);
        panelSuperior.add(txtValor);

        JButton btnInsertar = new JButton("Insertar");
        JButton btnEliminar = new JButton("Eliminar");
        JButton btnBuscar = new JButton("Buscar");

        panelSuperior.add(btnInsertar);
        panelSuperior.add(btnEliminar);
        panelSuperior.add(btnBuscar);

        // Panel de recorridos
        JPanel panelRecorridos = new JPanel(new FlowLayout());
        JButton btnInOrden = new JButton("InOrden");
        JButton btnPreOrden = new JButton("PreOrden");
        JButton btnPostOrden = new JButton("PostOrden");
        JButton btnLimpiar = new JButton("Limpiar Árbol");

        panelRecorridos.add(btnInOrden);
        panelRecorridos.add(btnPreOrden);
        panelRecorridos.add(btnPostOrden);
        panelRecorridos.add(btnLimpiar);

        JPanel panelControles = new JPanel(new GridLayout(2, 1));
        panelControles.add(panelSuperior);
        panelControles.add(panelRecorridos);

        add(panelControles, BorderLayout.NORTH);

        // Panel central: dibujo del árbol
        panelArbol = new JPanel() {
            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);

                // Se dibuja el árbol completo y se resalta si hubo nodo encontrado
                dibujarArbol(g, getWidth() / 2, 50, arbol.getRaiz(), getWidth() / 4);
            }
        };
        panelArbol.setBackground(Color.WHITE);
        add(new JScrollPane(panelArbol), BorderLayout.CENTER);

        // Panel inferior
        JPanel panelInferior = new JPanel(new BorderLayout());
        txtRecorrido = new JTextArea(5, 30);
        txtRecorrido.setEditable(false);
        panelInferior.add(new JScrollPane(txtRecorrido), BorderLayout.CENTER);

        lblMensaje = new JLabel("Listo para comenzar...");
        panelInferior.add(lblMensaje, BorderLayout.SOUTH);

        add(panelInferior, BorderLayout.SOUTH);

        // LISTENERS
        btnInsertar.addActionListener(e -> insertarNodo());
        btnEliminar.addActionListener(e -> eliminarNodo());
        btnBuscar.addActionListener(e -> buscarNodo());
        btnInOrden.addActionListener(e -> mostrarRecorrido("InOrden", arbol.inOrden()));
        btnPreOrden.addActionListener(e -> mostrarRecorrido("PreOrden", arbol.preOrden()));
        btnPostOrden.addActionListener(e -> mostrarRecorrido("PostOrden", arbol.postOrden()));
        btnLimpiar.addActionListener(e -> {
            arbol.limpiar();
            actualizarVista();
            lblMensaje.setText("Árbol limpiado");
        });

        setSize(800, 600);
        setLocationRelativeTo(null);
    }

    private void insertarNodo() {
        try {
            int valor = Integer.parseInt(txtValor.getText());
            if (arbol.insertar(valor)) {
                lblMensaje.setText("Nodo " + valor + " insertado correctamente");
                actualizarVista();
            } else {
                lblMensaje.setText("El valor " + valor + " ya existe en el árbol");
            }
        } catch (NumberFormatException ex) {
            lblMensaje.setText("Error: Ingrese un número válido");
        }
        txtValor.setText("");
    }

    private void eliminarNodo() {
        try {
            int valor = Integer.parseInt(txtValor.getText());

            if (arbol.eliminar(valor)) {
                lblMensaje.setText("Nodo " + valor + " eliminado correctamente");
                actualizarVista();
            } else {
                lblMensaje.setText("No se pudo eliminar el nodo " + valor);
            }

        } catch (NumberFormatException ex) {
            lblMensaje.setText("Error: Ingrese un número válido");
        }
        txtValor.setText("");
    }

    private void buscarNodo() {
        try {
            int valor = Integer.parseInt(txtValor.getText());
            Nodo encontrado = arbol.buscar(valor);

            if (encontrado != null) {
                lblMensaje.setText("Nodo " + valor + " encontrado");
            } else {
                lblMensaje.setText("Nodo " + valor + " NO encontrado");
            }

            // Se repinta para mostrar el nodo en rojo
            panelArbol.repaint();

        } catch (NumberFormatException ex) {
            lblMensaje.setText("Error: Ingrese un número válido");
        }
        txtValor.setText("");
    }

    private void mostrarRecorrido(String tipo, List<Integer> recorrido) {
        if (arbol.estaVacio()) {
            txtRecorrido.setText("El árbol está vacío");
            return;
        }

        StringBuilder sb = new StringBuilder();
        sb.append(tipo).append(": ");

        for (int i = 0; i < recorrido.size(); i++) {
            sb.append(recorrido.get(i));
            if (i < recorrido.size() - 1) sb.append(" → ");
        }

        txtRecorrido.setText(sb.toString());
    }

    /**
     * Dibuja recursivamente el árbol.
     * 
     * Esta versión resalta en color rojo el nodo que fue encontrado en la
     * última operación de búsqueda, utilizando la referencia obtenida desde
     * ArbolBinarioBusqueda.getNodoBuscadoVisual().
     * 
     * @param g objeto gráfico.
     * @param x posición X del nodo.
     * @param y posición Y del nodo.
     * @param nodo nodo actual a dibujar.
     * @param xOffset separación horizontal entre niveles.
     */
    private void dibujarArbol(Graphics g, int x, int y, Nodo nodo, int xOffset) {
        if (nodo == null) return;

        // <<< NUEVO: Determinar si el nodo debe mostrarse resaltado >>>
        boolean esBuscado = (nodo == arbol.getNodoBuscadoVisual());

        // Fondo del nodo
        if (esBuscado) {
            g.setColor(Color.RED); // nodo encontrado → rojo
        } else {
            g.setColor(Color.LIGHT_GRAY);
        }

        g.fillOval(x - 15, y - 15, 30, 30);

        // Borde del nodo
        g.setColor(Color.BLACK);
        g.drawOval(x - 15, y - 15, 30, 30);

        // Texto del nodo
        g.drawString(String.valueOf(nodo.valor), x - 5, y + 5);

        // Dibujo de conexiones e hijos
        if (nodo.izquierdo != null) {
            int xLeft = x - xOffset;
            int yLeft = y + 60;
            g.drawLine(x, y + 15, xLeft, yLeft - 15);
            dibujarArbol(g, xLeft, yLeft, nodo.izquierdo, xOffset / 2);
        }

        if (nodo.derecho != null) {
            int xRight = x + xOffset;
            int yRight = y + 60;
            g.drawLine(x, y + 15, xRight, yRight - 15);
            dibujarArbol(g, xRight, yRight, nodo.derecho, xOffset / 2);
        }
    }

    private void actualizarVista() {
        panelArbol.repaint();
        txtRecorrido.setText("");
    }
}

```
### Clase Main
 ``` java

package ArbolBinarioApp;

import javax.swing.SwingUtilities;

/**
 * Clase principal del programa.
 * Se encarga de iniciar la aplicación gráfica del Árbol Binario de Búsqueda.
 * Utiliza SwingUtilities.invokeLater para garantizar que la interfaz gráfica
 * se ejecute en el hilo de despacho de eventos de Swing (EDT), 
 * asegurando una ejecución segura y adecuada de los componentes visuales.
 * 
 * @author: Marisol Rincón Solís
 */
public class Main {

    /**
     * Método principal del programa.  
     * Crea y muestra la ventana gráfica ArbolGUI.
     * 
     * @param args argumentos de línea de comando (no utilizados).
     */
    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            /**
             * Ejecuta la creación de la ventana dentro del hilo de Swing.
             */
            @Override
            public void run() {
                new ArbolGUI().setVisible(true);
            }
        });
    }
}
```
# PRUBA 1 INSERCCION
<img width="740" height="571" alt="image" src="https://github.com/user-attachments/assets/966b5e28-f811-4ebe-adaf-26fb1ad61595" />
# PRUEBA 2 Prueba de Recorridos (Validación de Orden)
### En la parte inferior aparece el recorrido en Innorden
<img width="758" height="468" alt="image" src="https://github.com/user-attachments/assets/3ac05b15-9f12-4a84-9a7c-764124d14ecb" />
### En la parte inferior aparece el recorrido en Preorden 
<img width="1364" height="622" alt="image" src="https://github.com/user-attachments/assets/b30412ac-8d56-4271-9c3e-e715f6c3637a" />
### En la parte inferior aparece el recorrido en PostOrden
<img width="740" height="568" alt="image" src="https://github.com/user-attachments/assets/cdef603d-e186-4efe-97e2-4a5751b8d383" />

# Prueba de Busqeda
<img width="1361" height="615" alt="image" src="https://github.com/user-attachments/assets/ad9e6a23-4570-46a5-8605-de70f5bc3e4e" />
<img width="1334" height="715" alt="image" src="https://github.com/user-attachments/assets/105da5ba-c963-4180-a545-fc4171870fe6" />

# Grupo 4: Prueba de Eliminación (Casos Críticos)
### Caso 1
<img width="1324" height="700" alt="image" src="https://github.com/user-attachments/assets/95cccf6f-70f8-4ac7-b601-f76dfa074a6b" />

### Caso 2
<img width="1364" height="676" alt="image" src="https://github.com/user-attachments/assets/e876ecc2-0076-40de-902c-e6d1c5590c5c" />






