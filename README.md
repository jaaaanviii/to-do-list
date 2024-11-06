    #include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TASKS 100

typedef struct {
    char task[100];
    int is_completed;
} Task;

void add_task(Task tasks[], int *task_count) {
    if (*task_count == MAX_TASKS) {
        printf("Task list is full.\n");
        return;
    }

    int num_tasks;
    printf("How many tasks would you like to add? ");
    scanf("%d", &num_tasks);

    if (*task_count + num_tasks > MAX_TASKS) {
        printf("Adding %d tasks exceeds the maximum limit of %d. You can only add %d more tasks.\n",
               num_tasks, MAX_TASKS, MAX_TASKS - *task_count);
        num_tasks = MAX_TASKS - *task_count;
    }

    for (int i = 0; i < num_tasks; i++) {
        printf("Enter task %d: ", i + 1);
        scanf(" %[^\n]", tasks[*task_count].task); // Using %[^\n] to read a full line as a task
        tasks[*task_count].is_completed = 0;
        (*task_count)++;
        printf("Task %d added.\n", i + 1);
    }
}

void remove_task(Task tasks[], int *task_count) {
    if (*task_count == 0) {
        printf("No tasks to remove.\n");
        return;
    }

    int index;
    printf("Enter task index to remove: ");
    scanf("%d", &index);

    if (index < 1 || index > *task_count) {
        printf("Invalid index.\n");
        return;
    }

    for (int i = index - 1; i < *task_count - 1; i++) {
        strcpy(tasks[i].task, tasks[i + 1].task);
        tasks[i].is_completed = tasks[i + 1].is_completed;
    }
    (*task_count)--;
    printf("Task removed.\n");
}

void view_tasks(Task tasks[], int task_count) {
    if (task_count == 0) {
        printf("No tasks to display.\n");
        return;
    }

    printf("To-Do List:\n");
    for (int i = 0; i < task_count; i++) {
        printf("%d. %s %s\n", i + 1, tasks[i].task, tasks[i].is_completed ? "(Completed)" : "");
    }
}

void save_tasks(Task tasks[], int task_count) {
    FILE *fp = fopen("tasks.txt", "w");
    if (fp == NULL) {
        printf("Error saving tasks.\n");
        return;
    }

    for (int i = 0; i < task_count; i++) {
        fprintf(fp, "%s %d\n", tasks[i].task, tasks[i].is_completed);
    }

    fclose(fp);
    printf("Tasks saved to file.\n");
}

void load_tasks(Task tasks[], int *task_count) {
    FILE *fp = fopen("tasks.txt", "r");
    if (fp == NULL) {
        return;
    }

    *task_count = 0;
    while (fscanf(fp, "%[^\n] %d", tasks[*task_count].task, &tasks[*task_count].is_completed) != EOF) {
        (*task_count)++;
    }

    fclose(fp);
}

int main() {
    Task tasks[MAX_TASKS];
    int task_count = 0;

    load_tasks(tasks, &task_count);

    while (1) {
        printf("\nTo-Do List\n");
        printf("1. Add Task\n");
        printf("2. Remove Task\n");
        printf("3. View Tasks\n");
        printf("4. Save Tasks\n");
        printf("5. Exit\n");

        int choice;
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                add_task(tasks, &task_count);
                break;
            case 2:
                remove_task(tasks, &task_count);
                break;
            case 3:
                view_tasks(tasks, task_count);
                break;
            case 4:
                save_tasks(tasks, task_count);
                break;
            case 5:
                exit(0);
            default:
                printf("Invalid choice.\n");
        }
    }

    return 0;
}
