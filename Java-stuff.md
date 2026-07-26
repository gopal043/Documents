
1. Encapsulation
Definition: Wrapping data (fields) and behavior (methods) together, restricting 
access to internal state, and exposing controlled access via getters/setters.
Analogy: Think of a bank account—you don’t directly manipulate the balance; you use deposit/withdraw methods.
class BankAccount {
    private double balance; // hidden data
    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }
    // Controlled access
    public void deposit(double amount) {
        if(amount > 0) balance += amount;
    }
    public void withdraw(double amount) {
        if(amount > 0 && amount <= balance) balance -= amount;
    }
    public double getBalance() {
        return balance;
    }
}
Here, balance is encapsulated. Direct modification is prevented, ensuring data integrity.
2. Abstraction
Definition: Hiding implementation details and exposing only essential features. Focus on what an object does, not how it does it.
Analogy: When you drive a car, you use the steering wheel and pedals—you don’t care about the internal combustion process.
abstract class Payment {
    abstract void makePayment(double amount);
}
class CreditCardPayment extends Payment {
    @Override
    void makePayment(double amount) {
        System.out.println("Paid " + amount + " using Credit Card.");
    }
}
class UpiPayment extends Payment {
    @Override
    void makePayment(double amount) {
        System.out.println("Paid " + amount + " using UPI.");
    }
}
The client code only calls makePayment(). The abstraction hides the internal logic of each payment method
3. Inheritance
Definition: Mechanism to acquire properties and behaviours of another class, promoting code reuse.
Analogy: A child inherits traits from parents but can add its own unique features.
class Employee {
    String name;
    double salary;

    void work() {
        System.out.println(name + " is working.");
    }
}

class Manager extends Employee {
    int teamSize;

    void conductMeeting() {
        System.out.println(name + " is conducting a meeting with " + teamSize + " members.");
    }
}
Manager inherits name, salary, and work() from Employee, but adds its own behavior.
4. Polymorphism
Definition: Ability of an object to take many forms—same interface, different implementations.
Types:
	
                                              h
•	Compile-time (Method Overloading)
•	Runtime (Method Overriding)
Analogy: A person can be a father, employee, or customer depending on context.
class Shape {
    void draw() {
        System.out.println("Drawing a shape");
    }
}
class Circle extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing a Circle");
    }
}
class Square extends Shape {
    @Override
    void draw() {
        System.out.println("Drawing a Square");
    }
}

public class TestPolymorphism {
    public static void main(String[] args) {
        Shape s1 = new Circle();
        Shape s2 = new Square();

        s1.draw(); // Drawing a Circle
        s2.draw(); // Drawing a Square
    }
}
Difference between == and equals()
1. == Operator
•	Compares references (memory addresses) when used with objects.
•	For primitives, it compares actual values.
String s1 = new String("Hello");
String s2 = new String("Hello");

System.out.println(s1 == s2);      // false (different objects in heap)
System.out.println(s1.equals(s2)); // true (same content)
👉 Pitfall: Developers often mistakenly use == for string comparison, leading to bugs.
2. equals() Method
•	Defined in Object class, can be overridden.
•	Compares content/values of objects.
•	Example: String, Integer, and most wrapper classes override equals().
java
Integer i1 = 1000;
Integer i2 = 1000;

System.out.println(i1 == i2);      // false (different objects)
System.out.println(i1.equals(i2)); // true (same value)
👉 Best Practice: Always override equals() (and hashCode()) when creating custom classes.
With Primitives
•	== compares actual values directly.
•	equals() cannot be used because primitives are not objects (they don’t have methods).
java
int a = 10;
int b = 10;

System.out.println(a == b);      // true (values are equal)
// a.equals(b); ❌ Compile-time error (int is not an object)
👉 For primitives, only == works.
2. With Wrapper Classes (Objects)
•	== compares references (memory addresses).
•	equals() compares values/content (if overridden).
java
Integer x = new Integer(100);
Integer y = new Integer(100);

System.out.println(x == y);      // false (different objects in heap)
System.out.println(x.equals(y)); // true (same value)
👉 Even though both hold 100, == fails because they are different objects.
3. With String
•	Strings override equals() to compare content.
•	== compares references.
java
String s1 = "Hello";
String s2 = "Hello";
String s3 = new String("Hello");

System.out.println(s1 == s2);      // true (string pool optimization)
System.out.println(s1 == s3);      // false (new object in heap)
System.out.println(s1.equals(s3)); // true (same content)
👉 This is why string comparison must use equals(), not ==.
4. Special Case: Integer Caching
Java caches integers between -128 to 127. So == may return true unexpectedly.
Integer i1 = 127;
Integer i2 = 127;
System.out.println(i1 == i2);      // true (cached objects)

Integer i3 = 128;
Integer i4 = 128;
System.out.println(i3 == i4);      // false (outside cache range)
System.out.println(i3.equals(i4)); // true (same value)
👉 This is a tricky interview question—many candidates miss it.

Difference between ArrayList vs LinkedList
Feature	ArrayList	LinkedList
Underlying DS	Dynamic array	Doubly linked list
Access (get by index)	O(1)	O(n)
Insertion (middle)	O(n)	O(1) (if node reference known)
Memory overhead	Less (just array)	More (extra pointers per node)
Iteration	Faster (cache-friendly)	Slower
Use Case	Frequent random access	Frequent insert/delete
•  Use ArrayList for read-heavy operations.
•  Use LinkedList for insert/delete-heavy operations.
Difference between HashMap vs ConcurrentHashMap
1. HashMap
•	Not thread-safe.
•	Allows one null key and multiple null values.
•	Faster in single-threaded environments.
2. ConcurrentHashMap
•	Thread-safe (uses segmented locking or CAS in newer versions).
•	Does not allow null keys or null values.
•	Better performance than Hashtable in multi-threaded environments.
•  Use HashMap in single-threaded apps.
•  Use ConcurrentHashMap in multi-threaded apps where reads/writes happen concurrently.
class Animal {}
class Dog extends Animal {}
Animal a = new Dog();       // ✅ Upcasting (implicit)
Dog d = (Dog) a;            // ✅ Downcasting (explicit, safe here)
Animal a2 = new Animal();
Dog d2 = (Dog) a2;          // ❌ Runtime ClassCastException
The Contract Between equals() and hashCode()
1. If two objects are equal (a.equals(b) == true), then their hashCode() values must be the same.
•	This ensures that equal objects land in the same bucket in hash-based collections.
•	Example: Two String objects with the same content must produce the same hash code.
2. If two objects are not equal, their hashCode() values may or may not be different.
•	Different objects can share the same hash code (called a collision).
•	Collections handle collisions internally (via linked lists or trees in buckets).
3. Consistency
•	During a single execution, calling hashCode() on the same object must consistently return the same value, unless fields used in equals() change.
•	Example: If you use id in equals(), then hashCode() should also use id.
class Employee {
    int id;
    String name;

    // equals() based on id
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Employee)) return false;
        Employee e = (Employee) o;
        return this.id == e.id;
    }
    public int hashCode() {
        return Integer.hashCode(id);
    }
}
public class Test {
    public static void main(String[] args) {
        Employee e1 = new Employee();
        e1.id = 101;
        Employee e2 = new Employee();
        e2.id = 101;
        System.out.println(e1.equals(e2));   // true
        System.out.println(e1.hashCode());   // same as e2.hashCode()
        System.out.println(e2.hashCode());   // same
    }
}

Example of SQL Injection
Suppose you have this code:
String username = request.getParameter("username");

String query =
    "SELECT * FROM Users WHERE username = '" +
    username + "'";

Statement stmt = connection.createStatement();
ResultSet rs = stmt.executeQuery(query);
If a normal user enters:
john
The query becomes:
SELECT * FROM Users WHERE username = 'john'
No problem.
Attack Example
If an attacker enters:
' OR '1'='1
The query becomes:
SELECT * FROM Users
WHERE username = '' OR '1'='1'
Since '1'='1' is always true, the database may return all users.
________________________________________
Common SQL Injection Techniques
1. Authentication Bypass
Vulnerable code:
SELECT * FROM Users
WHERE username = 'admin'
AND password = 'password'
Attacker input:
admin' --
Query becomes:
SELECT * FROM Users
WHERE username = 'admin' -- '
AND password = 'anything'
-- comments out the rest of the query.
Result: login without knowing the password.
________________________________________
2. UNION Injection
Original query:
SELECT name,email FROM Users
WHERE id = 1
Attacker input:
1 UNION SELECT username,password FROM Admins
Result:
SELECT name,email FROM Users
WHERE id = 1
UNION
SELECT username,password FROM Admins
Can expose sensitive data.
________________________________________
3. Data Modification
Attacker input:
john'; DELETE FROM Users; --
Query becomes:
SELECT * FROM Users
WHERE username='john';

DELETE FROM Users;
Depending on database configuration, this could delete data.
________________________________________
4. Blind SQL Injection
The application doesn't show database errors, but attackers infer information by asking true/false questions.
Example:
?id=1 AND SUBSTRING(database(),1,1)='m'
If the page behaves differently, the attacker learns the first letter of the database name.
________________________________________
5. Time-Based Injection
Example:
?id=1; WAITFOR DELAY '00:00:05'
If the response takes 5 seconds longer, the attacker knows the injection worked.
________________________________________
How to Prevent SQL Injection
1. Use Prepared Statements (Best Solution)
Bad
String sql =
    "SELECT * FROM Users WHERE username='" +
    username + "'";
Good
String sql =
    "SELECT * FROM Users WHERE username=?";

PreparedStatement ps =
    connection.prepareStatement(sql);

ps.setString(1, username);

ResultSet rs = ps.executeQuery();
Why it works:
•	SQL structure is fixed. 
•	User input is sent separately. 
•	Database treats input as data, not executable SQL. 
Even if the input is:
' OR '1'='1
it is treated as a literal string value.
________________________________________
2. Validate Input
If usernames should contain only letters and numbers:
if (!username.matches("[a-zA-Z0-9]+")) {
    throw new IllegalArgumentException();
}
Validation is useful but not sufficient alone.
Always use prepared statements too.
________________________________________
3. Use ORM Frameworks
Examples:
•	Hibernate 
•	Spring Data JPA 
Instead of writing SQL directly:
userRepository.findByUsername(username);
ORMs usually generate parameterized queries automatically.
________________________________________
4. Least Privilege Database Accounts
Avoid:
Database user: root
Use a restricted account:
Can SELECT
Can UPDATE Customers
Cannot DROP tables
Cannot CREATE users
Even if an injection occurs, damage is limited.
________________________________________
5. Avoid Dynamic SQL
Dangerous
String sql =
    "SELECT * FROM Users ORDER BY " + userInput;
Attacker controls SQL syntax.
Safer
switch(sortField) {
    case "name":
        sql = "SELECT * FROM Users ORDER BY name";
        break;
    case "date":
        sql = "SELECT * FROM Users ORDER BY created_at";
        break;
}
Use a whitelist of allowed values.
________________________________________
6. Don't Leak Database Errors
Bad:
SQL Error:
Column password does not exist
This helps attackers understand your schema.
Instead:
Something went wrong.
and log details internally.
________________________________________
Golden Rule
Whenever you see code like this:
String sql =
    "SELECT * FROM Users WHERE username='"
    + username + "'";
or
"... " + userInput + " ..."
inside SQL, treat it as a potential SQL injection vulnerability.
The safest pattern is:
PreparedStatement ps =
    connection.prepareStatement(
        "SELECT * FROM Users WHERE username=?"
    );

ps.setString(1, username);
Using parameterized/prepared statements is the primary defense against SQL injection.

JVM Memory Model (Detailed)
•	Heap:
o	Divided into Young Generation (Eden + Survivor spaces) and Old Generation.
o	Objects are first allocated in Eden; survivors move to Survivor spaces; long-lived objects move to Old Gen.
o	GC algorithms: Serial, Parallel, CMS, G1, ZGC, Shenandoah.
•	Method Area (Metaspace in Java 8+):
o	Stores class structures, method code, static variables.
o	Before Java 8: PermGen; after Java 8: Metaspace (native memory).
•	Stack:
o	Each method call creates a stack frame with local variables, operand stack, and return address.
•	PC Register:
o	Tracks current instruction for each thread.
•	Native Method Stack:
o	Used when calling native code.
============================== JVM Memory Model =======================

1. JVM Memory Model (Highest Priority)
Understand exactly where objects live and why memory issues happen.
Learn
•	Heap 
o	Young Generation 
o	Old Generation 
•	Stack 
o	Method frames 
o	Local variables 
•	Metaspace 
•	Direct Memory (NIO) 
•	String Pool 
Be able to answer
String s1 = "hello";
String s2 = new String("hello");
•	Where are these objects stored? 
•	How many objects are created? 
•	What is stored in String Pool? 
Production issues you'll solve
•	OutOfMemoryError 
•	Memory leaks 
•	Excessive object creation 
•	High heap usage 
Tools
•	jmap 
•	jcmd 
•	Heap dumps 
•	Eclipse MAT 
________________________________________
2. Garbage Collection (Very Important)
Many performance issues are actually GC issues.
Learn
•	Object lifecycle 
•	Minor GC 
•	Major GC 
•	Full GC 
Modern collectors:
•	Serial GC 
•	Parallel GC 
•	G1GC 
•	ZGC 
•	Shenandoah 
Understand
Why this is bad:
for(int i=0;i<1000000;i++){
    String s = new String("hello");
}
Creates unnecessary garbage.
Production issues you'll solve
•	High CPU 
•	Long pauses 
•	Throughput drops 
•	Latency spikes 
Tools
•	GC logs 
•	jstat 
•	Java Flight Recorder (JFR) 
________________________________________
3. Threading and Concurrency
This is where many senior-level debugging tasks occur.
Learn deeply
•	Thread lifecycle 
•	Synchronization 
•	Monitor locks 
•	ReentrantLock 
•	volatile 
•	Atomic classes 
Understand
volatile boolean running = true;
Why volatile works here but not for:
count++;
Production issues
•	Deadlocks 
•	Race conditions 
•	Thread starvation 
•	High contention 
Tools
•	jstack 
•	Thread dumps 
•	JFR 
________________________________________
4. Java Memory Model (JMM)
Many developers use volatile and synchronized without understanding them.
Learn
•	Happens-before relationship 
•	Visibility 
•	Reordering 
•	CPU cache effects 
Example:
private boolean initialized;
private Object data;
Why another thread may see:
initialized = true
before data is fully initialized.
This knowledge is crucial for writing correct concurrent code.
________________________________________
5. Object Creation and Memory Layout
Understand the cost of objects.
Learn
•	Object headers 
•	Compressed Oops 
•	Alignment/Padding 
•	References vs objects 
Example:
class User {
    int age;
    boolean active;
}
Actual memory consumed is larger than expected.
Tool
•	JOL (Java Object Layout) 
________________________________________
6. Collections Internals
Senior interviews and production optimization heavily rely on this.
Learn
HashMap
•	Bucket structure 
•	Hashing 
•	Collision handling 
•	Treeification 
Understand:
hashCode()
equals()
ConcurrentHashMap
•	Segment-less design 
•	CAS operations 
ArrayList
•	Growth factor 
•	Resize costs 
LinkedList
•	Why it's usually slower than ArrayList 
Production issues
•	High memory usage 
•	Slow lookups 
•	Hash collisions 
________________________________________
7. Class Loading Internals
Very important in Spring Boot and microservices.
Learn
Class Loader hierarchy:
•	Bootstrap 
•	Platform 
•	Application 
Understand
Class.forName(...)
Production issues
•	ClassNotFoundException 
•	NoClassDefFoundError 
•	Classloader leaks 
________________________________________
8. JIT Compiler (Huge Optimization Area)
Many developers ignore this.
Java is not interpreted line-by-line forever.
Hot code gets compiled into native machine code.
Learn
•	C1 compiler 
•	C2 compiler 
•	Tiered compilation 
Concepts
•	Inlining 
•	Escape analysis 
•	Loop optimizations 
•	Dead code elimination 
Example:
public int add(int a,int b){
    return a+b;
}
Often gets inlined completely.
Tool
•	JITWatch 
________________________________________
9. Production Profiling
A senior engineer should know how to profile before optimizing.
Learn
CPU profiling
•	Method hotspots 
•	Flame graphs 
Memory profiling
•	Allocation hotspots 
Tools
•	Async Profiler 
•	JFR 
•	VisualVM 
•	YourKit 
•	JProfiler 
________________________________________
10. NIO and Networking
Critical for microservices and high-throughput systems.
Learn
•	Blocking IO 
•	Non-blocking IO 
•	Selectors 
•	ByteBuffer 
Understand
Why:
Socket And SocketChannel behave differently.
________________________________________
11. Spring Internals (Practical Value)
Since most Java developers use Spring:
Learn
•	Bean lifecycle 
•	Dependency injection internals 
•	AOP proxies 
•	Transaction management 
Understand why:
@Transactional
fails in self-invocation scenarios.
This is a common production bug.
________________________________________
12. Database Performance
Many "Java performance issues" are actually database issues.
Learn
•	Connection pools 
•	HikariCP 
•	JDBC batching 
•	Prepared statements 
Understand
N + 1 query problem
Production issues
•	Slow APIs 
•	DB saturation 
________________________________________
13. JVM Diagnostic Tools (Must Know)
If you're on-call or debugging production, learn:
Thread analysis
jstack
Heap analysis
jmap
JVM information
jcmd
GC statistics
jstat
Process info
jps
Modern approach
•	Java Flight Recorder (JFR) 
•	Mission Control 
These are heavily used in real production incidents.
________________________________________
Example Senior-Level Debugging Flow
Imagine:
CPU suddenly becomes 95%.
A strong Java engineer would:
1.	Check JVM process 
top
2.	Capture thread dump 
jstack <pid>
3.	Identify hot thread 
4.	Record JFR 
jcmd <pid> JFR.start
5.	Analyze flame graph 
6.	Check GC behavior 
jstat -gc <pid>
7.	Determine whether issue is: 
o	Infinite loop 
o	Lock contention 
o	Excessive allocations 
o	Full GC 
o	Database waiting 
This is much more valuable in production than knowing another framework.
Recommended Learning Order
1.	JVM Memory Areas 
2.	Garbage Collection 
3.	Concurrency + JMM 
4.	Collections Internals 
5.	Class Loading 
6.	JIT Compiler 
7.	Profiling Tools (JFR, Async Profiler) 
8.	Spring Internals 
9.	NIO 
10.	Advanced JVM Tuning
--------------------------------------------------------------------------------------------
Area	Purpose
Heap	Stores objects
Stack	Stores method execution data
Metaspace	Stores class metadata
Direct Memory	Off-heap memory used by NIO
String Pool	Reusable string literals
PC Register	Current instruction
Native Memory	JVM internal/native allocations
________________________________________
2. Heap Memory
The Heap is where almost all Java objects live.
Example:
User user = new User();
When JVM executes:
new User()
the object is allocated in the Heap.
________________________________________
Heap Structure
Modern JVM divides heap into:
Heap
|
+-- Young Generation
|      |
|      +-- Eden
|      +-- Survivor S0
|      +-- Survivor S1
|
+-- Old Generation
________________________________________
3. Young Generation
This is where new objects are created.
for(int i=0;i<100000;i++){
    User u = new User();
}
Every User initially goes into Eden.
Young Gen
|
+-- Eden
________________________________________
Why?
Most objects die quickly.
Example:
public void process() {
    User u = new User();
}
After method completes:
u
becomes unreachable.
JVM knows:
Most objects are short-lived.
So it places them in Young Gen.
________________________________________
Eden Space
User u = new User();
Stored in:
Young Gen
    Eden
Initially.
________________________________________
Minor GC
Suppose Eden becomes full.
Eden Full
JVM triggers:
Minor GC
Process:
Live Objects
    ->
Survivor Space

Dead Objects
    ->
Removed
________________________________________
Example:
User a = new User();
User b = new User();
If a is still referenced and b is not:
a -> Survivor
b -> Garbage
________________________________________
Survivor Spaces
There are two:
S0
S1
Objects bounce between them.
Eden -> S0 -> S1 -> S0 -> S1
Each GC increases age.
Example:
Age = 1
Age = 2
Age = 3
...
________________________________________
Promotion to Old Generation
If object survives many GCs:
Age > Threshold
Typically around 15.
It gets promoted:
Old Generation
Example:
Cache cache = new Cache();
Application cache lives long.
Eventually:
Old Gen
________________________________________
Old Generation
Contains long-lived objects.
Examples:
Cache
Singletons
Spring Beans
Session Data
Large Collections
________________________________________
When Old Gen fills:
Major GC / Full GC
This is expensive.
________________________________________
Why Full GC is Bad
Full GC scans:
Young Gen
Old Gen
Entire heap.
Can pause application.
100ms
500ms
2 sec
10 sec
depending on heap size.
________________________________________
Production Symptom
CPU spike
Latency spike
Application freezes
Often due to:
Too many Full GCs
________________________________________
4. Stack Memory
Every thread has its own stack.
Thread t1
Thread t2
Each gets:
Stack
________________________________________
Example:
public void login() {
    int id = 10;
}
Stack:
Frame
|
+-- id = 10
________________________________________
Method Frames
Every method call creates a frame.
Example:
main()
   |
   --> login()
            |
            --> validate()
Stack:
validate()
login()
main()
Top frame executes first.
________________________________________
Example
public void login() {
    int id = 1;
}
Frame contains:
Local Variables
Operand Stack
Return Address
________________________________________
Local Variables
Stored inside method frame.
int age = 30;
Stored directly in stack.
________________________________________
Reference variables are also stored in stack.
User user = new User();
Stack:
user -> 0x1234
Heap:
0x1234 -> User Object
Important:
The object is in Heap.
Reference is in Stack.
________________________________________
Example Visualization
public void test() {
    User u = new User();
}
Stack:
test()
|
+-- u (reference)
Heap:
User Object
________________________________________
Stack Overflow Error
Example:
public void recurse() {
    recurse();
}
Frames keep growing:
Frame
Frame
Frame
Frame
Frame
...
Eventually:
StackOverflowError
Not Heap issue.
Stack issue.
________________________________________
5. Metaspace
Before Java 8:
PermGen
After Java 8:
Metaspace
________________________________________
Stores:
Class metadata
Method metadata
Field metadata
Annotations
Bytecode structures
NOT object instances.
________________________________________
Example:
class User {
    String name;
}
Metadata:
Class User
Fields
Methods
Stored in Metaspace.
Actual objects:
new User()
stored in Heap.
________________________________________
Metaspace OutOfMemoryError
Example:
Generate millions of classes dynamically
Common in:
Spring
Hibernate
ByteBuddy
CGLIB
ClassLoaders
Error:
OutOfMemoryError: Metaspace
________________________________________
Example:
String s = "hello";
JVM checks:
Does "hello" already exist?
If yes:
Reuse it.
________________________________________
Example:
String a = "hello";
String b = "hello";
Only one string object.
Pool
|
+-- "hello"
Both references point to same object.
________________________________________
System.out.println(a == b);
Output:
true
________________________________________
7. Direct Memory (NIO)
Off-heap memory.
Not stored in Java heap.
Example:
ByteBuffer.allocateDirect(1024);
Memory:
Direct Memory
instead of Heap.
________________________________________
Why?
Avoid copying.
Useful for:
Netty
Kafka
Tomcat
High-performance I/O
________________________________________
Example:
ByteBuffer buffer =
        ByteBuffer.allocateDirect(1024);
Heap usage:
small
Direct memory usage:
1024 bytes
________________________________________
Possible Error
OutOfMemoryError:
Direct buffer memory
Even when heap looks healthy.
________________________________________
Memory Areas Summary
Memory Area	Stores
Stack	Method frames, local variables, references
Heap	Objects
Young Gen	New objects
Old Gen	Long-lived objects
Metaspace	Class metadata
String Pool	Interned strings
Direct Memory	NIO buffers
1. OutOfMemoryError: Java Heap Space
Cause:
List<User> users = new ArrayList<>();
while(true){
   users.add(new User());
}
Heap keeps growing.
Result:
OutOfMemoryError: Java heap space
________________________________________
2. Memory Leak
Java has GC, but leaks still happen.
Example:
Map<String,Object> cache = new HashMap<>();
Never removing entries.
cache.put(...)
forever.
Objects remain reachable.
GC cannot collect.
Heap grows continuously.
________________________________________
Symptoms:
Heap usage keeps increasing
Frequent Full GC
Eventually OOM
________________________________________
3. Excessive Object Creation
Bad:
for(int i=0;i<1000000;i++){
   new String("abc");
}
Creates huge garbage.
Results:
Frequent Minor GC
CPU increase
Latency increase
________________________________________
4. High Heap Usage
Common causes:
•	Large caches 
•	Memory leaks 
•	Huge collections 
•	Large JSON objects 
•	Excessive buffering 
________________________________________
JVM Troubleshooting Tools
jcmd
Most useful starting point.
Find process:
jcmd
Output:
12345 MyApplication
________________________________________
Check heap:
jcmd 12345 GC.heap_info
________________________________________
GC information:
jcmd 12345 GC.class_histogram
Shows:
Top object types
________________________________________
jmap
Heap inspection.
Histogram:
jmap -histo 12345
Example output:
1: byte[]
2: String
3: HashMap
Largest consumers.
________________________________________
Heap dump:
jmap -dump:live,format=b,file=heap.hprof 12345
Creates:
heap.hprof
________________________________________
Heap Dump
A snapshot of heap.
Contains:
All objects
References
Sizes
Retained sizes
GC roots
Used for memory leak analysis.
________________________________________
Eclipse MAT
Most widely used heap analyzer.
Open:
heap.hprof
inside MAT.
Look for:
Dominator Tree
Shows:
Who owns memory?
Example:
HashMap
  -> 5 GB retained
________________________________________
Leak Suspects Report
Automatically identifies:
Potential memory leaks
________________________________________
Biggest Objects
Find:
byte[]
String
HashMap
ArrayList
consuming memory.
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
A useful way to think about microservice patterns is:
Problem	Pattern
Service boundaries	Decomposition Patterns
Communication	API Gateway, Service Discovery
Data consistency	Saga, CQRS, Event Sourcing
Reliability	Circuit Breaker, Retry, Bulkhead
Performance	Cache Aside, CQRS
Deployment	Sidecar, Service Mesh
Observability	Distributed Tracing
________________________________________
1. API Gateway Pattern
Problem
Without gateway:
Mobile App
   |
---------------------------------
|      |      |      |          |
User  Order Payment Cart Inventory
Client needs to know:
•	Every service URL 
•	Authentication 
•	Routing 
Very difficult.
________________________________________
Solution
Client
   |
API Gateway
   |
-----------------------
|    |    |    |      |
User Order Cart Payment
Client only talks to gateway.
________________________________________
Internal Working
Request:
GET /orders/100
Gateway:
1 Authenticate JWT
2 Rate limit
3 Route request
4 Forward to Order Service
5 Return response
________________________________________
Why Needed
Centralized:
•	Security 
•	Logging 
•	Routing 
•	Rate limiting 
________________________________________
2. Service Discovery Pattern
Problem
Microservices scale dynamically.
Today:
Order Service

10.0.1.1
After scaling:
10.0.1.2
10.0.1.3
10.0.1.4
IP addresses change.
Hardcoding fails.
________________________________________
Solution
Service Registry
Services register themselves.
________________________________________
Internal Flow
Startup:
Order Service
    |
Register
    |
Registry
Consumer:
Payment Service
     |
Ask Registry
     |
Get Order Service Address
Then call it.
________________________________________
Why Needed
Supports:
•	Auto scaling 
•	Dynamic environments 
•	Kubernetes 
________________________________________
3. Circuit Breaker Pattern
Problem
Payment service is down.
Order -> Payment
Without protection:
10000 requests
10000 failures
10000 waiting threads
System collapse.
________________________________________
Solution
Circuit Breaker.
________________________________________
Internal States
Closed
Normal traffic.
Request -> Payment
________________________________________
Open
Failures exceed threshold.
Circuit OPEN

Request blocked immediately
No call made.
________________________________________
Half Open
After waiting period.
Try 1 request
Success:
Close circuit
Failure:
Open again
________________________________________
Why Needed
Prevents cascading failures.
________________________________________
4. Retry Pattern
Problem
Temporary network glitch.
Timeout
Actually service is healthy.
________________________________________
Solution
Retry.
________________________________________
Internal Working
Attempt 1 Fail
Wait 1 sec

Attempt 2 Fail
Wait 2 sec

Attempt 3 Success
Uses exponential backoff.
________________________________________
Why Needed
Most failures are transient.
________________________________________
5. Bulkhead Pattern
Inspired by ship compartments.
________________________________________
Problem
Payment service consumes all threads.
Thread Pool = 100
Payment uses all 100.
Inventory requests cannot execute.
________________________________________
Solution
Separate pools.
Payment Pool = 40

Inventory Pool = 30

Shipping Pool = 30
________________________________________
Why Needed
One service failure doesn't affect others.
________________________________________
6. Saga Pattern
You already studied this.
________________________________________
Problem
Transaction across services.
Order
Inventory
Payment
Three databases.
No ACID transaction.
________________________________________
Solution
Sequence of local transactions.
Compensation if failure.
________________________________________
Internal Flow
Create Order

Reserve Inventory

Process Payment
Payment fails:
Release Inventory

Cancel Order
________________________________________
Why Needed
Distributed transaction management.
________________________________________
7. CQRS Pattern
Command Query Responsibility Segregation.
________________________________________
Problem
Read workload differs from write workload.
Example:
1M reads

10k writes
Same database struggles.
________________________________________
Solution
Separate models.
Write Model

Read Model
________________________________________
Internal Flow
Write:
Order Service

Order DB
Event:
OrderCreated
Updates:
Read DB
Queries hit Read DB.
________________________________________
Why Needed
Optimized reads.
________________________________________
8. Event Sourcing
Problem
Need complete history.
Bank example.
Instead of:
Balance = 1000
Store events.
________________________________________
Internal Working
Deposited 500

Deposited 300

Withdrawn 100
Current balance:
500 + 300 -100
Calculated from events.
________________________________________
Why Needed
Auditability.
Replay capability.
________________________________________
9. Database per Service Pattern
Problem
Shared DB creates coupling.
Order
Payment
Inventory

All use same DB
________________________________________
Solution
Order DB

Payment DB

Inventory DB
Independent ownership.
________________________________________
Why Needed
True autonomy.
________________________________________
10. Strangler Fig Pattern
Migration pattern.
________________________________________
Problem
Huge monolith.
Cannot rewrite everything.
________________________________________
Solution
Gradually replace.
Client
   |
Gateway
   |
-----------------
|               |
Monolith    New Service
Over time:
Monolith shrinks
Eventually removed.
________________________________________
Why Needed
Low-risk migration.
________________________________________
11. Sidecar Pattern
Popular in Kubernetes.
________________________________________
Problem
Every service needs:
Logging
Monitoring
TLS
Duplicated code.
________________________________________
Solution
Attach helper container.
Pod
 |
 |-- App Container
 |
 |-- Sidecar Container
________________________________________
Internal Flow
Application:
Logs
Sidecar:
Collects logs
Ships them centrally.
________________________________________
Why Needed
Cross-cutting concerns separated.
________________________________________
12. Service Mesh Pattern
Evolution of Sidecar.
Popular with:
•	Istio 
•	Linkerd 
________________________________________
Problem
Every service implements:
Retry
TLS
Metrics
Circuit Breaker
Repeated everywhere.
________________________________________
Solution
Move networking into sidecars.
________________________________________
Internal Working
Service A
   |
Proxy
   |
Proxy
   |
Service B
Traffic goes through proxies.
Proxies handle:
Retries
Metrics
TLS
Load Balancing
Application code stays simple.
________________________________________
13. Cache Aside Pattern
Most common caching strategy.
________________________________________
Problem
Database overloaded.
________________________________________
Internal Flow
Read:
Check Cache
Hit:
Return
Miss:
Read DB

Store in Cache

Return
________________________________________
Why Needed
Massive performance improvement.
________________________________________
14. Outbox Pattern
One of the most important modern patterns.
________________________________________
Problem
Classic bug:
Save Order DB
Publish Kafka Event
What if:
DB save succeeds

Kafka publish fails
Data inconsistency.
________________________________________
Solution
Outbox Table.
________________________________________
Internal Working
Transaction:
Save Order

Save Event To Outbox
Same database transaction.
COMMIT
Background process:
Reads Outbox

Publishes Kafka Event

Marks Processed
________________________________________
Why Needed
Guaranteed event delivery.
Extremely common in production.
________________________________________
15. Distributed Tracing Pattern
Problem
Request travels through:
Gateway
Order
Inventory
Payment
Notification
Failure occurs.
Where?
Nobody knows.
________________________________________
Solution
Trace ID.
________________________________________
Internal Flow
Gateway creates:
TraceId=ABC123
Passed through all services.
Gateway
Order
Inventory
Payment
All logs contain same Trace ID.
________________________________________
Why Needed
Debugging distributed systems.
________________________________________
Most Important Patterns for Senior Interviews
If I were preparing a 6-year experienced engineer for interviews, I'd master these first:
1.	Service Decomposition 
2.	Database per Service 
3.	API Gateway 
4.	Service Discovery 
5.	Saga 
6.	Outbox Pattern 
7.	CQRS 
8.	Event Sourcing 
9.	Circuit Breaker 
10.	Retry + Bulkhead 
11.	Cache Aside 
12.	Distributed Tracing 
13.	Service Mesh 
14.	Strangler Fig

+++++++++++++++++++++++++++ JVM Memory Overview++++++++++++++++++++++
JVM Memory Overview
When a Java application starts, the JVM allocates several memory areas:
JVM Memory

├── Heap
│   ├── Young Generation
│   │   ├── Eden 
│   │   ├── Survivor S0
│   │   └── Survivor S1
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

