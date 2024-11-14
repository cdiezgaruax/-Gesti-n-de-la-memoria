# Gestión de la Memoria

## Enlace del Proyecto

Repositorio de GitHub: [Gestión de la Memoria](https://github.com/cdiezgaruax/-Gesti-n-de-la-memoria)

## Enunciado

Este programa en C crea una región de memoria compartida entre un proceso padre e hijo utilizando `mmap`. A continuación, se muestra el código completo:

```c
#include <sys/mman.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>

#define SIZE 4096

int main() {
    char *shared_memory = mmap(NULL, SIZE, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
    if (shared_memory == MAP_FAILED) {
        perror("mmap");
        exit(EXIT_FAILURE);
    }
    
    pid_t pid = fork();
    
    if (pid < 0) {
        perror("fork");
        exit(EXIT_FAILURE);
    } else if (pid == 0) {
        printf("Child reads: %s\n", shared_memory);
        munmap(shared_memory, SIZE);
        exit(EXIT_SUCCESS);
    } else {
        strcpy(shared_memory, "Hello, child process!");
        wait(NULL);
        munmap(shared_memory, SIZE);
    }

    return 0;
}
```

## Explicación Paso a Paso

1. **Importación de Bibliotecas**:  
   Se incluyen las bibliotecas necesarias para la asignación de memoria (`sys/mman.h`), manejo de procesos (`unistd.h`, `sys/wait.h`), e impresión en consola (`stdio.h` y `stdlib.h`).

2. **Definición del Tamaño de Memoria**:  
   Definimos `SIZE` como una constante que indica el tamaño de la región de memoria compartida (4096 bytes).

3. **Asignación de Memoria Compartida con `mmap`**:  
   Llamamos a la función `mmap()` para asignar la memoria compartida entre el proceso padre y el hijo. Los argumentos de `mmap()` son:
   - `NULL`: Permitimos que el sistema operativo elija la dirección de inicio de la región de memoria.
   - `SIZE`: El tamaño de la región de memoria.
   - `PROT_READ | PROT_WRITE`: Habilitamos permisos de lectura y escritura en la región de memoria.
   - `MAP_SHARED | MAP_ANONYMOUS`: 
     - `MAP_SHARED` permite que la memoria sea compartida entre procesos.
     - `MAP_ANONYMOUS` indica que la memoria no estará asociada a un archivo.
   - `-1` y `0`: No usamos archivo para respaldar la memoria, por lo que ponemos `-1` y `0`.

   Si `mmap()` falla, retorna `MAP_FAILED`, y en ese caso imprimimos un error y terminamos el programa.

4. **Creación de un Nuevo Proceso con `fork()`**:  
   Usamos `fork()` para crear un proceso hijo:
   - **Proceso Hijo (PID = 0)**:  
     Lee el contenido de `shared_memory` y lo imprime. Luego, libera la memoria compartida con `munmap()` y finaliza.
   - **Proceso Padre**:  
     Escribe el mensaje `"Hello, child process!"` en la memoria compartida, espera la finalización del hijo con `wait(NULL)`, y luego libera la memoria.

## Configuración y Ejecución del Proyecto en C con WSL y CMake en Windows

Para compilar y ejecutar tu proyecto en C usando CMake en un entorno de Linux dentro de Windows, seguiste estos pasos:

### 1. Instalación de WSL (Windows Subsystem for Linux)

1. **Abrir PowerShell como Administrador** y ejecutar:
   ```bash
   wsl --install
   ```
   Esto instala WSL y configura una versión de Linux en tu sistema. Después de este paso, es posible que debas **reiniciar tu ordenador**.

2. **Instalar una Distribución de Linux**:
   - Abre Microsoft Store, busca una distribución como **Ubuntu** e instálala.
   - Una vez instalada, ábrela desde el menú de inicio y completa la configuración inicial creando un usuario y contraseña para Linux.

### 2. Configuración de WSL

1. **Actualizar los Paquetes de Linux**:
   Una vez que WSL está configurado y tienes acceso a tu distribución de Linux (como Ubuntu), ejecuta los siguientes comandos para actualizar los paquetes:
   ```bash
   sudo apt update
   sudo apt upgrade
   ```

2. **Instalar CMake y Herramientas de Desarrollo**:
   Para compilar proyectos en C, necesitas instalar `CMake` y `build-essential` (este último incluye herramientas como `gcc` y `make`):
   ```bash
   sudo apt install cmake build-essential
   ```

### 3. Configuración y Compilación de tu Proyecto

1. **Navegar al Directorio de tu Proyecto**:
   Si tu proyecto está en Windows, navega a él desde WSL utilizando la ruta `/mnt/c/...`. Por ejemplo:
   ```bash
   cd /mnt/c/path/to/your/project
   ```

2. **Crear un Directorio de Compilación y Ejecutar CMake**:
   Para mantener organizada la estructura del proyecto, crea un directorio `build` y compila el proyecto desde allí:
   ```bash
   mkdir build
   cd build
   cmake ..
   make
   ```

### 4. Ejecutar tu Proyecto

   Una vez compilado, ejecuta el archivo generado:
   ```bash
   ./GestionDeLaMemoria
   ```

---

Estos pasos completan la configuración de WSL y la compilación de tu proyecto en C usando CMake. Esto te permite trabajar en un entorno de Linux directamente en Windows, facilitando la compilación y ejecución de proyectos de C.
