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


