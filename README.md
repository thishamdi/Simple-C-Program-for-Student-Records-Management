# Simple C Program for Student Records Management

This project provides a simple and user-friendly C program for managing student records. It allows you to:
- Add new student records.
- Display all stored records.
- Calculate average scores.
- Search for a student by name.
- Delete a record.
- Save records to a file for future use.
- Load records from a file to continue management.

The program is designed to practice fundamental C programming skills like file handling, loops, arrays, and basic string manipulation.

---

### **Code**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Define constants
#define MAX_ETUDIANTS 100 // Maximum number of students

// Structure for student information
typedef struct {
    char nom[50];        // Name and surname
    int age;             // Age
    float note1;         // Evaluation score 1
    float note2;         // Evaluation score 2
} Apprenant;

// Global variables
Apprenant apprenants[MAX_ETUDIANTS]; // Array to store student records
int nombreApprenants = 0;            // Counter for the number of students

// Function to add a new student record
void creerDossier() {
    if (nombreApprenants >= MAX_ETUDIANTS) {
        printf("\nError: Maximum number of students reached.\n");
        return;
    }

    printf("\n--- Add a New Record ---\n");
    printf("Enter the student's name and surname: ");
    scanf(" %[^\n]", apprenants[nombreApprenants].nom);

    printf("Enter the student's age: ");
    scanf("%d", &apprenants[nombreApprenants].age);

    printf("Enter Evaluation Score 1: ");
    scanf("%f", &apprenants[nombreApprenants].note1);

    printf("Enter Evaluation Score 2: ");
    scanf("%f", &apprenants[nombreApprenants].note2);

    nombreApprenants++;
    printf("\nRecord added successfully!\n");
}

// Function to display all student records
void afficherDossiers() {
    if (nombreApprenants == 0) {
        printf("\nNo records available.\n");
        return;
    }

    printf("\n--- List of Records ---\n");
    printf("No\tName and Surname\tAge\tScore 1\tScore 2\n");
    for (int i = 0; i < nombreApprenants; i++) {
        printf("%d\t%s\t\t%d\t%.2f\t%.2f\n", i + 1, apprenants[i].nom, apprenants[i].age, apprenants[i].note1, apprenants[i].note2);
    }
}

// Function to calculate and display average scores
void calculerMoyennes() {
    if (nombreApprenants == 0) {
        printf("\nNo records available to calculate averages.\n");
        return;
    }

    printf("\n--- Average Scores ---\n");
    for (int i = 0; i < nombreApprenants; i++) {
        float moyenne = (apprenants[i].note1 * 0.4) + (apprenants[i].note2 * 0.6);
        printf("Student %d (%s): Average = %.2f\n", i + 1, apprenants[i].nom, moyenne);
    }
}

// Function to search for a record by name
void rechercherDossier() {
    if (nombreApprenants == 0) {
        printf("\nNo records available for search.\n");
        return;
    }

    char nomRecherche[50];
    printf("\n--- Search Record ---\n");
    printf("Enter the name to search: ");
    scanf(" %[^\n]", nomRecherche);

    int found = 0;
    for (int i = 0; i < nombreApprenants; i++) {
        if (strstr(apprenants[i].nom, nomRecherche) != NULL) {
            printf("\nResult:\n");
            printf("No\tName and Surname\tAge\tScore 1\tScore 2\n");
            printf("%d\t%s\t\t%d\t%.2f\t%.2f\n", i + 1, apprenants[i].nom, apprenants[i].age, apprenants[i].note1, apprenants[i].note2);
            found = 1;
        }
    }

    if (!found) {
        printf("\nNo record found for the name: %s\n", nomRecherche);
    }
}

// Function to delete a record by its number
void supprimerDossier() {
    if (nombreApprenants == 0) {
        printf("\nNo records available to delete.\n");
        return;
    }

    int numero;
    printf("\n--- Delete Record ---\n");
    printf("Enter the record number to delete: ");
    scanf("%d", &numero);

    if (numero < 1 || numero > nombreApprenants) {
        printf("\nError: Invalid record number.\n");
        return;
    }

    for (int i = numero - 1; i < nombreApprenants - 1; i++) {
        apprenants[i] = apprenants[i + 1];
    }
    nombreApprenants--;

    printf("\nRecord number %d deleted successfully.\n", numero);
}

// Function to save records to a file
void sauvegarderDossiers() {
    if (nombreApprenants == 0) {
        printf("\nNo records available to save.\n");
        return;
    }

    char nomFichier[100];
    printf("\n--- Save Records to File ---\n");
    printf("Enter the file name (e.g., records.txt): ");
    scanf(" %[^\n]", nomFichier);

    FILE *fichier = fopen(nomFichier, "w");
    if (fichier == NULL) {
        printf("\nError: Unable to create the file.\n");
        return;
    }

    for (int i = 0; i < nombreApprenants; i++) {
        fprintf(fichier, "%s,%d,%.2f,%.2f\n", apprenants[i].nom, apprenants[i].age, apprenants[i].note1, apprenants[i].note2);
    }

    fclose(fichier);
    printf("\nRecords saved successfully to %s\n", nomFichier);
}

// Function to load records from a file
void chargerDossiers() {
    char nomFichier[100];
    printf("\n--- Load Records from File ---\n");
    printf("Enter the file name (e.g., records.txt): ");
    scanf(" %[^\n]", nomFichier);

    FILE *fichier = fopen(nomFichier, "r");
    if (fichier == NULL) {
        printf("\nError: Unable to open the file.\n");
        return;
    }

    char ligne[200];
    while (fgets(ligne, sizeof(ligne), fichier)) {
        if (nombreApprenants >= MAX_ETUDIANTS) {
            printf("\nError: Maximum student limit reached while loading.\n");
            break;
        }

        char nom[50];
        int age;
        float note1, note2;
        sscanf(ligne, "%[^,],%d,%f,%f", nom, &age, &note1, &note2);

        strcpy(apprenants[nombreApprenants].nom, nom);
        apprenants[nombreApprenants].age = age;
        apprenants[nombreApprenants].note1 = note1;
        apprenants[nombreApprenants].note2 = note2;
        nombreApprenants++;
    }

    fclose(fichier);
    printf("\nRecords loaded successfully from %s\n", nomFichier);
}

// Main function with menu
int main() {
    char choix;
    do {
        printf("\n--- Main Menu ---\n");
        printf("c) Add a new record\n");
        printf("a) Display records\n");
        printf("m) Calculate averages\n");
        printf("r) Search a record\n");
        printf("s) Delete a record\n");
        printf("v) Save records to a file\n");
        printf("l) Load records from a file\n");
        printf("q) Quit\n");
        printf("Enter your choice: ");
        scanf(" %c", &choix);

        switch (choix) {
            case 'c': creerDossier(); break;
            case 'a': afficherDossiers(); break;
            case 'm': calculerMoyennes(); break;
            case 'r': rechercherDossier(); break;
            case 's': supprimerDossier(); break;
            case 'v': sauvegarderDossiers(); break;
            case 'l': chargerDossiers(); break;
            case 'q': printf("\nProgram terminated. Goodbye!\n"); break;
            default: printf("\nInvalid choice. Try again.\n");
        }
    } while (choix != 'q');

    return 0;
}
```

---

### **Command Menu**

| Command | Action                          |
|---------|---------------------------------|
| `c`     | Add a new record                |
| `a`     | Display all records             |
| `m`     | Calculate average scores        |
| `r`     | Search for a student record     |
| `s`     | Delete a record                 |
| `v`     | Save records to a file          |
| `l`     | Load records from a file        |
| `q`     | Quit the program                |
