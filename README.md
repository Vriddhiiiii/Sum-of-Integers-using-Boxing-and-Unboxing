import java.io.*;
import java.util.*;

// ---------------- PART A ----------------
class SumCalculator {
    public static void runSum() {
        int[] numbers = {10, 20, 30, 40, 50};
        Integer sum = 0; // Autoboxing

        for (int num : numbers) {
            sum += num; // Autoboxing/Unboxing
        }

        System.out.println("Sum of Integers (Autoboxing/Unboxing) = " + sum);
    }
}

// ---------------- PART B ----------------
class Student implements Serializable {
    private static final long serialVersionUID = 1L;
    int id;
    String name;
    String grade;

    Student(int id, String name, String grade) {
        this.id = id;
        this.name = name;
        this.grade = grade;
    }

    @Override
    public String toString() {
        return "Student [ID=" + id + ", Name=" + name + ", Grade=" + grade + "]";
    }
}

class StudentSerializationDemo {
    public static void runSerialization() {
        String filename = "student.ser";

        // Serialization
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filename))) {
            Student s1 = new Student(101, "Aarush", "A");
            oos.writeObject(s1);
            System.out.println("Serialized: " + s1);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // Deserialization
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filename))) {
            Student s2 = (Student) ois.readObject();
            System.out.println("Deserialized: " + s2);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}

// ---------------- PART C ----------------
class Employee {
    int id;
    String name;
    double salary;

    Employee(int id, String name, double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    @Override
    public String toString() {
        return id + "," + name + "," + salary;
    }
}

class EmployeeManagement {
    static final String FILE_NAME = "employees.txt";

    public static void addEmployee(Employee e) throws IOException {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(FILE_NAME, true))) {
            bw.write(e.toString());
            bw.newLine();
        }
        System.out.println("Employee added successfully.");
    }

    public static void viewEmployees() throws IOException {
        File file = new File(FILE_NAME);
        if (!file.exists()) {
            System.out.println("No employees found.");
            return;
        }
        try (BufferedReader br = new BufferedReader(new FileReader(FILE_NAME))) {
            String line;
            System.out.println("Employee Records:");
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        }
    }

    public static void searchEmployee(int searchId) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(FILE_NAME))) {
            String line;
            boolean found = false;
            while ((line = br.readLine()) != null) {
                String[] data = line.split(",");
                if (Integer.parseInt(data[0]) == searchId) {
                    System.out.println("Employee Found: " + line);
                    found = true;
                    break;
                }
            }
            if (!found) System.out.println("Employee with ID " + searchId + " not found.");
        }
    }

    public static void deleteEmployee(int deleteId) throws IOException {
        File inputFile = new File(FILE_NAME);
        File tempFile = new File("temp.txt");

        try (BufferedReader br = new BufferedReader(new FileReader(inputFile));
             BufferedWriter bw = new BufferedWriter(new FileWriter(tempFile))) {

            String line;
            boolean deleted = false;
            while ((line = br.readLine()) != null) {
                String[] data = line.split(",");
                if (Integer.parseInt(data[0]) == deleteId) {
                    deleted = true;
                    continue; // skip
                }
                bw.write(line);
                bw.newLine();
            }

            if (deleted) {
                System.out.println("Employee deleted successfully.");
            } else {
                System.out.println("Employee with ID " + deleteId + " not found.");
            }
        }

        if (inputFile.delete()) {
            tempFile.renameTo(inputFile);
        }
    }
}

// ---------------- MAIN PROGRAM ----------------
public class MainProject {
    public static void main(String[] args) throws Exception {
        Scanner sc = new Scanner(System.in);

        while (true) {
            System.out.println("\n===== Main Menu =====");
            System.out.println("1. Sum of Integers (Autoboxing/Unboxing)");
            System.out.println("2. Student Serialization/Deserialization");
            System.out.println("3. Employee Management System");
            System.out.println("4. Exit");
            System.out.print("Enter choice: ");
            int choice = sc.nextInt();

            switch (choice) {
                case 1:
                    SumCalculator.runSum();
                    break;

                case 2:
                    StudentSerializationDemo.runSerialization();
                    break;

                case 3:
                    runEmployeeMenu();
                    break;

                case 4:
                    System.out.println("Exiting program...");
                    System.exit(0);

                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }
    }

    private static void runEmployeeMenu() throws Exception {
        Scanner sc = new Scanner(System.in);
        while (true) {
            System.out.println("\n--- Employee Management Menu ---");
            System.out.println("1. Add Employee");
            System.out.println("2. View Employees");
            System.out.println("3. Search Employee");
            System.out.println("4. Delete Employee");
            System.out.println("5. Back to Main Menu");
            System.out.print("Enter choice: ");
            int choice = sc.nextInt();

            switch (choice) {
                case 1:
                    System.out.print("Enter ID: ");
                    int id = sc.nextInt();
                    System.out.print("Enter Name: ");
                    String name = sc.next();
                    System.out.print("Enter Salary: ");
                    double sal = sc.nextDouble();
                    EmployeeManagement.addEmployee(new Employee(id, name, sal));
                    break;

                case 2:
                    EmployeeManagement.viewEmployees();
                    break;

                case 3:
                    System.out.print("Enter Employee ID to search: ");
                    int searchId = sc.nextInt();
                    EmployeeManagement.searchEmployee(searchId);
                    break;

                case 4:
                    System.out.print("Enter Employee ID to delete: ");
                    int deleteId = sc.nextInt();
                    EmployeeManagement.deleteEmployee(deleteId);
                    break;

                case 5:
                    return;

                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }
    }
}
