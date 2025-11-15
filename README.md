
public class Hotel {
    private int roomNumber;
    private String roomType;
    private double pricePerNight;
    private boolean booked;

    public Hotel(int roomNumber, String roomType, double pricePerNight) {
        this.roomNumber = roomNumber;
        this.roomType = roomType;
        this.pricePerNight = pricePerNight;
        this.booked = false;
    }

    public int getRoomNumber() { return roomNumber; }
    public String getRoomType() { return roomType; }
    public double getPricePerNight() { return pricePerNight; }
    public boolean isBooked() { return booked; }

    public void setBooked(boolean booked) { this.booked = booked; }

    @Override
    public String toString() {
        return "Room No: " + roomNumber + " | Type: " + roomType + 
               " | Price/Night: " + pricePerNight + " | Booked: " + booked;
    }
}


public class User {
    private int userId;
    private String name;
    private String phone;

    public User(int userId, String name, String phone) {
        this.userId = userId;
        this.name = name;
        this.phone = phone;
    }

    public int getUserId() { return userId; }
    public String getName() { return name; }
    public String getPhone() { return phone; }

    @Override
    public String toString() {
        return "UserID: " + userId + " | Name: " + name + " | Phone: " + phone;
    }
}

public class Booking {
    private int bookingId;
    private User user;
    private Hotel room;
    private int days;
    private double totalFare;

    public Booking(int bookingId, User user, Hotel room, int days) {
        this.bookingId = bookingId;
        this.user = user;
        this.room = room;
        this.days = days;
        this.totalFare = calculateFare();
    }

    private double calculateFare() {
        return days * room.getPricePerNight();
    }

    public int getBookingId() { return bookingId; }
    public User getUser() { return user; }
    public Hotel getRoom() { return room; }
    public int getDays() { return days; }
    public double getTotalFare() { return totalFare; }

    @Override
    public String toString() {
        return "BookingID: " + bookingId +
               " | User: " + user.getName() +
               " | Room: " + room.getRoomNumber() +
               " | Days: " + days +
               " | Total Fare: " + totalFare;
    }
}


import java.util.*;

public class Database {
    public static Map<Integer, User> users = new HashMap<>();
    public static Map<Integer, Hotel> rooms = new HashMap<>();
    public static Map<Integer, Booking> bookings = new HashMap<>();

    public static void loadRooms() {
        rooms.put(101, new Hotel(101, "Single", 1500));
        rooms.put(102, new Hotel(102, "Double", 2500));
        rooms.put(103, new Hotel(103, "Luxury", 5000));
    }
}

import java.util.Map;

public class AdminDashboard {

    public static void viewRooms() {
        System.out.println("\n==== ROOM LIST ====");
        for (Map.Entry<Integer, Hotel> entry : Database.rooms.entrySet()) {
            System.out.println(entry.getValue());
        }
    }

    public static void viewBookings() {
        System.out.println("\n==== ALL BOOKINGS ====");
        for (Map.Entry<Integer, Booking> entry : Database.bookings.entrySet()) {
            System.out.println(entry.getValue());
        }
    }

    public static void viewUsers() {
        System.out.println("\n==== USER LIST ====");
        for (Map.Entry<Integer, User> entry : Database.users.entrySet()) {
            System.out.println(entry.getValue());
        }
    }
}


import java.util.*;

public class HotelBookingSystem {

    private static Scanner sc = new Scanner(System.in);
    private static int userCounter = 1;
    private static int bookingCounter = 100;

    public static void main(String[] args) {

        Database.loadRooms();

        while (true) {
            System.out.println("\n===== HOTEL BOOKING SYSTEM =====");
            System.out.println("1. Register User");
            System.out.println("2. Book Room");
            System.out.println("3. Admin Dashboard");
            System.out.println("4. Exit");
            System.out.print("Enter choice: ");

            int choice = sc.nextInt();

            switch (choice) {
                case 1 -> registerUser();
                case 2 -> bookRoom();
                case 3 -> adminMenu();
                case 4 -> {
                    System.out.println("Thank you for using the system!");
                    return;
                }
                default -> System.out.println("Invalid choice!");
            }
        }
    }

    public static void registerUser() {
        sc.nextLine();
        System.out.print("Enter Name: ");
        String name = sc.nextLine();
        System.out.print("Enter Phone: ");
        String phone = sc.nextLine();

        User user = new User(userCounter++, name, phone);
        Database.users.put(user.getUserId(), user);

        System.out.println("User Registered Successfully!");
    }

    public static void bookRoom() {
        System.out.print("Enter User ID: ");
        int userId = sc.nextInt();

        if (!Database.users.containsKey(userId)) {
            System.out.println("User does not exist!");
            return;
        }

        AdminDashboard.viewRooms();
        System.out.print("Enter Room Number: ");
        int roomNumber = sc.nextInt();

        if (!Database.rooms.containsKey(roomNumber)) {
            System.out.println("Room not found!");
            return;
        }

        Hotel room = Database.rooms.get(roomNumber);

        if (room.isBooked()) {
            System.out.println("Room already booked!");
            return;
        }

        System.out.print("Enter Number of Days: ");
        int days = sc.nextInt();

        Booking booking = new Booking(bookingCounter++, Database.users.get(userId), room, days);

        Database.bookings.put(booking.getBookingId(), booking);
        room.setBooked(true);

        System.out.println("Booking Successful!");
        System.out.println(booking);
    }

    public static void adminMenu() {
        while (true) {
            System.out.println("\n===== ADMIN MENU =====");
            System.out.println("1. View Users");
            System.out.println("2. View Rooms");
            System.out.println("3. View Bookings");
            System.out.println("4. Back");
            System.out.print("Enter choice: ");

            int choice = sc.nextInt();

            switch (choice) {
                case 1 -> AdminDashboard.viewUsers();
                case 2 -> AdminDashboard.viewRooms();
                case 3 -> AdminDashboard.viewBookings();
                case 4 -> { return; }
                default -> System.out.println("Invalid choice!");
            }
        }
    }
}

