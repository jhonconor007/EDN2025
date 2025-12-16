#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct pedido { /* cola fifo */
    char destino[30];
    int cantidad;
    struct pedido *siguiente;
} pedido;

typedef struct nodoavl { /* arbol avl */
    int fecha;
    char producto[30];
    int stock;
    int altura;

    pedido *cola; /* cola de pedidos asociada a cada lote */

    struct nodoavl *izquierda;
    struct nodoavl *derecha;
} nodoavl;

int altura(nodoavl *n){
    if (n == NULL) return 0;
    return n->altura;
}

int max(int a, int b){
    return (a > b) ? a : b;
}

/* |
   |
   v
   esta funcion sirve para liberar toda la memoria de una cola */

void liberarcola(pedido *p){
    while (p != NULL){
        pedido *alm = p; /*variable auxiliar */
        p = p->siguiente;
        free(alm);
    }
}

nodoavl *nuevonodo(int fecha, char producto[], int stock){
    nodoavl *n = (nodoavl *)malloc(sizeof(nodoavl));
    if (n == NULL){
        perror("error al asignar memoria");
        exit(EXIT_FAILURE);
    }

    n->fecha = fecha;
    /* se prioriza la fecha de vencimiento
       izquierda = fechas mas antiguas */
    strcpy(n->producto, producto);
    n->stock = stock;
    n->cola = NULL;
    n->altura = 1;
    n->izquierda = NULL;
    n->derecha = NULL;

    return n;
}

nodoavl *rotarderecha(nodoavl *y){
    nodoavl *x = y->izquierda;
    nodoavl *t2 = x->derecha;

    /* rotacion */
    x->derecha = y;
    y->izquierda = t2;

    /* actualizar alturas */
    y->altura = max(altura(y->izquierda), altura(y->derecha)) + 1;
    x->altura = max(altura(x->izquierda), altura(x->derecha)) + 1;

    return x;
}

nodoavl *rotarizquierda(nodoavl *x){
    nodoavl *y = x->derecha;
    nodoavl *t2 = y->izquierda;

    /* rotacion */
    y->izquierda = x;
    x->derecha = t2;

    /* actualizar alturas */
    x->altura = max(altura(x->izquierda), altura(x->derecha)) + 1;
    y->altura = max(altura(y->izquierda), altura(y->derecha)) + 1;

    return y;
}

/* funcion que calcula el balance del arbol */
int balance(nodoavl *n){
    if (n == NULL) return 0;
    return altura(n->izquierda) - altura(n->derecha);
}

/* |
   |
   v
   aqui va el recibimiento de la mercancia */

nodoavl *insertaravl(nodoavl *raiz, int fecha, char producto[], int stock){
    if (raiz == NULL)
        return nuevonodo(fecha, producto, stock);

    if (fecha < raiz->fecha)
        raiz->izquierda = insertaravl(raiz->izquierda, fecha, producto, stock);
    else if (fecha > raiz->fecha)
        raiz->derecha = insertaravl(raiz->derecha, fecha, producto, stock);
    else{
        printf("⚠ la fecha %d ya existe\n", fecha);
        return raiz;
    }

    raiz->altura = 1 + max(altura(raiz->izquierda), altura(raiz->derecha));
    int b = balance(raiz);

    /* balanceo del arbol */
    if (b > 1 && fecha < raiz->izquierda->fecha)
        return rotarderecha(raiz);

    if (b < -1 && fecha > raiz->derecha->fecha)
        return rotarizquierda(raiz);

    return raiz;
}

/* cola fifo de pedidos de despacho */

void encolarpedido(nodoavl *nodo){
    pedido *nuevo = (pedido *)malloc(sizeof(pedido));
    if (nuevo == NULL){
        perror("error de memoria");
        return;
    }

    /* limpiar buffer de entrada */
    int c;
    while ((c = getchar()) != '\n' && c != EOF);

    printf("Destino: ");
    scanf(" %s", nuevo->destino);

    printf("Cantidad solicitada: ");
    scanf("%d", &nuevo->cantidad);

    if (nodo == NULL || nuevo->cantidad <= 0 || nuevo->cantidad > nodo->stock){
        printf("Cantidad invalida o stock insuficiente\n");
        free(nuevo);
        return;
    }

    /* descuento de stock y agregado FIFO */
    nodo->stock -= nuevo->cantidad;
    nuevo->siguiente = NULL;

    if (nodo->cola == NULL){
        nodo->cola = nuevo;
    } else{
        pedido *aux = nodo->cola;
        while (aux->siguiente != NULL){
            aux = aux->siguiente;
        }
        aux->siguiente = nuevo;
    }

    printf("✅ Pedido registrado. Stock restante: %d\n", nodo->stock);
}



/* funcion simple para buscar un lote por fecha */
nodoavl *buscar(nodoavl *raiz, int fecha){
    if (raiz == NULL || raiz->fecha == fecha)
        return raiz;
    if (fecha < raiz->fecha)
        return buscar(raiz->izquierda, fecha);
    return buscar(raiz->derecha, fecha);
}

/* ===================== MAIN Y MENU ===================== */

int main(){

    nodoavl *raiz = NULL;
    int opcion;

    do{
        printf(" SISTEMA LOGISTICO ");
        printf("1. Recibir mercancia (insertar lote)\n");
        printf("2. Registrar pedido de despacho\n");
        printf("3. Salir\n");
        printf("Seleccione una opcion: ");
        scanf("%d" , &opcion);

        if (opcion == 1){
            int fecha, stock;
            char producto[30];

            printf("Fecha de vencimiento: ");
            scanf("%d", &fecha);
            printf("Producto: ");
            scanf("%s", producto);
            printf("Stock: ");
            scanf("%d", &stock);

            raiz = insertaravl(raiz, fecha, producto, stock);
        
            printf("✅ Lote registrado correctamente\n");
        }

        else if (opcion == 2){
            int fecha;
            printf("Ingrese la fecha del lote: ");
            scanf("%d", &fecha);

            nodoavl *lote = buscar(raiz, fecha);
            if (lote == NULL){
                printf("❌ No existe un lote con esa fecha\n");
            } else{
                encolarpedido(lote);
            }
        }

    } while (opcion != 3);

    printf("Programa finalizado.\n");
    return 0;
}
