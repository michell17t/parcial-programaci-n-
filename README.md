# parcial-programacion
---------------------------------------------------------------Biblioteca-------------------------------------------------------------------------------
package com.uniminuto.biblioteca.modelo;

import java.util.ArrayList;
import java.util.Scanner;

// Clase principal de logica del sistema
public class Biblioteca {

    private ArrayList<Libro> libros;
    private ArrayList<Multa> multas;
    private Scanner scanner;

    public Biblioteca() {
        libros = new ArrayList<>();
        multas = new ArrayList<>();
        scanner = new Scanner(System.in);

        // Libros iniciales
        libros.add(new Libro("Cien Anos de Soledad", "Gabriel Garcia Marquez", "Realismo magico"));
        libros.add(new Libro("El Principito", "Antoine de Saint-Exupery", "Ficcion"));
        libros.add(new Libro("Don Quijote de la Mancha", "Miguel de Cervantes", "Clasico"));
    }

    public void menu() {
        int opcion;
        do {
            System.out.println("\n===== MENU BIBLIOTECA =====");
            System.out.println("1. Ver libros disponibles");
            System.out.println("2. Prestar libro");
            System.out.println("3. Devolver libro");
            System.out.println("4. Ver multas y pagar");
            System.out.println("5. Salir");
            System.out.print("Seleccione una opcion: ");
            opcion = scanner.nextInt();
            scanner.nextLine();

            switch (opcion) {
                case 1 -> mostrarLibros();
                case 2 -> prestarLibro();
                case 3 -> devolverLibro();
                case 4 -> mostrarYPagarMultas();
                case 5 -> System.out.println("Saliendo del sistema...");
                default -> System.out.println("Opcion invalida.");
            }
        } while (opcion != 5);
    }

    private void mostrarLibros() {
        System.out.println("\nLista de libros:");
        for (int i = 0; i < libros.size(); i++) {
            System.out.print((i + 1) + ". ");
            libros.get(i).mostrarInfo();
        }
    }

    private void prestarLibro() {
        mostrarLibros();
        System.out.print("\nIngrese el numero del libro a prestar: ");
        int indice = scanner.nextInt() - 1;

        if (indice >= 0 && indice < libros.size()) {
            Libro libro = libros.get(indice);
            if (!libro.isPrestado()) {
                libro.setPrestado(true);
                System.out.println("Has prestado el libro: " + libro.getTitulo());
            } else {
                System.out.println("El libro ya esta prestado.");
            }
        } else {
            System.out.println("Opcion invalida.");
        }
    }

    private void devolverLibro() {
        mostrarLibros();
        System.out.print("\nIngrese el numero del libro a devolver: ");
        int indice = scanner.nextInt() - 1;

        if (indice >= 0 && indice < libros.size()) {
            Libro libro = libros.get(indice);
            if (libro.isPrestado()) {
                System.out.print("Ingrese los dias de retraso (0 si fue a tiempo): ");
                int dias = scanner.nextInt();
                libro.setPrestado(false);
                System.out.println("Has devuelto el libro: " + libro.getTitulo());

                if (dias > 0) {
                    double monto = dias * 1000; // $1000 por dia
                    Multa multa = new Multa(monto, libro.getTitulo());
                    multas.add(multa);
                    System.out.println("Se genero una multa de $" + monto + " por retraso.");
                }
            } else {
                System.out.println("El libro no estaba prestado.");
            }
        } else {
            System.out.println("Opcion invalida.");
        }
    }

    private void mostrarYPagarMultas() {
        if (multas.isEmpty()) {
            System.out.println("\nNo hay multas registradas.");
            return;
        }

        System.out.println("\nMultas registradas:");
        for (Multa multa : multas) {
            multa.mostrarMulta();
        }

        System.out.print("\nDesea pagar una multa? (si/no): ");
        String respuesta = scanner.nextLine();

        if (respuesta.equalsIgnoreCase("si")) {
            System.out.print("Ingrese el titulo del libro con multa: ");
            String titulo = scanner.nextLine();

            boolean encontrada = false;
            for (Multa multa : multas) {
                if (multa.getTituloLibro().equalsIgnoreCase(titulo)) {
                    multa.pagar();
                    encontrada = true;
                    break;
                }
            }

            if (!encontrada) {
                System.out.println("No se encontro una multa para ese titulo.");
            }
        }
    }
}
----------------------------------------------------------------------------------------Libro----------------------------------------------------------------
package com.uniminuto.biblioteca.modelo;

// Herencia: Libro extiende de MaterialBibliografico
public class Libro extends MaterialBibliografico {

    private String genero;

    public Libro(String titulo, String autor, String genero) {
        super(titulo, autor);
        this.genero = genero;
    }

    public String getGenero() {
        return genero;
    }

    // Polimorfismo: sobrescribe metodo del padre
    @Override
    public void mostrarInfo() {
        String estado = isPrestado() ? "Prestado" : "Disponible";
        System.out.println("Titulo: " + getTitulo() + ", Autor: " + getAutor() + ", Genero: " + genero + " [" + estado + "]");
    }
}
-----------------------------------------------------------------------------------------Multa------------------------------------------------------------------
package com.uniminuto.biblioteca.modelo;

// Clase para manejar las multas
public class Multa {

    private double monto;
    private boolean pagada;
    private String tituloLibro; // Asociacion con el libro

    public Multa(double monto, String tituloLibro) {
        this.monto = monto;
        this.pagada = false;
        this.tituloLibro = tituloLibro;
    }

    public double getMonto() {
        return monto;
    }

    public boolean isPagada() {
        return pagada;
    }

    public String getTituloLibro() {
        return tituloLibro;
    }

    public void pagar() {
        if (!pagada) {
            pagada = true;
            System.out.println("La multa del libro '" + tituloLibro + "' ha sido pagada exitosamente.");
        } else {
            System.out.println("La multa del libro '" + tituloLibro + "' ya fue pagada anteriormente.");
        }
    }

    public void mostrarMulta() {
        String estado = pagada ? "Pagada" : "Pendiente";
        System.out.println("Libro: " + tituloLibro + " | Multa: $" + monto + " - Estado: " + estado);
    }
}
---------------------------------------------------------------------------------------------MaterialBibliografico------------------------------------------------
package com.uniminuto.biblioteca.modelo;

// Clase base (Herencia)
public class MaterialBibliografico {

    // Atributos (Encapsulamiento)
    private String titulo;
    private String autor;
    private boolean prestado;

    // Constructor
    public MaterialBibliografico(String titulo, String autor) {
        this.titulo = titulo;
        this.autor = autor;
        this.prestado = false;
    }

    // Metodos getters y setters
    public String getTitulo() {
        return titulo;
    }

    public String getAutor() {
        return autor;
    }

    public boolean isPrestado() {
        return prestado;
    }

    public void setPrestado(boolean prestado) {
        this.prestado = prestado;
    }

    // Metodo polimorfico
    public void mostrarInfo() {
        System.out.println("Titulo: " + titulo + ", Autor: " + autor);
    }
}
----------------------------------------------------------------------------BibliotecaApp-----------------------------------------------------------------------------
package com.uniminuto.biblioteca.modelo;

// Clase principal (punto de entrada del programa)
public class BibliotecaApp {
    public static void main(String[] args) {
        Biblioteca biblioteca
 = new Biblioteca();
        biblioteca.menu();
    }
}
