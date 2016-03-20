# Section A
### 1a)

Alphabet extension is used to extend the implicit alphabet of a process to include actions that are not used by it. It is used to prevent unwanted free actions from occurring when composing it with other processes. Since, after alphabet extension, two processes might share an action that does not occur in one of them, that action will not occur in the composite process either.

### bi)

```
MILK=COUNT[4],
COUNT[i:0..4] = (when(i>1) glass -> COUNT[i-2]
				|when(i>0) half_glass -> COUNT[i-1]
				|level[i] -> COUNT[i]
				|when(i==0) shop -> COUNT[4]).
				
```

### bii)

```
RANCID = COUNT[0],
COUNT[i:0..4] = (when (i<4) nextDay -> COUNT[i+1]
				|when (i<4) {glass, half_glass,shop} -> COUNT[0]
				|when (i==4) shop -> COUNT[0]
				|{glass, half_glass} -> ERROR)
				
				
```

### biii)

```
SHOPPING = (shop -> cloudy -> {beer,milk} -> SHOPPING
			|shop -> rainy -> milk-> SHOPPING
			|shop -> sunny -> beer-> SHOPPING).
```
```
const MAX = 4
SEQ_READ(N=2) = (open -> OPEN[N]),
OPEN[i:1..MAX] = (read -> OPEN[i-1]),
OPEN[0] = (close -> END).

||CONCAT = (a: SEQ_READ(2) || b:SEQ_READ(3))/{open/a.open,close/b.close,continue/{a.close,b.open}}.
```
```
SPACE_HOG = (new -> outOfMemory -> SPACE_HOG 
			|new -> reference -> SPACE_HOG).

SPACED_OUT = STOP + {outOfMemory}.

||TOGETHER = (SPACE_HOG||SPACED_OUT).
```
### 2ai)

### ii)

### iii)

### 2b)

Simply put, it depends on why your threads are waiting to be notified. Do you want to tell one of the waiting threads that something happened, or do you want to tell all of them at the same time?

In some cases, all waiting threads can take useful action once the wait finishes. An example would be a set of threads waiting for a certain task to finish; once the task has finished, all waiting threads can continue with their business. In such a case you would use notifyAll() to wake up all waiting threads at the same time.

Another case, for example mutually exclusive locking, only one of the waiting threads can do something useful after being notified (in this case acquire the lock). In such a case, you would rather use notify(). Properly implemented, you could use notifyAll() in this situation as well, but you would unnecessarily wake threads that can't do anything anyway.

http://stackoverflow.com/questions/37026/java-notify-vs-notifyall-all-over-again

```
Consider the following piece of code that's executed from multiple parallel threads:

synchronized(this) {
    while(busy) // a loop is necessary here
        wait();
    busy = true;
}
...
synchronized(this) {
    busy = false;
    notifyAll();
}

```

It can be made more efficient by using notify():
```
synchronized(this) {
    if(busy)   // replaced the loop with a condition which is evaluated only once
        wait();
    busy = true;
}
...
synchronized(this) {
    busy = false;
    notify();
}
```

### 3

a.

```
BUFF = COUNT[0],
COUNT[i:0..8] = (when i>0 get -> BUFF[i-1],
		|when i<8 put -> BUFF[i+1],
		|when 
```

b.

```
public class MyBuffer implements Buffer {
	private char[] chars;
	private int curr = 0;
	private int used = 0;

	public synchronized void put(char ch) throws InterruptedException {
		while (used == N) wait();
		used++;
		chars[curr] = ch;
		curr = (curr + 1) % N;
		notifyAll();
	}

	public synchronized char swap(char[] ch, int s) {
		while (used + s >= N || s >= used) wait();
		char[] ans;
		for (int i = 0; i < s; i++) ans[i] = get();
		for (int i = 0; i < s; i++) put(ch[i]);
		return ans;
	}

	public synchronized char get() throws InterruptedError {
		while (used == 0) wait();
		used--;
		char ans = chars[curr];
		curr (curr + N - 1) % N;
		return ans;
	}
}
```

c.



### 4

a.

```
public synchronized void aquireWrite() throws InterruptedException{
	while (readers > 0 || writing) wait();
	writer = true;
}

public synchronized void aquireRead() throws InterruptedException{
	while (writer) wait();
	readers++;
}

// how to share lock?
```

b.

```
public synchronized void releaseRead(){
	readers--;
	if(readers ==0) notify();
}

public synchronized void releaseWrite(){
	write = false;
	notifyAll();
}
```

c. 
Prevents accidental sharing or escapement of locks: Synchronized methods can sometimes cause bad behavior. One example is creating implicit dependencies between different synchronized methods of the same object, as they share the same lock. A worse scenario is declaring synchronized methods in a base class (which might even be a 3rd party class) and then adding new synchronized methods to a derived class. This creates implicit synchronization dependencies across the hierarchy and has the potential of creating throughput issues or even deadlocks. To avoid these, it’s recommended to use a privately held object as a lock to prevent accidental sharing or escapement of locks.

Increase security:  When using an intrinsic lock, an attacker can manipulate the system to trigger contention and deadlock by obtaining and indefinitely holding the intrinsic lock of an accessible class, consequently causing a denial of service (DoS). By using a privately held lock, lock contention between methods and those of a hostile class becomes impossible because the hostile class cannot access the private final lock object.


