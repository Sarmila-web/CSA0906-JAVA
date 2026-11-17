import java.util.Scanner;

public class Main {

    static final int MAX_SLOTS = 3;
    static ParkingTicket[] tickets = new ParkingTicket[MAX_SLOTS];
    static double dailyCollection = 0;

    // Abstract Vehicle class
    static abstract class Vehicle {
        String regNo;

        Vehicle(String regNo) {
            this.regNo = regNo;
        }

        abstract double calculateFee(int minutes);

        abstract String getType();
    }

    // Car class
    static class Car extends Vehicle {

        Car(String regNo) {
            super(regNo);
        }

        @Override
        double calculateFee(int minutes) {
            return Math.ceil(minutes / 60.0) * 30;
        }

        @Override
        String getType() {
            return "Car";
        }
    }

    // Bike class
    static class Bike extends Vehicle {

        Bike(String regNo) {
            super(regNo);
        }

        @Override
        double calculateFee(int minutes) {
            return Math.ceil(minutes / 60.0) * 10;
        }

        @Override
        String getType() {
            return "Bike";
        }
    }

    // Electric Car class
    static class ElectricCar extends Vehicle {

        ElectricCar(String regNo) {
            super(regNo);
        }

        @Override
        double calculateFee(int minutes) {
            return Math.ceil(minutes / 60.0) * 30;
        }

        @Override
        String getType() {
            return "Electric Car";
        }
    }

    // Parking Ticket class
    static class ParkingTicket {
        Vehicle vehicle;
        int slotNumber;

        ParkingTicket(Vehicle vehicle, int slotNumber) {
            this.vehicle = vehicle;
            this.slotNumber = slotNumber;
        }
    }

    // Find ticket
    static int findTicket(String regNo) {

        for (int i = 0; i < MAX_SLOTS; i++) {

            if (tickets[i] != null &&
                tickets[i].vehicle.regNo.equalsIgnoreCase(regNo)) {
                return i;
            }
        }

        return -1;
    }

    // Vehicle Entry
    static void vehicleEntry(Scanner sc) {

        System.out.print("Enter Vehicle Type (Car/Bike/Electric Car): ");
        String type = sc.nextLine().trim();

        System.out.print("Enter Registration Number: ");
        String regNo = sc.nextLine().trim();

        if (regNo.isEmpty()) {
            System.out.println("Registration number cannot be empty!");
            return;
        }

        if (findTicket(regNo) != -1) {
            System.out.println("Duplicate registration number!");
            return;
        }

        Vehicle vehicle;

        if (type.equalsIgnoreCase("Car")) {
            vehicle = new Car(regNo);
        }
        else if (type.equalsIgnoreCase("Bike")) {
            vehicle = new Bike(regNo);
        }
        else if (type.equalsIgnoreCase("Electric Car")) {
            vehicle = new ElectricCar(regNo);
        }
        else {
            System.out.println("Invalid vehicle type!");
            return;
        }

        System.out.print("Enter Slot Number (1-" + MAX_SLOTS + "): ");

        if (!sc.hasNextInt()) {
            System.out.println("Invalid slot number!");
            sc.nextLine();
            return;
        }

        int slotNo = sc.nextInt();
        sc.nextLine();

        if (slotNo < 1 || slotNo > MAX_SLOTS) {
            System.out.println("Invalid slot number!");
            return;
        }

        if (tickets[slotNo - 1] != null) {
            System.out.println("Slot is already occupied!");
            return;
        }

        tickets[slotNo - 1] = new ParkingTicket(vehicle, slotNo);

        System.out.println("Vehicle entered successfully!");
        System.out.println("Vehicle Type: " + vehicle.getType());
        System.out.println("Allocated Slot: " + slotNo);
    }

    // Vehicle Exit
    static void vehicleExit(Scanner sc) {

        System.out.print("Enter Registration Number: ");
        String regNo = sc.nextLine().trim();

        int index = findTicket(regNo);

        if (index == -1) {
            System.out.println("Vehicle not found!");
            return;
        }

        System.out.print("Enter Parking Duration in Minutes: ");

        if (!sc.hasNextInt()) {
            System.out.println("Invalid parking duration!");
            sc.nextLine();
            return;
        }

        int minutes = sc.nextInt();
        sc.nextLine();

        if (minutes < 0) {
            System.out.println("Invalid parking duration!");
            return;
        }

        ParkingTicket ticket = tickets[index];

        double fee = ticket.vehicle.calculateFee(minutes);

        System.out.println("\nPARKING EXIT DETAILS");
        System.out.println("Vehicle Type: " + ticket.vehicle.getType());
        System.out.println("Registration Number: " + ticket.vehicle.regNo);
        System.out.println("Slot Number: " + ticket.slotNumber);
        System.out.println("Parking Duration: " + minutes + " minutes");
        System.out.println("Parking Fee: Rs " + fee);

        dailyCollection += fee;
        tickets[index] = null;

        System.out.println("Vehicle exited successfully!");
    }

    // Display Parking Slots
    static void displaySlots() {

        System.out.println("\nPARKING SLOT STATUS");

        for (int i = 0; i < MAX_SLOTS; i++) {

            if (tickets[i] == null) {
                System.out.println("Slot " + (i + 1) + ": Available");
            }
            else {
                System.out.println("Slot " + (i + 1) +
                    ": Occupied by " + tickets[i].vehicle.regNo);
            }
        }
    }

    // Main method
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        int choice = 0;

        do {

            System.out.println("\nPARKING MANAGEMENT SYSTEM");
            System.out.println("1. Vehicle Entry");
            System.out.println("2. Vehicle Exit");
            System.out.println("3. Display Parking Slots");
            System.out.println("4. Daily Collection");
            System.out.println("5. Exit Program");

            System.out.print("Enter Your Choice: ");

            if (!sc.hasNextInt()) {
                System.out.println("Invalid menu choice!");
                sc.nextLine();
                continue;
            }

            choice = sc.nextInt();
            sc.nextLine();

            switch (choice) {

                case 1:
                    vehicleEntry(sc);
                    break;

                case 2:
                    vehicleExit(sc);
                    break;

                case 3:
                    displaySlots();
                    break;

                case 4:
                    System.out.println("Total Daily Collection: Rs " +
                        dailyCollection);
                    break;

                case 5:
                    System.out.println("Thank You!");
                    break;

                default:
                    System.out.println("Invalid menu choice!");
            }

        } while (choice != 5);

        sc.close();
    }
}
