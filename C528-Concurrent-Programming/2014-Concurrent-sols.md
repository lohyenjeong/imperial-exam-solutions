### 1ai)
```
ALARM = COUNT[5],
COUNT[i:0..5] = (when (i>1) tick[i] -> COUNT[i-1]
				|when (i==1) tick[i] -> beep -> STOP
				|off -> STOP
				|snooze -> ALARM).
```

### ii)

```
PROGRAM = (write -> COMPILE),

COMPILE = (compile -> COMPILED
			|compile->ERR),
COMPILED = (ok -> run-> STOP),

ERR = (error[1] -> fix[1] -> (compile->STOP|error[2]->fix[2]->compile->COMPILED)).
```

### iii)

```
property SAFE = (wakeup -> eat ->EATEN),

HAPPY = (wakeup -> eat -> EATEN),
EATEN = (sleep -> HAPPY
		|rest-> EATEN
		|surf->SURF
		|read -> READ),
READ = (read->READ
		|eat->EATEN),
SURF = (surf ->SURF
		|eat -> EATEN).
```

### bi)

```
TURTLE = (north -> move -> TURTLE
		| south -> move -> TURTLE).

CONSTRAIN = STOP + {north}.

||SYS = (CONSTRAIN || TURTLE).
```

### ii)

```
GATE = (in->out->GATE).

||AND = (a:GATE || b:GATE)/{out/{a.out,b.out}}
```

### iii)

```
set A = {heads,tails}

HeadsOrTails = PICK,
PICK = (pick[i:A] -> FLIP[i]),
FLIP[i:A] = (flip[j:A] -> RESULT[i][j]),
RESULT[i:A][j:A] = (when (i==j) win -> HeadsOrTails
					|when (j!=i) lose -> HeadsOrTails).
```

### ci)

synchronised prevents multiple invocations of synchronised methods on the same object from interleaving: threads calling synchronised methods must acquire the object’s monitor lock.
					
wait() suspends the current thread and releases the monitor lock. A thread calling wait on an object must own its monitor lock. Execution is resumed after a call to notify() or notifyAll().
When synchronising conditions, the usual pattern is:

```					
synchronised void doStuff() {
    while (!cond) {


					
wait(); }
					
}

```
					
Whenever the condition changes, notify() is called to wake up the current thread. wait() must be placed in a loop since the thread that woke up is not guaranteed to reacquire the monitor lock instantly, thus another condition can change the condition in the meantime.



### ii)

Yes - when the first thread calls wait, it releases the monitor lock, so the second thread is able to acquire it, enter the function, print the message and get blocked on wait().

### 2ai)

```
const MAX = 2


LIBRARY_ACC = COUNT[0],
COUNT[i:0..MAX] = (when i<MAX borrow -> COUNT[i+1]
				  |when i>0 return[j:1..i] -> COUNT[i-j]
				  |when i<MAX freeze -> FREEZE[i]),

FREEZE[i:0..MAX] = (when i>0 return[j:1..i] -> COUNT[i-j]
				   |when i>0 unfreeze -> COUNT[i]).
```

### 3a

“A safety property asserts that nothing bad happens during execution. A liveness property asserts that something good eventually happens. Another way of putting this is that safety is concerned with a program not reaching a bad state and that liveness is concerned with a program eventually reaching a good state.”

### b

```
const M = 5
const MAX = 7 

set Arsenal = {arsenal[1..MAX]}
set Chelsea = {chelsea[1..MAX]}

SUPPORTER = (enter -> dine -> exit -> SUPPORTER).
DININGROOM = 

||PUB = (Chelsea:SUPPORTER || ARSENAL:SUPPORTER|| DININGROOM).
```
