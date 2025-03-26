# Cpp-projects

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
