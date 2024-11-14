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

## Resumen

Este programa demuestra cómo usar `mmap` para compartir memoria entre un proceso padre e hijo, permitiendo que el hijo lea un mensaje escrito por el padre en la memoria compartida.
Si el valor devuelto es 0, significa que estamos en el proceso hijo. En este caso, leemos y mostramos la memoria compartida y luego liberamos la memoria utilizando la función munmap().

Si el valor devuelto por fork() es mayor que 0, significa que estamos en el proceso padre. En este caso, escribimos en la memoria compartida, esperamos a que el proceso hijo termine y luego liberamos la memoria.

Espero que esta explicación te ayude a entender mejor el código. Si tienes más preguntas, no dudes en hacerlas.
