# KEVIN
//descargar jar https://sourceforge.net/projects/jdbcsql/
package carlo;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import javax.swing.JOptionPane;
import javax.swing.table.DefaultTableModel;
import carlo.Conexion;


public class CategoriaDao {

    public String ingresarCategoria(String idCategoriaTexto, String nombre) {

        if (idCategoriaTexto.trim().isEmpty() || nombre.trim().isEmpty() 
                ) {
            return "Complete todos los datos de la categoria";
        }

        try {
            int idCategoria = Integer.parseInt(idCategoriaTexto);

            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "INSERT INTO categoria(id_categoria, nombre) VALUES (?, ?)";
            PreparedStatement ps = cn.prepareStatement(sql);

            ps.setInt(1, idCategoria);
            ps.setString(2, nombre);
            ps.executeUpdate();

            cn.close();

            return "Categoria ingresado correctamente";

        } catch (NumberFormatException e) {
            return "El id de la Categoria debe ser un numero";
        } catch (Exception e) {
            return "Error al ingresar categoria: " + e.getMessage();
        }
    }

    public DefaultTableModel mostrarCategoria() {
        DefaultTableModel modelo = new DefaultTableModel();

        modelo.addColumn("ID Categoria");
        modelo.addColumn("Nombre");

        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "SELECT * FROM categoria";
            PreparedStatement ps = cn.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                modelo.addRow(new Object[]{
                    rs.getInt("id_categoria"),
                    rs.getString("nombre"),
                });
            }

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al mostrar categoria: " + e.getMessage());
        }

        return modelo;
    }

    public DefaultTableModel aceptarCategoria(String idCategoria, String nombre) {

        String mensaje = ingresarCategoria(idCategoria, nombre);

        JOptionPane.showMessageDialog(null, mensaje);

        return mostrarCategoria();
    }
}
*************

package carlo;

import java.sql.*;
import javax.swing.table.DefaultTableModel;
import carlo.Conexion;


public class ProductosDao {

    public void insertarProducto(int idProducto, String nombre, int precio, int idCategoria) {
        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "INSERT INTO productos(id_producto, nombre, precio, id_categoria) VALUES (?, ?, ?, ?)";
            PreparedStatement ps = cn.prepareStatement(sql);

            ps.setInt(1, idProducto);
            ps.setString(2, nombre);
            ps.setInt(3, precio);
            ps.setInt(4, idCategoria);
            ps.executeUpdate();

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al insertar producto: " + e.getMessage());
        }
    }

    public String ingresarProducto(String idProductoTexto, 
            String nombre, String precioTexto, String idCategoriaTexto) {

        if (idProductoTexto.trim().isEmpty() || nombre.trim().isEmpty()
                || precioTexto.trim().isEmpty() || idCategoriaTexto.trim().isEmpty()) {
            return "Complete todos los datos de la categoria";
        }

        try {
            int idProducto = Integer.parseInt(idProductoTexto);
            int precio = Integer.parseInt(precioTexto);
            int idCategoria = Integer.parseInt(idCategoriaTexto);

            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            if (!existeCategoria(cn, idCategoria)) {
                cn.close();
                return "El id de la categoria no existe";
            }

            String sql = "INSERT INTO productos(id_producto, nombre, precio, id_categoria) VALUES (?, ?, ?, ?)";
            PreparedStatement ps = cn.prepareStatement(sql);

            ps.setInt(1, idProducto);
            ps.setString(2, nombre);
            ps.setInt(3, precio);
            ps.setInt(4, idCategoria);

            ps.executeUpdate();

            cn.close();

            return "Producto ingresado correctamente";

        } catch (NumberFormatException e) {
            return "ID producto, precio e ID categoria deben ser numeros";
        } catch (Exception e) {
            return "Error al ingresar producto: " + e.getMessage();
        }
    }

    private boolean existeCategoria(Connection cn, int idCategoria) throws SQLException {

        String sql = "SELECT id_categoria FROM categoria WHERE id_categoria = ?";
        PreparedStatement ps = cn.prepareStatement(sql);
        ps.setInt(1, idCategoria);

        ResultSet rs = ps.executeQuery();

        return rs.next();
    }

    public DefaultTableModel mostrarProductos() {
        DefaultTableModel modelo = new DefaultTableModel();

        modelo.addColumn("ID Producto");
        modelo.addColumn("Nombre");
        modelo.addColumn("Precio");
        modelo.addColumn("Categoria");

        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "SELECT l.id_producto, l.nombre, l.precio, a.nombre "
                    + "FROM productos l "
                    + "INNER JOIN categoria a ON l.id_categoria = a.id_categoria";

            PreparedStatement ps = cn.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                modelo.addRow(new Object[]{
                    rs.getInt("id_producto"),
                    rs.getString("nombre"),
                    rs.getInt("precio"),
                    rs.getString("nombre")
                });
            }

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al mostrar producto: " + e.getMessage());
        }

        return modelo;
    }

    public DefaultTableModel mostrarProductosPorCategoria(int idCategoria) {
        DefaultTableModel modelo = new DefaultTableModel();

        modelo.addColumn("ID Producto");
        modelo.addColumn("Nombre");
        modelo.addColumn("precio");
        modelo.addColumn("Categoria");

        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "SELECT l.id_producto, l.nombre, l.precio, a.nombre "
                    + "FROM productos l "
                    + "INNER JOIN categoria a ON l.id_categoria = a.id_categoria "
                    + "WHERE a.id_categoria = ?";

            PreparedStatement ps = cn.prepareStatement(sql);
            ps.setInt(1, idCategoria);

            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                modelo.addRow(new Object[]{
                    rs.getInt("id_producto"),
                    rs.getString("nombre"),
                    rs.getInt("precio"),
                    rs.getString("nombre")
                });
            }

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al mostrar productos por categoria: " + e.getMessage());
        }

        return modelo;
    }
}
***************************************
public class Conexion {
     Connection conectar;
    public Connection conectar(){
        try {
            Class.forName("com.mysql.cj.jdbc.Driver"); 

            conectar=DriverManager.getConnection(
                "jdbc:mysql://localhost/kira",
                "root",
                ""
            );

            JOptionPane.showMessageDialog(null, "Conectado a kira");

        } catch (Exception ex) {
            JOptionPane.showMessageDialog(null, ex);
        }
       return conectar; 
    }
}
*****************************************
 private void jBtnVerCategoriaActionPerformed(java.awt.event.ActionEvent evt) {                                                 
        jTabla.setModel(new CategoriaDao().mostrarCategoria());

    }                                                
*********************************************
    private void jbtnProductoActionPerformed(java.awt.event.ActionEvent evt) {                                             
             ProductosDao producto  = new ProductosDao();
            jTabla.setModel(producto.mostrarProductos());
    }                                            
***************************************************
    private void jbtnConexionActionPerformed(java.awt.event.ActionEvent evt) {                                             
        carlo.Conexion cn= new carlo.Conexion();
        cn.conectar();
         
    }                                            
*********************************************************
    private void jBtnAceptarProductoActionPerformed(java.awt.event.ActionEvent evt) {                                                    
         ProductosDao dao = new ProductosDao();
String mensaje = dao.ingresarProducto(
        jTxtidproducto.getText(),
        jTxtnombrepro.getText(),
        jTxtprecio.getText(),
        jTxtidecategoriapro.getText()
);
javax.swing.JOptionPane.showMessageDialog(this, mensaje);

    jTabla.setModel(dao.mostrarProductos());

    }                                                   
*************************************************************************
    private void jBtnAceptarCategoriaActionPerformed(java.awt.event.ActionEvent evt) {                                                     
        jTabla.setModel(new CategoriaDao().aceptarCategoria(
            jTxtidca.getText(),
            jTxtCategoria.getText()
    ));
    }                                                    



AUTROR
package kevin;

import java.sql.*;
import javax.swing.JOptionPane;
import javax.swing.table.DefaultTableModel;

public class AutorDAO {

    public String ingresarAutor(String idAutorTexto, String nombre, String nacionalidad) {

        if (idAutorTexto.trim().isEmpty() || nombre.trim().isEmpty() 
                || nacionalidad.trim().isEmpty()) {
            return "Complete todos los datos del autor";
        }

        try {
            int idAutor = Integer.parseInt(idAutorTexto);

            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "INSERT INTO autores(id_autor, nombre, nacionalidad) VALUES (?, ?, ?)";
            PreparedStatement ps = cn.prepareStatement(sql);

            ps.setInt(1, idAutor);
            ps.setString(2, nombre);
            ps.setString(3, nacionalidad);

            ps.executeUpdate();

            cn.close();

            return "Autor ingresado correctamente";

        } catch (NumberFormatException e) {
            return "El id del autor debe ser un numero";
        } catch (Exception e) {
            return "Error al ingresar autor: " + e.getMessage();
        }
    }

    public DefaultTableModel mostrarAutores() {
        DefaultTableModel modelo = new DefaultTableModel();

        modelo.addColumn("ID Autor");
        modelo.addColumn("Nombre");
        modelo.addColumn("Nacionalidad");

        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "SELECT * FROM autores";
            PreparedStatement ps = cn.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                modelo.addRow(new Object[]{
                    rs.getInt("id_autor"),
                    rs.getString("nombre"),
                    rs.getString("nacionalidad")
                });
            }

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al mostrar autores: " + e.getMessage());
        }

        return modelo;
    }

    public DefaultTableModel aceptarAutor(String idAutor, String nombre, String nacionalidad) {

        String mensaje = ingresarAutor(idAutor, nombre, nacionalidad);

        JOptionPane.showMessageDialog(null, mensaje);

        return mostrarAutores();
    }
}
*************************
package kevin;

import java.sql.*;
import javax.swing.table.DefaultTableModel;

public class LibroDAO {

    public void insertarLibro(int idLibro, String titulo, int anio, int idAutor) {
        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "INSERT INTO libros(id_libro, titulo, anio, id_autor) VALUES (?, ?, ?, ?)";
            PreparedStatement ps = cn.prepareStatement(sql);

            ps.setInt(1, idLibro);
            ps.setString(2, titulo);
            ps.setInt(3, anio);
            ps.setInt(4, idAutor);
            ps.executeUpdate();

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al insertar libro: " + e.getMessage());
        }
    }

    public String ingresarLibro(String idLibroTexto, String titulo, String anioTexto, String idAutorTexto) {

        if (idLibroTexto.trim().isEmpty() || titulo.trim().isEmpty()
                || anioTexto.trim().isEmpty() || idAutorTexto.trim().isEmpty()) {
            return "Complete todos los datos del libro";
        }

        try {
            int idLibro = Integer.parseInt(idLibroTexto);
            int anio = Integer.parseInt(anioTexto);
            int idAutor = Integer.parseInt(idAutorTexto);

            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            if (!existeAutor(cn, idAutor)) {
                cn.close();
                return "El id del autor no existe";
            }

            String sql = "INSERT INTO libros(id_libro, titulo, anio, id_autor) VALUES (?, ?, ?, ?)";
            PreparedStatement ps = cn.prepareStatement(sql);

            ps.setInt(1, idLibro);
            ps.setString(2, titulo);
            ps.setInt(3, anio);
            ps.setInt(4, idAutor);

            ps.executeUpdate();

            cn.close();

            return "Libro ingresado correctamente";

        } catch (NumberFormatException e) {
            return "ID libro, anio e ID autor deben ser numeros";
        } catch (Exception e) {
            return "Error al ingresar libro: " + e.getMessage();
        }
    }

    private boolean existeAutor(Connection cn, int idAutor) throws SQLException {

        String sql = "SELECT id_autor FROM autores WHERE id_autor = ?";
        PreparedStatement ps = cn.prepareStatement(sql);
        ps.setInt(1, idAutor);

        ResultSet rs = ps.executeQuery();

        return rs.next();
    }

    public DefaultTableModel mostrarLibros() {
        DefaultTableModel modelo = new DefaultTableModel();

        modelo.addColumn("ID Libro");
        modelo.addColumn("Titulo");
        modelo.addColumn("Anio");
        modelo.addColumn("Autor");

        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "SELECT l.id_libro, l.titulo, l.anio, a.nombre "
                    + "FROM libros l "
                    + "INNER JOIN autores a ON l.id_autor = a.id_autor";

            PreparedStatement ps = cn.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                modelo.addRow(new Object[]{
                    rs.getInt("id_libro"),
                    rs.getString("titulo"),
                    rs.getInt("anio"),
                    rs.getString("nombre")
                });
            }

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al mostrar libros: " + e.getMessage());
        }

        return modelo;
    }

    public DefaultTableModel mostrarLibrosPorAutor(int idAutor) {
        DefaultTableModel modelo = new DefaultTableModel();

        modelo.addColumn("ID Libro");
        modelo.addColumn("Titulo");
        modelo.addColumn("Anio");
        modelo.addColumn("Autor");

        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();

            String sql = "SELECT l.id_libro, l.titulo, l.anio, a.nombre "
                    + "FROM libros l "
                    + "INNER JOIN autores a ON l.id_autor = a.id_autor "
                    + "WHERE a.id_autor = ?";

            PreparedStatement ps = cn.prepareStatement(sql);
            ps.setInt(1, idAutor);

            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                modelo.addRow(new Object[]{
                    rs.getInt("id_libro"),
                    rs.getString("titulo"),
                    rs.getInt("anio"),
                    rs.getString("nombre")
                });
            }

            cn.close();

        } catch (Exception e) {
            System.out.println("Error al mostrar libros por autor: " + e.getMessage());
        }

        return modelo;
    }
}
***************
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Main.java to edit this template
 */
package kevin;

import java.sql.Connection;
import java.sql.DriverManager;
import javax.swing.JOptionPane;

/**
 *
 * @author Admin
 */
public class Conexion {
     Connection conectar;
    public Connection conectar(){
        try {
            Class.forName("com.mysql.cj.jdbc.Driver"); 

            conectar=DriverManager.getConnection(
                "jdbc:mysql://localhost/pandashina",
                "root",
                ""
            );

            JOptionPane.showMessageDialog(null, "Conectado");

        } catch (Exception ex) {
            JOptionPane.showMessageDialog(null, ex);
        }
       return conectar; 
    }
}
********************
 private void jBtnConexionActionPerformed(java.awt.event.ActionEvent evt) {                                             
      Conexion cn= new Conexion();
        cn.conectar();
    }                                            

    private void jTxtNombreAutorActionPerformed(java.awt.event.ActionEvent evt) {                                                
        // TODO add your handling code here:
    }                                               

    private void jTxtidlibroActionPerformed(java.awt.event.ActionEvent evt) {                                            
        // TODO add your handling code here:
    }                                           

    private void jBtnVerAutoresActionPerformed(java.awt.event.ActionEvent evt) {                                               
        // TODO add your handling code here:
        AutorDAO autor = new AutorDAO();
jTabla.setModel(autor.mostrarAutores());
    }                                              

    private void jBtnVerlibrosActionPerformed(java.awt.event.ActionEvent evt) {                                              
       LibroDAO libro = new LibroDAO();
jTabla.setModel(libro.mostrarLibros());
    }                                             

    private void jBtnAceptarAutorActionPerformed(java.awt.event.ActionEvent evt) {                                                 
         jTabla.setModel(new AutorDAO().aceptarAutor(
            jTxtid_autorN.getText(),
            jTxtNombreAutor.getText(),
            jTxtNacionalidad.getText()
    ));
    }                                                

    private void jBtnAceptarLibroActionPerformed(java.awt.event.ActionEvent evt) {                                                 
        LibroDAO dao = new LibroDAO();
String mensaje = dao.ingresarLibro(
        jTxtidlibro.getText(),
        jTxttitulo.getText(),
        jTxtanio.getText(),
        jTxtid_autor.getText()
);
javax.swing.JOptionPane.showMessageDialog(this, mensaje);

    jTabla.setModel(dao.mostrarLibros());

    }    
