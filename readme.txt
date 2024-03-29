Name  : Ilya Kopyl
Date  : 07/12/2018
Class : CSC 0415-01 Summer 2018

Compile Instructions:
$ make

Clean up target (delete previous build):
$ make clean

Run Instructions:
$ ./threadracer


Explain why your program produces the wrong out:

First we need to understand what has led me to write the code the way I did. The idea of a race condition implies that there are two (or more) threads trying to run concurrently. Because of that, it wouldn't make sense to have just one pthread_create() call at each loop iteration - instead, we need to place two (or more) pthread_create() calls next to each other. That's what I did inside the while loop. 

The problem of differentiating which thread is even-numbered and which one is odd is rather trivial: the first pthread_create() call inside the while loop will always be odd-numbered, and the second - even-numbered. And these two pthread_create() calls will accept different function pointers as their arguments, which are thread_sub() and thread_add(). Functionally, these two functions are almost equivalent: first they copy the value of global variable to their own local scope variable, then they modify the value in the local scope variable, and after that they copy the value from their local scope variable back to the global variable. And here is a thing: threads maintain their own copy of stack (local variables), but they share the Data Segment (global variables). The gist of the race condition in our context is that we are going to have 2 threads at a time (at each while-loop iteration) that would try to update the global variable with the respective values from their local scope variables at about the same time.

Since the main criteria of the homework assignment is to get the final answer the furthest away from 0 as possible, I couldn't help but do the following: I decided to insert just one nanosleep() call inside one of the two functions, pointers to which are passed as arguments during pthread_create() calls. Thus, I've inserted the nanosleep() call inside the thread_sub() function, right before the global variable should be updated with the value from the function's local variable. This would guarantee that in the majority of cases (although not always) the global variable will be decreased by specified value (i.e. by 1), because the thread_sub() would try to update the global variable PRESUMABLY AFTER that global variable was updated by thread_add() call.

Below is the example of program output, you could see that the value is decreasing, although not uniformly -- in ~10% of times the value is in fact increasing:

Current Value written to Global Variables by thread:      2     is      1
Current Value written to Global Variables by thread:      1     is     -1
Current Value written to Global Variables by thread:      4     is      0
Current Value written to Global Variables by thread:      3     is     -2
Current Value written to Global Variables by thread:      6     is     -1
Current Value written to Global Variables by thread:      5     is     -3
Current Value written to Global Variables by thread:      8     is     -2
Current Value written to Global Variables by thread:      7     is     -4
Current Value written to Global Variables by thread:     10     is     -3
Current Value written to Global Variables by thread:      9     is     -5
Current Value written to Global Variables by thread:      2     is     -4
Current Value written to Global Variables by thread:      1     is     -6
Current Value written to Global Variables by thread:      4     is     -5
Current Value written to Global Variables by thread:      3     is     -7
Current Value written to Global Variables by thread:      6     is     -6
Current Value written to Global Variables by thread:      5     is     -8
Current Value written to Global Variables by thread:      8     is     -7
Current Value written to Global Variables by thread:      7     is     -8
Current Value written to Global Variables by thread:     10     is     -7
Current Value written to Global Variables by thread:      9     is     -9
Current Value written to Global Variables by thread:      2     is     -8
Current Value written to Global Variables by thread:      1     is    -10
Current Value written to Global Variables by thread:      4     is     -9
Current Value written to Global Variables by thread:      3     is    -11
Current Value written to Global Variables by thread:      5     is    -12
Current Value written to Global Variables by thread:      6     is    -11
Current Value written to Global Variables by thread:      8     is    -10
Current Value written to Global Variables by thread:      7     is    -11
Current Value written to Global Variables by thread:     10     is    -10
Current Value written to Global Variables by thread:      9     is    -12
Current Value written to Global Variables by thread:      2     is    -11
Current Value written to Global Variables by thread:      1     is    -12
Current Value written to Global Variables by thread:      4     is    -11
Current Value written to Global Variables by thread:      3     is    -13
Current Value written to Global Variables by thread:      6     is    -12
Current Value written to Global Variables by thread:      5     is    -14
Current Value written to Global Variables by thread:      8     is    -13
Current Value written to Global Variables by thread:      7     is    -15
Current Value written to Global Variables by thread:     10     is    -14
Current Value written to Global Variables by thread:      9     is    -15
Current Value written to Global Variables by thread:      1     is    -16
Current Value written to Global Variables by thread:      2     is    -15
Current Value written to Global Variables by thread:      4     is    -14
Current Value written to Global Variables by thread:      3     is    -16
Current Value written to Global Variables by thread:      6     is    -15
Current Value written to Global Variables by thread:      5     is    -16
Current Value written to Global Variables by thread:      8     is    -15
Current Value written to Global Variables by thread:      7     is    -17
Current Value written to Global Variables by thread:     10     is    -16
Current Value written to Global Variables by thread:      9     is    -18
Current Value written to Global Variables by thread:      2     is    -17
Current Value written to Global Variables by thread:      1     is    -19
Current Value written to Global Variables by thread:      3     is    -20
Current Value written to Global Variables by thread:      4     is    -19
Current Value written to Global Variables by thread:      6     is    -18
Current Value written to Global Variables by thread:      5     is    -20
Current Value written to Global Variables by thread:      8     is    -19
Current Value written to Global Variables by thread:      7     is    -20
Current Value written to Global Variables by thread:      9     is    -21
Current Value written to Global Variables by thread:     10     is    -20
Current Value written to Global Variables by thread:      2     is    -19
Current Value written to Global Variables by thread:      1     is    -21
Current Value written to Global Variables by thread:      4     is    -20
Current Value written to Global Variables by thread:      3     is    -21
Current Value written to Global Variables by thread:      6     is    -20
Current Value written to Global Variables by thread:      5     is    -22
Current Value written to Global Variables by thread:      7     is    -23
Current Value written to Global Variables by thread:      8     is    -22
Current Value written to Global Variables by thread:     10     is    -21
Current Value written to Global Variables by thread:      9     is    -22
Current Value written to Global Variables by thread:      2     is    -21
Current Value written to Global Variables by thread:      1     is    -23
Current Value written to Global Variables by thread:      3     is    -24
Current Value written to Global Variables by thread:      4     is    -23
Current Value written to Global Variables by thread:      6     is    -22
Current Value written to Global Variables by thread:      5     is    -24
Current Value written to Global Variables by thread:      7     is    -25
Current Value written to Global Variables by thread:      8     is    -24
Current Value written to Global Variables by thread:     10     is    -23
Current Value written to Global Variables by thread:      9     is    -24
Current Value written to Global Variables by thread:      1     is    -25
Current Value written to Global Variables by thread:      2     is    -24
Current Value written to Global Variables by thread:      4     is    -23
Current Value written to Global Variables by thread:      3     is    -25
Current Value written to Global Variables by thread:      6     is    -24
Current Value written to Global Variables by thread:      5     is    -26
Current Value written to Global Variables by thread:      8     is    -25
Current Value written to Global Variables by thread:      7     is    -27
Current Value written to Global Variables by thread:     10     is    -26
Current Value written to Global Variables by thread:      9     is    -28
Current Value written to Global Variables by thread:      2     is    -27
Current Value written to Global Variables by thread:      1     is    -29
Current Value written to Global Variables by thread:      4     is    -28
Current Value written to Global Variables by thread:      3     is    -30
Current Value written to Global Variables by thread:      6     is    -29
Current Value written to Global Variables by thread:      5     is    -31
Current Value written to Global Variables by thread:      7     is    -32
Current Value written to Global Variables by thread:      8     is    -31
Current Value written to Global Variables by thread:     10     is    -30
Current Value written to Global Variables by thread:      9     is    -32
Current Value written to Global Variables by thread:      2     is    -31
Current Value written to Global Variables by thread:      1     is    -33
Current Value written to Global Variables by thread:      4     is    -32
Current Value written to Global Variables by thread:      3     is    -34
Current Value written to Global Variables by thread:      5     is    -35
Current Value written to Global Variables by thread:      6     is    -34
Current Value written to Global Variables by thread:      8     is    -33
Current Value written to Global Variables by thread:      7     is    -35
Current Value written to Global Variables by thread:     10     is    -34
Current Value written to Global Variables by thread:      9     is    -36
Current Value written to Global Variables by thread:      2     is    -35
Current Value written to Global Variables by thread:      1     is    -37
Current Value written to Global Variables by thread:      3     is    -38
Current Value written to Global Variables by thread:      4     is    -37
Current Value written to Global Variables by thread:      6     is    -36
Current Value written to Global Variables by thread:      5     is    -38
Current Value written to Global Variables by thread:      8     is    -37
Current Value written to Global Variables by thread:      7     is    -39
Current Value written to Global Variables by thread:     10     is    -38
Current Value written to Global Variables by thread:      9     is    -39
Current Value written to Global Variables by thread:      2     is    -38
Current Value written to Global Variables by thread:      1     is    -40
Current Value written to Global Variables by thread:      4     is    -39
Current Value written to Global Variables by thread:      3     is    -40
Current Value written to Global Variables by thread:      6     is    -39
Current Value written to Global Variables by thread:      5     is    -41
Current Value written to Global Variables by thread:      8     is    -40
Current Value written to Global Variables by thread:      7     is    -42
Current Value written to Global Variables by thread:     10     is    -41
Current Value written to Global Variables by thread:      9     is    -43
Current Value written to Global Variables by thread:      2     is    -42
Current Value written to Global Variables by thread:      1     is    -44
Current Value written to Global Variables by thread:      4     is    -43
Current Value written to Global Variables by thread:      3     is    -45
Current Value written to Global Variables by thread:      6     is    -44
Current Value written to Global Variables by thread:      5     is    -46
Current Value written to Global Variables by thread:      8     is    -45
Current Value written to Global Variables by thread:      7     is    -47
Current Value written to Global Variables by thread:     10     is    -46
Current Value written to Global Variables by thread:      9     is    -48
Current Value written to Global Variables by thread:      1     is    -49
Current Value written to Global Variables by thread:      2     is    -48
Current Value written to Global Variables by thread:      4     is    -47
Current Value written to Global Variables by thread:      3     is    -49
Current Value written to Global Variables by thread:      6     is    -48
Current Value written to Global Variables by thread:      5     is    -50
Current Value written to Global Variables by thread:      7     is    -51
Current Value written to Global Variables by thread:      8     is    -49
Current Value written to Global Variables by thread:      9     is    -52
Current Value written to Global Variables by thread:     10     is    -51
Current Value written to Global Variables by thread:      2     is    -50
Current Value written to Global Variables by thread:      1     is    -52
Current Value written to Global Variables by thread:      4     is    -51
Current Value written to Global Variables by thread:      3     is    -52
Current Value written to Global Variables by thread:      6     is    -51
Current Value written to Global Variables by thread:      5     is    -53
Current Value written to Global Variables by thread:      7     is    -54
Current Value written to Global Variables by thread:      8     is    -53
Current Value written to Global Variables by thread:     10     is    -52
Current Value written to Global Variables by thread:      9     is    -54
Current Value written to Global Variables by thread:      2     is    -53
Current Value written to Global Variables by thread:      1     is    -54
Current Value written to Global Variables by thread:      4     is    -53
Current Value written to Global Variables by thread:      3     is    -55
Current Value written to Global Variables by thread:      6     is    -54
Current Value written to Global Variables by thread:      5     is    -56
Current Value written to Global Variables by thread:      7     is    -57
Current Value written to Global Variables by thread:      8     is    -56
Current Value written to Global Variables by thread:     10     is    -55
Current Value written to Global Variables by thread:      9     is    -56
Current Value written to Global Variables by thread:      2     is    -55
Current Value written to Global Variables by thread:      1     is    -57
Current Value written to Global Variables by thread:      4     is    -56
Current Value written to Global Variables by thread:      3     is    -58
Current Value written to Global Variables by thread:      6     is    -57
Current Value written to Global Variables by thread:      5     is    -58
Current Value written to Global Variables by thread:      8     is    -57
Current Value written to Global Variables by thread:      7     is    -59
Current Value written to Global Variables by thread:     10     is    -58
Current Value written to Global Variables by thread:      9     is    -60
Current Value written to Global Variables by thread:      2     is    -59
Current Value written to Global Variables by thread:      1     is    -61
Current Value written to Global Variables by thread:      4     is    -60
Current Value written to Global Variables by thread:      3     is    -61
Current Value written to Global Variables by thread:      6     is    -60
Current Value written to Global Variables by thread:      5     is    -62
Current Value written to Global Variables by thread:      7     is    -63
Current Value written to Global Variables by thread:      8     is    -62
Current Value written to Global Variables by thread:     10     is    -61
Current Value written to Global Variables by thread:      9     is    -63
Current Value written to Global Variables by thread:      2     is    -62
Current Value written to Global Variables by thread:      1     is    -64
Current Value written to Global Variables by thread:      4     is    -63
Current Value written to Global Variables by thread:      3     is    -65
Current Value written to Global Variables by thread:      5     is    -66
Current Value written to Global Variables by thread:      6     is    -65
Current Value written to Global Variables by thread:      8     is    -64
Current Value written to Global Variables by thread:      7     is    -66
Current Value written to Global Variables by thread:     10     is    -65
Current Value written to Global Variables by thread:      9     is    -66
Current Value written to Global Variables by thread:      2     is    -65
Current Value written to Global Variables by thread:      1     is    -67
Current Value written to Global Variables by thread:      4     is    -66
Current Value written to Global Variables by thread:      3     is    -67
Current Value written to Global Variables by thread:      6     is    -66
Current Value written to Global Variables by thread:      5     is    -68
Current Value written to Global Variables by thread:      8     is    -67
Current Value written to Global Variables by thread:      7     is    -68
Current Value written to Global Variables by thread:     10     is    -67
Current Value written to Global Variables by thread:      9     is    -69
Current Value written to Global Variables by thread:      2     is    -68
Current Value written to Global Variables by thread:      1     is    -70
Current Value written to Global Variables by thread:      4     is    -69
Current Value written to Global Variables by thread:      3     is    -71
Current Value written to Global Variables by thread:      6     is    -70
Current Value written to Global Variables by thread:      5     is    -72
Current Value written to Global Variables by thread:      8     is    -71
Current Value written to Global Variables by thread:      7     is    -73
Current Value written to Global Variables by thread:     10     is    -72
Current Value written to Global Variables by thread:      9     is    -74
Current Value written to Global Variables by thread:      2     is    -73
Current Value written to Global Variables by thread:      1     is    -74
Current Value written to Global Variables by thread:      4     is    -73
Current Value written to Global Variables by thread:      3     is    -75
Current Value written to Global Variables by thread:      6     is    -74
Current Value written to Global Variables by thread:      5     is    -75
Current Value written to Global Variables by thread:      8     is    -74
Current Value written to Global Variables by thread:      7     is    -76
Current Value written to Global Variables by thread:     10     is    -75
Current Value written to Global Variables by thread:      9     is    -76
Current Value written to Global Variables by thread:      1     is    -77
Current Value written to Global Variables by thread:      2     is    -76
Current Value written to Global Variables by thread:      4     is    -75
Current Value written to Global Variables by thread:      3     is    -77
Current Value written to Global Variables by thread:      6     is    -76
Current Value written to Global Variables by thread:      5     is    -78
Current Value written to Global Variables by thread:      8     is    -77
Current Value written to Global Variables by thread:      7     is    -79
Current Value written to Global Variables by thread:     10     is    -78
Current Value written to Global Variables by thread:      9     is    -79
Current Value written to Global Variables by thread:      2     is    -78
Current Value written to Global Variables by thread:      1     is    -79
Current Value written to Global Variables by thread:      4     is    -78
Current Value written to Global Variables by thread:      3     is    -79
Current Value written to Global Variables by thread:      6     is    -78
Current Value written to Global Variables by thread:      5     is    -80
Current Value written to Global Variables by thread:      8     is    -79
Current Value written to Global Variables by thread:      7     is    -81
Current Value written to Global Variables by thread:     10     is    -80
Current Value written to Global Variables by thread:      9     is    -81
Final Value of Shared Variable is: -81

