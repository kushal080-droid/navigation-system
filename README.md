#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Tab {
    int pageID;
    char URL[100];
    struct Tab *prev, *next;
} Tab;

Tab *head = NULL, *current = NULL;
int nextPageID = 1;

// Function prototypes
void visitNewPage();
void goBack();
void goForward();
void showCurrentTab();
void closeCurrentTab();
void showHistory();

int main() {
    int choice;

    while (1) {
        printf("\nMenu:\n");
        printf("1. Visit a new page\n");
        printf("2. Go back\n");
        printf("3. Go forward\n");
        printf("4. Show current tab\n");
        printf("5. Close current tab\n");
        printf("6. Show history\n");
        printf("7. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        getchar(); // to consume newline after scanf

        switch (choice) {
            case 1: visitNewPage(); break;
            case 2: goBack(); break;
            case 3: goForward(); break;
            case 4: showCurrentTab(); break;
            case 5: closeCurrentTab(); break;
            case 6: showHistory(); break;
            case 7: exit(0);
            default: printf("Invalid choice!\n");
        }
    }
}

// Visit a new page
void visitNewPage() {
    char url;
    printf("Enter URL: ");
    fgets(url, sizeof(url), stdin);
    url[strcspn(url, "\n")] = 0; // remove newline

    Tab *newTab = (Tab*)malloc(sizeof(Tab));
    newTab->pageID = nextPageID++;
    strcpy(newTab->URL, url);
    newTab->prev = newTab->next = NULL;

    if (head == NULL) {
        head = newTab;
        current = newTab;
    } else {
        // Insert after current
        newTab->prev = current;
        newTab->next = current->next;
        if (current->next) current->next->prev = newTab;
        current->next = newTab;
        current = newTab;
    }
    showCurrentTab();
}

// Go back
void goBack() {
    if (current && current->prev) {
        current = current->prev;
        showCurrentTab();
    } else {
        printf("No previous tab!\n");
    }
}

// Go forward
void goForward() {
    if (current && current->next) {
        current = current->next;
        showCurrentTab();
    } else {
        printf("No next tab!\n");
    }
}

// Show current tab
void showCurrentTab() {
    if (current) {
        printf("Current Tab - PageID: %d, URL: %s\n", current->pageID, current->URL);
    } else {
        printf("No tabs open!\n");
    }
}

// Close current tab
void closeCurrentTab() {
    if (!current) return;

    Tab *temp = current;
    if (temp->prev) temp->prev->next = temp->next;
    if (temp->next) temp->next->prev = temp->prev;
    Tab *nextTab = temp->next ? temp->next : temp->prev;
    if (temp == head) head = temp->next ? temp->next : temp->prev;
    current = nextTab;
    free(temp);

    printf("Tab closed. ");
    showCurrentTab();
}

// Show history
void showHistory() {
    Tab *ptr = head;
    printf("Tab History:\n");
    while (ptr) {
        printf("PageID: %d, URL: %s\n", ptr->pageID, ptr->URL);
        ptr = ptr->next;
    }
}
