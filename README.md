#include "processStudents.h"

/*
   This file should compile with processStudentsSampleMain.cpp.
   Of course, it does not implement the required functionality.
*/

void addStudent(studentList *& l,
                string lastname,
                string firstname,
                int quizMarks[],
                int nQuizzes)
{
 // Step 1: Create a new student record
    studentRecord *newRecord = new studentRecord;

    // Assign a unique student ID (using a static counter for simplicity)
    static int uniqueID = 1;
    newRecord->id = uniqueID++; //increasing the value that the pointer points to

    // Allocate and copy the last name and first name
    newRecord->lastname = new char[lastname.size() + 1];
    strcpy(newRecord->lastname, lastname.c_str()); //strcpy copies the lastname into the array created

    newRecord->firstname = new char[firstname.size() + 1];
    strcpy(newRecord->firstname, firstname.c_str());

    // Allocate and copy quiz marks
    newRecord->quizMarks = new int[nQuizzes];
    for (int i = 0; i < nQuizzes; ++i) {
        newRecord->quizMarks[i] = quizMarks[i];
    }

    // Step 2: Create a new student list node to hold this student record
    studentList *newNode = new studentList;
    newNode->sr = newRecord; //newNode's sr pointer points to the newRecord created above

    // Step 3: Insert the new node at the beginning of the list
    newNode->next = l; //have newNode's next pointer points to current value of l (or what l points to)
    l = newNode; //set l to point to the new node
}


void removeStudent(studentList *& l, int studentID){
//get current node
//studentList->studentRecord->id = studentID;
// if current node ID == student ID (ID to be deleted), then reposition previous pointer to point to next node. Then deallocate memory.
//If curent node ID != student ID, go to next node
//If there is only one studentRecord in the linked list, set the pointer to nullptr

    studentList *current = l;    // Start with the head of the list, current pointer points to the same place as pointer l; current points to the first one in the list
    studentList *previous = nullptr;

    // Traverse the list to find the node with the matching studentID
    while (current != nullptr) {
        if (current->sr->id == studentID) { // Found the node to remove
            if (previous == nullptr) {
                // If the node to remove is the head, update head to the next node
                l = current->next;
            } else {
                // If the node is not the head, bypass it
                previous->next = current->next; //set the previous node's pointer = the current node's pointer (points to next)
            }

            // Deallocate memory for the removed student's data
            delete[] current->sr->lastname;
            delete[] current->sr->firstname;
            delete[] current->sr->quizMarks;
            delete current->sr;   // Delete the studentRecord
            delete current;       // Delete the node itself

            return; // Exit the function after deleting the node -> return or break?
        }

        // Move to the next node
        previous = current;
        current = current->next; //the current pointer is set to point to the same place that the next pointer points to within the current node
    }
}


studentRecord *findStudentByID(const studentList *l,
                               int studentID)
{
    // Your implementation here
    // Change the following to the correct return value


    //find the inputted student ID and have the studentList pointer point to the node that contains that studentID
    //1. check if studentList pointer is empty.
    //2. Go through the list by following each students node using a while loop (while pointer int StudentID != *sr -> studentID).
    //3. Check if pointer is pointing to correct node of desired student ID (include this in the while loop).
    //4. Have current pointer point to it.
    //5. Return nullptr if no match is found

    // Step 1: Check if the list pointer is empty
    if (l == nullptr) {
        return nullptr; //return if list is empty, pointer points to nothing
    }

    // Step 2: Traverse the list
    const studentList* current = l;  // Start at the beginning of the list
    while (current != nullptr) {
        // Step 3: Check if the current node's studentRecord has the desired studentID
        if (current->sr != nullptr && current->sr->id == studentID) {
            return current->sr;  // Found the student with the desired ID, then return that
        }
        // Step 4: Move to the next node
        current = current->next;
    }

    // Step 5: If no match is found, return nullptr
    return nullptr;

    /*
    studentRecord* current = l->head;  // Step 2: Initialize a pointer to start at the head

    // Step 3: Traverse the list
    while (current != nullptr) {
        // Step 4: Check if current node has the desired studentID
        if (current->studentID == studentID) {
            return current;  // Found the student with the desired ID
        }
        // Step 5: Move to the next node
        current = current->next;
    }

    // Step 6: If no match is found, return nullptr
    return nullptr;
    }
*/
}

studentList *findStudentsByLastName(const studentList *l,
                                    string lastname,
                                    int nQuizzes)
{
    //1. check if studentList pointer is empty.
    //2. Go through the list by following each students node using a while loop (while pointer string last name != *sr ->*lastname && int nQuizzes != *sr->*nQuizMarks).
    //3. Check if pointer is pointing to correct node of desired last name and quiz marks (check quiz marks (incase there are multiple studetns with the same last name) (include this in the while loop).
    //4. Have current pointer point to it.
    //5. Return nullptr if no match is found

    // Step 1: Check if the list pointer is empty
    if (l == nullptr) {
        return nullptr;
    }

    // Step 2: Initialize a new list to hold matching students
    studentList* matchingList = nullptr;  // pointer to the head of the new list
    studentList* lastNode = nullptr;

    // Step 3: Traverse the original list
    const studentList* current = l;  // Start at the head of the original list
    while (current != nullptr) {  // Traverse until the end of the list
        // Check if the current node's studentRecord has the desired last name and quizMarks
        if (current->sr != nullptr && current->sr->quizMarks != nullptr &&
            strcmp(current->sr->lastname, lastname.c_str()) == 0) {  // Use strcmp for string comparison

            // Check if the quizMarks array has the expected number of quizzes
            int quizCount = 0;
            while (quizCount < nQuizzes && current->sr->quizMarks[quizCount] != '\0') {
                quizCount++;
            }

            if (quizCount == nQuizzes) {
                // Step 4: Create a new node for the matching student
                studentList* newNode = new studentList;
                newNode->sr = current->sr;  // Point to the same studentRecord
                newNode->next = nullptr;

                // Append to the matching list
                if (matchingList == nullptr) {
                    matchingList = newNode;  // First match becomes the head
                } else {
                    lastNode->next = newNode;  // Link new node to last node in the list
                }
                lastNode = newNode;  // Update lastNode to the newly added node
            }
            }
        current = current->next;  // Move to the next node in the original list
    }

    // Step 5: Return the head of the matching list (or nullptr if no matches)
    return matchingList;
}

double quizAverage(const studentList *l, int quizNum) {
    // Your implementation here
    // Change the following to the correct return value
    double sum = 0.0;     // To store the sum of quiz marks
    int count = 0;        // To keep track of the number of students with a score for the quiz

    const studentList* current = l;  // Start at the head of the list

    // Traverse the linked list
    while (current != nullptr) { //keeps going as long as the current arrow points to a node, therefore there are still students to prcess
        // Check if the current student has a score for the specified quiz
        if (current->sr != nullptr && current->sr->quizMarks != nullptr) {
            sum += current->sr->quizMarks[quizNum];  // Access the student's number quizNum mark and add the student's quiz mark to the sum;
            count++;                                 // Increment the count
        }

        current = current->next;  // Move to the next node
    }

    // If at least one student has a score for this quiz, calculate the average
    if (count > 0) {
        return sum / count;
    }

    // Return 0.0 if no student has a score for this quiz
    return 0.0;
}
