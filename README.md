# devoir-acces-fichier

#include <stdio.h>

#include <stdlib.h>

#include <string.h>


#define MAX_LINE_LENGTH 1024

#define DEFAULT_TAIL_LINES 10

void tail(FILE *file, int n);

int main(int argc, char *argv[]) 
{
    if (argc < 2)
     {
        fprintf(stderr, "Usage: %s filename [n]\n", argv[0]);
        return 1;
    }

    char *filename = argv[1];
    int n = (argc >= 3) ? atoi(argv[2]) : DEFAULT_TAIL_LINES;

    FILE *file = fopen(filename, "r");
    if (!file) 
    {
        perror("fopen");
        return 1;
    }

    tail(file, n);

    fclose(file);
    return (0);
}
void tail(FILE *file, int n)
 {
    char **a = malloc(n * sizeof(char *));
    int ligne_lue = 0;
    size_t len = 0;

    for (int i = 0; i < n; i++)
     {
        a[i] = NULL;
    }

    char *ligne= NULL;
    while (getline(&ligne, &len, file) != -1) 
    {
        if (a[ligne_lue % n] != NULL)
        {
            free(a[ligne_lue % n]);
        }
        a[ligne_lue % n] = strdup(ligne);
        ligne_lue++;
    }
    free(ligne);

    int start = ligne_lue >= n ? ligne_lue % n : 0;
    int lines_to_print = ligne_lue< n ? ligne_lue : n;

    for (int i = 0; i < lines_to_print; i++) 
    {
        printf("%s", a[(start + i) % n]);
        free(a[(start + i) % n]);
    }
    free(a);
}
