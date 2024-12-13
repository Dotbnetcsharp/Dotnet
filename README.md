
public class PropertyNumberFormatter {
    public static void main(String[] args) {
        String propertyNumber = "1085221300001100";
        
        // Format the string as per the required output
        String formatted = propertyNumber.substring(0, 3) + " " + 
                           propertyNumber.substring(3, 4) + " " + 
                           propertyNumber.substring(4, 6) + " " + 
                           propertyNumber.substring(6, 8) + " " + 
                           propertyNumber.substring(8, 9) + " " + 
                           propertyNumber.substring(9, 15) + " " + 
                           propertyNumber.substring(15);
        
        // Print the formatted output
        System.out.println(formatted);
    }
}

Access Modifiers in Java:

1. public:

Access: The member (class, method, or variable) can be accessed from anywhere in the program.

Example:

public class MyClass { }



2. private:

Access: The member can only be accessed within the same class.

Example:

private int age;



3. protected:

Access: The member can be accessed within the same package or by subclasses (even if they are in different packages).

Example:

protected void display() { }



4. Default (No modifier):

Access: The member is accessible only within the same package.

Example:

class MyClass { }




Summary:

public: Accessible everywhere.

private: Accessible only within the same class.

protected: Accessible within the same package or by subclasses.

Default: Accessible within the same package.










Differences Between System.out.println and System.err.println:

1. System.out.println:

Used for: Regular output (e.g., results or information).

Example:

System.out.println("This is normal output.");



2. System.err.println:

Used for: Error messages or warnings.

Example:

System.err.println("This is an error message.");




Key Difference:

System.out.println is for regular info, and System.err.println is for error messages. Both usually show in the console, but errors can be redirected separately.









Class Variable:

Declared with static inside a class but outside methods.

Shared by all objects of the class.

Example:

class Example { static int count = 0; // Class variable

public static void main(String[] args) {
    System.out.println(count); // Output: 0
}
}

Local Variable:

Declared inside a method or block.

Exists only during method execution.

Example:

class Example { public static void main(String[] args) { int num = 5; // Local variable System.out.println(num); // Output: 5 } }







Class Variable:

Declared with static inside a class but outside methods.

Shared by all objects of the class.


Example:



class Example {
    static int count = 0; // Class variable

    public static void main(String[] args) {
        System.out.println(count); // Output: 0
    }
}


---

Local Variable:

Declared inside a method or block.

Exists only during method execution.


Example:

class Example {
    public static void main(String[] args) {
        int num = 5; // Local variable
        System.out.println(num); // Output: 5
    }
}




In Java, the main method must be static so the JVM (Java Virtual Machine) can call it directly without creating an object of the class. If main is not static, the program will not run, as the JVM wouldn't know how to execute it
Because the JVM always calls the main(String[] args) method as the entry point. The second main(String arr) method will not be called unless explicitly invoked.

The second main method (public static void main(String arr)) is not called automatically because the JVM only recognizes the main method with the exact signature:

public static void main(String[] args)

This specific signature is the entry point for Java programs. The second method does not match this signature and is treated like a regular method.

How to Call the Second main Method:

To call the second main method, you need to invoke it explicitly from the primary main method. Here's how:

public class ConstructorPractice {

    public static void main(String[] args) {
        System.out.println("1111"); // Called automatically by the JVM
        main("example");           // Explicitly calling the second main method
    }

    public static void main(String arr) {
        System.out.println("2222");
    }
}

Output:

1111
2222

Explanation:

1. The JVM starts by calling main(String[] args) as the entry point.


2. Inside the primary main method, main("example") is explicitly called, which invokes the second main method.





.
