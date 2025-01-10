## Overview of the Project
### Objective
This project aims to deepen the understanding of process creation, attributes, and signal handling in Linux. The key goals include:

1. Learning the different methods for creating processes in Linux, focusing on fork() and its behavior.
2. Understanding how processes handle signals and explore how signal dispositions and alarm timers behave in child processes.
3. Investigating the environment of processes created using fork() and observing their independence or shared attributes.
 
### Background
Processes are fundamental in operating systems, providing isolated execution environments. The Linux fork() system call is used to create new processes, where the child process is nearly identical to the parent. However, specific attributes, such as memory, signals, and timers, can behave differently in the child.

### Key concepts:

1. Process Duplication with fork():
○ Memory is copied using a copy-on-write mechanism. The child and parent processes share the same text segment but maintain separate data, stack, and heap segments. This ensures each process can modify its variables independently.
2. Attributes Shared/Inheritable with fork():
○ Signal dispositions: Custom handlers are inherited, while signals set to SIG_IGN or SIG_DFL remain unchanged.
○ Alarm timers: These are not inherited; the child starts with a clean state for timers.
3. Advanced Process Creation with clone():
While this project focuses on fork(), Linux also supports clone() for finer control over process attributes, enabling the sharing of resources like file descriptors, namespaces, and more.

## Code 1 

### Design
The program demonstrates the creation of a child process using the fork() system call in C. The key objective is to showcase the independence of memory spaces between the parent and child processes.
Key Structures
1.	Local Variable (var): This variable resides in the main function and is used to demonstrate that the parent and child processes maintain separate copies in their memory spaces.
2.	Process ID (pid): This is used to distinguish between the parent and child processes. The fork() call assigns a value to pid:
○	pid > 0: Identifies the parent process.
○	pid == 0: Identifies the child process.
○	pid < 0: Indicates that the fork failed.

### Execution Flow
The program starts by reading an integer value for var from the user.
It displays the initial state of the var variable and the parent process ID.
A fork() call is made to create a child process. The behavior splits as follows:
●	Child Process: Modifies the value of var and displays the changes.
●	Parent Process: Sleeps for 2 seconds to ensure the child executes first, then inspects the unchanged value of var in its memory space.
Both processes print their respective PID and the values of var before and after modification.
#### Complete Specification
The program does not include a shell or handle commands with semi-colons. Instead, it focuses on process creation and memory independence. To extend this logic for handling semi-colons, you would:
1.	Parse the input command string.
2.	Detect consecutive semi-colons with no commands between them.
3.	Skip these cases or print an error indicating invalid syntax.
#### For this specific code:
●	The child process independently modifies its copy of the variable.
●	The parent process retains the original value, confirming separation of process memory spaces.
#### Known Bugs or Problems
1.	Input Handling: No error checking is performed on the user input for var. Non-integer inputs may cause undefined behavior.
2.	Fork Failure: Although the program handles a fork failure, there is no retry mechanism or detailed logging for debugging.
3.	No Shell Implementation: The provided code is focused on demonstrating fork() rather than implementing a shell.
#### Test Cases and Expected Outputs
##### Case 1: Successful Execution
Input: 10
Expected Output:
Enter var: 10
Initial state: Parent PID = [ParentPID], Variable = 10
[Child] PID = [ChildPID], Variable before change = 10
[Child] PID = [ChildPID], Variable after change = 15
[Parent] PID = [ParentPID], Variable before changes = 10
[Parent] PID = [ParentPID], Variable after change = 10

###### Case 2: Fork Failure
If fork() fails (unlikely but possible due to system resource limitations), the program should display:
fork failed: [Error Message]

##### Case 3: Invalid Input
Input: Non-integer (e.g., abc)
Expected Behavior: the program will run and the variable will equal zero
