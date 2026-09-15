06 - POSIX Thread (pthread) Library for C and C++:


- As every program that runs as a process needs at least 'one thread of execution'.
- The default thread always begins in the main function. Whether its a few simple statements or calls to functions.

- Additionally, this does not limit multiple threads of executions within a processs to run at the same time.


Create and manage these extra threads in C and C++ using the POSIX thread library, knonw as pthreads.
- To set up a function for a thread to run, launch that thread, and how to keep the whole program alive until all threads finish, and how to pass data into and out of a thread.

---------------------------------------------------------------------------------------------------------------------------------------------

1. From One Thread to Many Threads

A Sinlge Thread of Execution:
int main()
{
    cout << "Hello world!\n";
    return 0;
}

- Starts at the top of main, executest the print statement and ends.


Now a more complex version, where main calls another function:
int main()
{
    printHelloWorld();
    // other ops
    return 0;
}

void printHelloWorld()
{
    cout << "Hello world!\n";
}

- As the execution hops from the function to main, it is 'still just one thread'.



Multiple, Independent Thread
- Fundamentally different from a normal function call. Once a new thread is created:
● Own independent sequence of operations.
● Runs on its own, asynchronously.
● Order of which operations from different threads execute is not fixed or predictable by default.



Visualizing this Structure:
● Main Thread: Starts and performs an operation, considered 'Thread 1', continues its operations, until it ends.

● Thread 1: Runs after being triggered by main, performs some operations, then starts 'Thread 2', and continues with more operations until it ends.

● Thread 2: Runs after being triggered by Thread 1, performs its own operations and ends.

- The only connections between the columns are the MOMENTS OF CREATIONED.
Then after, each thread proceeds on its own schedule, no guarentee on the order of operations occuring or interleaving.

- This program structure the pthread library is designed to help you build.

---------------------------------------------------------------------------------------------------------------------------------------------


2. Setting Up Your Program to Use pthreads

Two setup steps are required:
1) Include the header file - The program must have 'pthread.h' to access to the pthread functions and types.

2) Link the pthread library when compiling - Must add the 'linker flag' -lpthread when you compile your program.

EX: g++ test_pthread.cpp -lpthread

- Without this flagger, the compiler will still process your code. However the implementations of the pthread function you use will produce an
"undefined reference" error.

---------------------------------------------------------------------------------------------------------------------------------------------


3. The Two Steps of Creating and Running a Thread

1) Set up a sequence of instructions - Write a function that the new thread should execute.

2) Create the thread and tell it to run that function - Call the pthread library function that launches the thread.


Step 1: Writing the Thread's Function
- The pthread library requires this function to follow a very specific signature (prototype).

The function:
● Must take exactly 'one parameter of type void*'.
● Must 'retrun a value of type void*'.

EX: void* func(void *)

The reason for (void*) (void pointer / void star) is a special type of pointer in C and C++.
As the pointer can point to 'any type of data at all'. Allowing the pthread library to pass or return any type of data.
Reminder, convert your real data to and from void* as needed.


Step 2: Creating the Thread with pthread_create
- To launch a new thread, call the function 'pthread_create'
● Takes four parameters.
● Returns an int, indicating whether the thread was created successfully.

EX: Function signature
int pthread_create ( pthread_t *id,
                     const pthread_attr_t *attr,
                    void *(*start_func) (void *),
                    void *arg );

Breakdown of the parameters:
1) pthread_t *id - The address of a location where the pthread library will store the 'ID of the newly created thread'.
                 Thus, this program declares a variable of type pthread_t and then pass the address of that variable using & for the first arg.
                 The variable will now hold an unique ID the system has assigned to the new thread.

2) const pthread_attr_t *attr - A pointer to an object that specifices special attributes for the new thread (stack size or scheduling behavior).
                                For default attributes to from the library, just pass NULL.

3) void *(*start_func) (void *) - Reads as "the name of the function containing the sequence of instructions to be executed by the pthread".
                                  This is the function you wrote in STEP 1, and its signature must match 'void* func(void*)'.

4) void *arg - This is the argumenet that will be passed into the thread's function when it starts running. As a thread function only accepts a single void* parameter,
               Thus, if the function you're launching doesn't need any input, jsut pass NULL.


Retrun value: pthread_create retruns a 'int' status code:
● 0 - meands the thread was created successfully.
● Any positive value - An error.


---------------------------------------------------------------------------------------------------------------------------------------------


4. A First Example: Printing "Hello World" from a Thread:

Step 1 - Write the function the thread will run:
void* printHello(void* arg)
{
    cout << "Hello world!\n";
}


Step 2 - Launch the thread from main:
int main()
{
    pthread_t id;
    int rc;
    cout << "In main, creating thread\n";
    rc = pthread_create(&id, NULL, printHello, NULL);
    
    if (rc > 0) {
        cout << "Error creating thread\n";
        return -1;
    }

    return 0;
}

Breakdown:
● 'pthread_t id;' - Declares a variable to hold the thread's ID.
● &id - Passes the address of that variable as the first arg, so the system can fill it in.

● NULL - (second argument) indicates to use default thread attributes.
● printHello - (third argument) is the name of the function the thread should run.
● NULL - (forth argument) means no parameters need to be passed to 'printHello'.

● Returned status is checked, if positive and error occured; otherwise, the thread was created successfully.


NOTE: When the program is compiled with the linker flager -lpthread, the output is blank. This reveals two important facts about how threads behave.

---------------------------------------------------------------------------------------------------------------------------------------------


5. Why Doesn't "Hello World" Print? Understanding Thread Scheduling and Program Exit

- Two key reason why the new thread's output never appeared:

Reason 1: Thread Scheduling Is Not Guaranteed
- By default, the OS decides when each thread gets to run and what order. Thus, order of execution of the different threads is not guaranteed.

Reason 2: The Program Exits as Soon as main Finishes
- Once the last instruction in main completes, the program 'implicitly calls a function named exit'.
The exit function terminates the 'entire process', therefore every thread is terminated immediately, whether finished or not.


- Therefore in the 'Hello World' example, the OS may have not ran the 'printHello' thread before main finished and the whole process was shut down by the implicit exit call.
- Since threads run asynchronously, 'the main thread might easily finish before other threads it created have a change to run or complete'.



The Solution: pthread_exit
- Instead of letting the program implicitylu call exit(), when main finishes, you can explicitly call a different function 'pthread_exit'.

EX: void pthread_exit( void *retval)

● exit() terminates the entire process, alongside all its threads finished or not.
● pthread_exit() terminates only the specific thread that calls it, and 'leaves the rest of the process alive' until all threads have finished running.

- The single parameter to pthread_exit, 'retval' is a placeholder of type (void *), where the system can store the return value from the thread that is terminating.


Updated main function using pthread_exit:
int main()
{
    pthread_t id;
    int rc;
    cout << "In main, creating thread\n";
    rc = pthread_create(&id, NULL, printHello, NULL);

    if (rc > 0) {
        cout << "Error creating thread\n";
        return -1;
    }

    pthread_exit(0);
}

- The only change is instead of 'return 0;' which implicitly triggers exit, the program calls 'pthread_exit(0);'.
Tells the system 'terminate the main thread, but don't shut down the whole process - wait for other threads to finsish first.'

Result: When this version is compiled and runs, both messaes will now appear.


NOTE:
- Notice that 'printHello' doesn't explicitly call pthread_exit. Well any function launched using pthread_create implicitl calls pthread_exit when it finishes.
- Still good practice to include an explicit pthread_exit call at the end of the thread  function for readability.


---------------------------------------------------------------------------------------------------------------------------------------------


6. Passing a Single Parameter to a Thread Function

- Now analyze how to pass DATA into a thread when it starts.

Reminder that a thread function must have the signature (void* func(void *)) as it accepts a single void* parameter. Signifying that any data you want to
pass in must first be converted into a void*.

EX: Passing a single integer to the thread. Pass the address of the interger and converting that address to void*:
int arg;
void* to_pass =  (void*) &arg;

- Now use to_pass var as the forth arg to pthread_create.

Inside the thread function: The process is reversed: The function receives a void* and must convert it back to the appropriate pointer type (here its int*)
before extracting the actual value.


EX: Passing an Integer to a Thread

The updated thread function, which now expects an integer argument and prints it:
void* printHello(void* arg)
{
    int* actual_arg = *((int*) arg);
    cout << "Hello world from thread with arg: " << actual_arg << "!\n";
    return 0;
}


● arg arrives as void*, required by the pthread signature.
● It is cast to in*, to know the actual data being pointed to is an integer.
● The integer value itself is retrieved by dereferencing that pointer.


The updated main function, which sets up the integer and passes it in:
int main()
{
    pthread_t id;
    int rc;
    cout << "In main, creating thread\n";
    Int t = 1000;
    rc = pthread_create(&id, NULL, printHello, (void*) &t);

    if (rc > 0) {
        cout << "Error creating thread\n";
        return -1;
    }

    pthread_exit(0);
}


● 't' is a normal integer, set to 1000.
● Its address is taken and converted to void*.
● It is passed as the fourth argument to pthread_create.

Results:
In main, creating thread
Hello world from thread with arg: 1000!



Returning a Value from a Thread Function:
- Return values work in the same way; in reverse.
Thus the thread function return value must be converted into 'void*'.

EX: Returing char:
char retval;
void* to_return = (void*) retval;

- The converted value would be returned from the thread function and be retrieved by other parts of the program designed to receive it.


---------------------------------------------------------------------------------------------------------------------------------------------


7. Passing Multiple Parameters to a Thread Function

- Thread functions can only accept a single void* parameter, and cannot simply add more parameters to the function signature.
Thus, the program must create a 'user-defined data structure' to consolidate the parameters:

1) Create a custome data structure (struct) that holds all the values you want to pass.

2) Fill in that structure with the actual data you want to send.

3) Take the address of the structure, and convert it to void*, and pass that single void* to the thread, now the one pointer refers to an entire structure of grouped data.

4) Inside the thread function, convert the received void* back into a pointer to your custom structure type and then access its individual fields as needed.


EX: Passing an Integer and a Character Together:

Step 1 - Define the Structure that will hold both parameters:
struct my_args
{
    int arg_1;
    char arg_2;
};

- Two separate pieces of data into one object.


Step 2 - In main, create and fill in the structure, then pass its address:
my_args a;
a.arg_1 = 10;
a.arg_2 = 'Y';

void* to_pass = (void*) &a;

● A variable 'a' of type 'my_args' is created, its two fields are assigned values.
● The address of the entire structure (&a) is converted to void*, resulting in a single pointer representing both values at once.


Step 3 - Inside the thread function, unpack the structrue:
void* printHello(void* arg)
{
    my_args* argsPtr = (my_args*) arg;
    cout << "Hello world. I got two parameters, "
        << argsPtr->arg_1 << " and " << argsPtr->arg_2 << "\n";
    return NULL;
}

● The incoming void* is cast back to a pointer of type 'my_args*'.
● The individual fields are accessed normally using -> operator.