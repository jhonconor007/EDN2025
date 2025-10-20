# EDDSG1-2025
🛫 Gestión de Pasajeros en C Programa en C que administra la venta de tiquetes y el abordaje de pasajeros usando listas enlazadas. Permite establecer capacidad, sobreventa del 10%, y mostrar quién abordó o no.

✈️ Gestión de Memoria Dinámica y Listas Enlazadas
Sistema de Tiquetes de Avión

Este proyecto implementa un simulador de gestión de venta de tiquetes y abordaje de un avión en lenguaje C, aplicando los conceptos de memoria dinámica y listas enlazadas vistos en clase.

🧠 Objetivo del Taller

Aplicar el manejo de memoria dinámica mediante la creación, recorrido y liberación de una lista enlazada simple que almacena los pasajeros de un vuelo, controlando la venta de tiquetes y el abordaje del avión.

🚀 1. Requisitos del Programa
✈️ Capacidad y Sobreventa

El programa solicita al inicio la capacidad máxima del avión.

Se permite la venta de hasta un 10 % adicional de tiquetes (sobreventa).

👤 Datos del Pasajero

Cada pasajero almacena:

Primer apellido

Género: Femenino / Masculino / No Binario

🪪 Proceso de Abordaje

Los pasajeros abordan en orden de compra.

El abordaje se detiene al alcanzar la capacidad máxima (sin incluir la sobreventa).

Una vez iniciado el abordaje, no se pueden vender más tiquetes.

🧭 2. Funcionalidades del Menú
Opción	Descripción
1. Establecer Capacidad	Define la capacidad del avión (solo una vez).
2. Vender Tiquete	Registra un nuevo pasajero, si no se supera la sobreventa y el abordaje no ha comenzado.
3. Iniciar Abordaje	Marca como abordados a los primeros pasajeros hasta llenar la capacidad.
4. Ver Abordados	Muestra los pasajeros que subieron al avión.
5. Ver No Abordados	Muestra los pasajeros con tiquete que no pudieron abordar.
6. Salir	Finaliza el programa y libera la memoria usada.
💾 3. Gestión de Memoria

Se utilizan estructuras dinámicas (malloc, free) para manejar la lista enlazada de pasajeros.

Al salir, el programa libera toda la memoria asignada dinámicamente.




==================================================
typedef struct Pasajero {
    char apellido[30];
    char genero[15];
    int abordado; // 0: No abordado, 1: Abordado
    struct Pasajero *next;
} Pasajero;

==================================================
Cada nodo representa un pasajero y se enlaza al siguiente mediante el puntero next.
==================================================

INTEGRANTES : jhon freddy ballesteros lozano y Ana faisury García anizares


