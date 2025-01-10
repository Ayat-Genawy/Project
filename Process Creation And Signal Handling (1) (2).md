# Process Creation And Signal Handling

##### STATISTICAL & COMPUTER SCIENCES DEPARTMENT

##### MADE BY :

#### 1.Mai Makkawi Mohammed Abuzaid
#### 2.Ayat Abdalatife Genawy Mohammed 
#### 3.AbdAlrahman Mohammed Ahmed Abashar
#### 4.Ahmed Nagmeldin Goutbi Elhassan



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
3. Attributes Shared/Inheritable with fork():
   
○ Signal dispositions: Custom handlers are inherited, while signals set to SIG_IGN or SIG_DFL remain unchanged.

○ Alarm timers: These are not inherited; the child starts with a clean state for timers.
4. Advanced Process Creation with clone():
While this project focuses on fork(), Linux also supports clone() for finer control over process attributes, enabling the sharing of resources like file descriptors, namespaces, and more.

## Code 1 (program.c)

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


##### Case 2: Fork Failure
If fork() fails (unlikely but possible due to system resource limitations), the program should display:
fork failed: [Error Message]

##### Case 3: Invalid Input
Input: Non-integer (e.g., abc)
Expected Behavior: the program will run and the variable will equal zero


## Code 2 (program2.c)

### Design Overview
This program examines two attributes of processes—signal dispositions and alarm timers—to determine if they are inherited by a child process created using fork().
### Key Features
1.	Signal Dispositions:

○	A custom signal handler is set up for SIGUSR1 using the signal() function. This allows the process to handle SIGUSR1 with a user-defined behavior.

○	The child process checks if it inherits this signal handler or if the signal disposition is reset.

3.	Alarm Timers:

○	An alarm is set in the parent process using the alarm() function, which schedules a SIGALRM signal after a specified time.

○	The child process verifies whether it inherits the alarm timer or if it is reset.

### Execution Flow
1.	The parent process sets up a custom signal handler for SIGUSR1 and an alarm for 5 seconds.
   
2.	The fork() system call creates a child process:
   
○	Child Process:

■	Checks if the SIGUSR1 signal handler is inherited.

■	Retrieves the remaining time on the alarm timer.

○	Parent Process:

■	Waits for the child process to complete and continues execution.
 
### Complete Specification
#### Signal Dispositions
The program demonstrates whether the custom signal handler for SIGUSR1 is inherited by the child process. This is verified using the signal() function in the child.
#### Alarm Timers
The program tests if the child process inherits the alarm timer set by the parent. The alarm(0) function cancels any active alarm and retrieves the remaining time in the child process to verify this.
#### Ambiguities Addressed
1.	Signal Inheritance:
	
○	Signal handlers are inherited unless explicitly reset to default or ignored.
3.	Alarm Timer:

○	The alarm() timer is not shared between parent and child. The child does not inherit the parent’s timer.
 
### Known Bugs or Problems
1.	Signal Handling Validation:
   
○	The program only checks SIGUSR1. Other signals may have different inheritance behavior, which is not tested here.

2.	Alarm Timer Validation:

○	The program does not handle cases where the fork() call delays due to system load, which could reduce the accuracy of the remaining alarm time.


### Test Cases and Expected Outputs

#### Case 1: Signal Disposition
##### Expected Output:

Parent process PID: [ParentPID]

Child process PID: [ChildPID]

Child process inherited custom signal handler for SIGUSR1

Child process alarm timer: 0 seconds remaining

Parent process continues

#### Case 2: Alarm Timer
If the child inherits the alarm timer (unexpected behavior, as per POSIX standards):

Expected Output:

Child process alarm timer: 4 seconds remaining

#### Case 3: Fork Failure
If fork() fails: Expected Output:

fork failed: [Error Message]

#### Case 4: Ignored Signal
If SIGUSR1 is set to SIG_IGN instead of a custom handler: 

Expected Output:

Child process inherited SIGUSR1 as ignored

## Code 3 (program3.c)

### Design Overview
This program investigates whether signal dispositions and alarm timers are inherited by a child process created using fork(). The key focus is on observing which process receives the SIGALRM signal when the alarm expires and whether the signal handler is inherited by the child.
### Key Features
1.	Signal Dispositions:

○	The program sets up a custom signal handler for SIGALRM using the sigaction() function.

○	The child process checks if it inherits the signal handler by examining the sa_handler field.

3.	Alarm Timers:

○	The parent sets an alarm for 2 seconds using the alarm() function.

○	Both the parent and child processes run loops to observe which process receives the SIGALRM signal.

### Execution Flow
1.	The parent process sets up a custom signal handler for SIGALRM and starts a 2-second alarm timer.
2.	The fork() system call creates a child process:
   
○	Child Process:

■	Fetches the SIGALRM disposition using sigaction() and determines whether it has inherited the signal handler.

○	Both Processes:

■	Enter a loop to print their PID and wait for SIGALRM to trigger the signal handler.
 
### Complete Specification
#### Signal Dispositions
The custom SIGALRM handler is defined and set in the parent process. The child process uses sigaction() to determine if the SIGALRM disposition is inherited. According to POSIX standards:

●	Signal dispositions, except those set to SIG_IGN or SIG_DFL, are inherited.

#### Alarm Timers
The program checks whether the child process inherits the alarm timer. As per POSIX standards:

●	Alarm timers are not shared between parent and child. The timer is reset in the child process after fork().
#### Ambiguities Addressed

1.	Signal Handler Behavior:

○	The program verifies if the child inherits the parent’s signal handler using sa_handler.

2.	Alarm Behavior:

○	The program demonstrates that only one process receives the SIGALRM signal based on timer inheritance behavior.
 
### Known Bugs or Problems

1.	Signal Handling Validation:

○	The program only checks SIGALRM. Behavior for other signals is not validated.

2.	Timer Precision:

○	If the parent process executes slowly or the system is under load, the alarm may expire before the loop begins, potentially causing inconsistent results.


### Test Cases and Expected Outputs
#### Case 1: Signal Disposition
##### Expected Output:

Child inherits signal disposition

Process [ParentPID] is running

Process [ChildPID] is running

...

Received signal 14 in process [ParentPID]


●	The SIGALRM handler triggers in the parent because the child does not inherit the alarm timer.

#### Case 2: Alarm Timer
If the alarm timer is unexpectedly shared (non-POSIX behavior): Expected Output:

Received signal 14 in process [ChildPID]

#### Case 3: Fork Failure
If fork() fails: Expected Output:

fork: [Error Message]

### How to Compile and Run:

#### 1. **Compile the Programs:**
   ```bash
   gcc -o program program.c
   gcc -o program2 program2.c
   gcc -o program3 program3.c
   ```

#### 2. **Run the Executables:**
   - For Program :
     ```bash
     ./program
     ```
   - For Program 2:
     ```bash
     ./program2
     ```
   - For Program 3:
     ```bash
     ./program3
     ```
