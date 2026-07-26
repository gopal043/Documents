
+++++++++++++++++++++++++++ JVM Memory Overview++++++++++++++++++++++
JVM Memory Overview
When a Java application starts, the JVM allocates several memory areas:
JVM Memory

├── Heap
│   ├── Young Generation
│   │   ├── Eden 
│   │   ├── Survivor S0
│   │    └── Survivor S1
│   └── Old Generation
│
├── Thread 1 Stack
├── Thread 2 Stack
├── Thread N Stack
│
├── Metaspace
│
├── Code Cache
│
└── Native Memory
________________________________________
1. Heap Memory
What is Heap?
Heap is the memory area where objects and instance variables are stored.
Every object created using new is typically allocated in the heap.
Example:
Employee emp = new Employee();
emp reference is stored in Stack.
Employee object is stored in Heap.
________________________________________
Characteristics
•	Shared by all threads 
•	Largest memory area 
•	Managed by Garbage Collector 
•	Stores object instances 
•	Stores arrays 
________________________________________
Example
public class Test {

    public static void main(String[] args) {

        String name = "John";

        Employee emp = new Employee();

        emp.setId(101);
    }
}
Memory:
Stack                     Heap

name -------------> "John"

emp --------------> Employee Object 
id = 101
________________________________________
Heap Structure
Modern JVM divides heap into generations.
Heap

├── Young Generation
│   ├── Eden
│   ├── Survivor S0
│   └── Survivor S1
│
└── Old Generation
________________________________________
Eden Space
New objects are first created here.
for(int i=0;i<1000;i++) {
   new Employee();
 }
All 1000 objects start in Eden.
________________________________________
Survivor Spaces
Two survivor spaces:
S0
S1
After Minor GC:
Eden -> S0
Next GC:
S0 -> S1
Objects surviving multiple GCs get promoted.
________________________________________
Old Generation (Tenured)
Long-living objects move here.
Examples:
•	Cache objects 
•	Singleton objects 
•	Application-level data 
Young Gen
    ↓
Old Gen
________________________________________
Heap Garbage Collection
Minor GC
Cleans Young Generation.
Fast.
Eden Full
   ↓
Minor GC
________________________________________
Major GC
Cleans Old Generation.
Slower.
________________________________________
Full GC
Entire Heap cleanup.
Young + Old
Most expensive.
________________________________________
Common Heap Errors
OutOfMemoryError
List<Object> list = new ArrayList<>();

while(true){
    list.add(new Object());
}
Result:
java.lang.OutOfMemoryError: Java heap space
Heap exhausted.
________________________________________
Memory Leak in Java
Java can still have memory leaks.
Example:
static List<Employee> cache = new ArrayList<>();

cache.add(emp);
Objects remain referenced forever.
GC cannot remove them.
________________________________________
2. Stack Memory
What is Stack?
Each thread gets its own stack.
Stores:
•	Method calls 
•	Local variables 
•	References 
________________________________________
Example
public static void main(String[] args) {

    int a = 10;

    Employee emp = new Employee();

    display();
}
Stack Frame:
main()
----------------
a = 10

emp ---> Heap Object
________________________________________
Stack Frame
Each method invocation creates a frame.
main()
{
   methodA();
}
Memory:
Top

methodA Frame

main Frame

Bottom
When method returns:
methodA Frame removed
Automatically.
________________________________________
Example
public void methodA() {

    int x = 10;

    methodB();
}
Stack

methodB Frame

methodA Frame

main Frame
________________________________________
Why Stack is Fast
Stack follows:
LIFO
(Last In First Out)
Push:
method call
Pop:
method return
No GC needed.
________________________________________
Stack Overflow
Example:
public void test() {
    test();
}
Result:
StackOverflowError
Because:
Frame
Frame
Frame
Frame
Frame
...
Stack becomes full.
________________________________________
Stack vs Heap Example
Employee emp = new Employee();
Stack:
emp
Heap:
Employee Object
Reference stored in Stack.
Actual object stored in Heap.
________________________________________
Multi-threading Perspective
Each thread has its own stack.
Thread-1 Stack

Thread-2 Stack

Thread-3 Stack
Therefore:
Local variables are thread-safe.
public void process() {
    int count = 0;
}
Every thread gets its own count.
No sharing.
________________________________________
3. Metaspace
Introduced in Java 8.
Before Java 8:
PermGen
After Java 8:
Metaspace
________________________________________
What is Metaspace?
Stores class metadata.
Not object data.
Examples:
•	Class definitions 
•	Method metadata 
•	Field metadata 
•	Runtime constant pool 
________________________________________
Example:
class Employee {

    private int id;

    public void work() {}
}
Metaspace stores:
Employee Class Information

Fields:
id

Methods:
work()
Not Employee objects.
Objects go to Heap.
________________________________________
Memory Separation
Employee emp = new Employee();
Heap:
Employee Object
Metaspace:
Employee Class Metadata
Stack:
emp reference
________________________________________
Why Metaspace Was Introduced
PermGen had fixed size.
Developers frequently saw:
OutOfMemoryError: PermGen space
Java 8 replaced it.
Metaspace uses native memory.
Can grow dynamically.
________________________________________
Metaspace Error
Still possible.
Example:
while(true) {
   createClassDynamically();
}
Result:
OutOfMemoryError: Metaspace
Usually happens with:
•	Dynamic proxies 
•	Reflection-heavy frameworks 
•	Classloader leaks 
•	Application servers 
________________________________________
Class Loading Process
When JVM sees:
Employee emp = new Employee();
Step 1:
Class Loader loads Employee.
Step 2:
Metadata stored in Metaspace.
Step 3:
Static variables initialized.
Step 4:
Object allocated in Heap.
________________________________________
Where Static Variables Live?
This is a common interview question.
Before Java 8:
PermGen confusion
Modern JVM:
class Test {
    static int count = 100;
}
count is associated with class metadata but its storage is on the heap as part of the class's runtime representation.
So interview answer:
Class metadata is in Metaspace; static fields are stored in heap memory associated with the loaded class.
________________________________________
Memory Comparison
Feature	Stack	Heap	Metaspace
Stores	Local variables, references, method frames	Objects, arrays	Class metadata
Shared	No	Yes	Yes
Per Thread	Yes	No	No
GC Managed	No	Yes	Partially via class unloading
Speed	Very Fast	Slower	Fast
Error	StackOverflowError	OutOfMemoryError: Java heap space	OutOfMemoryError: Metaspace
________________________________________
Interview-Level Flow
For:
Employee emp = new Employee();
What happens?
1.	JVM checks if Employee class is loaded. 
2.	If not, ClassLoader loads it. 
3.	Metadata stored in Metaspace. 
4.	Memory allocated in Heap. 
5.	Constructor executes. 
6.	Reference variable emp created in current Stack frame. 
7.	emp points to Heap object. 
Memory view:
Stack
-----
emp ----+

Heap
-----
Employee Object

Metaspace
---------
Employee Class Metadata

1. If no GC is required for Stack, who cleans Stack after StackOverflowError?
First understand:
GC only works on Heap.
Stack memory works differently.
Every method call creates a stack frame:
main()
{
   methodA();
}
Stack:
methodA Frame
main Frame
When methodA() returns:
main Frame
The JVM simply moves the stack pointer back.
No garbage collection.
No scanning.
No marking.
No sweeping.
Just:
POP FRAME
which is an O(1) operation.
________________________________________
What happens during StackOverflowError?
public void test() {
    test();
}
Stack becomes:
test()
test()
test()
test()
test()
...
Eventually the thread reaches its maximum stack size.
Default is often around:
512 KB - 2 MB
depending on JVM and OS.
Then JVM throws:
StackOverflowError
________________________________________
Who cleans it?
When exception unwinds:
try {
    test();
}
catch(StackOverflowError e) {
}
JVM starts popping frames.
Frame 10000 removed
Frame 9999 removed
Frame 9998 removed
...
If thread terminates:
Entire stack disappears instantly.
Thread dies
↓
Entire Stack released
No GC involved.
________________________________________
2. Who moves objects from Eden → Survivor → Old Generation?
Answer:
Garbage Collector itself.
________________________________________
Example:
Employee emp = new Employee();
Object created in Eden.
Young Gen

Eden
 └── Employee
________________________________________
When Eden becomes full:
GC triggers Minor GC.
Not based on time.
Not every minute.
Not every hour.
Trigger:
Eden has insufficient space
for new allocation.
________________________________________
During Minor GC:
Step 1
Find live objects.
Employee -> referenced
Student  -> referenced

TempObject -> unreachable
________________________________________
Step 2
Delete unreachable objects.
TempObject removed
________________________________________
Step 3
Move survivors.
Eden → Survivor0
________________________________________
Next Minor GC:
Survivor0 → Survivor1
________________________________________
Next Minor GC:
Survivor1 → Survivor0
Objects "bounce" between survivor spaces.
________________________________________
Each object has an age.
Age = 1
Age = 2
Age = 3
...
When age reaches threshold:
Usually:
15
(default, configurable)
Object promoted:
Old Generation
________________________________________
3. How JVM decides to call GC?
Very common interview question.
The JVM doesn't run GC at fixed intervals.
It reacts to memory pressure.
________________________________________
Minor GC
Triggered when:
Eden becomes full
Example:
for(int i=0;i<100000;i++)
{
    new Employee();
}
Many allocations.
Eden fills.
Minor GC runs.
________________________________________
Major GC
Triggered when:
Old Generation is running low
Example:
Cache stores millions of objects
Old Gen fills.
Major GC runs.
________________________________________
Full GC
Triggered when JVM believes:
Need cleanup across heap
Possible reasons:
1
Heap almost exhausted.
2
Developer calls
System.gc();
(not guaranteed)
3
Metaspace pressure.
4
GC algorithm decides.
________________________________________
How does GC find live objects?
This is important.
GC does NOT scan every object randomly.
It starts from GC Roots.
GC Roots include:
Stack references

Static variables

JNI references

Running threads
Example:
Employee emp = new Employee();
Stack:
emp
Heap:
Employee Object
GC sees:
GC Root
  ↓
 emp
  ↓
 Employee
Reachable.
Keep it.
________________________________________
If:
emp = null;
Now:
Employee Object
has no path from GC Roots.
GC removes it.
________________________________________
4. What exactly is a Long-Lived Object?
Many people misunderstand this.
It does NOT mean:
Alive for 10 minutes
or
Alive for 1 hour
There is no clock.
________________________________________
GC cares about:
Number of GC cycles survived
not actual time.
________________________________________
Example:
Employee cache = new Employee();
Suppose:
Minor GC #1
cache survives
Age = 1
Minor GC #2
cache survives
Age = 2
...
Minor GC #15
Age = 15
Promoted.
Old Generation
________________________________________
Why move to Old Gen?
JVM uses a principle called:
Generational Hypothesis
Most objects die young.
Example:
String temp = request.getName();
dies quickly.
________________________________________
Objects surviving many GCs are likely important.
So JVM puts them in Old Gen.
________________________________________
Why GC doesn't remove them immediately?
It absolutely CAN.
This is a common misconception.
Old Gen objects are not immortal.
Example:
Employee cache = new Employee();

cache = null;
Now object is unreachable.
Major GC can reclaim it.
________________________________________
Old Gen only means:
Long surviving object
not
Permanent object
________________________________________
5. What are Memory Leaks in Java?
A Java memory leak means:
Object is no longer useful
BUT
still reachable
GC sees reference.
Therefore cannot remove it.
________________________________________
Leak Type 1: Static Collections
public class Cache {

   static List<Employee> list = new ArrayList<>();
}
list.add(employee);
Never removed.
Objects accumulate forever.
________________________________________
Leak Type 2: HashMap Cache
Map<String,Object> cache = new HashMap<>();
cache.put(id,obj);
Never evicted.
Memory grows forever.
________________________________________
Leak Type 3: Listener Leak
button.addListener(listener);
Forgot:
button.removeListener(listener);
Listener remains referenced.
________________________________________
Leak Type 4: ThreadLocal Leak
Very famous in Spring/Tomcat.
ThreadLocal<User> user = new ThreadLocal<>();
Set:
user.set(obj);
Forgot:
user.remove();
Thread pool thread survives.
Object survives.
Leak.
________________________________________
Leak Type 5: ClassLoader Leak
Common in application servers.
Tomcat
JBoss
WebLogic
Old ClassLoader still referenced.
Classes cannot unload.
Metaspace grows.
________________________________________
Leak Type 6: Unclosed Resources
Connection
InputStream
Socket
FileHandle
Not always heap leaks.
Can become native memory leaks.
________________________________________
6. What does "Metaspace uses Native Memory" mean?
This is one of the most misunderstood JVM topics.
________________________________________
Before Java 8:
Heap
 └── PermGen
PermGen was inside JVM-managed heap.
________________________________________
After Java 8:
Heap

Metaspace
   ↓
Native Memory
________________________________________
What is Native Memory?
Memory allocated from OS.
RAM
 ↓
Operating System
 ↓
JVM requests memory
Think:
malloc()
in C/C++ world.
________________________________________
Example JVM Memory
Total RAM = 16 GB

Heap = 8 GB

Metaspace = 500 MB

Code Cache = 200 MB

Thread Stacks = 300 MB

Native Buffers = 400 MB
Heap is only part of JVM memory.
________________________________________
Why move Metaspace to Native Memory?
PermGen caused many issues:
OutOfMemoryError: PermGen space
Frameworks like:
•	Spring Framework 
•	Hibernate 
•	Apache Tomcat 
load lots of classes.
PermGen filled quickly.
________________________________________
Metaspace can grow dynamically.
OS Memory
   ↓
Metaspace expands
until limit reached.
________________________________________
Can Metaspace still crash JVM?
Yes.
OutOfMemoryError: Metaspace
when:
•	Millions of generated classes 
•	ClassLoader leaks 
•	Proxy generation explosion 
________________________________________
Senior-Level Interview Summary
Remember these 5 points:
1.	Stack memory is cleaned by frame popping, not GC. 
2.	Minor GC moves objects Eden → Survivor → Old Generation. 
3.	GC is triggered by memory pressure, not time intervals. 
4.	Long-lived means surviving multiple GC cycles, not elapsed time. 
5.	Java memory leaks happen because reachable objects cannot be collected. 
6.	Metaspace lives in OS native memory, outside the Java heap. 

1. Why do objects start in Eden?
This comes from a JVM observation called:
Generational Hypothesis
JVM engineers observed:
Most objects die young.
Example:
public String getUserName() {

    String temp = request.getName();

    return temp;
}
Objects like:
•	Request DTOs 
•	Temporary Strings 
•	Stream objects 
•	Local collections 
often live only a few milliseconds.
________________________________________
Imagine:
for(int i=0;i<1000000;i++) {
    new Employee();
}
Suppose JVM directly put everything into Old Gen.
Old Gen

Emp1
Emp2
Emp3
...
Emp1000000
GC would need to scan a massive area repeatedly.
Very expensive.
________________________________________
Instead JVM says:
Most objects die quickly.
Let's keep them in a small area.
That area is:
Young Generation

└── Eden
________________________________________
Why Eden is Fast
Allocation is almost:
object = nextFreeAddress;
nextFreeAddress += objectSize;
No searching.
No fragmentation handling.
Just pointer movement.
This is why object creation in Java is surprisingly cheap.
________________________________________
2. How Survivor Aging Works
Let's create:
Employee emp = new Employee();
Initially:
Eden

Employee(age=0)
________________________________________
Minor GC runs.
Object still referenced.
Stack
  |
 emp
  |
Employee
So GC keeps it.
Moves to Survivor.
S0

Employee(age=1)
________________________________________
Next Minor GC
Still alive.
S1

Employee(age=2)
________________________________________
Next Minor GC
S0

Employee(age=3)
________________________________________
This continues.
Age=4
Age=5
Age=6
...
________________________________________
Why age objects?
Because JVM wants to know:
Temporary?
or
Likely long-term?
Think of airport security:
New traveler → extra checks

Frequent traveler → trusted lane
Same idea.
________________________________________
3. Why Promote to Old Generation?
Suppose object survived:
15 Minor GCs
JVM thinks:
This object is probably important.
Examples:
Cache cache = new Cache();

static Config config = new Config();

Singleton singleton = Singleton.getInstance();
These objects often live for hours.
________________________________________
Without promotion:
Minor GC

Scan object

Minor GC

Scan object

Minor GC

Scan object

Minor GC

Scan object
Wasteful.
________________________________________
Instead:
Young Gen
      ↓
Old Gen
Move it once.
Then Young GC can ignore it.
Huge performance improvement.
________________________________________
Why Old Gen Exists
Without generations:
GC scans 10 million objects
every collection.
Terrible.
________________________________________
With generations:
Minor GC

Only scan Young Gen
Maybe:
20 MB
instead of
8 GB
Massive difference.
________________________________________
4. How Stack Frames Are Created and Destroyed
Let's trace execution.
main()
{
   service();
}
Start:
Stack

main()
________________________________________
Call service()
Stack

service()
main()
________________________________________
Inside service:
void service() {

   int x = 10;

   dao();
}
Frame:
service Frame

x=10
________________________________________
Call dao()
Stack

dao()
service()
main()
________________________________________
dao returns.
JVM simply moves stack pointer.
Stack

service()
main()
No GC.
No scanning.
No cleanup.
Just:
POP
________________________________________
This is why stack memory is extremely fast.
________________________________________
5. How Class Loading Populates Metaspace
Suppose JVM sees:
Employee emp = new Employee();
First question:
What is Employee?
JVM doesn't know yet.
________________________________________
It loads:
Employee.class
using ClassLoader.
________________________________________
Class file contains:
class Employee {

   int id;

   String name;

   void work() {}
}
________________________________________
JVM parses bytecode.
Creates metadata:
Class Name

Employee

Fields
-------
id
name

Methods
-------
work()

Constructors

Annotations
________________________________________
Stores this in:
Metaspace
________________________________________
Then JVM can create objects.
Metaspace
---------
Employee Metadata


Heap
---------
Employee Object #1
Employee Object #2
Employee Object #3
One class definition.
Thousands of objects.
________________________________________
Class Loading Lifecycle
Loading
    ↓

Linking
    ↓

Verification
    ↓

Preparation
    ↓

Resolution
    ↓

Initialization
Interviewers love this.
________________________________________
Loading
Read .class
________________________________________
Verification
Check bytecode safety.
Example:
Prevent:
String x = (String) new Integer(5);
from crashing JVM internals.
________________________________________
Preparation
Allocate static memory.
static int count;
gets default value:
count = 0
________________________________________
Initialization
Run static blocks.
static {
   count = 100;
}
________________________________________
Now class is ready.
________________________________________
6. How GC Roots Determine Reachability
This is the most important GC concept.
________________________________________
GC does NOT ask:
Is object old?
Is object large?
GC only asks:
Can I still reach it?
________________________________________
Example
Employee emp = new Employee();
Memory:
GC Root
  |
 emp
  |
 Employee
Reachable.
Keep it.
________________________________________
Now:
emp = null;
Memory:
Employee
No path from root.
Remove it.
________________________________________
Major GC Concept
Suppose:
Cache cache = new Cache();
Cache promoted to Old Gen.
Years later:
cache = null;
Now:
Old Gen Object
has no path to roots.
Major GC removes it.
________________________________________
So remember:
GC does not collect based on age.

GC collects based on reachability.
Age only affects promotion.
________________________________________
Internal GC Algorithms
Now the fun part.
________________________________________
Mark Phase
GC starts from roots.
Root
 |
 A
 |
 B
 |
 C
Marks:
A ✓
B ✓
C ✓
________________________________________
Unreachable:
X
Y
Z
remain unmarked.
________________________________________
Result:
A ✓
B ✓
C ✓

X
Y
Z
________________________________________
Sweep Phase
Delete unmarked objects.
Before:
A ✓
B ✓
X
Y
C ✓
Z
After:
A ✓
B ✓
_
_
C ✓
_
Problem:
Memory holes
appear.
________________________________________
Compact Phase
Move live objects together.
Before:
A
_
_
B
_
C
After:
A
B
C
_
_
_
Now memory contiguous.
________________________________________
Copying Algorithm
Used heavily in Young Generation.
Instead of:
Mark
Sweep
Compact
JVM simply copies survivors.
________________________________________
Before:
Eden

A
B
C
D
E
Suppose only:
B
D
alive.
________________________________________
Copy:
Survivor

B
D
Discard entire Eden.
________________________________________
Very fast.
Reason Young GC performs so well.
________________________________________
Serial GC
Oldest GC.
Single thread.
GC Thread

Mark
Sweep
Compact
Application pauses.
________________________________________
Good for:
Small heaps
Small applications
________________________________________
Parallel GC
Multiple GC threads.
GC Thread 1
GC Thread 2
GC Thread 3
GC Thread 4
Still pauses application.
But faster.
________________________________________
Goal:
Maximum Throughput
________________________________________
CMS (Concurrent Mark Sweep)
Mostly obsolete now.
Goal:
Reduce pause times
Runs much work concurrently.
Problem:
Memory fragmentation
because no compaction.
Eventually replaced.
________________________________________
G1 GC (Most Common Today)
Default in many JVMs.
Heap split into regions.
Region1
Region2
Region3
Region4
...
Not fixed Young/Old memory blocks.
Regions can change role.
________________________________________
Goal:
Predictable pause times
Example:
Pause target = 200 ms
G1 tries to honor it.
________________________________________
Production default choice.
________________________________________
ZGC
Designed for:
Huge heaps
Examples:
64 GB
128 GB
512 GB
Pause times:
< 1 ms
to
few ms
even on huge heaps.
________________________________________
Uses:
Colored pointers
Load barriers
Concurrent relocation
Very advanced.
________________________________________
Shenandoah
Similar philosophy.
Goal:
Ultra-low pause
Moves objects while application runs.
________________________________________
Popular in:
•	Large server applications 
•	Low-latency systems 
________________________________________
Interview Cheat Sheet
Topic	Key Idea
Eden	New objects start here because most die young
Survivor	Tracks object age across Minor GCs
Promotion	Long-surviving objects move to Old Gen
Stack Frame	Created per method call, destroyed on return
Metaspace	Stores class metadata
GC Root	Starting point for reachability analysis
Mark	Find live objects
Sweep	Remove dead objects
Compact	Remove fragmentation
Copying	Copy survivors, discard rest
Serial GC	One GC thread
Parallel GC	Multiple GC threads
CMS	Concurrent, fragmented, largely obsolete
G1	Region-based, balanced default
ZGC	Very low pauses, huge heaps
Shenandoah	Concurrent compaction, low latency

you'll connect Class Loading + Metaspace + Stack + Heap + Object Headers + GC Roots + JIT + Allocation into one mental model.
Let's walk through it like the JVM does.
________________________________________
Step 0: Source Code
You write:
Employee emp = new Employee();
You compile:
javac Employee.java
Produces:
Employee.class
This is bytecode, not machine code.
________________________________________
Step 1: JVM Starts
When JVM starts:
java Main
it creates:
JVM Process

Heap
Metaspace
Code Cache
Main Thread Stack
GC Threads
JIT Threads
________________________________________
Step 2: Main Class Loads
JVM loads:
public class Main {
   public static void main(String[] args) {
      Employee emp = new Employee();
   }
}
Application ClassLoader loads:
Main.class
Metadata stored in Metaspace.
Metaspace

Main Metadata
________________________________________
Step 3: main() Stack Frame Created
Before executing:
main()
JVM creates frame:
Main Thread Stack

+------------------+
| main() frame     |
+------------------+
Inside frame:
Local Variables Table

args
emp (currently empty)
________________________________________
Step 4: JVM Reads Bytecode
For:
Employee emp = new Employee();
Bytecode roughly looks like:
new Employee
dup
invokespecial Employee.<init>()
astore_1
Meaning:
1. Create object
2. Duplicate reference
3. Call constructor
4. Store reference in local variable
________________________________________
Step 5: Is Employee Class Loaded?
JVM checks:
Has Employee class been loaded?
If NO:
Load Employee.class
________________________________________
Step 6: Class Loading
ClassLoader reads:
Employee.class
from disk.
Example:
class Employee {

    int id;
    String name;

    public Employee() {
    }
}
________________________________________
Step 7: Verification
JVM verifies bytecode.
Checks:
Valid instructions?
Valid inheritance?
Type safety?
Prevents corrupted bytecode.
________________________________________
Step 8: Prepare Metadata
JVM creates metadata structure.
Stored in Metaspace.
Metaspace

Employee Metadata

Fields:
--------
id
name

Methods:
--------
<Employee>()
toString()
hashCode()

Constant Pool

Method Tables
This is NOT an object.
This is class information.
________________________________________
Step 9: Class Initialization
Static fields initialized.
Example:
class Employee {

   static int counter = 100;
}
JVM executes:
<Employee>.clinit()
Static initializer.
Now class ready.
________________________________________
Step 10: Object Allocation Begins
JVM executes:
new Employee
Now Heap allocation starts.
________________________________________
Step 11: Heap Allocation
Assume:
Young Generation

Eden
Current memory:
Eden

Object1
Object2
Object3

NextFreePointer ->
Allocation is usually:
address = nextFreePointer;
nextFreePointer += objectSize;
Just pointer movement.
Very fast.
________________________________________
Step 12: Object Memory Reserved
Suppose Employee needs:
int id;
String name;
Object memory may look like:
Heap

0x1000
+----------------------+
| Object Header        |
+----------------------+
| int id               |
+----------------------+
| String name ref      |
+----------------------+
Memory reserved.
________________________________________
Step 13: Object Header Created
Every object has header.
Most developers know fields.
Few understand header.
Example:
new Employee()
Actually becomes:
+------------------------+
| Mark Word              |
+------------------------+
| Klass Pointer          |
+------------------------+
| Instance Fields        |
+------------------------+
________________________________________
Mark Word
Stores runtime information.
Examples:
Lock State

HashCode

GC Age

Biased Lock Info
Example:
emp.hashCode()
hash often stored here.
________________________________________
Klass Pointer
Points to class metadata.
Object
  |
  v
Employee Metadata
Stored in Metaspace.
________________________________________
Visual:
Heap

Employee Object
      |
      |
      v

Metaspace

Employee Metadata
This connection is extremely important.
________________________________________
Step 14: Zero Initialization
JVM initializes fields.
Before constructor:
int id;
String name;
becomes:
id = 0;
name = null;
Memory:
Employee

id=0
name=null
________________________________________
Step 15: Constructor Executes
Now JVM executes:
Employee() {
   id = 101;
}
Object becomes:
Employee

id=101
name=null
________________________________________
Step 16: Reference Stored In Stack
Now:
Employee emp = new Employee();
stores reference.
Stack:
main() Frame

emp -----> 0x1000
Heap:
0x1000

Employee Object
________________________________________
Final Memory Layout
Stack

main()
----------------

emp ----+
         |
         |
         v

Heap
----------------

Employee Object

Header
id=101
name=null

         |
         |
         v

Metaspace
----------------

Employee Metadata
________________________________________
Step 17: GC Root Tracking
Now GC sees:
GC Root
   |
main frame
   |
emp
   |
Employee Object
Object is reachable.
Cannot be collected.
________________________________________
What If emp = null?
emp = null;
Now:
main frame

emp = null
Object:
Employee Object
has no path from GC Roots.
________________________________________
GC View
Before:
Root
 |
emp
 |
Employee
After:
Root

(no path)

Employee
Unreachable.
Eligible for GC.
________________________________________
Important Interview Question
Is object immediately deleted after:
emp = null;
Answer:
No.
It becomes:
Eligible for GC
not
Removed immediately
GC decides when to reclaim.
________________________________________
What Happens During Minor GC?
Suppose object still referenced.
Root
 |
emp
 |
Employee
GC marks it alive.
Copies:
Eden
   ↓
Survivor
Age becomes:
Age=1
________________________________________
After many Minor GCs:
Age=15
Promoted.
Old Generation
________________________________________
What Happens If Employee Class Never Used Again?
Imagine web app redeploy.
ClassLoader removed.
No references remain.
Now:
Employee Metadata
becomes unreachable.
GC can unload:
Employee Class
from Metaspace.
This is called:
Class Unloading
________________________________________
Complete Lifecycle of new Employee()
Source Code
    ↓

Bytecode
    ↓

ClassLoader loads Employee.class
    ↓

Metadata stored in Metaspace
    ↓

main() frame created on Stack
    ↓

Heap memory reserved in Eden
    ↓

Object Header created
    ↓

Fields zero-initialized
    ↓

Constructor executes
    ↓

Reference stored in Stack
    ↓

GC Root now reaches object
    ↓

Object survives Minor GCs
    ↓

Moves Eden → Survivor
    ↓

Age increases
    ↓

Promoted to Old Gen
    ↓

Reference removed
    ↓

Object unreachable
    ↓

GC reclaims memory
This lifecycle is essentially the backbone of JVM memory management. Once you're comfortable with this flow, the next advanced topic is understanding object headers in detail (Mark Word, locking states, identity hash code), TLABs (Thread Local Allocation Buffers), escape analysis, and how synchronized uses the object header internally

1. Object Header Internals
When you create:
Employee emp = new Employee();
Most developers think:
Employee
---------
id
name
Reality:
+-------------------+
| Mark Word         |
+-------------------+
| Klass Pointer     |
+-------------------+
| Instance Fields   |
+-------------------+
On a 64-bit JVM, the header is usually 12–16 bytes depending on JVM configuration and alignment.
________________________________________
Mark Word
This is the most interesting part.
It stores runtime information about the object.
Think of it as:
Object Passport
The JVM keeps changing its contents during execution.
It may contain:
HashCode
GC Age
Lock State
Thread Ownership Info
Not all simultaneously; the bits are reused depending on object state.
________________________________________
Example
Employee emp = new Employee();
Initially:
Mark Word

Lock = Unlocked
Age = 0
Hash = Not Generated
________________________________________
GC Age in Mark Word
Remember survivor aging?
Eden -> S0 -> S1 -> Old Gen
Each Minor GC increments age.
Age = 1
Age = 2
Age = 3
That age is stored in the Mark Word.
Eventually:
Age = 15
Promotion may occur.
________________________________________
Identity Hash Code
When:
System.identityHashCode(emp);
or often:
emp.hashCode();
(if not overridden)
JVM generates a hash.
Example:
45123789
That value is typically stored in the Mark Word.
________________________________________
Interesting interview question:
synchronized(emp) {
}
and
emp.hashCode();
can interact because both want to use header bits.
The JVM has mechanisms to preserve both.
________________________________________
Klass Pointer
Every object knows its class.
Example:
Employee emp = new Employee();
Header contains:
Klass Pointer
      |
      v

Employee Metadata
in Metaspace.
This enables:
emp.getClass()
without searching everywhere.
________________________________________
How synchronized Uses Object Header
Consider:
synchronized(emp) {

}
Many developers think:
Lock stored somewhere else
For lightweight locks, it's often stored directly in the object's Mark Word.
________________________________________
Before Lock
Mark Word

Unlocked
________________________________________
Thread enters:
synchronized(emp)
JVM updates header.
Mark Word

Locked
Owner = Thread-1
________________________________________
Another thread arrives:
synchronized(emp)
It sees:
Already locked
and must wait or contend.
________________________________________
This is why every Java object can be used as a monitor:
Object lock = new Object();

synchronized(lock) {
}
The lock metadata is associated with the object's header.
________________________________________
Lock States (Modern JVM View)
Conceptually:
Unlocked
    ↓
Lightweight Lock
    ↓
Heavyweight Monitor
________________________________________
Lightweight Lock
If contention is low:
Only one thread uses lock
JVM uses a very cheap mechanism.
No OS mutex.
No context switch.
Very fast.
________________________________________
Heavyweight Monitor
If many threads compete:
Thread-1
Thread-2
Thread-3
Thread-4
JVM inflates lock into a monitor structure.
Now threads may block.
This is expensive.
________________________________________
Why synchronized Is Faster Than Before
In old Java versions:
Every lock = expensive OS monitor
Modern JVMs:
Lightweight lock
for uncontended cases.
Huge performance improvement.
________________________________________
2. TLAB (Thread Local Allocation Buffer)
This is one of the most practical JVM optimizations.
________________________________________
Problem
Suppose:
new Employee();
and
new Customer();
are executed by many threads.
Without optimization:
Thread-1 -> Eden

Thread-2 -> Eden

Thread-3 -> Eden
All threads fight over:
NextFreePointer
Need synchronization.
Slow.
________________________________________
JVM Solution
Give each thread a small private allocation area.
Eden

+----------+
| TLAB-1   |
+----------+

+----------+
| TLAB-2   |
+----------+

+----------+
| TLAB-3   |
+----------+
________________________________________
Now:
Thread-1 allocates:
new Employee();
inside:
TLAB-1
No lock.
No contention.
________________________________________
Allocation becomes:
pointer += objectSize;
Extremely fast.
________________________________________
What Happens When TLAB Fills?
Example:
TLAB Full
Thread requests another TLAB from Eden.
New TLAB allocated
Continues.
________________________________________
This is why object creation is surprisingly cheap in Java.
________________________________________
3. Escape Analysis
One of the coolest JVM optimizations.
________________________________________
Question:
Does this object really need to be on Heap?
Example:
public void process() {

    Employee emp = new Employee();

    emp.setId(10);
}
________________________________________
Observe:
emp never leaves method
No return.
No shared reference.
No field assignment.
________________________________________
JIT may conclude:
Object cannot escape method
This is called:
No Escape
________________________________________
Optimization 1: Stack Allocation
Instead of:
Heap
JIT may keep data in stack/registers.
Conceptually:
Stack Frame

id=10
No heap allocation.
No GC.
________________________________________
Optimization 2: Scalar Replacement
Even more aggressive.
Object disappears completely.
Example:
Employee emp = new Employee();

emp.id = 10;
becomes conceptually:
int id = 10;
Object never created.
________________________________________
Escape Analysis Levels
________________________________________
No Escape
void test() {
   Employee e = new Employee();
}
Used only inside method.
________________________________________
Method Escape
Employee test() {
   return new Employee();
}
Object escapes method.
Cannot optimize as aggressively.
________________________________________
Global Escape
class Service {

   Employee emp;

   void test() {
      emp = new Employee();
   }
}
Object stored globally.
Definitely escapes.
Heap allocation required.
________________________________________
Why Escape Analysis Matters
Without it:
1 million objects
created.
________________________________________
With it:
0 heap allocations
for some cases.
Less GC pressure.
Better performance.
________________________________________
Putting Everything Together
Suppose:
Employee emp = new Employee();
Modern JVM flow:
Thread-1
   |
   v

TLAB Allocation
   |
   v

Object Created

+----------------+
| Mark Word      |
+----------------+
| Klass Pointer  |
+----------------+
| Fields         |
+----------------+

   |
   v

Reference stored in Stack

   |
   v

GC Roots can reach object

   |
   v

Minor GC increments Age

   |
   v

Age stored in Mark Word

   |
   v

Promotion to Old Gen

   |
   v

Reference removed

   |
   v

Object unreachable

   |
   v

GC collects
________________________________________
Senior Interview Questions You Should Be Able to Answer
1.	Why is object creation in Java fast? 
o	TLABs 
o	Pointer bump allocation 
2.	What is stored in an object header? 
o	Mark Word 
o	Klass Pointer 
3.	What is stored in Mark Word? 
o	Lock state 
o	Hash code 
o	GC age 
4.	How does synchronized work internally? 
o	Uses object monitor information associated with the object's header/lock structures. 
o	Can transition from lightweight to heavyweight locking under contention. 
5.	What is Escape Analysis? 
o	JIT determines whether an object escapes its scope. 
6.	What is Scalar Replacement? 
o	Object fields become independent variables; object allocation can disappear. 
7.	What is TLAB? 
o	Per-thread allocation buffer inside Eden to avoid allocation contention. 
If you master these topics along with GC, heap generations, class loading, and thread stacks, you're approaching the JVM knowledge level expected for senior Java performance tuning, JVM troubleshooting, and many architect-level interviews.
Escape Analysis
Now the fun optimization.
________________________________________
Suppose:
public void process() {

    Employee e = new Employee();

    e.setId(10);

    System.out.println(e.getId());
}
Question:
Does this object need Heap?
________________________________________
JIT analyzes code.
Finds:
Object never leaves method
Therefore:
No Escape
________________________________________
What Can JVM Do?
________________________________________
Optimization 1
Avoid Heap Allocation
Instead of:
Heap

Employee
store data directly in stack/registers.
________________________________________
Optimization 2
Scalar Replacement
Object completely disappears.
________________________________________
Original:
Employee e = new Employee();

e.id = 10;
JIT internally may treat as:
int id = 10;
No object.
No allocation.
No GC.
________________________________________
Example
public int calculate() {

    Employee e = new Employee();

    e.salary = 100;

    return e.salary;
}
JIT may transform conceptually:
public int calculate() {

    int salary = 100;

    return salary;
}
Employee object never exists.
________________________________________
Escape Levels
________________________________________
No Escape
void test() {
    Employee e = new Employee();
}
Only local.
________________________________________
Method Escape
Employee test() {
    return new Employee();
}
Escapes method.
________________________________________
Global Escape
class Service {

    Employee e;

    void init() {
        e = new Employee();
    }
}
Escapes globally.
________________________________________
Synchronization Optimization via Escape Analysis
Example:
public void test() {

    Object lock = new Object();

    synchronized(lock) {
        // work
    }
}
No other thread can access lock.
JIT knows this.
May remove synchronization entirely.
Called:
Lock Elimination
Huge optimization.
________________________________________
Real Production Impact
Without Escape Analysis:
10 million objects/sec
created.
________________________________________
With Escape Analysis:
2 million actual heap objects
remaining.
The rest optimized away.
You should understand:
1. Why each GC was created
2. What problem it solved
3. How it works internally
4. What phases are STW
5. What phases are concurrent
6. Tradeoffs
7. When to use it
8. How JVM evolved from one GC to another
________________________________________
Evolution of Java Garbage Collectors
Think of JVM history.
Phase 1
Small Heaps
256 MB
512 MB
GC was simple.
↓
Phase 2
Multi-core CPUs
Bigger heaps
Need faster GC.
↓
Phase 3
24/7 servers
Web applications
Need shorter pauses.
↓
Phase 4
Cloud systems
100GB+ heaps
Need almost no pauses.
________________________________________
That's why collectors evolved:
Serial
   ↓

Parallel
   ↓

CMS
   ↓

G1
   ↓

ZGC / Shenandoah
________________________________________
Before Understanding Collectors
Let's understand GC Phases first.
All modern GCs are variations of:
Mark
Sweep
Compact
Copy
________________________________________
Example Heap
Suppose heap contains:
A
B
C
D
E
F
Reachability:
Root
 |
A
 |
B
 |
C
Only:
A
B
C
alive.
________________________________________
Phase 1: Mark
GC starts from roots.
Root
 |
A
 |
B
 |
C
Marks:
A ✓
B ✓
C ✓
Result:
A ✓
B ✓
C ✓

D
E
F
________________________________________
Question:
Why not delete immediately?
Because GC doesn't yet know which objects are dead.
Mark phase identifies survivors first.
________________________________________
Phase 2: Sweep
Delete unmarked objects.
Before:
A ✓
B ✓
D
E
C ✓
F
After:
A ✓
B ✓
_
_
C ✓
_
________________________________________
Problem:
Memory holes appear.
Called:
Fragmentation
________________________________________
Phase 3: Compact
Move objects together.
Before:
A
_
_
B
_
C
After:
A
B
C
_
_
_
Now contiguous memory.
________________________________________
Why Compact?
Suppose application wants:
new byte[200MB];
Free memory:
100MB
100MB
100MB
Total:
300MB
but fragmented.
Allocation still fails.
Compaction fixes that.
________________________________________
Copying Algorithm
Young GC usually uses copying.
Example:
Eden

A
B
C
D
E
Only:
B
D
alive.
Instead of:
Mark
Sweep
Compact
GC does:
Copy B
Copy D
to Survivor.
Then discard Eden.
Much faster.
________________________________________
Serial GC
Oldest collector.
JVM option:
-XX:+UseSerialGC
________________________________________
Why Created?
Early computers:
Single CPU
Small heap
No need for parallelism.
________________________________________
How It Works
One GC thread.
Application Running

STOP

GC Thread
   Mark
   Sweep
   Compact

START
Everything stops.
________________________________________
Example
Heap = 500MB
Pause might be:
50ms
100ms
200ms
acceptable.
________________________________________
Serial GC Characteristics
Pros
Simple.
Low overhead.
Good for:
Small applications
CLI programs
Containers with tiny memory
________________________________________
Cons
Large heap:
10GB
Pause becomes huge.
________________________________________
Parallel GC
JVM option:
-XX:+UseParallelGC
________________________________________
Problem with Serial:
1 GC Thread
Modern CPU:
16 cores
32 cores
64 cores
Wasteful.
________________________________________
Solution:
Multiple GC threads.
________________________________________
Example
Serial:
Mark:
 100 seconds
________________________________________
Parallel:
Thread1 -> 25%
Thread2 -> 25%
Thread3 -> 25%
Thread4 -> 25%
Maybe:
25 seconds
instead.
________________________________________
Important
Application still stops.
STOP THE WORLD
remains.
Only GC itself is parallelized.
________________________________________
Goal:
Maximum Throughput
Not pause time.
________________________________________
Used when:
Batch jobs
Backend processing
Analytics
________________________________________
CMS (Historical)
JVM option:
-XX:+UseConcMarkSweepGC
Removed from modern JDKs.
Still important conceptually.
________________________________________
Problem
Parallel GC:
Pause 5 seconds
Web users unhappy.
________________________________________
Idea:
Run most GC work while app runs.
________________________________________
CMS Phases
Initial Mark
STW
Quick.
Identify roots.
10ms
________________________________________
Concurrent Mark
Application runs.
GC runs simultaneously.
App Running
GC Running
________________________________________
Remark
Need another STW.
Because application modified references during mark.
________________________________________
Sweep
Concurrent.
________________________________________
CMS Problem
No compaction.
Result:
A
_
_
B
_
_
C
Fragmentation.
Eventually:
Full GC
which is terrible.
________________________________________
Therefore CMS died.
________________________________________
G1 GC
Most important collector today.
Default for many JVM versions.
JVM:
-XX:+UseG1GC
________________________________________
Why G1 Created?
Heap sizes exploded.
32GB
64GB
128GB
Old collectors struggled.
________________________________________
Traditional Heap
Young
Old
Huge fixed spaces.
________________________________________
G1 Idea
Break heap into regions.
+----+
| R1 |
+----+

+----+
| R2 |
+----+

+----+
| R3 |
+----+

...
Maybe:
2048 regions
________________________________________
Each region can become:
Young
Old
Survivor
Humongous
dynamically.
________________________________________
Why Called Garbage First?
G1 tracks:
Region 1 -> 90% garbage

Region 2 -> 10% garbage

Region 3 -> 95% garbage
Collect:
Region 3
Region 1
first.
Maximum gain.
________________________________________
G1 Phases
Young Collection
STW.
Collect young regions.
Fast.
________________________________________
Initial Mark
STW.
Start marking.
________________________________________
Concurrent Mark
Runs while application executes.
Find live objects.
________________________________________
Remark
STW.
Finalize markings.
________________________________________
Cleanup
Determine reclaimable regions.
________________________________________
Mixed Collection
Special G1 feature.
Collects:
Young Regions
+
Selected Old Regions
together.
Avoids giant Full GCs.
________________________________________
G1 Goal
User specifies:
-XX:MaxGCPauseMillis=200
G1 tries to achieve:
~200ms pauses
Not guaranteed.
Best effort.
________________________________________
ZGC
Game changer.
JVM:
-XX:+UseZGC
________________________________________
Problem
Even G1 pauses become noticeable on:
100GB heap
200GB heap
500GB heap
________________________________________
Goal:
Pause < 10ms
regardless of heap size.
________________________________________
ZGC Philosophy
Instead of:
Stop app
Move objects
Resume app
do:
Move objects
while app runs
________________________________________
Crazy idea.
Very hard.
________________________________________
Colored Pointers
ZGC's magic.
Pointer contains metadata.
Not just:
0x123456
but:
0x123456 + state bits
________________________________________
Allows JVM to know:
Marked?
Relocated?
Remapped?
while program runs.
________________________________________
ZGC Phases
Pause Mark Start
Very tiny.
<1ms
________________________________________
Concurrent Mark
Application continues.
GC marks.
________________________________________
Pause Mark End
Tiny pause.
________________________________________
Concurrent Relocate
Move objects.
Application still runs.
________________________________________
Concurrent Remap
Fix references.
Application still runs.
________________________________________
Result
Heap:
16GB
128GB
512GB
Pause remains:
1-5ms
typically.
________________________________________
Tradeoff:
More CPU.
More complexity.
________________________________________
Shenandoah
Developed by:
Red Hat
________________________________________
Goal:
Similar to ZGC.
Ultra-low pause
________________________________________
Key Feature
Concurrent Compaction.
Historically:
Compaction
=
Stop The World
Shenandoah:
Compaction while app runs
________________________________________
Shenandoah Phases
Initial Mark
Tiny pause.
________________________________________
Concurrent Mark
Application running.
________________________________________
Final Mark
Tiny pause.
________________________________________
Concurrent Evacuation
Move objects.
Application still running.
________________________________________
Concurrent Update References
Fix pointers.
Still running.
________________________________________
ZGC vs Shenandoah
Both:
Low latency
Concurrent
Huge heaps
________________________________________
Difference:
ZGC
Uses:
Colored Pointers
Very advanced.
________________________________________
Shenandoah
Uses:
Brooks Pointer
Extra indirection.
Different implementation strategy.
________________________________________
Real World Comparison
Collector	STW Time	Throughput	Heap Size
Serial	High	Low	Small
Parallel	High	Very High	Medium-Large
CMS	Medium	Good	Legacy
G1	Medium-Low	Good	Large
ZGC	Extremely Low	Good	Very Large
Shenandoah	Extremely Low	Good	Very Large
________________________________________
What Most Companies Use Today
Small Services
G1
________________________________________
Enterprise Applications
G1
________________________________________
Spring Boot APIs
Spring Boot
Usually:
G1
________________________________________
Massive Heaps
100GB+
Often:
ZGC
or
Shenandoah
________________________________________
Senior Interview Summary
If an interviewer asks:
Why did JVM evolve from Serial → Parallel → G1 → ZGC?
Answer:
Serial:
    Optimize simplicity

Parallel:
    Optimize throughput

CMS:
    Reduce pauses

G1:
    Predictable pauses on large heaps

ZGC/Shenandoah:
    Near-pause-less GC for huge heaps
