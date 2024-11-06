# Gestor de Inventario

Este proyecto implementa un sistema básico de gestión de inventario para una tienda, utilizando C++. Incluye funcionalidades para agregar, buscar, actualizar, eliminar y contar productos. La organización de los productos se realiza mediante diferentes estructuras de datos para optimizar el manejo del inventario.

## Características

- **Agregar Producto**: Agrega un nuevo producto al inventario.
- **Buscar Producto**: Busca un producto específico por ID.
- **Actualizar Producto**: Modifica la cantidad de un producto existente.
- **Eliminar Producto**: Elimina un producto del inventario.
- **Contar Productos**: Muestra el total de productos en el inventario.
- **Mostrar Inventario Completo**: Lista todos los productos en orden.
- **Historial de Cambios

#### Estructuras de Datos Utilizadas

- **unordered_map**: Para almacenar productos por ID, permitiendo búsqueda rápida.
- **BST**: Para organizar los productos en un árbol binario, permitiendo listar los productos en orden.
- **list**: Para iteración secuencial.
- **stack**: Para almacenar el historial de cambios.

## Instrucciones de Uso

1. **Compilación**: Compila el programa usando un compilador de C++. Ejemplo:
   ```bash
   g++ inventario.cpp -o 
   
   Una vez que el archivo se haya compilado sin errores, ejecuta el programa en la terminal con:
   ./TiendaLasCumbres

## CODIGO!

   #include <iostream>
#include <string>
#include <unordered_map>
#include <list>
#include <stack>
#include <memory>

// Clase Producto
class Producto {
public:
    int id;
    std::string nombre;
    int cantidad;
    float precio;

    Producto(int id, const std::string& nombre, int cantidad, float precio)
        : id(id), nombre(nombre), cantidad(cantidad), precio(precio) {}
};

// Nodo para el Árbol Binario de Búsqueda (BST)
class NodoBST {
public:
    std::shared_ptr<Producto> producto;
    NodoBST* izquierdo;
    NodoBST* derecho;

    NodoBST(std::shared_ptr<Producto> prod) : producto(prod), izquierdo(nullptr), derecho(nullptr) {}
};

// Clase para gestionar el Árbol Binario de Búsqueda
class BST {
private:
    NodoBST* raiz;

    NodoBST* insertar(NodoBST* nodo, std::shared_ptr<Producto> producto) {
        if (!nodo) return new NodoBST(producto);
        if (producto->id < nodo->producto->id) {
            nodo->izquierdo = insertar(nodo->izquierdo, producto);
        } else if (producto->id > nodo->producto->id) {
            nodo->derecho = insertar(nodo->derecho, producto);
        }
        return nodo;
    }

    void mostrar(NodoBST* nodo) {
        if (nodo) {
            mostrar(nodo->izquierdo);
            std::cout << "Producto ID: " << nodo->producto->id << " - " << nodo->producto->nombre << "\n";
            mostrar(nodo->derecho);
        }
    }

public:
    BST() : raiz(nullptr) {}

    void insertarProducto(std::shared_ptr<Producto> producto) {
        raiz = insertar(raiz, producto);
    }

    void mostrarProductos() {
        mostrar(raiz);
    }
};

// Clase Inventario
class Inventario {
private:
    std::unordered_map<int, std::shared_ptr<Producto>> productosMap; // Búsqueda rápida por ID
    BST productosBST; // Búsqueda y organización en Árbol Binario
    std::list<std::shared_ptr<Producto>> productosLista; // Lista para iteración ordenada
    std::stack<std::string> historialCambios; // Pila para historial de cambios

public:
    void agregarProducto(int id, const std::string& nombre, int cantidad, float precio) {
        auto nuevoProducto = std::make_shared<Producto>(id, nombre, cantidad, precio);
        productosMap[id] = nuevoProducto;
        productosBST.insertarProducto(nuevoProducto);
        productosLista.push_back(nuevoProducto);
        historialCambios.push("Producto agregado: " + nombre);
        std::cout << "Producto agregado exitosamente.\n";
    }

    void buscarProducto(int id) {
        if (productosMap.find(id) != productosMap.end()) {
            auto producto = productosMap[id];
            std::cout << "Producto encontrado: " << producto->nombre << ", Cantidad: " << producto->cantidad << ", Precio: $" << producto->precio << "\n";
        } else {
            std::cout << "Producto no encontrado.\n";
        }
    }

    void actualizarProducto(int id, int nuevaCantidad) {
        if (productosMap.find(id) != productosMap.end()) {
            productosMap[id]->cantidad = nuevaCantidad;
            historialCambios.push("Cantidad actualizada para Producto ID: " + std::to_string(id));
            std::cout << "Cantidad actualizada.\n";
        } else {
            std::cout << "Producto no encontrado.\n";
        }
    }

    void eliminarProducto(int id) {
        if (productosMap.find(id) != productosMap.end()) {
            auto nombre = productosMap[id]->nombre;
            productosMap.erase(id);
            historialCambios.push("Producto eliminado: " + nombre);
            std::cout << "Producto eliminado.\n";
        } else {
            std::cout << "Producto no encontrado.\n";
        }
    }

    int contarProductos() {
        return productosMap.size();
    }

    void mostrarInventario() {
        std::cout << "\nInventario Completo:\n";
        productosBST.mostrarProductos();
    }

    void mostrarHistorialCambios() {
        std::cout << "\nHistorial de Cambios:\n";
        auto historial = historialCambios;  // Copia de la pila para visualizarla sin borrar
        while (!historial.empty()) {
            std::cout << historial.top() << "\n";
            historial.pop();
        }
    }
};

// Función para mostrar el menú
void mostrarMenu() {
    std::cout << "\n--- Menú de Inventario ---\n";
    std::cout << "1. Agregar Producto\n";
    std::cout << "2. Buscar Producto\n";
    std::cout << "3. Actualizar Cantidad de Producto\n";
    std::cout << "4. Eliminar Producto\n";
    std::cout << "5. Contar Productos\n";
    std::cout << "6. Mostrar Inventario Completo\n";
    std::cout << "7. Mostrar Historial de Cambios\n";
    std::cout << "8. Salir\n";
    std::cout << "Seleccione una opción: ";
}

int main() {
    Inventario inventario;
    int opcion;

    do {
        mostrarMenu();
        std::cin >> opcion;

        switch (opcion) {
            case 1: {
                int id, cantidad;
                float precio;
                std::string nombre;
                std::cout << "Ingrese ID del producto: ";
                std::cin >> id;
                std::cin.ignore();
                std::cout << "Ingrese nombre del producto: ";
                std::getline(std::cin, nombre);
                std::cout << "Ingrese cantidad: ";
                std::cin >> cantidad;
                std::cout << "Ingrese precio: ";
                std::cin >> precio;
                inventario.agregarProducto(id, nombre, cantidad, precio);
                break;
            }
            case 2: {
                int id;
                std::cout << "Ingrese ID del producto a buscar: ";
                std::cin >> id;
                inventario.buscarProducto(id);
                break;
            }
            case 3: {
                int id, nuevaCantidad;
                std::cout << "Ingrese ID del producto a actualizar: ";
                std::cin >> id;
                std::cout << "Ingrese nueva cantidad: ";
                std::cin >> nuevaCantidad;
                inventario.actualizarProducto(id, nuevaCantidad);
                break;
            }
            case 4: {
                int id;
                std::cout << "Ingrese ID del producto a eliminar: ";
                std::cin >> id;
                inventario.eliminarProducto(id);
                break;
            }
            case 5: {
                std::cout << "Total de productos en inventario: " << inventario.contarProductos() << "\n";
                break;
            }
            case 6: {
                inventario.mostrarInventario();
                break;
            }
            case 7: {
                inventario.mostrarHistorialCambios();
                break;
            }
            case 8: {
                std::cout << "Saliendo del programa...\n";
                break;
            }
            default:
                std::cout << "Opción no válida. Intente de nuevo.\n";
        }
    } while (opcion != 8);

    return 0;
}
