# Cpp-projects

# College events solver

```
#include <iostream>
#include <string>
using namespace std;

// Structure to store event details
struct Event {
    string name;
    string date;
    int start_time;
    int end_time;
    string venue;
    string department;
};

// Node structure for Interval Tree
struct TreeNode {
    Event event;
    int max_end;  // Maximum end time in this subtree
    TreeNode* left;
    TreeNode* right;
};

// Function to create a new tree node
TreeNode* createNode(Event newEvent) {
    TreeNode* node = new TreeNode;
    node->event = newEvent;
    node->max_end = newEvent.end_time;
    node->left = node->right = nullptr;
    return node;
}

// Insert event into the Interval Tree
TreeNode* insert(TreeNode* root, Event newEvent) {
    if (!root) return createNode(newEvent);

    if (newEvent.start_time < root->event.start_time)
        root->left = insert(root->left, newEvent);
    else
        root->right = insert(root->right, newEvent);

    root->max_end = max(root->max_end, newEvent.end_time);
    return root;
}

// Function to check if two events overlap
bool isOverlapping(Event e1, Event e2) {
    return (e1.start_time < e2.end_time && e2.start_time < e1.end_time);
}

// Function to check for event conflicts in the tree
bool checkConflict(TreeNode* root, Event newEvent) {
    if (!root) return false;

    if (isOverlapping(root->event, newEvent)) {
        cout << "\n⚠ Conflict found with: " << root->event.name
             << " (Time: " << root->event.start_time << " to " << root->event.end_time << ")\n";
        return true;
    }

    if (root->left && root->left->max_end >= newEvent.start_time)
        return checkConflict(root->left, newEvent);

    return checkConflict(root->right, newEvent);
}

// Function to suggest a new time slot if there is a conflict
void suggestNewTime(Event &e) {
    cout << "✅ Suggested New Time: " << e.start_time + 1 << " to " << e.end_time + 1 << endl;
    e.start_time += 1;
    e.end_time += 1;
}

// Function to display all scheduled events in Inorder (Sorted Order)
void displayEvents(TreeNode* root) {
    if (!root) return;
    displayEvents(root->left);
    cout << "📅 " << root->event.name << " | " << root->event.date
         << " | Time: " << root->event.start_time << " to " << root->event.end_time
         << " | Venue: " << root->event.venue
         << " | Department: " << root->event.department << endl;
    displayEvents(root->right);
}

// Main function
int main() {
    TreeNode* root = nullptr;
    int choice;
    
    cout << "🎯 College Event Conflict Resolver 🎯\n";

    while (true) {
        cout << "\n1. Add Event\n2. View Events\n3. Exit\nEnter your choice: ";
        cin >> choice;

        if (choice == 1) {
            Event newEvent;
            cout << "\nEnter Event Name: ";
            cin.ignore();
            getline(cin, newEvent.name);
            cout << "Enter Date (YYYY-MM-DD): ";
            cin >> newEvent.date;
            cout << "Enter Start Time (24-hour format): ";
            cin >> newEvent.start_time;
            cout << "Enter End Time (24-hour format): ";
            cin >> newEvent.end_time;
            cout << "Enter Venue: ";
            cin.ignore();
            getline(cin, newEvent.venue);
            cout << "Enter Department: ";
            getline(cin, newEvent.department);

            if (checkConflict(root, newEvent)) {
                suggestNewTime(newEvent);
            }

            root = insert(root, newEvent);
            cout << "✅ Event Added Successfully!\n";
        }
        else if (choice == 2) {
            cout << "\n📌 Scheduled Events:\n";
            if (!root) {
                cout << "No events scheduled yet.\n";
            } else {
                displayEvents(root);
            }
        }
        else if (choice == 3) {
            cout << "🚀 Exiting... Thank you!\n";
            break;
        }
        else {
            cout << "❌ Invalid Choice! Try Again.\n";
        }
    }

    return 0;
}

```






# sudoko solver


```
#include <iostream>
using namespace std;

// Define the structure to hold the Sudoku board
struct Sudoku {
    int **grid;  // Pointer to dynamically allocated 2D array
};

// Function to check if placing a number is valid
bool isSafe(Sudoku *sudoku, int row, int col, int num) {
    for (int x = 0; x < 9; x++) {
        if (sudoku->grid[row][x] == num || sudoku->grid[x][col] == num)
            return false;
    }

    // Check the 3x3 sub-grid
    int startRow = row - row % 3, startCol = col - col % 3;
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (sudoku->grid[i + startRow][j + startCol] == num)
                return false;
        }
    }
    return true;
}

// Function to solve Sudoku using backtracking
bool solveSudoku(Sudoku *sudoku, int row, int col) {
    if (row == 9) return true; // Solved

    if (col == 9) return solveSudoku(sudoku, row + 1, 0);

    if (sudoku->grid[row][col] != 0)
        return solveSudoku(sudoku, row, col + 1);

    for (int num = 1; num <= 9; num++) {
        if (isSafe(sudoku, row, col, num)) {
            sudoku->grid[row][col] = num;
            if (solveSudoku(sudoku, row, col + 1))
                return true;
            sudoku->grid[row][col] = 0; // Backtrack
        }
    }
    return false;
}

// Function to print the Sudoku board
void printSudoku(Sudoku *sudoku) {
    for (int i = 0; i < 9; i++) {
        for (int j = 0; j < 9; j++) {
            cout << sudoku->grid[i][j] << " ";
        }
        cout << endl;
    }
}

int main() {
    Sudoku sudoku;

    // Dynamically allocate memory for 9x9 grid
    sudoku.grid = new int*[9];
    for (int i = 0; i < 9; i++)
        sudoku.grid[i] = new int[9];

    // Input Sudoku puzzle (0 represents empty cells)
    int inputGrid[9][9] = {
        {5, 3, 0, 0, 7, 0, 0, 0, 0},
        {6, 0, 0, 1, 9, 5, 0, 0, 0},
        {0, 9, 8, 0, 0, 0, 0, 6, 0},
        {8, 0, 0, 0, 6, 0, 0, 0, 3},
        {4, 0, 0, 8, 0, 3, 0, 0, 1},
        {7, 0, 0, 0, 2, 0, 0, 0, 6},
        {0, 6, 0, 0, 0, 0, 2, 8, 0},
        {0, 0, 0, 4, 1, 9, 0, 0, 5},
        {0, 0, 0, 0, 8, 0, 0, 7, 9}
    };

    // Copy input grid to dynamically allocated grid
    for (int i = 0; i < 9; i++)
        for (int j = 0; j < 9; j++)
            sudoku.grid[i][j] = inputGrid[i][j];

    cout << "Sudoku Puzzle:" << endl;
    printSudoku(&sudoku);

    if (solveSudoku(&sudoku, 0, 0)) {
        cout << "\nSolved Sudoku:" << endl;
        printSudoku(&sudoku);
    } else {
        cout << "\nNo solution exists!" << endl;
    }

    // Free dynamically allocated memory
    for (int i = 0; i < 9; i++)
        delete[] sudoku.grid[i];
    delete[] sudoku.grid;

    return 0;
}

```
