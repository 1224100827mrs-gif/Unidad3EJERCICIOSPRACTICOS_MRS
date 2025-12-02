# EJERCICIO 3

### Clase contacto 

```java

package GestionContactosApp;

import java.util.Objects;

/**
 * Clase que representa un contacto en la agenda
 * Contiene 5 datos de información como se solicita
 * Implementa equals y hashCode para uso correcto en HashSet
 *  @author DELL
 */
public class Contacto {
    private String nombre;
    private String telefono;
    private String email;
    private String ciudad;
    private String categoria; // FAMILIA, TRABAJO, AMIGOS, OTROS
    
    /**
     * Constructor de la clase Contacto
     * @param nombre Nombre completo del contacto
     * @param telefono Número de teléfono
     * @param email Dirección de correo electrónico
     * @param ciudad Ciudad de residencia
     * @param categoria Categoría del contacto
     */
    public Contacto(String nombre, String telefono, String email, String ciudad, String categoria) {
        this.nombre = nombre;
        this.telefono = telefono;
        this.email = email;
        this.ciudad = ciudad;
        this.categoria = categoria.toUpperCase();
    }
    
    // Getters
    public String getNombre() { return nombre; }
    public String getTelefono() { return telefono; }
    public String getEmail() { return email; }
    public String getCiudad() { return ciudad; }
    public String getCategoria() { return categoria; }
    
    // Setters
    public void setNombre(String nombre) { this.nombre = nombre; }
    public void setTelefono(String telefono) { this.telefono = telefono; }
    public void setEmail(String email) { this.email = email; }
    public void setCiudad(String ciudad) { this.ciudad = ciudad; }
    public void setCategoria(String categoria) { this.categoria = categoria.toUpperCase(); }
    
    /**
     * Método equals para comparar contactos por email (campo único)
     * @param obj Objeto a comparar
     * @return true si los contactos son iguales
     */
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Contacto contacto = (Contacto) obj;
        return email.equalsIgnoreCase(contacto.email);
    }
    
    /**
     * Método hashCode consistente con equals
     * @return hashCode del email en minúsculas
     */
    @Override
    public int hashCode() {
        return Objects.hash(email.toLowerCase());
    }
    
    /**
     * Representación en String del contacto
     * @return String con todos los datos del contacto
     */
    @Override
    public String toString() {
        return String.format("Nombre: %s | Tel: %s | Email: %s | Ciudad: %s | Categoría: %s",
                nombre, telefono, email, ciudad, categoria);
    }
}

```
### Contactos GUI
```java

package GestionContactosApp;

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.List;

/**
 * Interfaz gráfica para la gestión de contactos
 * Implementa una interfaz completa y funcional con Swing
 *  @author Marisol Rincón Solís
 */
public class ContactosGUI extends JFrame {
    private GestorContactos gestor;
    private JTable tablaContactos;
    private DefaultTableModel modeloTabla;
    private JTextField txtNombre, txtTelefono, txtEmail, txtCiudad;
    private JComboBox<String> comboCategoria;
    private JLabel lblEstadisticas;
    
    /**
     * Constructor principal de la interfaz
     */
    public ContactosGUI() {
        gestor = new GestorContactos();
        inicializarGUI();
        cargarDatosEjemplo(); // Cargar datos de ejemplo
        actualizarTabla();
        actualizarEstadisticas();
    }
    
    /**
     * Inicializa todos los componentes de la interfaz gráfica
     */
    private void inicializarGUI() {
        setTitle("Sistema de Gestión de Contactos - Conjuntos Java");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout(10, 10));
        
        // Panel superior - Formulario de entrada
        JPanel panelFormulario = crearPanelFormulario();
        add(panelFormulario, BorderLayout.NORTH);
        
        // Panel central - Tabla de contactos
        JPanel panelTabla = crearPanelTabla();
        add(panelTabla, BorderLayout.CENTER);
        
        // Panel inferior - Botones de operaciones y estadísticas
        JPanel panelInferior = crearPanelInferior();
        add(panelInferior, BorderLayout.SOUTH);
        
        pack();
        setSize(800, 600);
        setLocationRelativeTo(null);
    }
    
    /**
     * Crea el panel del formulario para ingresar contactos
     * @return Panel con los campos de entrada
     */
    private JPanel crearPanelFormulario() {
        JPanel panel = new JPanel(new GridLayout(2, 1, 5, 5));
        panel.setBorder(BorderFactory.createTitledBorder("Nuevo Contacto"));
        
        // Fila 1: Campos básicos
        JPanel panelFila1 = new JPanel(new FlowLayout());
        panelFila1.add(new JLabel("Nombre:"));
        txtNombre = new JTextField(15);
        panelFila1.add(txtNombre);
        
        panelFila1.add(new JLabel("Teléfono:"));
        txtTelefono = new JTextField(10);
        panelFila1.add(txtTelefono);
        
        // Fila 2: Campos restantes
        JPanel panelFila2 = new JPanel(new FlowLayout());
        panelFila2.add(new JLabel("Email:"));
        txtEmail = new JTextField(15);
        panelFila2.add(txtEmail);
        
        panelFila2.add(new JLabel("Ciudad:"));
        txtCiudad = new JTextField(10);
        panelFila2.add(txtCiudad);
        
        panelFila2.add(new JLabel("Categoría:"));
        String[] categorias = {"FAMILIA", "TRABAJO", "AMIGOS", "OTROS"};
        comboCategoria = new JComboBox<>(categorias);
        panelFila2.add(comboCategoria);
        
        panel.add(panelFila1);
        panel.add(panelFila2);
        
        return panel;
    }
    
    /**
     * Crea el panel con la tabla de contactos
     * @return Panel con JTable y scroll
     */
    private JPanel crearPanelTabla() {
        JPanel panel = new JPanel(new BorderLayout());
        panel.setBorder(BorderFactory.createTitledBorder("Lista de Contactos"));
        
        // Configurar modelo de tabla
        String[] columnas = {"Nombre", "Teléfono", "Email", "Ciudad", "Categoría"};
        modeloTabla = new DefaultTableModel(columnas, 0) {
            @Override
            public boolean isCellEditable(int row, int column) {
                return false; // Tabla no editable
            }
        };
        
        tablaContactos = new JTable(modeloTabla);
        JScrollPane scrollPane = new JScrollPane(tablaContactos);
        panel.add(scrollPane, BorderLayout.CENTER);
        
        return panel;
    }
    
    /**
     * Crea el panel inferior con botones y estadísticas
     * @return Panel con controles
     */
    private JPanel crearPanelInferior() {
        JPanel panel = new JPanel(new BorderLayout());
        
        // Panel de botones
        JPanel panelBotones = new JPanel(new FlowLayout());
        
        JButton btnAgregar = new JButton("Agregar Contacto");
        JButton btnEliminar = new JButton("Eliminar Contacto");
        JButton btnBuscar = new JButton("Buscar por Email");
        JButton btnLimpiar = new JButton("Limpiar Todos");
        JButton btnListar = new JButton("Listar con Iterator");
        JButton btnFiltrar = new JButton("Filtrar por Ciudad");
        
        panelBotones.add(btnAgregar);
        panelBotones.add(btnEliminar);
        panelBotones.add(btnBuscar);
        panelBotones.add(btnLimpiar);
        panelBotones.add(btnListar);
        panelBotones.add(btnFiltrar);
        
        // Panel de estadísticas
        JPanel panelEstadisticas = new JPanel(new FlowLayout());
        lblEstadisticas = new JLabel("Total contactos: 0");
        panelEstadisticas.add(lblEstadisticas);
        
        panel.add(panelBotones, BorderLayout.NORTH);
        panel.add(panelEstadisticas, BorderLayout.SOUTH);
        
        // Agregar listeners a los botones
        agregarListeners(btnAgregar, btnEliminar, btnBuscar, btnLimpiar, btnListar, btnFiltrar);
        
        return panel;
    }
    
    /**
     * Agrega los action listeners a todos los botones
     */
    private void agregarListeners(JButton btnAgregar, JButton btnEliminar, JButton btnBuscar,
                                 JButton btnLimpiar, JButton btnListar, JButton btnFiltrar) {
        
        // OPERACIÓN ADD - Agregar contacto
        btnAgregar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                agregarContacto();
            }
        });
        
        // OPERACIÓN REMOVE - Eliminar contacto
        btnEliminar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                eliminarContacto();
            }
        });
        
        // OPERACIÓN CONTAINS - Buscar contacto
        btnBuscar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                buscarContacto();
            }
        });
        
        // OPERACIÓN CLEAR - Limpiar todos
        btnLimpiar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                limpiarContactos();
            }
        });
        
        // OPERACIÓN ITERATOR - Listar con iterator
        btnListar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                listarConIterator();
            }
        });
        
        // Operación adicional - Filtrar
        btnFiltrar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                filtrarPorCiudad();
            }
        });
    }
    
    /**
     * Implementa la operación ADD del conjunto
     */
    private void agregarContacto() {
        try {
            String nombre = txtNombre.getText().trim();
            String telefono = txtTelefono.getText().trim();
            String email = txtEmail.getText().trim();
            String ciudad = txtCiudad.getText().trim();
            String categoria = (String) comboCategoria.getSelectedItem();
            
            if (nombre.isEmpty() || email.isEmpty()) {
                JOptionPane.showMessageDialog(this, "Nombre y Email son obligatorios", "Error", JOptionPane.ERROR_MESSAGE);
                return;
            }
            
            Contacto nuevoContacto = new Contacto(nombre, telefono, email, ciudad, categoria);
            
            // OPERACIÓN ADD - Agregar al conjunto
            if (gestor.agregarContacto(nuevoContacto)) {
                JOptionPane.showMessageDialog(this, "Contacto agregado exitosamente");
                limpiarFormulario();
                actualizarTabla();
                actualizarEstadisticas();
            } else {
                JOptionPane.showMessageDialog(this, "El contacto ya existe (mismo email)", "Advertencia", JOptionPane.WARNING_MESSAGE);
            }
            
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(this, "Error al agregar contacto: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }
    
    /**
     * Implementa la operación REMOVE del conjunto
     */
    private void eliminarContacto() {
        String email = JOptionPane.showInputDialog(this, "Ingrese el email del contacto a eliminar:");
        if (email != null && !email.trim().isEmpty()) {
            // OPERACIÓN REMOVE - Eliminar del conjunto
            if (gestor.eliminarContacto(email.trim())) {
                JOptionPane.showMessageDialog(this, "Contacto eliminado exitosamente");
                actualizarTabla();
                actualizarEstadisticas();
            } else {
                JOptionPane.showMessageDialog(this, "No se encontró el contacto", "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }
    
    /**
     * Implementa la operación CONTAINS del conjunto
     */
    private void buscarContacto() {
        String email = JOptionPane.showInputDialog(this, "Ingrese el email a buscar:");
        if (email != null && !email.trim().isEmpty()) {
            // OPERACIÓN CONTAINS - Verificar existencia
            boolean existe = gestor.existeContacto(email.trim());
            if (existe) {
                JOptionPane.showMessageDialog(this, "✓ El contacto SÍ existe en la agenda");
            } else {
                JOptionPane.showMessageDialog(this, "✗ El contacto NO existe en la agenda");
            }
        }
    }
    
    /**
     * Implementa la operación CLEAR del conjunto
     */
    private void limpiarContactos() {
        int confirmacion = JOptionPane.showConfirmDialog(this, 
            "¿Está seguro de eliminar TODOS los contactos?", "Confirmar", JOptionPane.YES_NO_OPTION);
        
        if (confirmacion == JOptionPane.YES_OPTION) {
            // OPERACIÓN CLEAR - Limpiar conjunto
            gestor.limpiarContactos();
            actualizarTabla();
            actualizarEstadisticas();
            JOptionPane.showMessageDialog(this, "Todos los contactos han sido eliminados");
        }
    }
    
    /**
     * Implementa la operación ITERATOR del conjunto
     */
    private void listarConIterator() {
        StringBuilder sb = new StringBuilder();
        sb.append("=== LISTADO USANDO ITERATOR ===\n\n");
        
        // OPERACIÓN ITERATOR - Recorrer conjunto
        int contador = 0;
        var iterator = gestor.obtenerIterator();
        while (iterator.hasNext()) {
            Contacto contacto = iterator.next();
            sb.append(++contador).append(". ").append(contacto.toString()).append("\n");
        }
        
        if (contador == 0) {
            sb.append("No hay contactos en la agenda");
        }
        
        JTextArea textArea = new JTextArea(sb.toString(), 15, 50);
        textArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(textArea);
        JOptionPane.showMessageDialog(this, scrollPane, "Listado con Iterator", JOptionPane.INFORMATION_MESSAGE);
    }
    
    /**
     * Operación adicional - Filtrar por ciudad
     */
    private void filtrarPorCiudad() {
        String ciudad = JOptionPane.showInputDialog(this, "Ingrese la ciudad para filtrar:");
        if (ciudad != null && !ciudad.trim().isEmpty()) {
            List<Contacto> contactosFiltrados = gestor.buscarPorCiudad(ciudad.trim());
            
            StringBuilder sb = new StringBuilder();
            sb.append("Contactos en ").append(ciudad).append(":\n\n");
            
            if (contactosFiltrados.isEmpty()) {
                sb.append("No hay contactos en esta ciudad");
            } else {
                for (int i = 0; i < contactosFiltrados.size(); i++) {
                    sb.append(i + 1).append(". ").append(contactosFiltrados.get(i).toString()).append("\n");
                }
            }
            
            JTextArea textArea = new JTextArea(sb.toString(), 10, 50);
            textArea.setEditable(false);
            JScrollPane scrollPane = new JScrollPane(textArea);
            JOptionPane.showMessageDialog(this, scrollPane, "Filtrado por Ciudad", JOptionPane.INFORMATION_MESSAGE);
        }
    }
    
    /**
     * Actualiza la tabla con los contactos actuales
     */
    private void actualizarTabla() {
        modeloTabla.setRowCount(0); // Limpiar tabla
        
        List<Contacto> contactosOrdenados = gestor.obtenerContactosOrdenados();
        for (Contacto contacto : contactosOrdenados) {
            Object[] fila = {
                contacto.getNombre(),
                contacto.getTelefono(),
                contacto.getEmail(),
                contacto.getCiudad(),
                contacto.getCategoria()
            };
            modeloTabla.addRow(fila);
        }
    }
    
    /**
     * Actualiza las estadísticas usando la operación SIZE
     */
    private void actualizarEstadisticas() {
        // OPERACIÓN SIZE - Obtener cantidad
        int total = gestor.cantidadContactos();
        lblEstadisticas.setText("Total contactos: " + total);
    }
    
    /**
     * Limpia el formulario de entrada
     */
    private void limpiarFormulario() {
        txtNombre.setText("");
        txtTelefono.setText("");
        txtEmail.setText("");
        txtCiudad.setText("");
        comboCategoria.setSelectedIndex(0);
        txtNombre.requestFocus();
    }
    
    /**
     * Carga datos de ejemplo para demostración
     */
    private void cargarDatosEjemplo() {
        // Datos de ejemplo con los 5 campos de información
        gestor.agregarContacto(new Contacto("Ana García", "555-0101", "ana@email.com", "Madrid", "FAMILIA"));
        gestor.agregarContacto(new Contacto("Carlos López", "555-0102", "carlos@empresa.com", "Barcelona", "TRABAJO"));
        gestor.agregarContacto(new Contacto("María Rodríguez", "555-0103", "maria@email.com", "Madrid", "AMIGOS"));
        gestor.agregarContacto(new Contacto("Pedro Martínez", "555-0104", "pedro@tech.com", "Valencia", "TRABAJO"));
        gestor.agregarContacto(new Contacto("Laura Fernández", "555-0105", "laura@email.com", "Sevilla", "AMIGOS"));
    }
}
```
### Clase GestorContactos
```java
package GestionContactosApp;
/**
 * Clase que gestiona los contactos utilizando conjuntos de Java
 * Implementa 6 operaciones de conjuntos como se solicita
 * @author Marisol Rincón Solís
 */
import java.util.*;

public class GestorContactos {
    // Conjunto principal para almacenar contactos
    private Set<Contacto> contactos;
    
    /**
     * Constructor que inicializa el HashSet de contactos
     */
    public GestorContactos() {
        this.contactos = new HashSet<>();
    }
    
    // OPERACIÓN 1: ADD - Agregar contacto al conjunto
    /**
     * Agrega un contacto al conjunto
     * @param contacto Contacto a agregar
     * @return true si se agregó correctamente, false si ya existía
     */
    public boolean agregarContacto(Contacto contacto) {
        if (contacto == null) {
            throw new IllegalArgumentException("El contacto no puede ser nulo");
        }
        return contactos.add(contacto);
    }
    
    // OPERACIÓN 2: REMOVE - Eliminar contacto del conjunto
    /**
     * Elimina un contacto del conjunto por email
     * @param email Email del contacto a eliminar
     * @return true si se eliminó, false si no existía
     */
    public boolean eliminarContacto(String email) {
        return contactos.removeIf(contacto -> contacto.getEmail().equalsIgnoreCase(email));
    }
    
    // OPERACIÓN 3: CONTAINS - Verificar si existe un contacto
    /**
     * Verifica si existe un contacto con el email especificado
     * @param email Email a buscar
     * @return true si existe, false si no
     */
    public boolean existeContacto(String email) {
        return contactos.stream()
                .anyMatch(contacto -> contacto.getEmail().equalsIgnoreCase(email));
    }
    
    // OPERACIÓN 4: SIZE - Obtener cantidad de contactos
    /**
     * Obtiene el número total de contactos en el conjunto
     * @return Cantidad de contactos
     */
    public int cantidadContactos() {
        return contactos.size();
    }
    
    // OPERACIÓN 5: CLEAR - Limpiar todos los contactos
    /**
     * Elimina todos los contactos del conjunto
     */
    public void limpiarContactos() {
        contactos.clear();
    }
    
    // OPERACIÓN 6: ITERATOR - Recorrer contactos
    /**
     * Obtiene un iterator para recorrer todos los contactos
     * @return Iterator del conjunto de contactos
     */
    public Iterator<Contacto> obtenerIterator() {
        return contactos.iterator();
    }
    
    // OPERACIONES ADICIONALES ÚTILES
    
    /**
     * Obtiene todos los contactos como lista ordenada
     * @return Lista ordenada de contactos por nombre
     */
    public List<Contacto> obtenerContactosOrdenados() {
        List<Contacto> listaOrdenada = new ArrayList<>(contactos);
        listaOrdenada.sort((c1, c2) -> c1.getNombre().compareToIgnoreCase(c2.getNombre()));
        return listaOrdenada;
    }
    
    /**
     * Busca contactos por ciudad
     * @param ciudad Ciudad a filtrar
     * @return Lista de contactos en esa ciudad
     */
    public List<Contacto> buscarPorCiudad(String ciudad) {
        List<Contacto> resultado = new ArrayList<>();
        Iterator<Contacto> iterator = contactos.iterator();
        while (iterator.hasNext()) {
            Contacto contacto = iterator.next();
            if (contacto.getCiudad().equalsIgnoreCase(ciudad)) {
                resultado.add(contacto);
            }
        }
        return resultado;
    }
    
    /**
     * Obtiene contactos por categoría
     * @param categoria Categoría a filtrar
     * @return Lista de contactos en esa categoría
     */
    public List<Contacto> buscarPorCategoria(String categoria) {
        List<Contacto> resultado = new ArrayList<>();
        for (Contacto contacto : contactos) {
            if (contacto.getCategoria().equalsIgnoreCase(categoria)) {
                resultado.add(contacto);
            }
        }
        return resultado;
    }
    
    /**
     * Verifica si el conjunto está vacío
     * @return true si no hay contactos, false si hay
     */
    public boolean estaVacio() {
        return contactos.isEmpty();
    }
    
    /**
     * Obtiene todos los contactos
     * @return Conjunto completo de contactos
     */
    public Set<Contacto> getContactos() {
        return new HashSet<>(contactos);
    }
}
```
### Clase Main
```java
package GestionContactosApp;
/**
 * @author Marisol Rincón Solís
 * Clase principal que inicia la aplicación
 * Punto de entrada del programa
 */
import javax.swing.SwingUtilities;
public class Main {
    /**
     * Método main - inicia la aplicación Swing
     * @param args Argumentos de línea de comandos (no se usan)
     */
    public static void main(String[] args) {
        // Usar SwingUtilities para garantizar thread safety
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                new ContactosGUI().setVisible(true);
            }
        });
    }
}
```
