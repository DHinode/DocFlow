# MPI

## Compilation 

```bash
mpicc -o programme programme.c
```

## Exécution 

```bash
mpirun --oversubscribe -np <nb_proc> ./programme
```

Il est recommendé de faire un alias : `alias mpirun="mpirun --oversubscribe"`

## Exécution sur plusieurs machine

```bash
mpirun --hostfile <file> -np <nb_proc> ./programme
```

## Primitives de Bases

```c
#include <mpi.h>
int MPI_Init(int* argc, char** argv);
int MPI_Finalize();
```

## Communicateur

Type : `MPI_Comm`  
- `MPI_COMM_WORLD` : communicateur regroupant l'ensemble des processus du système.  
- `int MPI_Comm_size(MPI_Comm comm, int *size);` : renvoie dans `size` le nombre de processus présents dans le communicateur `comm`.  
- `int MPI_Comm_rank(MPI_Comm comm, int *rank);` : renvoie dans `rank` le numéro dans le communicateur `comm`.

## Type de données

| **MPI**               | **C**               |
|-----------------------|---------------------|
| `MPI_CHAR`            | `signed char`       |
| `MPI_SHORT`           | `signed short`      |
| `MPI_INT`             | `signed int`        |
| `MPI_LONG`            | `signed long`       |
| `MPI_UNSIGNED_CHAR`   | `unsigned char`     |
| `MPI_UNSIGNED_SHORT`  | `unsigned short`    |
| `MPI_UNSIGNED`        | `unsigned int`      |
| `MPI_UNSIGNED_LONG`   | `unsigned long`     |
| `MPI_FLOAT`           | `float`             |
| `MPI_DOUBLE`          | `double`            |
| `MPI_LONG_DOUBLE`     | `long double`       |

## Objet `MPI_Status`

- `status.MPI_TAG` : accès à la valeur du tag   
- `status.MPI_SOURCE` : accès à l'identité de l'émetteur  
- `MPI_Get_Count(&status, datatype, &count);` : renvoie dans `count` le nombre d’objets de type `datatype` reçus  

> Les flags peuvent influencer les messages reçu. 

## Jokers

- `MPI_ANY_SOURCE` : recevoir un message de n'importe qui  
- `MPI_ANY_TAG` : recevoir un message dont on ne connait pas le type a priori

## Primitives de communication MPI 

### Émission bloquante (retour lorque le buffer est réutilisable) :
```c
int MPI_Send(void *buf, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm);
```

### Réception bloquante : 
```c
int MPI_Recv(void *buf, int count, MPI_Datatype datatype, int source, int tag, MPI_Comm comm, MPI_Status *status);
```

### Communications collectives 

- `MPI_Bcast` : broadcast 
- `MPI_Barrier` : barrière de synchronisation 
- `MPI_Scatter` : distribution de données  
- `MPI_Gather` : rassemblement de données 
- `MPI_Reduce` : réduction (combinaison des données de plusieurs processus pour obtenir un résultat) 


```c
int MPI_Bcast(void *buffer, int count, MPI_Datatype datatype, int root, MPI_Comm comm);
```
- `root` : id du processus source

```c
int MPI_Init_thread(int *argc, char **argv, int required, int *provided);
```
- `required` : niveaux d'exécution 
  - `MPI_THREAD_SINGLE` : un seul thread par processus (équivalent à `MPI_Init`)
  - `MPI_THREAD_FUNNELED` : plusieurs threads, mais un seul autorisé à utiliser les primitives MPI (celui qui a appelé `MPI_Init_thread`)
  - `MPI_THREAD_SERIALIZED` : plusieurs threads, mais les appels aux primitives MPI se font de manière exclusive : au plus un seul appel au même moment
  - `MPI_THREAD_MULTIPLE` : plusieurs threads peuvent appeler les primitives MPI sans aucune restriction. *(Actuellement peu testé et encore instable !)*
- `provided` : paramètre résultat indiquant le niveau disponible sur les machines (dépend de l'implémentation et de la version) 


### Autres 

- **Bloquantes :**
  - **Asynchrone**
    - **Émission (`MPI_Send`)** : L’émission se termine lorsque le message a bien été envoyé sur le réseau, le buffer d’émission peut ainsi être réutilisé.
    - **Réception (`MPI_Recv`)** : La réception se termine lorsque les données attendues sont disponibles dans le buffer de réception.

  - **Synchrone**
    - **Émission (`MPI_Ssend`)** : L’émission se termine lorsque le destinataire a fait sa réception.

- **Non bloquantes** : Permet de « recouvrir » les communications par le calcul. (`MPI_Isend`, `MPI_Irecv` et `MPI_Issend`)


## Tester le buffer de réception 

> Possibilité de savoir si un message est présent sans le consommer

### Version bloquante 

```c
int MPI_Probe(int source, int tag, MPI_Comm comm, MPI_Status *status);
```

### Version non-bloquante 

```c
int MPI_Iprobe(int source, int tag, MPI_Comm comm, int *flag,MPI_Status status);
```

- `flag` : paramètre résultat, vaut 0 s'il n'y a pas de message du processus source avec l'étiquette tag dans le buffer de réception


## Exemple 

```c
#include <stdio.h>
#include <string.h>
#define BUFLEN 20
#include <mpi.h>

int main(int argc, char *argv[]) {
    char *msg = "Hello World!";
    char buf[BUFLEN];
    int rank;
    MPI_Status status;

    MPI_Init(&argc, &argv);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);

    printf("rank = %d: ", rank);
    if (rank == 0) {
        MPI_Recv(buf, BUFLEN, MPI_CHAR, MPI_ANY_SOURCE, 99, MPI_COMM_WORLD, &status);
        printf("received from %d: %s\n", status.MPI_SOURCE, buf);
    } else {
        printf("send to 0: %s\n", msg);
        MPI_Send(msg, strlen(msg) + 1, MPI_CHAR, 0, 99, MPI_COMM_WORLD);
    }

    MPI_Finalize();
    return 0;
}
```

### Chang & Roberts
```c
#include <stdio.h>
#include <string.h>
#include <mpi.h>
#include <stdlib.h>
#include <unistd.h>

#define SIZE 2
#define ELEC 0
#define LEADER 1

#define NON_CANDIDAT 0
#define CANDIDAT 1
#define ELU 2
#define PERDU 3

void proposer_candidature(int myrank, int nb_proc, int *etat, int *leader) {
    *etat = CANDIDAT;
    *leader = myrank;
    int dest = (myrank + 1) % nb_proc;
    int message[2] = {ELEC, *leader}; // Message format: [Type, Leader ID]
    
    printf("Processus %d se porte candidat et envoie (%d, %d) à %d\n", myrank, message[0], message[1], dest);
    MPI_Send(message, 2, MPI_INT, dest, 0, MPI_COMM_WORLD);
}

int main(int argc, char **argv) {
    int my_rank, nb_proc;
    MPI_Status status;
    
    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &nb_proc);
    MPI_Comm_rank(MPI_COMM_WORLD, &my_rank);

    srand(getpid());
    int initiateur = rand() % nb_proc; // Un processus aléatoire sera initiateur

    int leader = -1; // Aucun leader au début
    int etat = NON_CANDIDAT;

    if (my_rank == initiateur) {
        proposer_candidature(my_rank, nb_proc, &etat, &leader);
    }

    while (1) {
        int message[2];
        MPI_Recv(message, 2, MPI_INT, MPI_ANY_SOURCE, 0, MPI_COMM_WORLD, &status);
        int type = message[0];
        int id_recu = message[1];

        printf("Processus %d reçoit (%d, %d) de %d\n", my_rank, type, id_recu, status.MPI_SOURCE);

        if (type == ELEC) {
            if (id_recu > my_rank) {
                etat = PERDU;
                leader = id_recu;
                int dest = (my_rank + 1) % nb_proc;
                printf("Processus %d relaie (%d, %d) à %d\n", my_rank, type, id_recu, dest);
                MPI_Send(message, 2, MPI_INT, dest, 0, MPI_COMM_WORLD);
            } else if (id_recu < my_rank && etat == NON_CANDIDAT) {
                proposer_candidature(my_rank, nb_proc, &etat, &leader);
            } else if (id_recu == my_rank) {
                etat = ELU;
                leader = my_rank;
                int dest = (my_rank + 1) % nb_proc;
                int leader_msg[2] = {LEADER, leader};
                printf("Processus %d devient leader et envoie (%d, %d) à %d\n", my_rank, LEADER, leader, dest);
                MPI_Send(leader_msg, 2, MPI_INT, dest, 0, MPI_COMM_WORLD);
            }
        } else if (type == LEADER) {
            leader = id_recu;
            if (my_rank != leader) {
                int dest = (my_rank + 1) % nb_proc;
                printf("Processus %d relaie le message leader (%d, %d) à %d\n", my_rank, LEADER, leader, dest);
                MPI_Send(message, 2, MPI_INT, dest, 0, MPI_COMM_WORLD);
            }
            break;
        }
    }

    printf("Processus %d: Leader élu est %d\n", my_rank, leader);
    
    MPI_Finalize();
    return 0;
}
```
