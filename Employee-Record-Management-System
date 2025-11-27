#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
struct Employee {
    int id;
    char name[50];
    char department[50];
    char salary[100];
};
#define CSV_FILE "employees.csv"
#define TEMP_FILE "temp.csv"
#define LINE_MAX 512
static void trim_newline(char *s) {
    size_t n = strlen(s);
    while (n > 0 && (s[n-1] == '\n' || s[n-1] == '\r')) {
        s[n-1] = '\0';
        n--;
    }
}
static void read_line(char *buf, size_t size) {
    if (fgets(buf, (int)size, stdin) == NULL) {
        buf[0] = '\0';
        return;
    }
    trim_newline(buf);
}
static int read_int(const char *prompt) {
    char buf[LINE_MAX];
    char *endptr;
    long val;
    while (1) {
        printf("%s", prompt);
        read_line(buf, sizeof(buf));
        if (buf[0] == '\0') {
            printf("Input cannot be empty. Please try again.\n");
            continue;
        }
        val = strtol(buf, &endptr, 10);
        if (*endptr == '\0') {
            return (int)val;
        } else {
            printf("You gave wrong data type. Please enter a valid integer.\n");
        }
    }
}
static void read_string(const char *prompt, char *out, size_t out_size) {
    while (1) {
        printf("%s", prompt);
        read_line(out, out_size);
        if (out[0] == '\0') {
            printf("Input cannot be empty. Please try again.\n");
        } else {
            break;
        }
    }
}
void ensureHeader() {
    FILE *fp = fopen(CSV_FILE, "r");
    if (fp == NULL) {
        fp = fopen(CSV_FILE, "w");
        if (fp) {
            fprintf(fp, "ID,Name,Department,Salary\n");
            fclose(fp);
        }
        return;
    }
    char firstLine[LINE_MAX];
    int found = 0;
    while (fgets(firstLine, sizeof(firstLine), fp)) {
        trim_newline(firstLine);
        if (firstLine[0] == '\0') continue;
        found = 1;
        break;
    }
    if (!found || strncmp(firstLine, "ID,", 3) != 0) {
        rewind(fp);
        FILE *temp = fopen(TEMP_FILE, "w");
        if (!temp) {
            fclose(fp);
            printf("Error creating temporary file to fix header.\n");
            return;
        }
        fprintf(temp, "ID,Name,Department,Salary\n");
        char line[LINE_MAX];
        while (fgets(line, sizeof(line), fp)) {
            fprintf(temp, "%s", line);
        }
        fclose(fp);
        fclose(temp);
        remove(CSV_FILE);
        rename(TEMP_FILE, CSV_FILE);
    } else {
        fclose(fp);
    }
}
void addEmployee() {
    ensureHeader();
    struct Employee emp;
    emp.id = read_int("Enter Employee ID: ");
    read_string("Enter Name: ", emp.name, sizeof(emp.name));
    read_string("Enter Department: ", emp.department, sizeof(emp.department));
    read_string("Enter Salary (20+ digits allowed): ", emp.salary, sizeof(emp.salary));
    FILE *fp = fopen(CSV_FILE, "w");
    if (fp == NULL) {
        printf("Error opening file for writing!\n");
        return;
    }
    fprintf(fp, "ID,Name,Department,Salary\n");
    fprintf(fp, "%d,%s,%s,%s\n", emp.id, emp.name, emp.department, emp.salary);
    fclose(fp);
    printf("Employee added to CSV successfully.\n");
}
void addanotherEmployee() {
    ensureHeader();
    struct Employee emp;
    emp.id = read_int("Enter Employee ID: ");
    read_string("Enter Name: ", emp.name, sizeof(emp.name));
    read_string("Enter Department: ", emp.department, sizeof(emp.department));
    read_string("Enter Salary (20+ digits allowed): ", emp.salary, sizeof(emp.salary));
    FILE *fp = fopen(CSV_FILE, "a");
    if (fp == NULL) {
        printf("Error opening file for appending!\n");
        return;
    }
    fprintf(fp, "%d,%s,%s,%s\n", emp.id, emp.name, emp.department, emp.salary);
    fclose(fp);
    printf("Employee added to CSV successfully.\n");
}
void displayEmployees() {
    ensureHeader();
    FILE *fp = fopen(CSV_FILE, "r");
    if (fp == NULL) {
        printf("Unable to open file!\n");
        return;
    }
    char line[LINE_MAX];
    printf("\n--- Employee Records (Read From CSV) ---\n");
    if (fgets(line, sizeof(line), fp))
        printf("%s", line);
    else {
        printf("No records.\n");
        fclose(fp);
        return;
    }
    printf("-----------------------------------------------\n");
    while (fgets(line, sizeof(line), fp)) {
        trim_newline(line);
        if (line[0] == '\0') continue;
        int id;
        char name[50], dept[50], salary[100];
        int scanned = sscanf(line, "%d,%49[^,],%49[^,],%99[^,]", &id, name, dept, salary);
        if (scanned == 4) {
            printf("%d\t%s\t%s\t%s\n", id, name, dept, salary);
        } else {
            printf("Malformed line: %s\n", line);
        }
    }
    fclose(fp);
}
void updateEmployee() {
    ensureHeader();
    int id = read_int("Enter Employee ID to update: ");
    FILE *fp = fopen(CSV_FILE, "r");
    if (fp == NULL) {
        printf("No records found!\n");
        return;
    }
    FILE *temp = fopen(TEMP_FILE, "w");
    if (temp == NULL) {
        fclose(fp);
        printf("Error creating temporary file!\n");
        return;
    }
    char line[LINE_MAX];
    if (fgets(line, sizeof(line), fp)) {
        fprintf(temp, "%s", line);
    } else {
        fclose(fp);
        fclose(temp);
        remove(TEMP_FILE);
        printf("No records available to update.\n");
        return;
    }
    int found = 0;
    while (fgets(line, sizeof(line), fp)) {
        trim_newline(line);
        if (line[0] == '\0') continue;
        int cid;
        char name[50], dept[50], salary[100];
        int scanned = sscanf(line, "%d,%49[^,],%49[^,],%99[^,]", &cid, name, dept, salary);
        if (scanned != 4) {
            fprintf(temp, "%s\n", line);
            continue;
        }
        if (cid == id) {
            found = 1;
            struct Employee emp;
            emp.id = id;
            printf("Updating record for ID %d.\n", id);
            read_string("Enter new Name: ", emp.name, sizeof(emp.name));
            read_string("Enter new Department: ", emp.department, sizeof(emp.department));
            read_string("Enter new Salary (20+ digits allowed): ", emp.salary, sizeof(emp.salary));
            fprintf(temp, "%d,%s,%s,%s\n", emp.id, emp.name, emp.department, emp.salary);
        } else {
            fprintf(temp, "%d,%s,%s,%s\n", cid, name, dept, salary);
        }
    }
    fclose(fp);
    fclose(temp);
    remove(CSV_FILE);
    rename(TEMP_FILE, CSV_FILE);
    if (found)
        printf("Record updated successfully.\n");
    else
        printf("Employee ID not found.\n");
}
int main() {
    int choice;
    while (1) {
        printf("\n===== Employee Record System (CSV Based) =====\n");
        printf("1. Add Employee\n");
        printf("2. Add Another Employee\n");
        printf("3. Update Employee\n");
        printf("4. Display Employees\n");
        printf("5. Exit\n");
        choice = read_int("Enter your choice: ");
        switch (choice) {
            case 1:
                addEmployee();
                break;
            case 2:
                addanotherEmployee();
                break;
            case 3:
                updateEmployee();
                break;
            case 4:
                displayEmployees();
                break;
            case 5:
                printf("Exiting program...\n");
                return 0;
            default:
                printf("Invalid choice! Try again.\n");
        }
    }
    return 0;
}
