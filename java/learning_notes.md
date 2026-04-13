<details>
<summary>Number System</summary>

```text
1. Binary Number Systems[BASE-2]  
    - ob or OB 
    - 0 and 1 are only valid digits
    int b=0b10;---> valid 
    int c=0B1010;---> valid 
    
2. Octal Number Systems[BASE-8]
    - 0 or 0O or 0o   (zero followed by letter O or o)
    - 0 to 7 are valid digits
    int a=010;---> valid 
    int b=0o10;---> valid 
    int c=0O10;---> valid 
    
3. Decimal Number Systems[BASE-10] 
    - No prefix
    - 0 to 9 are valid digits
    int a=10;---> valid 
    int b=0a10;---> invalid 
    int c=0A10;---> invalid
4. Hexa Decimal Number Systems[BASE-16] 
    - 0 or 0X or 0x   (zero followed by letter X or x)
    - 0 to 9 and A to F are valid digits
    int a=0x10;---> valid 
    int b=0X10;---> valid 
    int c=0a10;---> invalid
```
</details>


<details>
<summary>var arg  int... a</summary>

```text
- int... a converts to int[] a={--- listof arguments---} 
- var-Arg parameter must be last parameter in var-Arg method. 
- only one var-Arg parameter is allowed in var-Arg method.
```
```text
class A   
 {   
     void add(int ... a)// int[] a={--- listof arguments---}   
     {   
         System.out.println("No Of Arguments  :"+a.length);   
         int result=0;   
         System.out.print("Argument List    :");   
         for(int i=0;i<a.length;i++)   
         {   
             System.out.print(a[i]+"  ");   
             result=result+a[i];   
         }   
         System.out.println();   
         System.out.println("Addition         :"+result);   
         System.out.println("----------------------------");   
    }   
 }   
 class Test    
 {   
    public static void main(String[] args)    
     {   
         A a=new A();   
         a.add();   
         a.add(10);   
         a.add(10,20);   
         a.add(10,20,30);   
     }   
 }   
```
</details>


<details>
<summary>object creation</summary>

```text
1. Creating Memory for the object

When JVM encounter "new" keyword in Object creation statement then JVM will 
check to which class we are creating object on the basis of Constructor name then JVM 
will load the respective class byte code to the memory [Method Area] 
 
After loading class byte code, JVM will identify minimal object memory size by 
recognizing all the instance variables and their data types 
 
After getting memory size, JVM will send a request to Heap manager about to create 
an object with the specified minimal memory. 
 
As per JVM requirement, Heap Manager will create the required block of memory at 
Heap Memory. 

```

```text
Generating Identities for the Object

After creating a block of memory [Object] for JVM requirements, Heap manager will 
assign an integer value as an identity for the object called as "HashCode". 
 
After getting HashCode value, Heap Manager will send that hashCode value to JVM, 
where JVM will convert that hashCode value to its Hexa Decimal form called as 
"Reference Value". 
 
After getting Object reference value, JVM will assign that reference value to a variable 
called as "Reference Variable". 
```

```text
Providing initializations inside the Object

After creating object and its identities, JVM will allocate memory for all the instance 
variables inside the object on the basis of their data types. 
 
After getting memory for the instance variables, JVM will provide initial values to the 
instance variables by searching initializations at class level declaration and at 
constructor. 
If any instance variable is not having initialization at both constructor and at class level 
declaration then JVM will store default value on the basis of their data type as initial 
value inside the object.
```
</details>


<details>
<summary>enum</summary>

```text
public static final int MIN_PRIORITY=1;  
public static final int NORM_PRIORITY=5;  
public static final int MAX_PRIORITY=10;  
 
EX:    
1) class User_Status {    
2)           public static final String AVAILABLE="Available";    
3)           public static final String BUSY="Busy";    
4)           public static final String IDLE="Idle";    
5) }    
6) class Test {    
7)           public static void main(String args[]) {    
8)                 System.out.println(User_Status.AVAILABLE);    
9)                 System.out.println(User_Status.BUSY);    
10)                 System.out.println(User_Status.IDLE);    
11)          }    
12) }    
OUTPUT: 
Available  
Busy  
Idle  
 
To declare constant variables in Java applications if we use the above convention then we 
are able to get the following problems.  
 
1) We must declare "public static final" for each and every constant variable explicitly.  
2) It is possible to allow multiple data types to represent one type, it will reduce 
typedness in Java applications.  
3) If we access constant variables then these variables will display their values, here 
constant variable values may or may not reflect the actual    meaning of constant 
variables.   
 
 
 
 
 
 
                                                                                                                              
 
186 http://youtube.com/durgasoftware 
 
Core Java 
To overcome all the problems, we have to go for "enum".  
In case of enum,  
 
1) All the constant variables are by default "public static final", no need   to declare 
explicitly.  
2) All the constant variables are by default the same enum type, it will    improve 
typedness in Java applications.  
3) All the constant variables are by default "Named Constants" that is, these constant 
variables are displaying their names instead of their   values. 
Syntax:  
[Access_modifier] enum Enum_Name 
{ 
   ----- List of constants----- 
} 
   
EX:    
1) enum User_Status {    
2)         AVAILABLE,BUSY,IDLE;    
3) }    
4) class Test {    
5)          public static void main(String args[]) {    
6)                  System.out.println(User_Status.AVAILABLE);    
7)                  System.out.println(User_Status.BUSY);    
8)                  System.out.println(User_Status.IDLE);    
9)          }   
10) }    
OUTPUT:  
Available  
Busy  
Idle   
 
NOTE: The default super class for every enum is “java.lang.Enum” class and “Object” class 
is Super class to “Enum” class.  
If we compile the above Java file then Compiler will translate "User_Status" enum into 
"User_Status" final class like below.  
 
final class User_Status extends java.lang.Enum {  
             public static final MailStatus AVAILABLE;  
             public static final MailStatus BUSY;  
             public static final MailStatus IDLE;  
             ---------  
}  
 
                                                                                                                              
 
187 http://youtube.com/durgasoftware 
 
Core Java 
NOTE: In Java, java.lang.Object class is common and default super class for all the classes. 
Similarly,All the Java enums are having a common and default super class that is 
"java.lang.Enum". 
 
NOTE: In Java applications,it is possible to implement inheritance between two classes but 
it is not possible to implement inheritance between two "enums", because, bydefault, 
enums are final classses. 
 
In Java applications, we can utilize enum like as classes, where we can provide normal 
variables, methods, constructors....     
 
EX  
1) enum Apple {    
2)          A(500),B(250),C(100);    
3)          int price;    
4)          Apple(int price) {    
5)                this.price = price;    
6)          }    
7)          public int getPrice() {    
8)                return price;    
9)          }   
10) }    
11) class Test {    
12)          public static void main(String args[]) {    
13)                   System.out.println("A-Grade Apple   :"+Apple.A.getPrice());    
14)                   System.out.println("B-Grade Apple   :"+Apple.B.getPrice());    
15)                   System.out.println("C-Grade Apple   :"+Apple.C.getPrice());    
16)          }   
17) }    
OUTPUT: A-Grade Apple  :500  
                    B-Grade Apple  :250  
                    C-Grade Apple  :100  
 
If we compile the above program, then compiler will translate enum into the following 
class:   
1) final class Apple extends Enum {    
2)            public static final Apple A = new Apple(500);    
3)            public static final Apple B = new Apple(250);    
4)            public static final Apple C = new Apple(100);    
5)            int price;    
6)            Apple(int price) {    
7)                   this.price = price;    
                                                                                                                              
 
188 http://youtube.com/durgasoftware 
 
Core Java 
8)            }    
9)            public int getPrice() {    
10)                    return price;    
11)            }    
12) ----    
13) }     
EX:     
1) enum Book {    
2)            A(500,250),B(300,150),C(200,100);    
3)            int no_of_pages;    
4)            int cost;    
5)            Book(int no_of_pages, int cost) {    
6)                   this.no_of_pages = no_of_pages;    
7)                   this.cost = cost;    
8)             }    
9)             public void getBookDetails() {    
10)                       System.out.println(no_of_pages+"--------->"+cost);    
11)             }   
12) }    
13) class Test {    
14)          public static void main(String args[]) {    
15)                    System.out.println("Durga Books Store");    
16)                    System.out.println("-------------------");    
17)                    System.out.println("No of Pages   Cost");    
18)                    System.out.println("-------------------");    
19)                    Book.A.getBookDetails();    
20)                    Book.B.getBookDetails();    
21)                    Book.C.getBookDetails();    
22)          }   
23) }    
OUTPUT:  
 
Durga Books Store  ---------------------------  
No of Pages     Cost  ---------------------------  
500------------->250  
300------------->150  
200------------->100  
 
If we compile the above program, then compiler will translate the enum Into the following 
class  
 
                                                                                                                              
 
189 http://youtube.com/durgasoftware 
 
Core Java 
Translated Code for the above enum(Book):  
1) final class Book extends Enum {    
2)            public static final Book A=new Book(500,250);    
3)            public static final Book B=new Book(300,150);    
4)            public static final Book C=new Book(200,100);    
5)            int cost;    
6)            int no_of_pages;    
7)            Book(int no_of_pages,int cost) {    
8)                       this.no_of_pages = no_of_pages;    
9)                       this.cost = cost;    
10)            }    
11)            public void getBookDetails() {    
12)                     System.out.println(no_of_pages+"--------->"+cost);    
13)            }    
14)             ----    
15) }    

```
</details>
