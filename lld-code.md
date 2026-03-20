# LLD Handbook — Full Java Implementations (30 Problems)

> Each problem: **Functional Requirements → Entities → Class Diagram → Full Java Code → Service Layer → Driver Code (main)**

---

# 📑 Index (Click to Navigate)

### 🟢 Easy (1-7)
| # | Problem | Jump |
|---|---------|------|
| 1 | Parking Lot | [→ Go](#1--parking-lot-system-) |
| 2 | Logger System | [→ Go](#2--logger-system-) |
| 3 | Vending Machine | [→ Go](#3--vending-machine-) |
| 4 | Stack Overflow (Q&A) | [→ Go](#4--stack-overflow-qa-platform-) |
| 5 | Library Management | [→ Go](#5--library-management-system-) |
| 6 | Tic-Tac-Toe | [→ Go](#6--tic-tac-toe-) |
| 7 | Traffic Signal | [→ Go](#7--traffic-signal-system-) |

### 🟡 Medium (8-23)
| # | Problem | Jump |
|---|---------|------|
| 8 | Elevator System | [→ Go](#8--elevator-system-) |
| 9 | In-Memory KV Store | [→ Go](#9--in-memory-key-value-store-) |
| 10 | LRU Cache | [→ Go](#10--lru-cache-) |
| 11 | BookMyShow | [→ Go](#11--movie-ticket-booking-bookmyshow-) |
| 12 | Chess Game | [→ Go](#12--chess-game-) |
| 13 | Snake & Ladder | [→ Go](#13--snake--ladder-) |
| 14 | ATM Machine | [→ Go](#14--atm-machine-) |
| 15 | Shopping Cart | [→ Go](#15--online-shopping-cart-) |
| 16 | Food Delivery | [→ Go](#16--food-delivery-swiggyzomato-) |
| 17 | Hotel Booking | [→ Go](#17--hotel-booking-system-) |
| 18 | In-Memory File System | [→ Go](#18--in-memory-file-system-) |
| 19 | Notification Service | [→ Go](#19--notification-service-) |
| 20 | Chat Application | [→ Go](#20--chat-application-) |
| 21 | Job Scheduler | [→ Go](#21--in-memory-job-scheduler-) |
| 22 | Rate Limiter | [→ Go](#22--rate-limiter-) |
| 23 | Splitwise | [→ Go](#23--splitwise-expense-sharing-) |

### 🔴 Hard (24-30)
| # | Problem | Jump |
|---|---------|------|
| 24 | Ride Sharing (Uber) | [→ Go](#24--ride-sharing-uberola-) |
| 25 | Cricket Scorecard | [→ Go](#25--cricket-scorecard-cricinfo-) |
| 26 | LinkedIn | [→ Go](#26--linkedin-) |
| 27 | Amazon Locker | [→ Go](#27--amazon-locker-system-) |
| 28 | Concert Tickets | [→ Go](#28--concert-ticket-system-) |
| 29 | Stock Exchange | [→ Go](#29--stock-exchange--trading-system-) |
| 30 | Task Management (Jira) | [→ Go](#30--task-management-system-jira-like-) |

### Reference
- [Pattern Cheat Sheet](#pattern-cheat-sheet) · [Imports Reference](#imports-reference)

---

# EASY PROBLEMS (1-7)

---

## 1 · Parking Lot System 🟢
**Pattern: Singleton, Strategy**

### 1.1 Functional Requirements
1. Multi-floor parking lot with different spot types (Compact, Large, Handicapped)
2. Vehicle types: Car, Truck, Motorcycle — each fits specific spot types
3. Assign nearest available spot on entry; generate ticket
4. Free spot on exit; calculate fee based on duration
5. Display available spots per floor/type

### 1.2 Entities
- `ParkingLot` — singleton, manages floors and active tickets
- `Floor` — holds parking spots, finds available ones
- `ParkingSpot` — tracks occupancy, checks vehicle compatibility
- `Vehicle` — abstract (Car/Truck/Motorcycle), carries license plate + type
- `Ticket` — records vehicle, spot, entry/exit time
- `FeeStrategy` — interface for fee calculation (hourly, flat)

### 1.3 Class Diagram
```
ParkingLot (Singleton)
 └── List<Floor>
      └── List<ParkingSpot>
           - spotId, type, isOccupied, vehicle

Vehicle (abstract) → Car, Truck, Motorcycle
 - licensePlate, vehicleType

Ticket
 - ticketId, vehicle, spot, entryTime, exitTime

FeeStrategy (interface) → HourlyFee, FlatFee
```

### 1.4 Java Code

```java
// ── Enums ──
public enum VehicleType { CAR, TRUCK, MOTORCYCLE }
public enum SpotType { COMPACT, LARGE, HANDICAPPED }

// ── Vehicle ──
public abstract class Vehicle {
    private String licensePlate;
    private VehicleType type;

    public Vehicle(String licensePlate, VehicleType type) {
        this.licensePlate = licensePlate;
        this.type = type;
    }

    public String getLicensePlate() { return licensePlate; }
    public VehicleType getType() { return type; }
}

public class Car extends Vehicle {
    public Car(String plate) { super(plate, VehicleType.CAR); }
}

public class Truck extends Vehicle {
    public Truck(String plate) { super(plate, VehicleType.TRUCK); }
}

public class Motorcycle extends Vehicle {
    public Motorcycle(String plate) { super(plate, VehicleType.MOTORCYCLE); }
}

// ── ParkingSpot ──
public class ParkingSpot {
    private String spotId;
    private SpotType type;
    private boolean isOccupied;
    private Vehicle vehicle;

    public ParkingSpot(String spotId, SpotType type) {
        this.spotId = spotId;
        this.type = type;
        this.isOccupied = false;
    }

    public boolean canFit(Vehicle v) {
        return switch (v.getType()) {
            case MOTORCYCLE -> true; // fits anywhere
            case CAR -> type == SpotType.COMPACT || type == SpotType.LARGE;
            case TRUCK -> type == SpotType.LARGE;
        };
    }

    public void occupy(Vehicle v) { this.vehicle = v; this.isOccupied = true; }
    public void release() { this.vehicle = null; this.isOccupied = false; }

    // getters
    public String getSpotId() { return spotId; }
    public SpotType getType() { return type; }
    public boolean isOccupied() { return isOccupied; }
    public Vehicle getVehicle() { return vehicle; }
}

// ── Floor ──
public class Floor {
    private int floorNumber;
    private List<ParkingSpot> spots;

    public Floor(int floorNumber, List<ParkingSpot> spots) {
        this.floorNumber = floorNumber;
        this.spots = spots;
    }

    public ParkingSpot findAvailableSpot(Vehicle v) {
        return spots.stream()
                .filter(s -> !s.isOccupied() && s.canFit(v))
                .findFirst()
                .orElse(null);
    }

    public long getAvailableCount(SpotType type) {
        return spots.stream().filter(s -> !s.isOccupied() && s.getType() == type).count();
    }

    public int getFloorNumber() { return floorNumber; }
    public List<ParkingSpot> getSpots() { return spots; }
}

// ── Ticket ──
public class Ticket {
    private String ticketId;
    private Vehicle vehicle;
    private ParkingSpot spot;
    private LocalDateTime entryTime;
    private LocalDateTime exitTime;

    public Ticket(String ticketId, Vehicle vehicle, ParkingSpot spot) {
        this.ticketId = ticketId;
        this.vehicle = vehicle;
        this.spot = spot;
        this.entryTime = LocalDateTime.now();
    }

    public void markExit() { this.exitTime = LocalDateTime.now(); }

    public long getParkedHours() {
        if (exitTime == null) return 0;
        return ChronoUnit.HOURS.between(entryTime, exitTime) + 1; // round up
    }

    // getters
    public String getTicketId() { return ticketId; }
    public Vehicle getVehicle() { return vehicle; }
    public ParkingSpot getSpot() { return spot; }
    public LocalDateTime getEntryTime() { return entryTime; }
    public LocalDateTime getExitTime() { return exitTime; }
}

// ── FeeStrategy (Strategy Pattern) ──
public interface FeeStrategy {
    double calculateFee(Ticket ticket);
}

public class HourlyFeeStrategy implements FeeStrategy {
    private Map<VehicleType, Double> ratePerHour;

    public HourlyFeeStrategy() {
        ratePerHour = Map.of(
            VehicleType.MOTORCYCLE, 10.0,
            VehicleType.CAR, 20.0,
            VehicleType.TRUCK, 30.0
        );
    }

    @Override
    public double calculateFee(Ticket ticket) {
        double rate = ratePerHour.getOrDefault(ticket.getVehicle().getType(), 20.0);
        return rate * ticket.getParkedHours();
    }
}

public class FlatFeeStrategy implements FeeStrategy {
    private double flatRate;
    public FlatFeeStrategy(double flatRate) { this.flatRate = flatRate; }

    @Override
    public double calculateFee(Ticket ticket) { return flatRate; }
}

// ── ParkingLot (Singleton) ──
public class ParkingLot {
    private static ParkingLot instance;
    private List<Floor> floors;
    private FeeStrategy feeStrategy;
    private Map<String, Ticket> activeTickets; // ticketId -> Ticket

    private ParkingLot() {
        this.floors = new ArrayList<>();
        this.activeTickets = new HashMap<>();
    }

    public static synchronized ParkingLot getInstance() {
        if (instance == null) instance = new ParkingLot();
        return instance;
    }

    public void addFloor(Floor floor) { floors.add(floor); }
    public void setFeeStrategy(FeeStrategy strategy) { this.feeStrategy = strategy; }

    public Ticket parkVehicle(Vehicle vehicle) {
        for (Floor floor : floors) {
            ParkingSpot spot = floor.findAvailableSpot(vehicle);
            if (spot != null) {
                spot.occupy(vehicle);
                Ticket ticket = new Ticket(UUID.randomUUID().toString(), vehicle, spot);
                activeTickets.put(ticket.getTicketId(), ticket);
                return ticket;
            }
        }
        throw new RuntimeException("No available spot for vehicle: " + vehicle.getLicensePlate());
    }

    public double unparkVehicle(String ticketId) {
        Ticket ticket = activeTickets.remove(ticketId);
        if (ticket == null) throw new RuntimeException("Invalid ticket");
        ticket.markExit();
        ticket.getSpot().release();
        return feeStrategy.calculateFee(ticket);
    }

    public void displayAvailability() {
        for (Floor floor : floors) {
            System.out.println("Floor " + floor.getFloorNumber() + ":");
            for (SpotType type : SpotType.values()) {
                System.out.println("  " + type + ": " + floor.getAvailableCount(type) + " available");
            }
        }
    }
}
```

### 1.5 Service Layer

```java
public class ParkingService {
    private final ParkingLot parkingLot;

    public ParkingService() {
        this.parkingLot = ParkingLot.getInstance();
    }

    public Ticket entry(Vehicle vehicle) {
        Ticket ticket = parkingLot.parkVehicle(vehicle);
        System.out.println("Vehicle " + vehicle.getLicensePlate() +
                " parked at spot " + ticket.getSpot().getSpotId());
        return ticket;
    }

    public double exit(String ticketId) {
        double fee = parkingLot.unparkVehicle(ticketId);
        System.out.println("Fee for ticket " + ticketId + ": ₹" + fee);
        return fee;
    }

    public void showAvailability() {
        parkingLot.displayAvailability();
    }
}
```

### 1.6 Driver Code (main)
```java
public class ParkingLotDemo {
    public static void main(String[] args) {
        // 1. Setup parking lot
        ParkingLot lot = ParkingLot.getInstance();
        lot.setFeeStrategy(new HourlyFeeStrategy());

        List<ParkingSpot> floor1Spots = new ArrayList<>();
        for (int i = 1; i <= 5; i++) floor1Spots.add(new ParkingSpot("F1-C" + i, SpotType.COMPACT));
        for (int i = 1; i <= 3; i++) floor1Spots.add(new ParkingSpot("F1-L" + i, SpotType.LARGE));
        lot.addFloor(new Floor(1, floor1Spots));

        ParkingService service = new ParkingService();

        // 2. Park vehicles
        Vehicle car = new Car("KA-01-1234");
        Ticket ticket = service.entry(car);
        // → "Vehicle KA-01-1234 parked at spot F1-C1"

        Vehicle truck = new Truck("KA-02-5678");
        Ticket ticket2 = service.entry(truck);
        // → "Vehicle KA-02-5678 parked at spot F1-L1"

        // 3. Display availability
        service.showAvailability();
        // Floor 1: COMPACT: 4 available, LARGE: 2 available

        // 4. Exit and pay
        double fee = service.exit(ticket.getTicketId());
        // → "Fee for ticket xxx: ₹20.0"
    }
}
```

---

## 2 · Logger System 🟢
**Pattern: Singleton, Chain of Responsibility**

### 2.1 Functional Requirements
1. Log levels: DEBUG, INFO, WARN, ERROR, FATAL
2. Each handler processes its level and forwards to next (chain)
3. Output sinks: Console, File (pluggable)
4. Singleton global logger instance
5. Thread-safe logging

### 2.2 Entities
- `Logger` — singleton, entry point for all log calls
- `LogLevel` — enum (DEBUG→FATAL) with severity ranking
- `LogHandler` — abstract chain node, processes or forwards messages
- `LogMessage` — holds level, message, timestamp

### 2.3 Class Diagram
```
Logger (Singleton)
 - handlerChain: LogHandler

LogHandler (abstract) → Chain of Resp.
 ├── DebugHandler
 ├── InfoHandler
 ├── WarnHandler
 ├── ErrorHandler
 └── FatalHandler
     - nextHandler: LogHandler

LogMessage
 - level, message, timestamp
```

### 2.4 Java Code

```java
// ── Enum ──
public enum LogLevel {
    DEBUG(1), INFO(2), WARN(3), ERROR(4), FATAL(5);

    private int severity;
    LogLevel(int severity) { this.severity = severity; }
    public int getSeverity() { return severity; }
}

// ── LogMessage ──
public class LogMessage {
    private LogLevel level;
    private String message;
    private LocalDateTime timestamp;

    public LogMessage(LogLevel level, String message) {
        this.level = level;
        this.message = message;
        this.timestamp = LocalDateTime.now();
    }

    public LogLevel getLevel() { return level; }
    public String getMessage() { return message; }
    public LocalDateTime getTimestamp() { return timestamp; }

    @Override
    public String toString() {
        return "[" + timestamp + "] [" + level + "] " + message;
    }
}

// ── LogHandler (Chain of Responsibility) ──
public abstract class LogHandler {
    protected LogHandler nextHandler;
    protected LogLevel level;

    public LogHandler(LogLevel level) {
        this.level = level;
    }

    public LogHandler setNext(LogHandler next) {
        this.nextHandler = next;
        return next;
    }

    public void handle(LogMessage msg) {
        if (msg.getLevel().getSeverity() >= level.getSeverity()) {
            write(msg);
        }
        if (nextHandler != null) {
            nextHandler.handle(msg);
        }
    }

    protected abstract void write(LogMessage msg);
}

public class ConsoleHandler extends LogHandler {
    public ConsoleHandler(LogLevel level) { super(level); }

    @Override
    protected void write(LogMessage msg) {
        System.out.println("CONSOLE: " + msg);
    }
}

public class FileHandler extends LogHandler {
    private String filePath;

    public FileHandler(LogLevel level, String filePath) {
        super(level);
        this.filePath = filePath;
    }

    @Override
    protected void write(LogMessage msg) {
        // In production: append to file
        System.out.println("FILE [" + filePath + "]: " + msg);
    }
}

// ── Logger (Singleton) ──
public class Logger {
    private static volatile Logger instance;
    private LogHandler handlerChain;

    private Logger() {}

    public static Logger getInstance() {
        if (instance == null) {
            synchronized (Logger.class) {
                if (instance == null) {
                    instance = new Logger();
                }
            }
        }
        return instance;
    }

    public void setHandlerChain(LogHandler chain) {
        this.handlerChain = chain;
    }

    public void log(LogLevel level, String message) {
        LogMessage msg = new LogMessage(level, message);
        if (handlerChain != null) {
            handlerChain.handle(msg);
        }
    }

    public void debug(String msg) { log(LogLevel.DEBUG, msg); }
    public void info(String msg)  { log(LogLevel.INFO, msg); }
    public void warn(String msg)  { log(LogLevel.WARN, msg); }
    public void error(String msg) { log(LogLevel.ERROR, msg); }
    public void fatal(String msg) { log(LogLevel.FATAL, msg); }
}
```

### 2.5 Service Layer

```java
public class LoggingService {
    private final Logger logger;

    public LoggingService() {
        this.logger = Logger.getInstance();
        // Setup chain: DEBUG → console, ERROR → file
        LogHandler consoleHandler = new ConsoleHandler(LogLevel.DEBUG);
        LogHandler fileHandler = new FileHandler(LogLevel.ERROR, "/var/log/app.log");
        consoleHandler.setNext(fileHandler);
        logger.setHandlerChain(consoleHandler);
    }

    public void logDebug(String msg) { logger.debug(msg); }
    public void logInfo(String msg)  { logger.info(msg); }
    public void logError(String msg) { logger.error(msg); }

    // Usage from any service
    // loggingService.logInfo("User logged in");
    // loggingService.logError("Database connection failed");
}
```

### 2.6 Driver Code (main)
```java
public class LoggerDemo {
    public static void main(String[] args) {
        // 1. Build handler chain: DEBUG(console) → ERROR(file)
        LogHandler consoleHandler = new ConsoleHandler(LogLevel.DEBUG);
        LogHandler fileHandler = new FileHandler(LogLevel.ERROR, "/var/log/app.log");
        consoleHandler.setNext(fileHandler);

        // 2. Setup singleton logger
        Logger logger = Logger.getInstance();
        logger.setHandlerChain(consoleHandler);

        // 3. Log messages — chain decides who handles
        logger.info("Server started on port 8080");
        // CONSOLE: [2026-03-20T10:00] [INFO] Server started on port 8080

        logger.error("Database connection failed");
        // CONSOLE: [2026-03-20T10:00] [ERROR] Database connection failed
        // FILE [/var/log/app.log]: [2026-03-20T10:00] [ERROR] Database connection failed

        logger.debug("Cache hit ratio: 92%");
        // CONSOLE: [2026-03-20T10:00] [DEBUG] Cache hit ratio: 92%
    }
}
```

---

## 3 · Vending Machine 🟢
**Pattern: State**

### 3.1 Functional Requirements
1. Display products with price and quantity
2. Accept coins/notes, track inserted amount
3. States: Idle → HasMoney → Dispensing → ReturnChange
4. Dispense product if enough money; else return change
5. Admin can refill products

### 3.2 Entities
- `VendingMachine` — holds state, inventory, balance; delegates to current state
- `Product` — code, name, price
- `Inventory` — maps product codes to quantities
- `VendingState` — interface for state-dependent behavior (Idle/HasMoney/Dispensing/ReturnChange)

### 3.3 Class Diagram
```
VendingMachine
 - currentState, inventory, currentBalance, selectedProduct

State (interface)
 ├── IdleState
 ├── HasMoneyState
 ├── DispensingState
 └── ReturnChangeState

Inventory → Map<Product, Integer>
Product → name, price, code
```

### 3.4 Java Code

```java
// ── Product ──
public class Product {
    private String code;
    private String name;
    private double price;

    public Product(String code, String name, double price) {
        this.code = code;
        this.name = name;
        this.price = price;
    }

    public String getCode() { return code; }
    public String getName() { return name; }
    public double getPrice() { return price; }
}

// ── Inventory ──
public class Inventory {
    private Map<String, Product> products;    // code -> product
    private Map<String, Integer> quantities;  // code -> qty

    public Inventory() {
        this.products = new HashMap<>();
        this.quantities = new HashMap<>();
    }

    public void addProduct(Product p, int qty) {
        products.put(p.getCode(), p);
        quantities.merge(p.getCode(), qty, Integer::sum);
    }

    public Product getProduct(String code) { return products.get(code); }

    public boolean isAvailable(String code) {
        return quantities.getOrDefault(code, 0) > 0;
    }

    public void reduceQuantity(String code) {
        quantities.computeIfPresent(code, (k, v) -> v - 1);
    }
}

// ── State Interface ──
public interface VendingState {
    void insertMoney(VendingMachine vm, double amount);
    void selectProduct(VendingMachine vm, String code);
    void dispense(VendingMachine vm);
    void returnChange(VendingMachine vm);
}

// ── Idle State ──
public class IdleState implements VendingState {
    @Override
    public void insertMoney(VendingMachine vm, double amount) {
        vm.setCurrentBalance(amount);
        vm.setState(new HasMoneyState());
        System.out.println("Money inserted: ₹" + amount);
    }

    @Override
    public void selectProduct(VendingMachine vm, String code) {
        System.out.println("Please insert money first");
    }

    @Override
    public void dispense(VendingMachine vm) {
        System.out.println("Please insert money and select product");
    }

    @Override
    public void returnChange(VendingMachine vm) {
        System.out.println("No money to return");
    }
}

// ── HasMoney State ──
public class HasMoneyState implements VendingState {
    @Override
    public void insertMoney(VendingMachine vm, double amount) {
        vm.setCurrentBalance(vm.getCurrentBalance() + amount);
        System.out.println("Total balance: ₹" + vm.getCurrentBalance());
    }

    @Override
    public void selectProduct(VendingMachine vm, String code) {
        Product product = vm.getInventory().getProduct(code);
        if (product == null) {
            System.out.println("Invalid product code");
            return;
        }
        if (!vm.getInventory().isAvailable(code)) {
            System.out.println("Product out of stock");
            return;
        }
        if (vm.getCurrentBalance() < product.getPrice()) {
            System.out.println("Insufficient balance. Need ₹" +
                    (product.getPrice() - vm.getCurrentBalance()) + " more");
            return;
        }
        vm.setSelectedProduct(product);
        vm.setState(new DispensingState());
        vm.dispense();
    }

    @Override
    public void dispense(VendingMachine vm) {
        System.out.println("Please select a product first");
    }

    @Override
    public void returnChange(VendingMachine vm) {
        System.out.println("Returning ₹" + vm.getCurrentBalance());
        vm.setCurrentBalance(0);
        vm.setState(new IdleState());
    }
}

// ── Dispensing State ──
public class DispensingState implements VendingState {
    @Override
    public void insertMoney(VendingMachine vm, double amount) {
        System.out.println("Please wait, dispensing in progress");
    }

    @Override
    public void selectProduct(VendingMachine vm, String code) {
        System.out.println("Please wait, dispensing in progress");
    }

    @Override
    public void dispense(VendingMachine vm) {
        Product p = vm.getSelectedProduct();
        vm.getInventory().reduceQuantity(p.getCode());
        vm.setCurrentBalance(vm.getCurrentBalance() - p.getPrice());
        System.out.println("Dispensed: " + p.getName());

        if (vm.getCurrentBalance() > 0) {
            vm.setState(new ReturnChangeState());
            vm.returnChange();
        } else {
            vm.setState(new IdleState());
        }
    }

    @Override
    public void returnChange(VendingMachine vm) {
        System.out.println("Please wait, dispensing in progress");
    }
}

// ── ReturnChange State ──
public class ReturnChangeState implements VendingState {
    @Override
    public void insertMoney(VendingMachine vm, double amount) {
        System.out.println("Please collect your change first");
    }

    @Override
    public void selectProduct(VendingMachine vm, String code) {
        System.out.println("Please collect your change first");
    }

    @Override
    public void dispense(VendingMachine vm) {
        System.out.println("Already dispensed");
    }

    @Override
    public void returnChange(VendingMachine vm) {
        System.out.println("Returning change: ₹" + vm.getCurrentBalance());
        vm.setCurrentBalance(0);
        vm.setSelectedProduct(null);
        vm.setState(new IdleState());
    }
}

// ── VendingMachine ──
public class VendingMachine {
    private VendingState currentState;
    private Inventory inventory;
    private double currentBalance;
    private Product selectedProduct;

    public VendingMachine() {
        this.currentState = new IdleState();
        this.inventory = new Inventory();
    }

    public void insertMoney(double amount)    { currentState.insertMoney(this, amount); }
    public void selectProduct(String code)    { currentState.selectProduct(this, code); }
    public void dispense()                    { currentState.dispense(this); }
    public void returnChange()                { currentState.returnChange(this); }

    // Getters and Setters
    public VendingState getState() { return currentState; }
    public void setState(VendingState state) { this.currentState = state; }
    public Inventory getInventory() { return inventory; }
    public double getCurrentBalance() { return currentBalance; }
    public void setCurrentBalance(double b) { this.currentBalance = b; }
    public Product getSelectedProduct() { return selectedProduct; }
    public void setSelectedProduct(Product p) { this.selectedProduct = p; }
}
```

### 3.5 Service Layer

```java
public class VendingMachineService {
    private final VendingMachine machine;

    public VendingMachineService() {
        this.machine = new VendingMachine();
    }

    public void setupProducts() {
        machine.getInventory().addProduct(new Product("A1", "Coke", 40), 5);
        machine.getInventory().addProduct(new Product("A2", "Pepsi", 35), 3);
        machine.getInventory().addProduct(new Product("B1", "Chips", 20), 10);
    }

    public void purchaseProduct(double money, String productCode) {
        machine.insertMoney(money);
        machine.selectProduct(productCode);
    }

    public void cancelAndRefund() {
        machine.returnChange();
    }

    public void refillProduct(Product product, int qty) {
        machine.getInventory().addProduct(product, qty);
    }
}
```

### 3.6 Driver Code (main)
```java
public class VendingMachineDemo {
    public static void main(String[] args) {
        VendingMachineService service = new VendingMachineService();
        service.setupProducts();

        // Happy path: purchase with enough money
        service.purchaseProduct(50, "A1");
        // "Money inserted: ₹50"
        // "Dispensed: Coke"
        // "Returning change: ₹10"

        // Insufficient money
        service.purchaseProduct(10, "A1");
        // "Money inserted: ₹10"
        // "Insufficient balance. Need ₹30 more"
        service.cancelAndRefund();
        // "Returning ₹10"

        // Out of stock (after buying all 5 Cokes)
        for (int i = 0; i < 4; i++) service.purchaseProduct(40, "A1");
        service.purchaseProduct(40, "A1");
        // "Product out of stock"
    }
}
```

---

## 4 · Stack Overflow (Q&A Platform) 🟢
**Pattern: Observer**

### 4.1 Functional Requirements
1. Users post questions, answer questions
2. Upvote/downvote questions and answers
3. Tags on questions, search by tag
4. Accept answer, reputation system
5. Comment on questions and answers

### 4.2 Entities
- `User` — posts questions/answers, earns reputation, observes questions
- `Question` — observable; holds title, body, tags, answers, votes
- `Answer` — votable; can be accepted by question author
- `Comment` — text attached to question or answer
- `Vote` — UP or DOWN, linked to a user
- `Votable` — interface shared by Question and Answer

### 4.3 Class Diagram
```
User - id, name, email, reputation
Question - id, title, body, author, tags[], answers[], votes[], comments[], observers[]
Answer - id, body, author, votes[], isAccepted, comments[]
Comment - id, body, author, timestamp
Vote - voteType(UP/DOWN), user
```

### 4.4 Java Code

```java
// ── Enums ──
public enum VoteType { UP, DOWN }

// ── Observer interface ──
public interface QuestionObserver {
    void onNewAnswer(Question question, Answer answer);
}

// ── User ──
public class User implements QuestionObserver {
    private String id;
    private String name;
    private String email;
    private int reputation;

    public User(String id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.reputation = 0;
    }

    public void addReputation(int points) { this.reputation += points; }

    @Override
    public void onNewAnswer(Question question, Answer answer) {
        System.out.println("Notification to " + name + ": New answer on '" +
                question.getTitle() + "' by " + answer.getAuthor().getName());
    }

    // getters
    public String getId() { return id; }
    public String getName() { return name; }
    public int getReputation() { return reputation; }
}

// ── Comment ──
public class Comment {
    private String id;
    private String body;
    private User author;
    private LocalDateTime timestamp;

    public Comment(String id, String body, User author) {
        this.id = id;
        this.body = body;
        this.author = author;
        this.timestamp = LocalDateTime.now();
    }

    public String getBody() { return body; }
    public User getAuthor() { return author; }
}

// ── Vote ──
public class Vote {
    private VoteType type;
    private User user;

    public Vote(VoteType type, User user) {
        this.type = type;
        this.user = user;
    }

    public VoteType getType() { return type; }
    public User getUser() { return user; }
}

// ── Votable (shared by Question & Answer) ──
public interface Votable {
    void addVote(Vote vote);
    int getVoteCount();
}

// ── Answer ──
public class Answer implements Votable {
    private String id;
    private String body;
    private User author;
    private List<Vote> votes;
    private boolean isAccepted;
    private List<Comment> comments;

    public Answer(String id, String body, User author) {
        this.id = id;
        this.body = body;
        this.author = author;
        this.votes = new ArrayList<>();
        this.comments = new ArrayList<>();
        this.isAccepted = false;
    }

    @Override
    public void addVote(Vote vote) {
        votes.add(vote);
        int rep = vote.getType() == VoteType.UP ? 10 : -2;
        author.addReputation(rep);
    }

    @Override
    public int getVoteCount() {
        return (int) votes.stream().filter(v -> v.getType() == VoteType.UP).count()
             - (int) votes.stream().filter(v -> v.getType() == VoteType.DOWN).count();
    }

    public void accept() { this.isAccepted = true; author.addReputation(15); }
    public void addComment(Comment c) { comments.add(c); }

    public String getId() { return id; }
    public String getBody() { return body; }
    public User getAuthor() { return author; }
    public boolean isAccepted() { return isAccepted; }
}

// ── Question (Observable) ──
public class Question implements Votable {
    private String id;
    private String title;
    private String body;
    private User author;
    private List<String> tags;
    private List<Answer> answers;
    private List<Vote> votes;
    private List<Comment> comments;
    private List<QuestionObserver> observers;

    public Question(String id, String title, String body, User author, List<String> tags) {
        this.id = id;
        this.title = title;
        this.body = body;
        this.author = author;
        this.tags = tags;
        this.answers = new ArrayList<>();
        this.votes = new ArrayList<>();
        this.comments = new ArrayList<>();
        this.observers = new ArrayList<>();
    }

    public void subscribe(QuestionObserver observer) { observers.add(observer); }
    public void unsubscribe(QuestionObserver observer) { observers.remove(observer); }

    public void addAnswer(Answer answer) {
        answers.add(answer);
        observers.forEach(o -> o.onNewAnswer(this, answer));
    }

    public void acceptAnswer(String answerId) {
        answers.stream()
                .filter(a -> a.getId().equals(answerId))
                .findFirst()
                .ifPresent(Answer::accept);
    }

    @Override
    public void addVote(Vote vote) {
        votes.add(vote);
        int rep = vote.getType() == VoteType.UP ? 5 : -1;
        author.addReputation(rep);
    }

    @Override
    public int getVoteCount() {
        return (int) votes.stream().filter(v -> v.getType() == VoteType.UP).count()
             - (int) votes.stream().filter(v -> v.getType() == VoteType.DOWN).count();
    }

    public void addComment(Comment c) { comments.add(c); }

    // getters
    public String getId() { return id; }
    public String getTitle() { return title; }
    public User getAuthor() { return author; }
    public List<String> getTags() { return tags; }
    public List<Answer> getAnswers() { return answers; }
}
```

### 4.5 Service Layer

```java
public class QnAService {
    private Map<String, Question> questions = new HashMap<>();
    private Map<String, User> users = new HashMap<>();

    public User registerUser(String id, String name, String email) {
        User user = new User(id, name, email);
        users.put(id, user);
        return user;
    }

    public Question postQuestion(String userId, String title, String body, List<String> tags) {
        User author = users.get(userId);
        Question q = new Question(UUID.randomUUID().toString(), title, body, author, tags);
        q.subscribe(author); // author gets notified of answers
        questions.put(q.getId(), q);
        return q;
    }

    public Answer postAnswer(String userId, String questionId, String body) {
        User author = users.get(userId);
        Question q = questions.get(questionId);
        Answer a = new Answer(UUID.randomUUID().toString(), body, author);
        q.addAnswer(a); // triggers observer notification
        return a;
    }

    public void upvoteQuestion(String userId, String questionId) {
        questions.get(questionId).addVote(new Vote(VoteType.UP, users.get(userId)));
    }

    public void upvoteAnswer(String questionId, String answerId, String userId) {
        questions.get(questionId).getAnswers().stream()
                .filter(a -> a.getId().equals(answerId))
                .findFirst()
                .ifPresent(a -> a.addVote(new Vote(VoteType.UP, users.get(userId))));
    }

    public void acceptAnswer(String questionId, String answerId) {
        questions.get(questionId).acceptAnswer(answerId);
    }

    public List<Question> searchByTag(String tag) {
        return questions.values().stream()
                .filter(q -> q.getTags().contains(tag))
                .collect(Collectors.toList());
    }
}
```

### 4.6 Driver Code (main)
```java
public class StackOverflowDemo {
    public static void main(String[] args) {
        QnAService service = new QnAService();

        // 1. Register users
        User alice = service.registerUser("u1", "Alice", "alice@email.com");
        User bob = service.registerUser("u2", "Bob", "bob@email.com");

        // 2. Alice posts question (auto-subscribes as observer)
        Question q = service.postQuestion("u1", "How does HashMap work?",
                "Explain internal implementation", List.of("java", "collections"));

        // 3. Bob answers → Alice gets notified
        Answer a = service.postAnswer("u2", q.getId(), "It uses array of linked lists...");
        // "Notification to Alice: New answer on 'How does HashMap work?' by Bob"

        // 4. Upvote + accept
        service.upvoteAnswer(q.getId(), a.getId(), "u1");  // Bob: rep 0 → 10
        service.acceptAnswer(q.getId(), a.getId());         // Bob: rep 10 → 25
        System.out.println("Bob's reputation: " + bob.getReputation()); // 25

        // 5. Search by tag
        List<Question> javaQs = service.searchByTag("java");
        System.out.println("Java questions: " + javaQs.size()); // 1
    }
}
```

---

## 5 · Library Management System 🟢
**Pattern: Clean OOP**

### 5.1 Functional Requirements
1. Add/remove books; track multiple copies per book
2. Members search, borrow, and return books
3. Max 5 books per member, 14-day return window
4. Fine of ₹1/day for overdue books
5. Reservation if all copies are lent out

### 5.2 Entities
- `Book` — metadata (ISBN, title, author, subject)
- `BookItem` — physical copy with barcode, tracks availability + due date
- `Member` — borrower with max 5 active loans
- `Loan` — links BookItem to Member with issue/due/return dates
- `Reservation` — queued request when all copies are lent out
- `Fine` — calculated from overdue days × rate

### 5.3 Class Diagram
```
Book - isbn, title, author, subject
BookItem - barcode, book, isAvailable, dueDate
Member - memberId, name, activeLoans[], reservations[]
Loan - loanId, bookItem, member, issueDate, dueDate, returnDate
Reservation - id, book, member, status(WAITING/FULFILLED/CANCELLED)
```

### 5.4 Java Code

```java
// ── Enums ──
public enum ReservationStatus { WAITING, FULFILLED, CANCELLED }

// ── Book ──
public class Book {
    private String isbn;
    private String title;
    private String author;
    private String subject;

    public Book(String isbn, String title, String author, String subject) {
        this.isbn = isbn;
        this.title = title;
        this.author = author;
        this.subject = subject;
    }

    public String getIsbn() { return isbn; }
    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    public String getSubject() { return subject; }
}

// ── BookItem (physical copy) ──
public class BookItem {
    private String barcode;
    private Book book;
    private boolean isAvailable;
    private LocalDate dueDate;

    public BookItem(String barcode, Book book) {
        this.barcode = barcode;
        this.book = book;
        this.isAvailable = true;
    }

    public void checkout(LocalDate due) {
        this.isAvailable = false;
        this.dueDate = due;
    }

    public void returnItem() {
        this.isAvailable = true;
        this.dueDate = null;
    }

    public String getBarcode() { return barcode; }
    public Book getBook() { return book; }
    public boolean isAvailable() { return isAvailable; }
    public LocalDate getDueDate() { return dueDate; }
}

// ── Member ──
public class Member {
    private String memberId;
    private String name;
    private List<Loan> activeLoans;
    private List<Reservation> reservations;
    private static final int MAX_BOOKS = 5;

    public Member(String memberId, String name) {
        this.memberId = memberId;
        this.name = name;
        this.activeLoans = new ArrayList<>();
        this.reservations = new ArrayList<>();
    }

    public boolean canBorrow() { return activeLoans.size() < MAX_BOOKS; }
    public void addLoan(Loan loan) { activeLoans.add(loan); }
    public void removeLoan(Loan loan) { activeLoans.remove(loan); }
    public void addReservation(Reservation r) { reservations.add(r); }

    public String getMemberId() { return memberId; }
    public String getName() { return name; }
    public List<Loan> getActiveLoans() { return activeLoans; }
}

// ── Loan ──
public class Loan {
    private String loanId;
    private BookItem bookItem;
    private Member member;
    private LocalDate issueDate;
    private LocalDate dueDate;
    private LocalDate returnDate;

    public Loan(String loanId, BookItem bookItem, Member member) {
        this.loanId = loanId;
        this.bookItem = bookItem;
        this.member = member;
        this.issueDate = LocalDate.now();
        this.dueDate = issueDate.plusDays(14);
    }

    public void markReturned() { this.returnDate = LocalDate.now(); }

    public long getOverdueDays() {
        LocalDate returned = returnDate != null ? returnDate : LocalDate.now();
        long days = ChronoUnit.DAYS.between(dueDate, returned);
        return Math.max(0, days);
    }

    public String getLoanId() { return loanId; }
    public BookItem getBookItem() { return bookItem; }
    public Member getMember() { return member; }
    public LocalDate getDueDate() { return dueDate; }
}

// ── Reservation ──
public class Reservation {
    private String id;
    private Book book;
    private Member member;
    private ReservationStatus status;
    private LocalDate createdDate;

    public Reservation(String id, Book book, Member member) {
        this.id = id;
        this.book = book;
        this.member = member;
        this.status = ReservationStatus.WAITING;
        this.createdDate = LocalDate.now();
    }

    public void fulfill() { this.status = ReservationStatus.FULFILLED; }
    public void cancel() { this.status = ReservationStatus.CANCELLED; }

    public Book getBook() { return book; }
    public Member getMember() { return member; }
    public ReservationStatus getStatus() { return status; }
}

// ── Fine ──
public class Fine {
    private Member member;
    private Loan loan;
    private double amount;

    public Fine(Member member, Loan loan, double amount) {
        this.member = member;
        this.loan = loan;
        this.amount = amount;
    }

    public double getAmount() { return amount; }
}
```

### 5.5 Service Layer

```java
public class LibraryService {
    private Map<String, Book> books = new HashMap<>();            // isbn -> Book
    private Map<String, List<BookItem>> bookItems = new HashMap<>(); // isbn -> copies
    private Map<String, Member> members = new HashMap<>();
    private Map<String, Loan> activeLoans = new HashMap<>();       // loanId -> Loan
    private Queue<Reservation> reservationQueue = new LinkedList<>();
    private static final double FINE_PER_DAY = 1.0;

    public void addBook(Book book, int copies) {
        books.put(book.getIsbn(), book);
        List<BookItem> items = new ArrayList<>();
        for (int i = 0; i < copies; i++) {
            items.add(new BookItem(UUID.randomUUID().toString(), book));
        }
        bookItems.put(book.getIsbn(), items);
    }

    public Loan borrowBook(String memberId, String isbn) {
        Member member = members.get(memberId);
        if (!member.canBorrow()) {
            throw new RuntimeException("Member has reached max borrow limit");
        }

        List<BookItem> items = bookItems.get(isbn);
        BookItem available = items.stream()
                .filter(BookItem::isAvailable)
                .findFirst()
                .orElse(null);

        if (available == null) {
            // No copy available — create reservation
            Reservation r = new Reservation(UUID.randomUUID().toString(), books.get(isbn), member);
            reservationQueue.add(r);
            member.addReservation(r);
            System.out.println("All copies lent. Reservation created for " + member.getName());
            return null;
        }

        available.checkout(LocalDate.now().plusDays(14));
        Loan loan = new Loan(UUID.randomUUID().toString(), available, member);
        member.addLoan(loan);
        activeLoans.put(loan.getLoanId(), loan);
        System.out.println(member.getName() + " borrowed '" + available.getBook().getTitle() + "'");
        return loan;
    }

    public Fine returnBook(String loanId) {
        Loan loan = activeLoans.remove(loanId);
        loan.markReturned();
        loan.getBookItem().returnItem();
        loan.getMember().removeLoan(loan);

        // Check reservation queue
        fulfillReservation(loan.getBookItem().getBook().getIsbn());

        // Calculate fine
        long overdue = loan.getOverdueDays();
        if (overdue > 0) {
            Fine fine = new Fine(loan.getMember(), loan, overdue * FINE_PER_DAY);
            System.out.println("Fine for " + loan.getMember().getName() + ": ₹" + fine.getAmount());
            return fine;
        }
        return null;
    }

    private void fulfillReservation(String isbn) {
        reservationQueue.stream()
                .filter(r -> r.getBook().getIsbn().equals(isbn)
                        && r.getStatus() == ReservationStatus.WAITING)
                .findFirst()
                .ifPresent(r -> {
                    r.fulfill();
                    System.out.println("Reservation fulfilled for " + r.getMember().getName());
                    borrowBook(r.getMember().getMemberId(), isbn);
                });
    }

    public List<Book> searchByTitle(String keyword) {
        return books.values().stream()
                .filter(b -> b.getTitle().toLowerCase().contains(keyword.toLowerCase()))
                .collect(Collectors.toList());
    }

    public void registerMember(Member member) {
        members.put(member.getMemberId(), member);
    }
}
```

### 5.6 Driver Code (main)
```java
public class LibraryDemo {
    public static void main(String[] args) {
        LibraryService lib = new LibraryService();

        // 1. Setup
        Book java = new Book("ISBN-001", "Effective Java", "Bloch", "Programming");
        lib.addBook(java, 2);  // 2 copies
        lib.registerMember(new Member("M1", "Alice"));
        lib.registerMember(new Member("M2", "Bob"));
        lib.registerMember(new Member("M3", "Charlie"));

        // 2. Alice borrows copy 1
        Loan loan1 = lib.borrowBook("M1", "ISBN-001");
        // "Alice borrowed 'Effective Java'"

        // 3. Bob borrows copy 2
        Loan loan2 = lib.borrowBook("M2", "ISBN-001");

        // 4. Charlie tries — no copies → reservation
        lib.borrowBook("M3", "ISBN-001");
        // "All copies lent. Reservation created for Charlie"

        // 5. Alice returns → fine if overdue + Charlie auto-fulfilled
        Fine fine = lib.returnBook(loan1.getLoanId());
        if (fine != null) System.out.println("Fine: ₹" + fine.getAmount());
        // "Reservation fulfilled for Charlie"
    }
}
```

---

## 6 · Tic-Tac-Toe 🟢
**Pattern: Clean OOP**

### 6.1 Functional Requirements
1. 3×3 board, two players (X and O)
2. Players take turns placing marks
3. Detect win (row/col/diagonal) or draw
4. Support replaying

### 6.2 Entities
- `Game` — manages board, players, turn order, win/draw detection
- `Board` — NxN grid, handles placement and win checking
- `Player` — name + assigned piece (X or O)
- `Piece` — enum (X, O)
- `GameStatus` — enum (IN_PROGRESS, WIN, DRAW)

### 6.3 Class Diagram
```
Game - board, players[], currentTurnIndex, status
Board - Cell[][] grid, size
Cell - row, col, piece
Player - name, piece(X/O)
Piece (enum) - X, O
```

### 6.4 Java Code

```java
// ── Enums ──
public enum Piece { X, O }
public enum GameStatus { IN_PROGRESS, WIN, DRAW }

// ── Player ──
public class Player {
    private String name;
    private Piece piece;

    public Player(String name, Piece piece) {
        this.name = name;
        this.piece = piece;
    }

    public String getName() { return name; }
    public Piece getPiece() { return piece; }
}

// ── Board ──
public class Board {
    private Piece[][] grid;
    private int size;
    private int filledCells;

    public Board(int size) {
        this.size = size;
        this.grid = new Piece[size][size];
        this.filledCells = 0;
    }

    public boolean placePiece(int row, int col, Piece piece) {
        if (row < 0 || row >= size || col < 0 || col >= size || grid[row][col] != null) {
            return false;
        }
        grid[row][col] = piece;
        filledCells++;
        return true;
    }

    public boolean checkWin(int row, int col, Piece piece) {
        // Check row
        boolean rowWin = true;
        for (int c = 0; c < size; c++) {
            if (grid[row][c] != piece) { rowWin = false; break; }
        }
        if (rowWin) return true;

        // Check column
        boolean colWin = true;
        for (int r = 0; r < size; r++) {
            if (grid[r][col] != piece) { colWin = false; break; }
        }
        if (colWin) return true;

        // Check main diagonal
        if (row == col) {
            boolean diagWin = true;
            for (int i = 0; i < size; i++) {
                if (grid[i][i] != piece) { diagWin = false; break; }
            }
            if (diagWin) return true;
        }

        // Check anti-diagonal
        if (row + col == size - 1) {
            boolean antiDiagWin = true;
            for (int i = 0; i < size; i++) {
                if (grid[i][size - 1 - i] != piece) { antiDiagWin = false; break; }
            }
            if (antiDiagWin) return true;
        }

        return false;
    }

    public boolean isFull() { return filledCells == size * size; }

    public void display() {
        for (int r = 0; r < size; r++) {
            for (int c = 0; c < size; c++) {
                System.out.print(grid[r][c] == null ? "." : grid[r][c]);
                if (c < size - 1) System.out.print(" | ");
            }
            System.out.println();
            if (r < size - 1) System.out.println("---------");
        }
    }

    public int getSize() { return size; }
}

// ── Game ──
public class Game {
    private Board board;
    private Player[] players;
    private int currentTurnIndex;
    private GameStatus status;
    private Player winner;

    public Game(Player p1, Player p2, int boardSize) {
        this.board = new Board(boardSize);
        this.players = new Player[] { p1, p2 };
        this.currentTurnIndex = 0;
        this.status = GameStatus.IN_PROGRESS;
    }

    public boolean makeMove(int row, int col) {
        if (status != GameStatus.IN_PROGRESS) {
            System.out.println("Game is already over");
            return false;
        }

        Player current = players[currentTurnIndex];
        if (!board.placePiece(row, col, current.getPiece())) {
            System.out.println("Invalid move. Try again.");
            return false;
        }

        if (board.checkWin(row, col, current.getPiece())) {
            status = GameStatus.WIN;
            winner = current;
            System.out.println(current.getName() + " wins!");
        } else if (board.isFull()) {
            status = GameStatus.DRAW;
            System.out.println("It's a draw!");
        } else {
            currentTurnIndex = (currentTurnIndex + 1) % 2;
        }
        return true;
    }

    public Board getBoard() { return board; }
    public Player getCurrentPlayer() { return players[currentTurnIndex]; }
    public GameStatus getStatus() { return status; }
    public Player getWinner() { return winner; }
}
```

### 6.5 Service Layer

```java
public class TicTacToeService {
    private Game game;

    public void startGame(String player1Name, String player2Name) {
        Player p1 = new Player(player1Name, Piece.X);
        Player p2 = new Player(player2Name, Piece.O);
        game = new Game(p1, p2, 3);
        System.out.println("Game started! " + p1.getName() + " (X) vs " + p2.getName() + " (O)");
    }

    public boolean playTurn(int row, int col) {
        boolean valid = game.makeMove(row, col);
        if (valid) game.getBoard().display();
        return valid;
    }

    public boolean isGameOver() {
        return game.getStatus() != GameStatus.IN_PROGRESS;
    }

    public String getResult() {
        return switch (game.getStatus()) {
            case WIN -> game.getWinner().getName() + " wins!";
            case DRAW -> "Draw!";
            default -> "Game in progress";
        };
    }
}
```

### 6.6 Driver Code (main)
```java
public class TicTacToeDemo {
    public static void main(String[] args) {
        TicTacToeService service = new TicTacToeService();
        service.startGame("Alice", "Bob");

        service.playTurn(0, 0); // Alice: X at (0,0)
        service.playTurn(1, 1); // Bob:   O at (1,1)
        service.playTurn(0, 1); // Alice: X at (0,1)
        service.playTurn(2, 2); // Bob:   O at (2,2)
        service.playTurn(0, 2); // Alice: X at (0,2) → top row complete!
        // "Alice wins!"

        System.out.println(service.getResult());
    }
}
```

---

## 7 · Traffic Signal System 🟢
**Pattern: State**

### 7.1 Functional Requirements
1. Multiple roads at an intersection
2. Each signal cycles: Green → Yellow → Red
3. Configurable duration per state
4. Emergency override to force green on one road

### 7.2 Entities
- `Intersection` — groups multiple traffic signals
- `Road` — named road at the intersection
- `TrafficSignal` — links a road to its current state, supports transitions
- `SignalState` — interface (Green/Yellow/Red); knows next state + duration
- `TrafficSignalService` — orchestrates cycle + emergency override

### 7.3 Class Diagram
```
TrafficSignal - currentState, road
SignalState (interface) → GreenState, YellowState, RedState
 + next(), getDuration(), getColor()
SignalController - signals[], timer
```

### 7.4 Java Code

```java
// ── Signal State (State Pattern) ──
public interface SignalState {
    SignalState next();
    int getDurationSeconds();
    String getColor();
}

public class GreenState implements SignalState {
    private int duration;
    public GreenState(int durationSeconds) { this.duration = durationSeconds; }

    @Override public SignalState next() { return new YellowState(5); }
    @Override public int getDurationSeconds() { return duration; }
    @Override public String getColor() { return "GREEN"; }
}

public class YellowState implements SignalState {
    private int duration;
    public YellowState(int durationSeconds) { this.duration = durationSeconds; }

    @Override public SignalState next() { return new RedState(30); }
    @Override public int getDurationSeconds() { return duration; }
    @Override public String getColor() { return "YELLOW"; }
}

public class RedState implements SignalState {
    private int duration;
    public RedState(int durationSeconds) { this.duration = durationSeconds; }

    @Override public SignalState next() { return new GreenState(30); }
    @Override public int getDurationSeconds() { return duration; }
    @Override public String getColor() { return "RED"; }
}

// ── Road ──
public class Road {
    private String name;
    public Road(String name) { this.name = name; }
    public String getName() { return name; }
}

// ── TrafficSignal ──
public class TrafficSignal {
    private Road road;
    private SignalState currentState;

    public TrafficSignal(Road road, SignalState initialState) {
        this.road = road;
        this.currentState = initialState;
    }

    public void transition() {
        currentState = currentState.next();
        System.out.println(road.getName() + " → " + currentState.getColor());
    }

    public void forceState(SignalState state) {
        this.currentState = state;
        System.out.println("EMERGENCY: " + road.getName() + " forced to " + state.getColor());
    }

    public Road getRoad() { return road; }
    public SignalState getCurrentState() { return currentState; }
}

// ── Intersection ──
public class Intersection {
    private String name;
    private List<TrafficSignal> signals;

    public Intersection(String name) {
        this.name = name;
        this.signals = new ArrayList<>();
    }

    public void addSignal(TrafficSignal signal) { signals.add(signal); }
    public List<TrafficSignal> getSignals() { return signals; }
    public String getName() { return name; }
}
```

### 7.5 Service Layer

```java
public class TrafficSignalService {
    private Intersection intersection;

    public TrafficSignalService(Intersection intersection) {
        this.intersection = intersection;
    }

    public void startCycle() {
        // In production this would use a ScheduledExecutorService
        for (TrafficSignal signal : intersection.getSignals()) {
            signal.transition();
        }
    }

    public void emergencyOverride(String roadName) {
        for (TrafficSignal signal : intersection.getSignals()) {
            if (signal.getRoad().getName().equals(roadName)) {
                signal.forceState(new GreenState(60));
            } else {
                signal.forceState(new RedState(60));
            }
        }
    }

    public void displayStatus() {
        System.out.println("Intersection: " + intersection.getName());
        for (TrafficSignal signal : intersection.getSignals()) {
            System.out.println("  " + signal.getRoad().getName() +
                    ": " + signal.getCurrentState().getColor());
        }
    }
}
```

### 7.6 Driver Code (main)
```java
public class TrafficSignalDemo {
    public static void main(String[] args) {
        Intersection intersection = new Intersection("Main St & 5th Ave");
        intersection.addSignal(new TrafficSignal(new Road("Main St"), new GreenState(30)));
        intersection.addSignal(new TrafficSignal(new Road("5th Ave"), new RedState(30)));

        TrafficSignalService controller = new TrafficSignalService(intersection);

        controller.displayStatus();
        // Main St: GREEN, 5th Ave: RED

        controller.startCycle();  // transitions all signals
        controller.displayStatus();
        // Main St: YELLOW, 5th Ave: GREEN

        // Emergency override
        controller.emergencyOverride("5th Ave");
        // 5th Ave: GREEN(60s), Main St: RED(60s)
        controller.displayStatus();
    }
}
```

---

# MEDIUM PROBLEMS (8-23)

---

## 8 · Elevator System 🟡
**Pattern: Strategy, State, Observer**

### 8.1 Functional Requirements
1. Building with N floors and M elevators
2. Requests from floor (up/down) or inside (go to floor)
3. Nearest elevator assignment (scheduling strategy)
4. Elevator states: Moving, Idle, DoorOpen
5. Direction-aware: serve requests in current direction first (SCAN)

### 8.2 Entities
- `Building` — holds N elevators + scheduling strategy
- `Elevator` — tracks floor, direction, stops (TreeSets); notifies observers
- `Request` — source floor + destination floor + direction
- `ElevatorScheduler` — strategy interface to pick which elevator serves a request
- `ElevatorObserver` — notified on floor changes (e.g., FloorDisplay)

### 8.3 Class Diagram
```
Building - elevators[], scheduler
Elevator - id, currentFloor, direction, state, requests(TreeSet)
Request - sourceFloor, destinationFloor, direction
Scheduler (Strategy) → NearestElevatorScheduler, ZoneBasedScheduler
ElevatorState → MovingState, IdleState, DoorOpenState
```

### 8.4 Java Code

```java
// ── Enums ──
public enum Direction { UP, DOWN, IDLE }
public enum ElevatorStatus { MOVING, IDLE, DOOR_OPEN }

// ── Request ──
public class Request {
    private int sourceFloor;
    private int destinationFloor;
    private Direction direction;

    public Request(int sourceFloor, int destinationFloor) {
        this.sourceFloor = sourceFloor;
        this.destinationFloor = destinationFloor;
        this.direction = destinationFloor > sourceFloor ? Direction.UP : Direction.DOWN;
    }

    public int getSourceFloor() { return sourceFloor; }
    public int getDestinationFloor() { return destinationFloor; }
    public Direction getDirection() { return direction; }
}

// ── Display Observer ──
public interface ElevatorObserver {
    void onFloorChange(int elevatorId, int floor, Direction direction);
}

public class FloorDisplay implements ElevatorObserver {
    private int floorNumber;

    public FloorDisplay(int floorNumber) { this.floorNumber = floorNumber; }

    @Override
    public void onFloorChange(int elevatorId, int floor, Direction direction) {
        if (floor == floorNumber) {
            System.out.println("Floor " + floorNumber + " display: Elevator " +
                    elevatorId + " arrived, going " + direction);
        }
    }
}

// ── Elevator ──
public class Elevator {
    private int id;
    private int currentFloor;
    private Direction direction;
    private ElevatorStatus status;
    private TreeSet<Integer> upStops;
    private TreeSet<Integer> downStops;
    private List<ElevatorObserver> observers;

    public Elevator(int id) {
        this.id = id;
        this.currentFloor = 0;
        this.direction = Direction.IDLE;
        this.status = ElevatorStatus.IDLE;
        this.upStops = new TreeSet<>();
        this.downStops = new TreeSet<>(Collections.reverseOrder());
        this.observers = new ArrayList<>();
    }

    public void addObserver(ElevatorObserver o) { observers.add(o); }

    public void addRequest(int floor) {
        if (floor > currentFloor) upStops.add(floor);
        else if (floor < currentFloor) downStops.add(floor);
    }

    public void move() {
        if (direction == Direction.UP || direction == Direction.IDLE) {
            if (!upStops.isEmpty()) {
                direction = Direction.UP;
                currentFloor = upStops.pollFirst();
                status = ElevatorStatus.DOOR_OPEN;
                notifyObservers();
                System.out.println("Elevator " + id + " stopped at floor " + currentFloor);
            } else if (!downStops.isEmpty()) {
                direction = Direction.DOWN;
                currentFloor = downStops.pollFirst();
                status = ElevatorStatus.DOOR_OPEN;
                notifyObservers();
            } else {
                direction = Direction.IDLE;
                status = ElevatorStatus.IDLE;
            }
        } else { // DOWN
            if (!downStops.isEmpty()) {
                currentFloor = downStops.pollFirst();
                status = ElevatorStatus.DOOR_OPEN;
                notifyObservers();
            } else if (!upStops.isEmpty()) {
                direction = Direction.UP;
                currentFloor = upStops.pollFirst();
                status = ElevatorStatus.DOOR_OPEN;
                notifyObservers();
            } else {
                direction = Direction.IDLE;
                status = ElevatorStatus.IDLE;
            }
        }
    }

    private void notifyObservers() {
        observers.forEach(o -> o.onFloorChange(id, currentFloor, direction));
    }

    public boolean hasStops() { return !upStops.isEmpty() || !downStops.isEmpty(); }
    public int getId() { return id; }
    public int getCurrentFloor() { return currentFloor; }
    public Direction getDirection() { return direction; }
    public ElevatorStatus getStatus() { return status; }
}

// ── Scheduler (Strategy) ──
public interface ElevatorScheduler {
    Elevator assignElevator(List<Elevator> elevators, Request request);
}

public class NearestElevatorScheduler implements ElevatorScheduler {
    @Override
    public Elevator assignElevator(List<Elevator> elevators, Request request) {
        return elevators.stream()
                .min(Comparator.comparingInt(e ->
                        Math.abs(e.getCurrentFloor() - request.getSourceFloor())))
                .orElseThrow(() -> new RuntimeException("No elevator available"));
    }
}

// ── Building ──
public class Building {
    private List<Elevator> elevators;
    private ElevatorScheduler scheduler;
    private int totalFloors;

    public Building(int totalFloors, int numElevators, ElevatorScheduler scheduler) {
        this.totalFloors = totalFloors;
        this.scheduler = scheduler;
        this.elevators = new ArrayList<>();
        for (int i = 0; i < numElevators; i++) {
            elevators.add(new Elevator(i));
        }
    }

    public List<Elevator> getElevators() { return elevators; }
    public ElevatorScheduler getScheduler() { return scheduler; }
}
```

### 8.5 Service Layer

```java
public class ElevatorService {
    private final Building building;

    public ElevatorService(Building building) {
        this.building = building;
    }

    public void requestElevator(int sourceFloor, int destinationFloor) {
        Request request = new Request(sourceFloor, destinationFloor);
        Elevator elevator = building.getScheduler().assignElevator(
                building.getElevators(), request);

        elevator.addRequest(sourceFloor);
        elevator.addRequest(destinationFloor);
        System.out.println("Elevator " + elevator.getId() +
                " assigned for floor " + sourceFloor + " → " + destinationFloor);
    }

    public void step() {
        // Move all elevators one step
        for (Elevator e : building.getElevators()) {
            if (e.hasStops()) e.move();
        }
    }

    public void displayStatus() {
        for (Elevator e : building.getElevators()) {
            System.out.println("Elevator " + e.getId() + ": Floor " +
                    e.getCurrentFloor() + " | " + e.getDirection() + " | " + e.getStatus());
        }
    }
}
```

### 8.6 Driver Code (main)
```java
public class ElevatorDemo {
    public static void main(String[] args) {
        Building building = new Building(10, 3, new NearestElevatorScheduler());
        ElevatorService service = new ElevatorService(building);

        // User on floor 3 wants floor 7
        service.requestElevator(3, 7);
        // "Elevator 0 assigned for floor 3 → 7"

        // Another user: floor 5 → floor 1
        service.requestElevator(5, 1);

        // Step through movements
        service.step(); // Elevator 0 moves to floor 3
        service.step(); // Elevator 0 moves to floor 7
        service.step(); // Elevator 1 moves to floor 5

        service.displayStatus();
        // Elevator 0: Floor 7 | IDLE
        // Elevator 1: Floor 5 | DOWN
        // Elevator 2: Floor 0 | IDLE
    }
}
```

---

## 9 · In-Memory Key-Value Store 🟡
**Pattern: Singleton**

### 9.1 Functional Requirements
1. GET, PUT, DELETE operations
2. Optional TTL (time-to-live) per key
3. Thread-safe reads/writes
4. Snapshot support for persistence

### 9.2 Entities
- `KVStore` — singleton, thread-safe ConcurrentHashMap-based store
- `Entry` — key, value, createdAt, TTL; knows if it's expired
- `ExpiryManager` — ScheduledExecutorService that evicts expired keys periodically

### 9.3 Class Diagram
```
KVStore (Singleton)
 - ConcurrentHashMap<String, Entry> store
 - ExpiryManager

Entry - key, value, createdAt, ttlMs
ExpiryManager - ScheduledExecutorService
```

### 9.4 Java Code

```java
// ── Entry ──
public class Entry {
    private String key;
    private String value;
    private long createdAt;  // epoch ms
    private long ttlMs;      // 0 means no expiry

    public Entry(String key, String value, long ttlMs) {
        this.key = key;
        this.value = value;
        this.createdAt = System.currentTimeMillis();
        this.ttlMs = ttlMs;
    }

    public boolean isExpired() {
        if (ttlMs <= 0) return false;
        return System.currentTimeMillis() - createdAt > ttlMs;
    }

    public String getKey() { return key; }
    public String getValue() { return value; }
    public void setValue(String value) { this.value = value; this.createdAt = System.currentTimeMillis(); }
}

// ── KVStore (Singleton, Thread-Safe) ──
public class KVStore {
    private static volatile KVStore instance;
    private final ConcurrentHashMap<String, Entry> store;
    private final ScheduledExecutorService expiryService;

    private KVStore() {
        this.store = new ConcurrentHashMap<>();
        this.expiryService = Executors.newSingleThreadScheduledExecutor();
        // Eviction runs every second
        expiryService.scheduleAtFixedRate(this::evictExpired, 1, 1, TimeUnit.SECONDS);
    }

    public static KVStore getInstance() {
        if (instance == null) {
            synchronized (KVStore.class) {
                if (instance == null) instance = new KVStore();
            }
        }
        return instance;
    }

    public void put(String key, String value) {
        put(key, value, 0);
    }

    public void put(String key, String value, long ttlMs) {
        store.put(key, new Entry(key, value, ttlMs));
    }

    public String get(String key) {
        Entry entry = store.get(key);
        if (entry == null) return null;
        if (entry.isExpired()) {
            store.remove(key);
            return null;
        }
        return entry.getValue();
    }

    public boolean delete(String key) {
        return store.remove(key) != null;
    }

    public boolean exists(String key) {
        return get(key) != null;
    }

    private void evictExpired() {
        store.entrySet().removeIf(e -> e.getValue().isExpired());
    }

    public Map<String, String> snapshot() {
        Map<String, String> snap = new HashMap<>();
        store.forEach((k, v) -> {
            if (!v.isExpired()) snap.put(k, v.getValue());
        });
        return Collections.unmodifiableMap(snap);
    }

    public int size() {
        return (int) store.values().stream().filter(e -> !e.isExpired()).count();
    }
}
```

### 9.5 Service Layer

```java
public class KVStoreService {
    private final KVStore store;

    public KVStoreService() {
        this.store = KVStore.getInstance();
    }

    public void set(String key, String value) {
        store.put(key, value);
    }

    public void setWithTTL(String key, String value, long ttlMs) {
        store.put(key, value, ttlMs);
    }

    public String get(String key) {
        String val = store.get(key);
        if (val == null) {
            System.out.println("Key not found or expired: " + key);
        }
        return val;
    }

    public boolean delete(String key) {
        return store.delete(key);
    }

    public Map<String, String> takeSnapshot() {
        return store.snapshot();
    }
}
```

### 9.6 Driver Code (main)
```java
public class KVStoreDemo {
    public static void main(String[] args) throws InterruptedException {
        KVStoreService service = new KVStoreService();

        // Basic CRUD
        service.set("user:1", "Alice");
        service.set("user:2", "Bob");
        System.out.println(service.get("user:1")); // "Alice"
        service.delete("user:2");

        // TTL: key expires after 2 seconds
        service.setWithTTL("session:abc", "token-xyz", 2000);
        System.out.println(service.get("session:abc")); // "token-xyz"
        Thread.sleep(3000);
        System.out.println(service.get("session:abc")); // null (expired)

        // Snapshot
        Map<String, String> snap = service.takeSnapshot();
        System.out.println("Snapshot: " + snap); // {user:1=Alice}
    }
}
```

---

## 10 · LRU Cache 🟡
**Pattern: Data Structure (HashMap + Doubly Linked List)**

### 10.1 Functional Requirements
1. Fixed capacity cache
2. GET returns value and marks as recently used
3. PUT inserts; evicts least-recently-used if full
4. O(1) for both GET and PUT

### 10.2 Entities
- `LRUCache<K,V>` — fixed-capacity cache, O(1) get/put via HashMap + DLL
- `DoublyLinkedList<K,V>` — maintains access order; dummy head/tail for clean ops
- `Node<K,V>` — holds key, value, prev, next pointers

### 10.3 Class Diagram
```
LRUCache<K,V>
 - capacity, map(HashMap<K, Node>), dll

Node<K,V>
 - key, value, prev, next

DoublyLinkedList<K,V>
 - head(dummy), tail(dummy)
 + addToFront, remove, removeLast
```

### 10.4 Java Code

```java
// ── Node ──
public class Node<K, V> {
    K key;
    V value;
    Node<K, V> prev;
    Node<K, V> next;

    public Node(K key, V value) {
        this.key = key;
        this.value = value;
    }
}

// ── Doubly Linked List ──
public class DoublyLinkedList<K, V> {
    private Node<K, V> head; // dummy head
    private Node<K, V> tail; // dummy tail

    public DoublyLinkedList() {
        head = new Node<>(null, null);
        tail = new Node<>(null, null);
        head.next = tail;
        tail.prev = head;
    }

    public void addToFront(Node<K, V> node) {
        node.next = head.next;
        node.prev = head;
        head.next.prev = node;
        head.next = node;
    }

    public void remove(Node<K, V> node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    public Node<K, V> removeLast() {
        if (tail.prev == head) return null; // empty
        Node<K, V> last = tail.prev;
        remove(last);
        return last;
    }

    public void moveToFront(Node<K, V> node) {
        remove(node);
        addToFront(node);
    }
}

// ── LRU Cache ──
public class LRUCache<K, V> {
    private final int capacity;
    private final Map<K, Node<K, V>> map;
    private final DoublyLinkedList<K, V> dll;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.map = new HashMap<>();
        this.dll = new DoublyLinkedList<>();
    }

    public V get(K key) {
        Node<K, V> node = map.get(key);
        if (node == null) return null;
        dll.moveToFront(node);
        return node.value;
    }

    public void put(K key, V value) {
        if (map.containsKey(key)) {
            Node<K, V> node = map.get(key);
            node.value = value;
            dll.moveToFront(node);
        } else {
            if (map.size() == capacity) {
                Node<K, V> evicted = dll.removeLast();
                if (evicted != null) map.remove(evicted.key);
            }
            Node<K, V> newNode = new Node<>(key, value);
            dll.addToFront(newNode);
            map.put(key, newNode);
        }
    }

    public void remove(K key) {
        Node<K, V> node = map.remove(key);
        if (node != null) dll.remove(node);
    }

    public int size() { return map.size(); }
}
```

### 10.5 Service Layer

```java
public class CacheService {
    private final LRUCache<String, Object> cache;

    public CacheService(int capacity) {
        this.cache = new LRUCache<>(capacity);
    }

    public void cacheResult(String key, Object value) {
        cache.put(key, value);
    }

    public Object getCached(String key) {
        Object val = cache.get(key);
        if (val == null) {
            System.out.println("Cache miss: " + key);
        } else {
            System.out.println("Cache hit: " + key);
        }
        return val;
    }

    public void invalidate(String key) {
        cache.remove(key);
    }

    // Example: caching database query results
    public Object getUser(String userId) {
        Object cached = cache.get("user:" + userId);
        if (cached != null) return cached;

        // Simulate DB call
        Object user = "User_" + userId; // would be a real DB call
        cache.put("user:" + userId, user);
        return user;
    }
}
```

### 10.6 Driver Code (main)
```java
public class LRUCacheDemo {
    public static void main(String[] args) {
        LRUCache<String, String> cache = new LRUCache<>(3);

        cache.put("a", "1");  // DLL: [a]
        cache.put("b", "2");  // DLL: [b, a]
        cache.put("c", "3");  // DLL: [c, b, a]

        cache.get("a");       // access "a" → DLL: [a, c, b]

        cache.put("d", "4");  // full → evicts LRU "b" → DLL: [d, a, c]

        System.out.println(cache.get("b")); // null (evicted)
        System.out.println(cache.get("a")); // "1" (still here)
        System.out.println(cache.get("c")); // "3" (still here)

        // Update existing
        cache.put("c", "33"); // DLL: [c, a, d]
        System.out.println(cache.get("c")); // "33"
    }
}
```

---

## 11 · Movie Ticket Booking (BookMyShow) 🟡
**Pattern: Strategy, Observer**

### 11.1 Functional Requirements
1. Browse movies by city, theatre, showtime
2. View seat map; select seats (Regular, Premium, VIP)
3. Lock seats during booking (5-min timeout)
4. Payment via Strategy; confirm or release seats
5. Notification on booking confirmation

### 11.2 Entities
- `Movie` — id, title, genre, duration
- `Theatre` — name, city, screens, shows
- `Screen` — list of physical seats
- `Show` — links movie + screen + time; holds seat status map (synchronized locking)
- `Seat` — id, type (Regular/Premium/VIP), row, number, price
- `Booking` — user + show + selected seats + status (PENDING/CONFIRMED/CANCELLED)
- `PaymentStrategy` — interface (Card/UPI); processes payment amount

### 11.3 Class Diagram
```
Movie - id, title, genre, duration
Theatre - id, name, city, screens[]
Screen - id, seats[]
Show - id, movie, screen, startTime, seatStatus(Map<Seat, SeatStatus>)
Seat - id, type, row, number
Booking - id, user, show, seats[], status, payment
PaymentStrategy → CardPayment, UPIPayment
```

### 11.4 Java Code

```java
// ── Enums ──
public enum SeatType { REGULAR, PREMIUM, VIP }
public enum SeatStatus { AVAILABLE, LOCKED, BOOKED }
public enum BookingStatus { PENDING, CONFIRMED, CANCELLED }

// ── Movie ──
public class Movie {
    private String id;
    private String title;
    private String genre;
    private int durationMinutes;

    public Movie(String id, String title, String genre, int durationMinutes) {
        this.id = id; this.title = title;
        this.genre = genre; this.durationMinutes = durationMinutes;
    }

    public String getId() { return id; }
    public String getTitle() { return title; }
    public String getGenre() { return genre; }
}

// ── Seat ──
public class Seat {
    private String id;
    private SeatType type;
    private String row;
    private int number;
    private double price;

    public Seat(String id, SeatType type, String row, int number, double price) {
        this.id = id; this.type = type;
        this.row = row; this.number = number; this.price = price;
    }

    public String getId() { return id; }
    public SeatType getType() { return type; }
    public double getPrice() { return price; }
}

// ── Screen ──
public class Screen {
    private String id;
    private List<Seat> seats;

    public Screen(String id, List<Seat> seats) {
        this.id = id; this.seats = seats;
    }

    public String getId() { return id; }
    public List<Seat> getSeats() { return seats; }
}

// ── Show ──
public class Show {
    private String id;
    private Movie movie;
    private Screen screen;
    private LocalDateTime startTime;
    private Map<String, SeatStatus> seatStatusMap; // seatId -> status

    public Show(String id, Movie movie, Screen screen, LocalDateTime startTime) {
        this.id = id; this.movie = movie;
        this.screen = screen; this.startTime = startTime;
        this.seatStatusMap = new ConcurrentHashMap<>();
        screen.getSeats().forEach(s -> seatStatusMap.put(s.getId(), SeatStatus.AVAILABLE));
    }

    public synchronized boolean lockSeats(List<Seat> seats) {
        for (Seat s : seats) {
            if (seatStatusMap.get(s.getId()) != SeatStatus.AVAILABLE) return false;
        }
        seats.forEach(s -> seatStatusMap.put(s.getId(), SeatStatus.LOCKED));
        return true;
    }

    public void bookSeats(List<Seat> seats) {
        seats.forEach(s -> seatStatusMap.put(s.getId(), SeatStatus.BOOKED));
    }

    public void releaseSeats(List<Seat> seats) {
        seats.forEach(s -> seatStatusMap.put(s.getId(), SeatStatus.AVAILABLE));
    }

    public List<Seat> getAvailableSeats() {
        return screen.getSeats().stream()
                .filter(s -> seatStatusMap.get(s.getId()) == SeatStatus.AVAILABLE)
                .collect(Collectors.toList());
    }

    public String getId() { return id; }
    public Movie getMovie() { return movie; }
    public Screen getScreen() { return screen; }
    public LocalDateTime getStartTime() { return startTime; }
}

// ── Theatre ──
public class Theatre {
    private String id;
    private String name;
    private String city;
    private List<Screen> screens;
    private List<Show> shows;

    public Theatre(String id, String name, String city) {
        this.id = id; this.name = name; this.city = city;
        this.screens = new ArrayList<>(); this.shows = new ArrayList<>();
    }

    public void addScreen(Screen s) { screens.add(s); }
    public void addShow(Show s) { shows.add(s); }

    public List<Show> getShowsForMovie(String movieId) {
        return shows.stream().filter(s -> s.getMovie().getId().equals(movieId))
                .collect(Collectors.toList());
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public String getCity() { return city; }
}

// ── Payment Strategy ──
public interface PaymentStrategy {
    boolean pay(double amount);
}

public class CardPayment implements PaymentStrategy {
    private String cardNumber;
    public CardPayment(String cardNumber) { this.cardNumber = cardNumber; }

    @Override
    public boolean pay(double amount) {
        System.out.println("Paid ₹" + amount + " via Card ending " +
                cardNumber.substring(cardNumber.length() - 4));
        return true;
    }
}

public class UPIPayment implements PaymentStrategy {
    private String upiId;
    public UPIPayment(String upiId) { this.upiId = upiId; }

    @Override
    public boolean pay(double amount) {
        System.out.println("Paid ₹" + amount + " via UPI: " + upiId);
        return true;
    }
}

// ── Booking ──
public class Booking {
    private String id;
    private User user;
    private Show show;
    private List<Seat> seats;
    private BookingStatus status;
    private double totalAmount;

    public Booking(String id, User user, Show show, List<Seat> seats) {
        this.id = id; this.user = user;
        this.show = show; this.seats = seats;
        this.status = BookingStatus.PENDING;
        this.totalAmount = seats.stream().mapToDouble(Seat::getPrice).sum();
    }

    public void confirm() { this.status = BookingStatus.CONFIRMED; }
    public void cancel() { this.status = BookingStatus.CANCELLED; }

    public String getId() { return id; }
    public double getTotalAmount() { return totalAmount; }
    public BookingStatus getStatus() { return status; }
    public Show getShow() { return show; }
    public List<Seat> getSeats() { return seats; }
    public User getUser() { return user; }
}

// ── User ──
public class User {
    private String id;
    private String name;
    private String email;

    public User(String id, String name, String email) {
        this.id = id; this.name = name; this.email = email;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public String getEmail() { return email; }
}
```

### 11.5 Service Layer

```java
public class BookingService {
    private Map<String, Theatre> theatres = new HashMap<>();
    private Map<String, Booking> bookings = new HashMap<>();
    private ScheduledExecutorService lockTimer = Executors.newScheduledThreadPool(2);

    public Booking initiateBooking(User user, Show show, List<Seat> seats) {
        if (!show.lockSeats(seats)) {
            throw new RuntimeException("Some seats are no longer available");
        }

        Booking booking = new Booking(UUID.randomUUID().toString(), user, show, seats);
        bookings.put(booking.getId(), booking);

        // Auto-release after 5 min if not confirmed
        lockTimer.schedule(() -> {
            if (booking.getStatus() == BookingStatus.PENDING) {
                show.releaseSeats(seats);
                booking.cancel();
                System.out.println("Booking " + booking.getId() + " expired. Seats released.");
            }
        }, 5, TimeUnit.MINUTES);

        return booking;
    }

    public void confirmBooking(String bookingId, PaymentStrategy paymentStrategy) {
        Booking booking = bookings.get(bookingId);
        if (booking.getStatus() != BookingStatus.PENDING) {
            throw new RuntimeException("Booking is not in pending state");
        }

        boolean paid = paymentStrategy.pay(booking.getTotalAmount());
        if (paid) {
            booking.confirm();
            booking.getShow().bookSeats(booking.getSeats());
            System.out.println("Booking confirmed: " + bookingId);
            // Notify user
            System.out.println("Notification sent to " + booking.getUser().getEmail());
        }
    }

    public void cancelBooking(String bookingId) {
        Booking booking = bookings.get(bookingId);
        booking.cancel();
        booking.getShow().releaseSeats(booking.getSeats());
        System.out.println("Booking cancelled: " + bookingId);
    }

    public List<Show> searchShows(String city, String movieId) {
        return theatres.values().stream()
                .filter(t -> t.getCity().equalsIgnoreCase(city))
                .flatMap(t -> t.getShowsForMovie(movieId).stream())
                .collect(Collectors.toList());
    }
}
```

### 11.6 Driver Code (main)
```java
public class BookMyShowDemo {
    public static void main(String[] args) {
        BookingService service = new BookingService();

        Movie movie = new Movie("M1", "Inception", "Sci-Fi", 148);
        List<Seat> seats = new ArrayList<>();
        for (int i = 1; i <= 50; i++) seats.add(new Seat("S" + i, SeatType.REGULAR, "A", i, 200));
        Screen screen = new Screen("SC1", seats);
        Show show = new Show("SH1", movie, screen, LocalDateTime.of(2026, 3, 20, 18, 0));

        User user = new User("U1", "Alice", "alice@email.com");
        List<Seat> selected = show.getAvailableSeats().subList(0, 2);

        // Lock seats (5 min hold)
        Booking booking = service.initiateBooking(user, show, selected);

        // Confirm with payment
        service.confirmBooking(booking.getId(), new UPIPayment("alice@upi"));
        // → "Paid ₹400 via UPI. Booking confirmed."

        // Cancel another booking
        // service.cancelBooking(bookingId); // releases seats
    }
}
```

---

## 12 · Chess Game 🟡
**Pattern: Strategy/Polymorphism, Factory**

### 12.1 Functional Requirements
1. 8×8 board, 2 players (White/Black)
2. Each piece type has unique movement rules
3. Validate move legality
4. Turn-based play; detect check/checkmate/stalemate
5. Move history

### 12.2 Entities
- `ChessGame` — manages board, players, turns, move history, game status
- `Board` — 8×8 Cell grid; setup, getCell, isPathClear
- `Cell` — (x, y) + current piece (nullable)
- `Piece` — abstract; each subclass (King/Queen/Rook/Bishop/Knight/Pawn) implements canMove()
- `Player` — name + color (WHITE/BLACK)
- `Move` — records player, piece, from, to, captured piece

### 12.3 Class Diagram
```
Piece (abstract) → King, Queen, Rook, Bishop, Knight, Pawn
 + canMove(Board, Cell from, Cell to): boolean
Board - Cell[8][8]
Move - player, piece, from, to, killedPiece
Game - board, players[], moves[], status
```

### 12.4 Java Code

```java
// ── Enums ──
public enum Color { WHITE, BLACK }
public enum ChessGameStatus { ACTIVE, CHECK, CHECKMATE, STALEMATE, RESIGNED }

// ── Cell ──
public class Cell {
    private int x, y;
    private Piece piece;

    public Cell(int x, int y) { this.x = x; this.y = y; }

    public int getX() { return x; }
    public int getY() { return y; }
    public Piece getPiece() { return piece; }
    public void setPiece(Piece piece) { this.piece = piece; }
    public boolean isEmpty() { return piece == null; }
}

// ── Piece (Abstract — Strategy via polymorphism) ──
public abstract class Piece {
    protected Color color;
    protected boolean killed;

    public Piece(Color color) {
        this.color = color;
        this.killed = false;
    }

    public abstract boolean canMove(Board board, Cell from, Cell to);
    public abstract String getSymbol();

    public Color getColor() { return color; }
    public boolean isKilled() { return killed; }
    public void setKilled(boolean killed) { this.killed = killed; }
}

// ── Piece Implementations ──
public class King extends Piece {
    public King(Color color) { super(color); }

    @Override
    public boolean canMove(Board board, Cell from, Cell to) {
        int dx = Math.abs(from.getX() - to.getX());
        int dy = Math.abs(from.getY() - to.getY());
        if (to.getPiece() != null && to.getPiece().getColor() == this.color) return false;
        return dx <= 1 && dy <= 1 && (dx + dy > 0);
    }

    @Override public String getSymbol() { return color == Color.WHITE ? "♔" : "♚"; }
}

public class Queen extends Piece {
    public Queen(Color color) { super(color); }

    @Override
    public boolean canMove(Board board, Cell from, Cell to) {
        if (to.getPiece() != null && to.getPiece().getColor() == this.color) return false;
        int dx = Math.abs(from.getX() - to.getX());
        int dy = Math.abs(from.getY() - to.getY());
        // Rook-like or Bishop-like
        boolean straight = from.getX() == to.getX() || from.getY() == to.getY();
        boolean diagonal = dx == dy;
        if (!straight && !diagonal) return false;
        return board.isPathClear(from, to);
    }

    @Override public String getSymbol() { return color == Color.WHITE ? "♕" : "♛"; }
}

public class Rook extends Piece {
    public Rook(Color color) { super(color); }

    @Override
    public boolean canMove(Board board, Cell from, Cell to) {
        if (to.getPiece() != null && to.getPiece().getColor() == this.color) return false;
        if (from.getX() != to.getX() && from.getY() != to.getY()) return false;
        return board.isPathClear(from, to);
    }

    @Override public String getSymbol() { return color == Color.WHITE ? "♖" : "♜"; }
}

public class Bishop extends Piece {
    public Bishop(Color color) { super(color); }

    @Override
    public boolean canMove(Board board, Cell from, Cell to) {
        if (to.getPiece() != null && to.getPiece().getColor() == this.color) return false;
        int dx = Math.abs(from.getX() - to.getX());
        int dy = Math.abs(from.getY() - to.getY());
        if (dx != dy || dx == 0) return false;
        return board.isPathClear(from, to);
    }

    @Override public String getSymbol() { return color == Color.WHITE ? "♗" : "♝"; }
}

public class Knight extends Piece {
    public Knight(Color color) { super(color); }

    @Override
    public boolean canMove(Board board, Cell from, Cell to) {
        if (to.getPiece() != null && to.getPiece().getColor() == this.color) return false;
        int dx = Math.abs(from.getX() - to.getX());
        int dy = Math.abs(from.getY() - to.getY());
        return (dx == 2 && dy == 1) || (dx == 1 && dy == 2);
    }

    @Override public String getSymbol() { return color == Color.WHITE ? "♘" : "♞"; }
}

public class Pawn extends Piece {
    public Pawn(Color color) { super(color); }

    @Override
    public boolean canMove(Board board, Cell from, Cell to) {
        int direction = color == Color.WHITE ? -1 : 1;
        int dx = to.getX() - from.getX();
        int dy = to.getY() - from.getY();

        // Move forward one
        if (dy == 0 && dx == direction && to.isEmpty()) return true;

        // Move forward two from starting position
        int startRow = color == Color.WHITE ? 6 : 1;
        if (dy == 0 && dx == 2 * direction && from.getX() == startRow
                && to.isEmpty() && board.getCell(from.getX() + direction, from.getY()).isEmpty())
            return true;

        // Capture diagonally
        if (Math.abs(dy) == 1 && dx == direction
                && to.getPiece() != null && to.getPiece().getColor() != this.color)
            return true;

        return false;
    }

    @Override public String getSymbol() { return color == Color.WHITE ? "♙" : "♟"; }
}

// ── Board ──
public class Board {
    private Cell[][] grid;

    public Board() {
        grid = new Cell[8][8];
        for (int i = 0; i < 8; i++)
            for (int j = 0; j < 8; j++)
                grid[i][j] = new Cell(i, j);
    }

    public void setupStandardGame() {
        // Black pieces
        grid[0][0].setPiece(new Rook(Color.BLACK));
        grid[0][1].setPiece(new Knight(Color.BLACK));
        grid[0][2].setPiece(new Bishop(Color.BLACK));
        grid[0][3].setPiece(new Queen(Color.BLACK));
        grid[0][4].setPiece(new King(Color.BLACK));
        grid[0][5].setPiece(new Bishop(Color.BLACK));
        grid[0][6].setPiece(new Knight(Color.BLACK));
        grid[0][7].setPiece(new Rook(Color.BLACK));
        for (int i = 0; i < 8; i++) grid[1][i].setPiece(new Pawn(Color.BLACK));

        // White pieces
        grid[7][0].setPiece(new Rook(Color.WHITE));
        grid[7][1].setPiece(new Knight(Color.WHITE));
        grid[7][2].setPiece(new Bishop(Color.WHITE));
        grid[7][3].setPiece(new Queen(Color.WHITE));
        grid[7][4].setPiece(new King(Color.WHITE));
        grid[7][5].setPiece(new Bishop(Color.WHITE));
        grid[7][6].setPiece(new Knight(Color.WHITE));
        grid[7][7].setPiece(new Rook(Color.WHITE));
        for (int i = 0; i < 8; i++) grid[6][i].setPiece(new Pawn(Color.WHITE));
    }

    public Cell getCell(int x, int y) { return grid[x][y]; }

    public boolean isPathClear(Cell from, Cell to) {
        int dx = Integer.signum(to.getX() - from.getX());
        int dy = Integer.signum(to.getY() - from.getY());
        int x = from.getX() + dx, y = from.getY() + dy;
        while (x != to.getX() || y != to.getY()) {
            if (!grid[x][y].isEmpty()) return false;
            x += dx; y += dy;
        }
        return true;
    }
}

// ── Move ──
public class Move {
    private Player player;
    private Piece piece;
    private Cell from;
    private Cell to;
    private Piece killedPiece;

    public Move(Player player, Piece piece, Cell from, Cell to) {
        this.player = player; this.piece = piece;
        this.from = from; this.to = to;
        this.killedPiece = to.getPiece();
    }

    public Piece getKilledPiece() { return killedPiece; }
}

// ── Player ──
public class Player {
    private String name;
    private Color color;

    public Player(String name, Color color) {
        this.name = name; this.color = color;
    }

    public String getName() { return name; }
    public Color getColor() { return color; }
}

// ── Game ──
public class ChessGame {
    private Board board;
    private Player[] players;
    private int currentTurn;
    private List<Move> moveHistory;
    private ChessGameStatus status;

    public ChessGame(Player white, Player black) {
        this.board = new Board();
        board.setupStandardGame();
        this.players = new Player[] { white, black };
        this.currentTurn = 0;
        this.moveHistory = new ArrayList<>();
        this.status = ChessGameStatus.ACTIVE;
    }

    public boolean makeMove(int fromX, int fromY, int toX, int toY) {
        Player current = players[currentTurn];
        Cell from = board.getCell(fromX, fromY);
        Cell to = board.getCell(toX, toY);

        if (from.isEmpty() || from.getPiece().getColor() != current.getColor()) {
            System.out.println("Invalid: no piece or wrong color");
            return false;
        }

        Piece piece = from.getPiece();
        if (!piece.canMove(board, from, to)) {
            System.out.println("Invalid move for " + piece.getSymbol());
            return false;
        }

        Move move = new Move(current, piece, from, to);
        if (to.getPiece() != null) {
            to.getPiece().setKilled(true);
        }
        to.setPiece(piece);
        from.setPiece(null);
        moveHistory.add(move);

        currentTurn = (currentTurn + 1) % 2;
        return true;
    }

    public Board getBoard() { return board; }
    public ChessGameStatus getStatus() { return status; }
}
```

### 12.5 Service Layer

```java
public class ChessGameService {
    private ChessGame game;

    public void startGame(String whiteName, String blackName) {
        Player white = new Player(whiteName, Color.WHITE);
        Player black = new Player(blackName, Color.BLACK);
        game = new ChessGame(white, black);
        System.out.println("Chess game started: " + whiteName + " vs " + blackName);
    }

    public boolean playMove(int fromX, int fromY, int toX, int toY) {
        return game.makeMove(fromX, fromY, toX, toY);
    }

    public ChessGameStatus getStatus() {
        return game.getStatus();
    }
}
```

### 12.6 Driver Code (main)
```java
public class ChessDemo {
    public static void main(String[] args) {
        ChessGameService service = new ChessGameService();
        service.startGame("Alice", "Bob");

        // White (Alice) opens: Knight from (7,1) to (5,2)
        service.playMove(7, 1, 5, 2);
        // Black (Bob): Pawn from (1,4) to (3,4)
        service.playMove(1, 4, 3, 4);
        // White: Bishop from (7,2) to (4,5)
        service.playMove(7, 2, 4, 5);

        // Invalid move attempt
        boolean valid = service.playMove(0, 0, 5, 5); // Rook can't go diagonal
        System.out.println("Valid move: " + valid); // false
    }
}
```

---

## 13 · Snake & Ladder 🟡
**Pattern: Clean OOP**

### 13.1 Functional Requirements
1. N×N board with snakes and ladders
2. 2-4 players roll dice, move forward
3. Snake head → slide down; ladder bottom → climb up
4. First to reach/exceed last cell wins
5. Configurable board

### 13.2 Entities
- `SnakeLadderGame` — runs game loop: roll → move → check snake/ladder → check win
- `Board` — size + snake map (head→tail) + ladder map (bottom→top)
- `Player` — name + current position
- `Dice` — configurable number of dice + faces; returns random roll

### 13.3 Class Diagram
```
Board - size, snakes(Map<Int,Int>), ladders(Map<Int,Int>)
Player - name, position
Dice - numberOfDice, faceCount
Game - board, players(Queue), dice, winner
```

### 13.4 Java Code

```java
// ── Dice ──
public class Dice {
    private int numberOfDice;
    private int faceCount;
    private Random random;

    public Dice(int numberOfDice, int faceCount) {
        this.numberOfDice = numberOfDice;
        this.faceCount = faceCount;
        this.random = new Random();
    }

    public int roll() {
        int total = 0;
        for (int i = 0; i < numberOfDice; i++) {
            total += random.nextInt(faceCount) + 1;
        }
        return total;
    }
}

// ── Player ──
public class Player {
    private String name;
    private int position;

    public Player(String name) {
        this.name = name;
        this.position = 0;
    }

    public String getName() { return name; }
    public int getPosition() { return position; }
    public void setPosition(int position) { this.position = position; }
}

// ── Board ──
public class Board {
    private int size; // total cells = size * size
    private Map<Integer, Integer> snakes;  // head -> tail
    private Map<Integer, Integer> ladders; // bottom -> top

    public Board(int size) {
        this.size = size * size;
        this.snakes = new HashMap<>();
        this.ladders = new HashMap<>();
    }

    public void addSnake(int head, int tail) {
        if (head <= tail) throw new IllegalArgumentException("Snake head must be above tail");
        snakes.put(head, tail);
    }

    public void addLadder(int bottom, int top) {
        if (bottom >= top) throw new IllegalArgumentException("Ladder bottom must be below top");
        ladders.put(bottom, top);
    }

    public int getFinalPosition(int position) {
        if (snakes.containsKey(position)) {
            System.out.println("  Bitten by snake! Slide from " + position + " to " + snakes.get(position));
            return snakes.get(position);
        }
        if (ladders.containsKey(position)) {
            System.out.println("  Climbed ladder! From " + position + " to " + ladders.get(position));
            return ladders.get(position);
        }
        return position;
    }

    public int getSize() { return size; }
}

// ── Game ──
public class SnakeLadderGame {
    private Board board;
    private Queue<Player> players;
    private Dice dice;
    private Player winner;

    public SnakeLadderGame(Board board, List<Player> playerList, Dice dice) {
        this.board = board;
        this.dice = dice;
        this.players = new LinkedList<>(playerList);
    }

    public void play() {
        while (winner == null) {
            Player current = players.poll();
            int rolled = dice.roll();
            int newPos = current.getPosition() + rolled;

            System.out.println(current.getName() + " rolled " + rolled +
                    " | " + current.getPosition() + " → " + newPos);

            if (newPos > board.getSize()) {
                System.out.println("  Exceeds board. Stay at " + current.getPosition());
                players.offer(current);
                continue;
            }

            newPos = board.getFinalPosition(newPos);
            current.setPosition(newPos);

            if (newPos == board.getSize()) {
                winner = current;
                System.out.println(current.getName() + " WINS!");
            } else {
                players.offer(current);
            }
        }
    }

    public Player getWinner() { return winner; }
}
```

### 13.5 Service Layer

```java
public class SnakeLadderService {
    public void startGame() {
        Board board = new Board(10); // 10x10 = 100 cells
        board.addSnake(99, 10);
        board.addSnake(65, 25);
        board.addSnake(45, 6);
        board.addLadder(2, 38);
        board.addLadder(7, 14);
        board.addLadder(28, 84);

        List<Player> playerList = List.of(
                new Player("Alice"),
                new Player("Bob")
        );

        Dice dice = new Dice(1, 6);
        SnakeLadderGame game = new SnakeLadderGame(board, playerList, dice);
        game.play();

        System.out.println("Winner: " + game.getWinner().getName());
    }
}
```

### 13.6 Driver Code (main)
```java
public class SnakeLadderDemo {
    public static void main(String[] args) {
        SnakeLadderService service = new SnakeLadderService();
        service.startGame();
        // Output:
        // Alice rolled 4 | 0 → 4
        // Bob rolled 2 | 0 → 2  "Climbed ladder! From 2 to 38"
        // Alice rolled 6 | 4 → 10
        // ... continues until someone reaches 100
        // "Alice WINS!"
    }
}
```

---

## 14 · ATM Machine 🟡
**Pattern: State, Chain of Responsibility**

### 14.1 Functional Requirements
1. Authenticate via card + PIN
2. Operations: Withdraw, Deposit, Balance Inquiry
3. Cash dispenser: dispense in denominations (500, 200, 100)
4. State transitions: Idle → CardInserted → Authenticated → Transaction → Idle
5. Insufficient funds/cash handling

### 14.2 Entities
- `ATM` — holds current state, card, account, cash dispenser chain
- `Card` — card number + linked account number
- `Account` — balance, PIN validation, withdraw/deposit
- `CashHandler` — chain of responsibility; each denomination dispenses what it can, forwards remainder
- `ATMState` — interface (Idle/CardInserted/Authenticated); handles insertCard/enterPin/withdraw/ejectCard
- `BankService` — simulated bank, stores accounts by account number

### 14.3 Class Diagram
```
ATM - currentState, cashDispenser, bankService
ATMState (interface) → IdleState, CardInsertedState, AuthenticatedState, TransactionState
CashDispenser (Chain of Resp) → FiveHundredHandler → TwoHundredHandler → HundredHandler
Transaction - type, amount, timestamp
```

### 14.4 Java Code

```java
// ── Enums ──
public enum TransactionType { WITHDRAW, DEPOSIT, BALANCE }

// ── Account (simulated bank) ──
public class Account {
    private String accountNumber;
    private double balance;
    private String pin;

    public Account(String accountNumber, double balance, String pin) {
        this.accountNumber = accountNumber;
        this.balance = balance;
        this.pin = pin;
    }

    public boolean validatePin(String pin) { return this.pin.equals(pin); }
    public double getBalance() { return balance; }
    public void withdraw(double amount) { this.balance -= amount; }
    public void deposit(double amount) { this.balance += amount; }
    public String getAccountNumber() { return accountNumber; }
}

// ── Card ──
public class Card {
    private String cardNumber;
    private String accountNumber;

    public Card(String cardNumber, String accountNumber) {
        this.cardNumber = cardNumber;
        this.accountNumber = accountNumber;
    }

    public String getAccountNumber() { return accountNumber; }
}

// ── Cash Dispenser (Chain of Responsibility) ──
public abstract class CashHandler {
    protected CashHandler next;
    protected int denomination;
    protected int count; // available notes

    public CashHandler(int denomination, int count) {
        this.denomination = denomination;
        this.count = count;
    }

    public CashHandler setNext(CashHandler next) {
        this.next = next;
        return next;
    }

    public void dispense(int amount) {
        int notesNeeded = amount / denomination;
        int notesToDispense = Math.min(notesNeeded, count);
        int remainder = amount - notesToDispense * denomination;
        count -= notesToDispense;

        if (notesToDispense > 0) {
            System.out.println("  Dispensing " + notesToDispense + " x ₹" + denomination);
        }
        if (remainder > 0) {
            if (next != null) next.dispense(remainder);
            else System.out.println("  Cannot dispense remaining ₹" + remainder);
        }
    }

    public int getTotalCash() {
        int total = denomination * count;
        if (next != null) total += next.getTotalCash();
        return total;
    }
}

public class FiveHundredHandler extends CashHandler {
    public FiveHundredHandler(int count) { super(500, count); }
}
public class TwoHundredHandler extends CashHandler {
    public TwoHundredHandler(int count) { super(200, count); }
}
public class HundredHandler extends CashHandler {
    public HundredHandler(int count) { super(100, count); }
}

// ── ATM State Machine ──
public interface ATMState {
    void insertCard(ATM atm, Card card);
    void enterPin(ATM atm, String pin);
    void withdraw(ATM atm, int amount);
    void deposit(ATM atm, double amount);
    void checkBalance(ATM atm);
    void ejectCard(ATM atm);
}

public class IdleState implements ATMState {
    @Override
    public void insertCard(ATM atm, Card card) {
        atm.setCurrentCard(card);
        atm.setState(new CardInsertedState());
        System.out.println("Card inserted.");
    }
    @Override public void enterPin(ATM atm, String pin) { System.out.println("Insert card first"); }
    @Override public void withdraw(ATM atm, int amount) { System.out.println("Insert card first"); }
    @Override public void deposit(ATM atm, double amount) { System.out.println("Insert card first"); }
    @Override public void checkBalance(ATM atm) { System.out.println("Insert card first"); }
    @Override public void ejectCard(ATM atm) { System.out.println("No card inserted"); }
}

public class CardInsertedState implements ATMState {
    @Override public void insertCard(ATM atm, Card card) { System.out.println("Card already inserted"); }

    @Override
    public void enterPin(ATM atm, String pin) {
        Account acc = atm.getBankService().getAccount(atm.getCurrentCard().getAccountNumber());
        if (acc != null && acc.validatePin(pin)) {
            atm.setCurrentAccount(acc);
            atm.setState(new AuthenticatedState());
            System.out.println("PIN verified. Choose operation.");
        } else {
            System.out.println("Invalid PIN.");
            atm.ejectCard();
        }
    }

    @Override public void withdraw(ATM atm, int amount) { System.out.println("Enter PIN first"); }
    @Override public void deposit(ATM atm, double amount) { System.out.println("Enter PIN first"); }
    @Override public void checkBalance(ATM atm) { System.out.println("Enter PIN first"); }

    @Override
    public void ejectCard(ATM atm) {
        atm.setCurrentCard(null);
        atm.setState(new IdleState());
        System.out.println("Card ejected.");
    }
}

public class AuthenticatedState implements ATMState {
    @Override public void insertCard(ATM atm, Card card) { System.out.println("Session active"); }
    @Override public void enterPin(ATM atm, String pin) { System.out.println("Already authenticated"); }

    @Override
    public void withdraw(ATM atm, int amount) {
        Account acc = atm.getCurrentAccount();
        if (amount > acc.getBalance()) {
            System.out.println("Insufficient balance");
            return;
        }
        if (amount > atm.getCashDispenser().getTotalCash()) {
            System.out.println("ATM has insufficient cash");
            return;
        }
        if (amount % 100 != 0) {
            System.out.println("Amount must be multiple of 100");
            return;
        }
        acc.withdraw(amount);
        System.out.println("Withdrawing ₹" + amount);
        atm.getCashDispenser().dispense(amount);
        System.out.println("Remaining balance: ₹" + acc.getBalance());
    }

    @Override
    public void deposit(ATM atm, double amount) {
        atm.getCurrentAccount().deposit(amount);
        System.out.println("Deposited ₹" + amount + ". Balance: ₹" + atm.getCurrentAccount().getBalance());
    }

    @Override
    public void checkBalance(ATM atm) {
        System.out.println("Balance: ₹" + atm.getCurrentAccount().getBalance());
    }

    @Override
    public void ejectCard(ATM atm) {
        atm.setCurrentCard(null);
        atm.setCurrentAccount(null);
        atm.setState(new IdleState());
        System.out.println("Card ejected. Thank you.");
    }
}

// ── BankService (simulated) ──
public class BankService {
    private Map<String, Account> accounts = new HashMap<>();

    public void addAccount(Account acc) { accounts.put(acc.getAccountNumber(), acc); }
    public Account getAccount(String accountNumber) { return accounts.get(accountNumber); }
}

// ── ATM ──
public class ATM {
    private ATMState currentState;
    private CashHandler cashDispenser;
    private BankService bankService;
    private Card currentCard;
    private Account currentAccount;

    public ATM(BankService bankService) {
        this.bankService = bankService;
        this.currentState = new IdleState();

        // Setup cash dispenser chain
        CashHandler h500 = new FiveHundredHandler(100);
        CashHandler h200 = new TwoHundredHandler(100);
        CashHandler h100 = new HundredHandler(100);
        h500.setNext(h200).setNext(h100);
        this.cashDispenser = h500;
    }

    public void insertCard(Card card) { currentState.insertCard(this, card); }
    public void enterPin(String pin) { currentState.enterPin(this, pin); }
    public void withdraw(int amount) { currentState.withdraw(this, amount); }
    public void deposit(double amount) { currentState.deposit(this, amount); }
    public void checkBalance() { currentState.checkBalance(this); }
    public void ejectCard() { currentState.ejectCard(this); }

    // Getters/Setters
    public void setState(ATMState s) { this.currentState = s; }
    public Card getCurrentCard() { return currentCard; }
    public void setCurrentCard(Card c) { this.currentCard = c; }
    public Account getCurrentAccount() { return currentAccount; }
    public void setCurrentAccount(Account a) { this.currentAccount = a; }
    public CashHandler getCashDispenser() { return cashDispenser; }
    public BankService getBankService() { return bankService; }
}
```

### 14.5 Service Layer

```java
public class ATMService {
    private final ATM atm;

    public ATMService() {
        BankService bankService = new BankService();
        bankService.addAccount(new Account("ACC001", 50000, "1234"));
        bankService.addAccount(new Account("ACC002", 25000, "5678"));
        this.atm = new ATM(bankService);
    }

    public void performWithdrawal(Card card, String pin, int amount) {
        atm.insertCard(card);
        atm.enterPin(pin);
        atm.withdraw(amount);
        atm.ejectCard();
    }

    public void performDeposit(Card card, String pin, double amount) {
        atm.insertCard(card);
        atm.enterPin(pin);
        atm.deposit(amount);
        atm.ejectCard();
    }

    public void checkBalance(Card card, String pin) {
        atm.insertCard(card);
        atm.enterPin(pin);
        atm.checkBalance();
        atm.ejectCard();
    }
}
```

### 14.6 Driver Code (main)
```java
public class ATMDemo {
    public static void main(String[] args) {
        ATMService service = new ATMService();
        Card card = new Card("CARD-001", "ACC001");

        // Withdrawal: ₹1700 → 3x500 + 1x200
        service.performWithdrawal(card, "1234", 1700);
        // "Card inserted." "PIN verified."
        // "Withdrawing ₹1700" "3 x ₹500" "1 x ₹200"
        // "Remaining balance: ₹48300" "Card ejected."

        // Wrong PIN
        service.performWithdrawal(card, "0000", 500);
        // "Card inserted." "Invalid PIN." "Card ejected."

        // Check balance
        service.checkBalance(card, "1234");
        // "Balance: ₹48300"
    }
}
```

---

## 15 · Online Shopping Cart 🟡
**Pattern: Strategy, Observer**

### 15.1 Functional Requirements
1. Browse products by category, search
2. Add/remove items to cart, update quantity
3. Apply coupon/discount codes
4. Checkout with payment strategy (Card, UPI, Wallet)
5. Order tracking, notifications

### 15.2 Entities
- `Product` — id, name, price, category
- `CartItem` — product + quantity; calculates subtotal
- `Cart` — per-user item map; add/remove/update/getTotal/clear
- `Coupon` — code, discount%, expiry; validates and applies discount
- `Order` — created from cart at checkout; tracks status + notifies observers
- `PaymentStrategy` — interface (Card/UPI/Wallet)
- `OrderObserver` — notified on order status changes

### 15.3 Class Diagram
```
Product - id, name, price, category
CartItem - product, quantity
Cart - userId, items[]
Order - id, user, items[], total, status, payment
PaymentStrategy → CardPayment, UPIPayment, WalletPayment
Coupon - code, discountPercent, validUntil
```

### 15.4 Java Code

```java
// ── Enums ──
public enum OrderStatus { PLACED, CONFIRMED, SHIPPED, DELIVERED, CANCELLED }

// ── Product ──
public class Product {
    private String id;
    private String name;
    private double price;
    private String category;

    public Product(String id, String name, double price, String category) {
        this.id = id; this.name = name;
        this.price = price; this.category = category;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public double getPrice() { return price; }
    public String getCategory() { return category; }
}

// ── CartItem ──
public class CartItem {
    private Product product;
    private int quantity;

    public CartItem(Product product, int quantity) {
        this.product = product; this.quantity = quantity;
    }

    public double getSubtotal() { return product.getPrice() * quantity; }
    public Product getProduct() { return product; }
    public int getQuantity() { return quantity; }
    public void setQuantity(int q) { this.quantity = q; }
}

// ── Cart ──
public class Cart {
    private String userId;
    private Map<String, CartItem> items; // productId -> item

    public Cart(String userId) {
        this.userId = userId;
        this.items = new LinkedHashMap<>();
    }

    public void addItem(Product product, int qty) {
        items.merge(product.getId(),
                new CartItem(product, qty),
                (existing, newItem) -> {
                    existing.setQuantity(existing.getQuantity() + qty);
                    return existing;
                });
    }

    public void removeItem(String productId) {
        items.remove(productId);
    }

    public void updateQuantity(String productId, int qty) {
        CartItem item = items.get(productId);
        if (item != null) {
            if (qty <= 0) items.remove(productId);
            else item.setQuantity(qty);
        }
    }

    public double getTotal() {
        return items.values().stream().mapToDouble(CartItem::getSubtotal).sum();
    }

    public List<CartItem> getItems() { return new ArrayList<>(items.values()); }
    public void clear() { items.clear(); }
    public String getUserId() { return userId; }
}

// ── Coupon ──
public class Coupon {
    private String code;
    private double discountPercent;
    private LocalDate validUntil;

    public Coupon(String code, double discountPercent, LocalDate validUntil) {
        this.code = code; this.discountPercent = discountPercent;
        this.validUntil = validUntil;
    }

    public boolean isValid() { return LocalDate.now().isBefore(validUntil); }
    public double apply(double amount) { return amount * (1 - discountPercent / 100); }
    public String getCode() { return code; }
}

// ── Payment ──
// Reuse PaymentStrategy from Problem 11 (CardPayment, UPIPayment)
public class WalletPayment implements PaymentStrategy {
    private String walletId;
    private double walletBalance;

    public WalletPayment(String walletId, double balance) {
        this.walletId = walletId; this.walletBalance = balance;
    }

    @Override
    public boolean pay(double amount) {
        if (walletBalance >= amount) {
            walletBalance -= amount;
            System.out.println("Paid ₹" + amount + " via Wallet: " + walletId);
            return true;
        }
        System.out.println("Insufficient wallet balance");
        return false;
    }
}

// ── Observer for Order Status ──
public interface OrderObserver {
    void onStatusChange(Order order, OrderStatus newStatus);
}

// ── Order ──
public class Order {
    private String id;
    private String userId;
    private List<CartItem> items;
    private double totalAmount;
    private OrderStatus status;
    private List<OrderObserver> observers;

    public Order(String id, String userId, List<CartItem> items, double totalAmount) {
        this.id = id; this.userId = userId;
        this.items = items; this.totalAmount = totalAmount;
        this.status = OrderStatus.PLACED;
        this.observers = new ArrayList<>();
    }

    public void addObserver(OrderObserver o) { observers.add(o); }

    public void setStatus(OrderStatus status) {
        this.status = status;
        observers.forEach(o -> o.onStatusChange(this, status));
    }

    public String getId() { return id; }
    public double getTotalAmount() { return totalAmount; }
    public OrderStatus getStatus() { return status; }
    public String getUserId() { return userId; }
}
```

### 15.5 Service Layer

```java
public class ShoppingService {
    private Map<String, Cart> carts = new HashMap<>();
    private Map<String, Order> orders = new HashMap<>();
    private Map<String, Coupon> coupons = new HashMap<>();
    private Map<String, Product> catalog = new HashMap<>();

    public void addToCatalog(Product p) { catalog.put(p.getId(), p); }
    public void addCoupon(Coupon c) { coupons.put(c.getCode(), c); }

    public Cart getOrCreateCart(String userId) {
        return carts.computeIfAbsent(userId, Cart::new);
    }

    public void addToCart(String userId, String productId, int qty) {
        Cart cart = getOrCreateCart(userId);
        Product product = catalog.get(productId);
        cart.addItem(product, qty);
    }

    public Order checkout(String userId, PaymentStrategy ps, String couponCode) {
        Cart cart = carts.get(userId);
        if (cart == null || cart.getItems().isEmpty()) {
            throw new RuntimeException("Cart is empty");
        }

        double total = cart.getTotal();

        // Apply coupon
        if (couponCode != null) {
            Coupon coupon = coupons.get(couponCode);
            if (coupon != null && coupon.isValid()) {
                total = coupon.apply(total);
                System.out.println("Coupon applied. New total: ₹" + total);
            }
        }

        // Payment
        boolean paid = ps.pay(total);
        if (!paid) throw new RuntimeException("Payment failed");

        // Create order
        Order order = new Order(UUID.randomUUID().toString(), userId, cart.getItems(), total);
        order.setStatus(OrderStatus.CONFIRMED);
        orders.put(order.getId(), order);
        cart.clear();

        System.out.println("Order placed: " + order.getId());
        return order;
    }

    public void updateOrderStatus(String orderId, OrderStatus status) {
        orders.get(orderId).setStatus(status);
    }

    public List<Product> searchByCategory(String category) {
        return catalog.values().stream()
                .filter(p -> p.getCategory().equalsIgnoreCase(category))
                .collect(Collectors.toList());
    }
}
```

### 15.6 Driver Code (main)
```java
public class ShoppingCartDemo {
    public static void main(String[] args) {
        ShoppingService shop = new ShoppingService();
        shop.addToCatalog(new Product("P1", "Laptop", 50000, "Electronics"));
        shop.addToCatalog(new Product("P2", "Mouse", 500, "Electronics"));
        shop.addCoupon(new Coupon("SAVE10", 10.0, LocalDate.of(2026, 12, 31)));

        // Add to cart
        shop.addToCart("user1", "P1", 1);
        shop.addToCart("user1", "P2", 2);

        // Checkout with coupon + UPI
        Order order = shop.checkout("user1", new UPIPayment("user@upi"), "SAVE10");
        // "Coupon applied. New total: ₹45900"
        // "Paid ₹45900 via UPI"
        // "Order placed: ORD-xxx"

        // Track status
        shop.updateOrderStatus(order.getId(), OrderStatus.SHIPPED);
    }
}
```

---

## 16 · Food Delivery (Swiggy/Zomato) 🟡
**Pattern: Strategy, Observer**

### 16.1 Functional Requirements
1. User browses restaurants by location
2. Add items from one restaurant to cart
3. Place order → assign delivery agent
4. Track order: Placed → Preparing → PickedUp → Delivered
5. Payment, ratings

### 16.2 Entities
- `Restaurant` — id, name, location, menu items, open/closed status
- `MenuItem` — id, name, price, availability
- `DeliveryAgent` — id, name, location, availability; assigned/released per order
- `FoodOrder` — user + restaurant + items + agent + status (PLACED→DELIVERED)
- `AgentAssignmentStrategy` — interface to pick agent (nearest, round-robin)
- `Location` — lat/lng with distance calculation

### 16.3 Class Diagram
```
Restaurant - id, name, location, menu[], isOpen
MenuItem - id, name, price, isAvailable
Order - id, user, restaurant, items[], agent, status, payment
DeliveryAgent - id, name, location, isAvailable
AgentAssignmentStrategy → NearestAgentStrategy, RoundRobinStrategy
```

### 16.4 Java Code

```java
// ── Enums ──
public enum FoodOrderStatus { PLACED, PREPARING, PICKED_UP, DELIVERED, CANCELLED }

// ── Location ──
public class Location {
    private double lat;
    private double lng;

    public Location(double lat, double lng) { this.lat = lat; this.lng = lng; }

    public double distanceTo(Location other) {
        return Math.sqrt(Math.pow(lat - other.lat, 2) + Math.pow(lng - other.lng, 2));
    }

    public double getLat() { return lat; }
    public double getLng() { return lng; }
}

// ── MenuItem ──
public class MenuItem {
    private String id;
    private String name;
    private double price;
    private boolean isAvailable;

    public MenuItem(String id, String name, double price) {
        this.id = id; this.name = name;
        this.price = price; this.isAvailable = true;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public double getPrice() { return price; }
    public boolean isAvailable() { return isAvailable; }
    public void setAvailable(boolean a) { this.isAvailable = a; }
}

// ── Restaurant ──
public class Restaurant {
    private String id;
    private String name;
    private Location location;
    private List<MenuItem> menu;
    private boolean isOpen;

    public Restaurant(String id, String name, Location location) {
        this.id = id; this.name = name;
        this.location = location; this.menu = new ArrayList<>();
        this.isOpen = true;
    }

    public void addMenuItem(MenuItem item) { menu.add(item); }
    public List<MenuItem> getMenu() { return menu; }

    public String getId() { return id; }
    public String getName() { return name; }
    public Location getLocation() { return location; }
    public boolean isOpen() { return isOpen; }
}

// ── DeliveryAgent ──
public class DeliveryAgent {
    private String id;
    private String name;
    private Location location;
    private boolean isAvailable;
    private double rating;

    public DeliveryAgent(String id, String name, Location location) {
        this.id = id; this.name = name;
        this.location = location; this.isAvailable = true;
        this.rating = 5.0;
    }

    public void assignOrder() { this.isAvailable = false; }
    public void completeDelivery() { this.isAvailable = true; }

    public String getId() { return id; }
    public String getName() { return name; }
    public Location getLocation() { return location; }
    public boolean isAvailable() { return isAvailable; }
    public double getRating() { return rating; }
    public void updateRating(double r) { this.rating = (this.rating + r) / 2; }
}

// ── Agent Assignment Strategy ──
public interface AgentAssignmentStrategy {
    DeliveryAgent assign(List<DeliveryAgent> agents, Location restaurantLocation);
}

public class NearestAgentStrategy implements AgentAssignmentStrategy {
    @Override
    public DeliveryAgent assign(List<DeliveryAgent> agents, Location restaurantLocation) {
        return agents.stream()
                .filter(DeliveryAgent::isAvailable)
                .min(Comparator.comparingDouble(a -> a.getLocation().distanceTo(restaurantLocation)))
                .orElseThrow(() -> new RuntimeException("No agents available"));
    }
}

// ── FoodOrder ──
public class FoodOrder {
    private String id;
    private User user;
    private Restaurant restaurant;
    private List<MenuItem> items;
    private DeliveryAgent agent;
    private FoodOrderStatus status;
    private double totalAmount;
    private List<OrderObserver> observers;

    public FoodOrder(String id, User user, Restaurant restaurant, List<MenuItem> items) {
        this.id = id; this.user = user;
        this.restaurant = restaurant; this.items = items;
        this.totalAmount = items.stream().mapToDouble(MenuItem::getPrice).sum();
        this.status = FoodOrderStatus.PLACED;
        this.observers = new ArrayList<>();
    }

    public void addObserver(OrderObserver o) { observers.add(o); }

    public void updateStatus(FoodOrderStatus newStatus) {
        this.status = newStatus;
        System.out.println("Order " + id + " → " + newStatus);
    }

    public void setAgent(DeliveryAgent agent) { this.agent = agent; }

    public String getId() { return id; }
    public double getTotalAmount() { return totalAmount; }
    public FoodOrderStatus getStatus() { return status; }
    public Restaurant getRestaurant() { return restaurant; }
    public DeliveryAgent getAgent() { return agent; }
}
```

### 16.5 Service Layer

```java
public class FoodDeliveryService {
    private Map<String, Restaurant> restaurants = new HashMap<>();
    private List<DeliveryAgent> agents = new ArrayList<>();
    private Map<String, FoodOrder> orders = new HashMap<>();
    private AgentAssignmentStrategy agentStrategy;

    public FoodDeliveryService(AgentAssignmentStrategy strategy) {
        this.agentStrategy = strategy;
    }

    public void registerRestaurant(Restaurant r) { restaurants.put(r.getId(), r); }
    public void registerAgent(DeliveryAgent a) { agents.add(a); }

    public List<Restaurant> searchByLocation(Location userLocation, double maxDistance) {
        return restaurants.values().stream()
                .filter(Restaurant::isOpen)
                .filter(r -> r.getLocation().distanceTo(userLocation) <= maxDistance)
                .collect(Collectors.toList());
    }

    public FoodOrder placeOrder(User user, String restaurantId, List<String> itemIds,
                                PaymentStrategy payment) {
        Restaurant restaurant = restaurants.get(restaurantId);
        List<MenuItem> items = restaurant.getMenu().stream()
                .filter(m -> itemIds.contains(m.getId()) && m.isAvailable())
                .collect(Collectors.toList());

        FoodOrder order = new FoodOrder(UUID.randomUUID().toString(), user, restaurant, items);

        // Payment
        payment.pay(order.getTotalAmount());

        // Assign agent
        DeliveryAgent agent = agentStrategy.assign(agents, restaurant.getLocation());
        agent.assignOrder();
        order.setAgent(agent);

        orders.put(order.getId(), order);
        System.out.println("Order placed. Agent " + agent.getName() + " assigned.");
        return order;
    }

    public void updateOrderStatus(String orderId, FoodOrderStatus status) {
        FoodOrder order = orders.get(orderId);
        order.updateStatus(status);
        if (status == FoodOrderStatus.DELIVERED) {
            order.getAgent().completeDelivery();
        }
    }

    public void rateAgent(String orderId, double rating) {
        FoodOrder order = orders.get(orderId);
        order.getAgent().updateRating(rating);
    }
}
```

### 16.6 Driver Code (main)
```java
public class FoodDeliveryDemo {
    public static void main(String[] args) {
        FoodDeliveryService service = new FoodDeliveryService(new NearestAgentStrategy());

        Restaurant r = new Restaurant("R1", "Pizza Palace", new Location(12.9, 77.6));
        r.addMenuItem(new MenuItem("M1", "Margherita", 250));
        r.addMenuItem(new MenuItem("M2", "Garlic Bread", 150));
        service.registerRestaurant(r);
        service.registerAgent(new DeliveryAgent("A1", "Ravi", new Location(12.91, 77.61)));

        User user = new User("U1", "Alice", "alice@email.com");
        FoodOrder order = service.placeOrder(user, "R1", List.of("M1", "M2"),
                new UPIPayment("user@upi"));
        // → "Paid ₹400. Agent Ravi assigned."

        service.updateOrderStatus(order.getId(), FoodOrderStatus.PREPARING);
        service.updateOrderStatus(order.getId(), FoodOrderStatus.PICKED_UP);
        service.updateOrderStatus(order.getId(), FoodOrderStatus.DELIVERED);
        // Agent Ravi now available again

        service.rateAgent(order.getId(), 4.5);
    }
}
```

---

## 17 · Hotel Booking System 🟡
**Pattern: Strategy, Observer**

### 17.1 Functional Requirements
1. Search rooms by date range, room type, city
2. Room types: Single, Double, Suite
3. Book room → block dates → payment
4. Cancel booking with refund policy
5. Notifications

### 17.2 Entities
- `Hotel` — id, name, city, rooms; searches available rooms by type + date range
- `Room` — id, type (Single/Double/Suite), price/night; checks date-range availability
- `Guest` — id, name, email
- `HotelBooking` — guest + room + check-in/out dates + total + status
- `PricingStrategy` — interface (Standard × nights, PeakSeason × multiplier)

### 17.3 Class Diagram
```
Hotel - id, name, city, rooms[]
Room - id, type, pricePerNight, bookings[]
Booking - id, guest, room, checkIn, checkOut, status
Guest - id, name, email
PricingStrategy → StandardPricing, PeakPricing
```

### 17.4 Java Code

```java
// ── Enums ──
public enum RoomType { SINGLE, DOUBLE, SUITE }
public enum HotelBookingStatus { CONFIRMED, CANCELLED, CHECKED_IN, CHECKED_OUT }

// ── Guest ──
public class Guest {
    private String id;
    private String name;
    private String email;

    public Guest(String id, String name, String email) {
        this.id = id; this.name = name; this.email = email;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public String getEmail() { return email; }
}

// ── Room ──
public class Room {
    private String id;
    private RoomType type;
    private double pricePerNight;
    private List<HotelBooking> bookings;

    public Room(String id, RoomType type, double pricePerNight) {
        this.id = id; this.type = type;
        this.pricePerNight = pricePerNight;
        this.bookings = new ArrayList<>();
    }

    public boolean isAvailable(LocalDate checkIn, LocalDate checkOut) {
        return bookings.stream()
                .filter(b -> b.getStatus() != HotelBookingStatus.CANCELLED)
                .noneMatch(b -> checkIn.isBefore(b.getCheckOut()) && checkOut.isAfter(b.getCheckIn()));
    }

    public void addBooking(HotelBooking b) { bookings.add(b); }

    public String getId() { return id; }
    public RoomType getType() { return type; }
    public double getPricePerNight() { return pricePerNight; }
}

// ── Hotel ──
public class Hotel {
    private String id;
    private String name;
    private String city;
    private List<Room> rooms;

    public Hotel(String id, String name, String city) {
        this.id = id; this.name = name; this.city = city;
        this.rooms = new ArrayList<>();
    }

    public void addRoom(Room room) { rooms.add(room); }

    public List<Room> searchAvailableRooms(RoomType type, LocalDate in, LocalDate out) {
        return rooms.stream()
                .filter(r -> r.getType() == type && r.isAvailable(in, out))
                .collect(Collectors.toList());
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public String getCity() { return city; }
}

// ── Pricing Strategy ──
public interface PricingStrategy {
    double calculate(Room room, LocalDate checkIn, LocalDate checkOut);
}

public class StandardPricing implements PricingStrategy {
    @Override
    public double calculate(Room room, LocalDate checkIn, LocalDate checkOut) {
        long nights = ChronoUnit.DAYS.between(checkIn, checkOut);
        return room.getPricePerNight() * nights;
    }
}

public class PeakSeasonPricing implements PricingStrategy {
    private double multiplier;
    public PeakSeasonPricing(double multiplier) { this.multiplier = multiplier; }

    @Override
    public double calculate(Room room, LocalDate checkIn, LocalDate checkOut) {
        long nights = ChronoUnit.DAYS.between(checkIn, checkOut);
        return room.getPricePerNight() * nights * multiplier;
    }
}

// ── HotelBooking ──
public class HotelBooking {
    private String id;
    private Guest guest;
    private Room room;
    private LocalDate checkIn;
    private LocalDate checkOut;
    private HotelBookingStatus status;
    private double totalAmount;

    public HotelBooking(String id, Guest guest, Room room, LocalDate checkIn,
                        LocalDate checkOut, double totalAmount) {
        this.id = id; this.guest = guest; this.room = room;
        this.checkIn = checkIn; this.checkOut = checkOut;
        this.totalAmount = totalAmount;
        this.status = HotelBookingStatus.CONFIRMED;
    }

    public void cancel() { this.status = HotelBookingStatus.CANCELLED; }

    public String getId() { return id; }
    public HotelBookingStatus getStatus() { return status; }
    public LocalDate getCheckIn() { return checkIn; }
    public LocalDate getCheckOut() { return checkOut; }
    public double getTotalAmount() { return totalAmount; }
    public Guest getGuest() { return guest; }
}
```

### 17.5 Service Layer

```java
public class HotelBookingService {
    private Map<String, Hotel> hotels = new HashMap<>();
    private Map<String, HotelBooking> bookings = new HashMap<>();
    private PricingStrategy pricingStrategy;

    public HotelBookingService(PricingStrategy pricingStrategy) {
        this.pricingStrategy = pricingStrategy;
    }

    public void registerHotel(Hotel hotel) { hotels.put(hotel.getId(), hotel); }

    public List<Room> searchRooms(String city, RoomType type, LocalDate in, LocalDate out) {
        return hotels.values().stream()
                .filter(h -> h.getCity().equalsIgnoreCase(city))
                .flatMap(h -> h.searchAvailableRooms(type, in, out).stream())
                .collect(Collectors.toList());
    }

    public HotelBooking bookRoom(Guest guest, Room room, LocalDate in, LocalDate out,
                                 PaymentStrategy payment) {
        if (!room.isAvailable(in, out)) {
            throw new RuntimeException("Room not available for selected dates");
        }

        double total = pricingStrategy.calculate(room, in, out);
        payment.pay(total);

        HotelBooking booking = new HotelBooking(
                UUID.randomUUID().toString(), guest, room, in, out, total);
        room.addBooking(booking);
        bookings.put(booking.getId(), booking);

        System.out.println("Booking confirmed: " + booking.getId() +
                " for " + guest.getName() + " | ₹" + total);
        return booking;
    }

    public double cancelBooking(String bookingId) {
        HotelBooking booking = bookings.get(bookingId);
        booking.cancel();
        // 50% refund policy
        double refund = booking.getTotalAmount() * 0.5;
        System.out.println("Booking cancelled. Refund: ₹" + refund);
        return refund;
    }
}
```

### 17.6 Driver Code (main)
```java
public class HotelBookingDemo {
    public static void main(String[] args) {
        HotelBookingService service = new HotelBookingService(new StandardPricing());

        Hotel hotel = new Hotel("H1", "Grand Hyatt", "Mumbai");
        hotel.addRoom(new Room("R101", RoomType.DOUBLE, 5000));
        hotel.addRoom(new Room("R102", RoomType.SUITE, 12000));
        service.registerHotel(hotel);

        // Search
        List<Room> rooms = service.searchRooms("Mumbai", RoomType.DOUBLE,
                LocalDate.of(2026, 4, 10), LocalDate.of(2026, 4, 12));

        // Book
        Guest guest = new Guest("G1", "Alice", "alice@email.com");
        HotelBooking booking = service.bookRoom(guest, rooms.get(0),
                LocalDate.of(2026, 4, 10), LocalDate.of(2026, 4, 12),
                new CardPayment("4111111111111111"));
        // → "Paid ₹10000. Booking confirmed."

        // Cancel → 50% refund
        double refund = service.cancelBooking(booking.getId());
        // → "Refund: ₹5000"
    }
}
```

---

## 18 · In-Memory File System 🟡
**Pattern: Composite**

### 18.1 Functional Requirements
1. Create files and directories
2. Navigate: cd, ls, pwd
3. Read/write file content
4. Delete files/directories (recursive)
5. Search by name

### 18.2 Entities
- `Entry` — abstract (Composite); name, parent, getSize(), getFullPath()
- `File` — leaf; holds content string, size = content.length
- `Directory` — composite; children map, addEntry, removeEntry, listEntries
- `FileSystem` — root + currentDir; mkdir, touch, cd, ls, pwd, rm, find

### 18.3 Class Diagram
```
Entry (abstract) → Composite Pattern
 ├── File → content, size
 └── Directory → children(Map<String, Entry>)
 - name, createdAt, parent

FileSystem - root, currentDir
```

### 18.4 Java Code

```java
// ── Entry (Composite) ──
public abstract class Entry {
    protected String name;
    protected Directory parent;
    protected LocalDateTime createdAt;

    public Entry(String name) {
        this.name = name;
        this.createdAt = LocalDateTime.now();
    }

    public abstract int getSize();
    public abstract String getType();

    public String getName() { return name; }
    public Directory getParent() { return parent; }
    public void setParent(Directory parent) { this.parent = parent; }

    public String getFullPath() {
        if (parent == null) return "/";
        String parentPath = parent.getFullPath();
        return parentPath.equals("/") ? "/" + name : parentPath + "/" + name;
    }
}

// ── File ──
public class File extends Entry {
    private StringBuilder content;

    public File(String name) {
        super(name);
        this.content = new StringBuilder();
    }

    public String read() { return content.toString(); }

    public void write(String data) { content = new StringBuilder(data); }

    public void append(String data) { content.append(data); }

    @Override
    public int getSize() { return content.length(); }

    @Override
    public String getType() { return "FILE"; }
}

// ── Directory ──
public class Directory extends Entry {
    private Map<String, Entry> children;

    public Directory(String name) {
        super(name);
        this.children = new LinkedHashMap<>();
    }

    public void addEntry(Entry entry) {
        children.put(entry.getName(), entry);
        entry.setParent(this);
    }

    public Entry getEntry(String name) { return children.get(name); }

    public boolean removeEntry(String name) {
        return children.remove(name) != null;
    }

    public List<Entry> listEntries() {
        return new ArrayList<>(children.values());
    }

    @Override
    public int getSize() {
        return children.values().stream().mapToInt(Entry::getSize).sum();
    }

    @Override
    public String getType() { return "DIR"; }

    public Map<String, Entry> getChildren() { return children; }
}

// ── FileSystem ──
public class FileSystem {
    private Directory root;
    private Directory currentDir;

    public FileSystem() {
        this.root = new Directory("");
        this.currentDir = root;
    }

    public void mkdir(String name) {
        if (currentDir.getEntry(name) != null) {
            throw new RuntimeException("Directory already exists: " + name);
        }
        currentDir.addEntry(new Directory(name));
    }

    public void touch(String name) {
        if (currentDir.getEntry(name) != null) {
            throw new RuntimeException("File already exists: " + name);
        }
        currentDir.addEntry(new File(name));
    }

    public void cd(String path) {
        if (path.equals("..")) {
            if (currentDir.getParent() != null) currentDir = currentDir.getParent();
            return;
        }
        if (path.equals("/")) {
            currentDir = root;
            return;
        }
        Entry entry = currentDir.getEntry(path);
        if (entry instanceof Directory) {
            currentDir = (Directory) entry;
        } else {
            throw new RuntimeException("Not a directory: " + path);
        }
    }

    public List<String> ls() {
        return currentDir.listEntries().stream()
                .map(e -> e.getName() + (e instanceof Directory ? "/" : ""))
                .collect(Collectors.toList());
    }

    public String pwd() {
        return currentDir.getFullPath();
    }

    public void writeFile(String name, String content) {
        Entry entry = currentDir.getEntry(name);
        if (entry instanceof File) {
            ((File) entry).write(content);
        } else {
            throw new RuntimeException("Not a file: " + name);
        }
    }

    public String readFile(String name) {
        Entry entry = currentDir.getEntry(name);
        if (entry instanceof File) {
            return ((File) entry).read();
        }
        throw new RuntimeException("Not a file: " + name);
    }

    public boolean rm(String name) {
        return currentDir.removeEntry(name);
    }

    public List<String> find(String fileName) {
        List<String> results = new ArrayList<>();
        findRecursive(root, fileName, results);
        return results;
    }

    private void findRecursive(Directory dir, String fileName, List<String> results) {
        for (Entry entry : dir.listEntries()) {
            if (entry.getName().equals(fileName)) {
                results.add(entry.getFullPath());
            }
            if (entry instanceof Directory) {
                findRecursive((Directory) entry, fileName, results);
            }
        }
    }
}
```

### 18.5 Service Layer

```java
public class FileSystemService {
    private final FileSystem fs;

    public FileSystemService() {
        this.fs = new FileSystem();
    }

    public void createDirectory(String name) {
        fs.mkdir(name);
        System.out.println("Created directory: " + name);
    }

    public void createFile(String name, String content) {
        fs.touch(name);
        fs.writeFile(name, content);
        System.out.println("Created file: " + name);
    }

    public void navigate(String path) {
        fs.cd(path);
        System.out.println("Current: " + fs.pwd());
    }

    public void listContents() {
        System.out.println("Contents of " + fs.pwd() + ":");
        fs.ls().forEach(e -> System.out.println("  " + e));
    }

    public String readFile(String name) {
        return fs.readFile(name);
    }

    public void delete(String name) {
        fs.rm(name);
        System.out.println("Deleted: " + name);
    }

    public List<String> search(String fileName) {
        return fs.find(fileName);
    }
}
```

### 18.6 Driver Code (main)
```java
public class FileSystemDemo {
    public static void main(String[] args) {
        FileSystemService fs = new FileSystemService();

        fs.createDirectory("home");
        fs.navigate("home");
        fs.createDirectory("docs");
        fs.createFile("readme.txt", "Hello World!");

        fs.listContents();
        // Contents of /home: docs/, readme.txt

        System.out.println(fs.readFile("readme.txt")); // "Hello World!"

        List<String> found = fs.search("readme.txt");
        System.out.println("Found: " + found); // ["/home/readme.txt"]

        fs.navigate("..");
        fs.delete("home"); // recursive delete
    }
}
```

---

## 19 · Notification Service 🟡
**Pattern: Observer, Strategy, Factory**

### 19.1 Functional Requirements
1. Send notifications via Email, SMS, Push
2. User preferences for channel selection
3. Priority levels: Low, Medium, High, Critical
4. Template-based messages
5. Retry on failure

### 19.2 Entities
- `Notification` — id, userId, message, channel, priority, status, retryCount
- `NotificationChannel` — strategy interface (Email/SMS/Push); sends notification
- `ChannelFactory` — creates channel by type
- `Template` — id, name, body with {placeholders}; renders with params map
- `UserPreference` — userId + preferred channel list

### 19.3 Class Diagram
```
Notification - id, userId, message, channel, priority, status
Channel (interface/Strategy) → EmailChannel, SMSChannel, PushChannel
ChannelFactory (Factory) + createChannel(type)
Template - id, name, body
```

### 19.4 Java Code

```java
// ── Enums ──
public enum ChannelType { EMAIL, SMS, PUSH }
public enum Priority { LOW, MEDIUM, HIGH, CRITICAL }
public enum NotificationStatus { PENDING, SENT, FAILED, RETRYING }

// ── Template ──
public class Template {
    private String id;
    private String name;
    private String body; // e.g., "Hello {name}, your order {orderId} is {status}"

    public Template(String id, String name, String body) {
        this.id = id; this.name = name; this.body = body;
    }

    public String render(Map<String, String> params) {
        String result = body;
        for (Map.Entry<String, String> entry : params.entrySet()) {
            result = result.replace("{" + entry.getKey() + "}", entry.getValue());
        }
        return result;
    }

    public String getId() { return id; }
}

// ── Notification ──
public class Notification {
    private String id;
    private String userId;
    private String message;
    private ChannelType channelType;
    private Priority priority;
    private NotificationStatus status;
    private int retryCount;

    public Notification(String id, String userId, String message,
                        ChannelType channelType, Priority priority) {
        this.id = id; this.userId = userId;
        this.message = message; this.channelType = channelType;
        this.priority = priority; this.status = NotificationStatus.PENDING;
        this.retryCount = 0;
    }

    public void incrementRetry() { retryCount++; }

    public String getId() { return id; }
    public String getMessage() { return message; }
    public ChannelType getChannelType() { return channelType; }
    public Priority getPriority() { return priority; }
    public NotificationStatus getStatus() { return status; }
    public void setStatus(NotificationStatus s) { this.status = s; }
    public int getRetryCount() { return retryCount; }
}

// ── Channel (Strategy) ──
public interface NotificationChannel {
    boolean send(Notification notification);
}

public class EmailChannel implements NotificationChannel {
    @Override
    public boolean send(Notification notification) {
        System.out.println("EMAIL → [" + notification.getPriority() + "] " +
                notification.getMessage());
        return true; // simulated success
    }
}

public class SMSChannel implements NotificationChannel {
    @Override
    public boolean send(Notification notification) {
        System.out.println("SMS → [" + notification.getPriority() + "] " +
                notification.getMessage());
        return true;
    }
}

public class PushChannel implements NotificationChannel {
    @Override
    public boolean send(Notification notification) {
        System.out.println("PUSH → [" + notification.getPriority() + "] " +
                notification.getMessage());
        return true;
    }
}

// ── Channel Factory ──
public class ChannelFactory {
    public NotificationChannel createChannel(ChannelType type) {
        return switch (type) {
            case EMAIL -> new EmailChannel();
            case SMS -> new SMSChannel();
            case PUSH -> new PushChannel();
        };
    }
}

// ── User Preference ──
public class UserPreference {
    private String userId;
    private List<ChannelType> preferredChannels;

    public UserPreference(String userId, List<ChannelType> channels) {
        this.userId = userId; this.preferredChannels = channels;
    }

    public List<ChannelType> getPreferredChannels() { return preferredChannels; }
}
```

### 19.5 Service Layer

```java
public class NotificationService {
    private ChannelFactory channelFactory;
    private Map<String, Template> templates;
    private Map<String, UserPreference> preferences;
    private Queue<Notification> retryQueue;
    private static final int MAX_RETRIES = 3;

    public NotificationService() {
        this.channelFactory = new ChannelFactory();
        this.templates = new HashMap<>();
        this.preferences = new HashMap<>();
        this.retryQueue = new LinkedList<>();
    }

    public void registerTemplate(Template t) { templates.put(t.getId(), t); }
    public void setUserPreference(UserPreference p) { preferences.put(p.getUserId(), p); }

    public void send(String userId, String templateId, Map<String, String> params,
                     Priority priority) {
        Template template = templates.get(templateId);
        String message = template.render(params);

        UserPreference pref = preferences.get(userId);
        List<ChannelType> channels = pref != null ? pref.getPreferredChannels()
                : List.of(ChannelType.EMAIL);

        for (ChannelType type : channels) {
            Notification notification = new Notification(
                    UUID.randomUUID().toString(), userId, message, type, priority);
            dispatch(notification);
        }
    }

    private void dispatch(Notification notification) {
        NotificationChannel channel = channelFactory.createChannel(notification.getChannelType());
        boolean sent = channel.send(notification);

        if (sent) {
            notification.setStatus(NotificationStatus.SENT);
        } else {
            notification.setStatus(NotificationStatus.FAILED);
            if (notification.getRetryCount() < MAX_RETRIES) {
                notification.incrementRetry();
                notification.setStatus(NotificationStatus.RETRYING);
                retryQueue.add(notification);
            }
        }
    }

    public void processRetryQueue() {
        while (!retryQueue.isEmpty()) {
            Notification n = retryQueue.poll();
            dispatch(n);
        }
    }
}
```

### 19.6 Driver Code (main)
```java
public class NotificationDemo {
    public static void main(String[] args) {
        NotificationService service = new NotificationService();

        service.registerTemplate(new Template("T1", "OrderUpdate",
                "Hi {name}, your order {orderId} is {status}"));
        service.setUserPreference(new UserPreference("U1",
                List.of(ChannelType.EMAIL, ChannelType.PUSH)));

        service.send("U1", "T1",
                Map.of("name", "Alice", "orderId", "ORD-123", "status", "shipped"),
                Priority.HIGH);
        // EMAIL → [HIGH] Hi Alice, your order ORD-123 is shipped
        // PUSH  → [HIGH] Hi Alice, your order ORD-123 is shipped

        service.processRetryQueue();
    }
}
```

---

## 20 · Chat Application 🟡
**Pattern: Observer, Mediator**

### 20.1 Functional Requirements
1. 1-to-1 and group messaging
2. Online/offline status
3. Message history per conversation
4. Typing indicator, read receipts
5. Media sharing (text, image, file)

### 20.2 Entities
- `ChatUser` — id, name, online/offline status; receives messages
- `Message` — id, sender, content, type (TEXT/IMAGE/FILE), status (SENT/DELIVERED/READ)
- `Conversation` — abstract; holds message history + participant list
- `OneToOneConversation` — two users
- `GroupConversation` — N users + admin
- `ChatMediator` — routes messages to all participants except sender

### 20.3 Class Diagram
```
User - id, name, status(ONLINE/OFFLINE), conversations[]
Message - id, sender, content, type, timestamp, status(SENT/DELIVERED/READ)
Conversation (abstract) → OneToOne, Group
ChatMediator - routes messages
```

### 20.4 Java Code

```java
// ── Enums ──
public enum UserStatus { ONLINE, OFFLINE }
public enum MessageType { TEXT, IMAGE, FILE }
public enum MessageStatus { SENT, DELIVERED, READ }

// ── Message ──
public class Message {
    private String id;
    private ChatUser sender;
    private String content;
    private MessageType type;
    private LocalDateTime timestamp;
    private MessageStatus status;

    public Message(String id, ChatUser sender, String content, MessageType type) {
        this.id = id; this.sender = sender;
        this.content = content; this.type = type;
        this.timestamp = LocalDateTime.now();
        this.status = MessageStatus.SENT;
    }

    public void markDelivered() { this.status = MessageStatus.DELIVERED; }
    public void markRead() { this.status = MessageStatus.READ; }

    public String getId() { return id; }
    public ChatUser getSender() { return sender; }
    public String getContent() { return content; }
    public MessageType getType() { return type; }
    public MessageStatus getStatus() { return status; }
    public LocalDateTime getTimestamp() { return timestamp; }
}

// ── ChatUser ──
public class ChatUser {
    private String id;
    private String name;
    private UserStatus status;

    public ChatUser(String id, String name) {
        this.id = id; this.name = name;
        this.status = UserStatus.OFFLINE;
    }

    public void goOnline() { this.status = UserStatus.ONLINE; }
    public void goOffline() { this.status = UserStatus.OFFLINE; }

    public void receive(Message msg) {
        if (status == UserStatus.ONLINE) {
            System.out.println("[" + name + "] received: " + msg.getContent() +
                    " (from " + msg.getSender().getName() + ")");
            msg.markDelivered();
        }
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public UserStatus getStatus() { return status; }
}

// ── Conversation ──
public abstract class Conversation {
    protected String id;
    protected List<Message> messages;

    public Conversation(String id) {
        this.id = id;
        this.messages = new ArrayList<>();
    }

    public void addMessage(Message msg) { messages.add(msg); }
    public abstract List<ChatUser> getParticipants();

    public List<Message> getMessageHistory() {
        return Collections.unmodifiableList(messages);
    }

    public String getId() { return id; }
}

public class OneToOneConversation extends Conversation {
    private ChatUser user1;
    private ChatUser user2;

    public OneToOneConversation(String id, ChatUser user1, ChatUser user2) {
        super(id);
        this.user1 = user1; this.user2 = user2;
    }

    @Override
    public List<ChatUser> getParticipants() {
        return List.of(user1, user2);
    }
}

public class GroupConversation extends Conversation {
    private String groupName;
    private List<ChatUser> participants;
    private ChatUser admin;

    public GroupConversation(String id, String groupName, ChatUser admin) {
        super(id);
        this.groupName = groupName;
        this.admin = admin;
        this.participants = new ArrayList<>();
        this.participants.add(admin);
    }

    public void addParticipant(ChatUser user) { participants.add(user); }
    public void removeParticipant(ChatUser user) { participants.remove(user); }

    @Override
    public List<ChatUser> getParticipants() {
        return Collections.unmodifiableList(participants);
    }

    public String getGroupName() { return groupName; }
}

// ── ChatMediator ──
public class ChatMediator {
    private Map<String, Conversation> conversations;

    public ChatMediator() {
        this.conversations = new HashMap<>();
    }

    public void addConversation(Conversation c) { conversations.put(c.getId(), c); }

    public void sendMessage(Message msg, String conversationId) {
        Conversation conv = conversations.get(conversationId);
        conv.addMessage(msg);
        conv.getParticipants().stream()
                .filter(u -> !u.getId().equals(msg.getSender().getId()))
                .forEach(u -> u.receive(msg));
    }
}
```

### 20.5 Service Layer

```java
public class ChatService {
    private ChatMediator mediator;
    private Map<String, ChatUser> users;
    private Map<String, Conversation> conversations;

    public ChatService() {
        this.mediator = new ChatMediator();
        this.users = new HashMap<>();
        this.conversations = new HashMap<>();
    }

    public ChatUser registerUser(String id, String name) {
        ChatUser user = new ChatUser(id, name);
        users.put(id, user);
        return user;
    }

    public Conversation startOneToOne(String userId1, String userId2) {
        ChatUser u1 = users.get(userId1);
        ChatUser u2 = users.get(userId2);
        OneToOneConversation conv = new OneToOneConversation(
                UUID.randomUUID().toString(), u1, u2);
        mediator.addConversation(conv);
        conversations.put(conv.getId(), conv);
        return conv;
    }

    public GroupConversation createGroup(String groupName, String adminId) {
        ChatUser admin = users.get(adminId);
        GroupConversation group = new GroupConversation(
                UUID.randomUUID().toString(), groupName, admin);
        mediator.addConversation(group);
        conversations.put(group.getId(), group);
        return group;
    }

    public void addToGroup(String groupId, String userId) {
        GroupConversation group = (GroupConversation) conversations.get(groupId);
        group.addParticipant(users.get(userId));
    }

    public void sendMessage(String senderId, String conversationId, String content,
                            MessageType type) {
        ChatUser sender = users.get(senderId);
        Message msg = new Message(UUID.randomUUID().toString(), sender, content, type);
        mediator.sendMessage(msg, conversationId);
    }

    public List<Message> getHistory(String conversationId) {
        return conversations.get(conversationId).getMessageHistory();
    }
}
```

### 20.6 Driver Code (main)
```java
public class ChatAppDemo {
    public static void main(String[] args) {
        ChatService chat = new ChatService();

        ChatUser alice = chat.registerUser("U1", "Alice");
        ChatUser bob = chat.registerUser("U2", "Bob");
        ChatUser charlie = chat.registerUser("U3", "Charlie");
        alice.goOnline(); bob.goOnline(); charlie.goOnline();

        // 1-to-1 DM
        Conversation dm = chat.startOneToOne("U1", "U2");
        chat.sendMessage("U1", dm.getId(), "Hey Bob!", MessageType.TEXT);
        // [Bob] received: Hey Bob! (from Alice)

        // Group chat
        GroupConversation group = chat.createGroup("Weekend Plans", "U1");
        chat.addToGroup(group.getId(), "U2");
        chat.addToGroup(group.getId(), "U3");
        chat.sendMessage("U1", group.getId(), "Hiking Saturday?", MessageType.TEXT);
        // [Bob] received: Hiking Saturday? (from Alice)
        // [Charlie] received: Hiking Saturday? (from Alice)

        // History
        List<Message> history = chat.getHistory(dm.getId());
        System.out.println("DM messages: " + history.size()); // 1
    }
}
```

---

## 21 · In-Memory Job Scheduler 🟡
**Pattern: Strategy, Observer**

### 21.1 Functional Requirements
1. Submit jobs with scheduled time or cron-like recurrence
2. One-time and recurring jobs
3. Priority-based execution
4. Configurable thread pool
5. Job states: QUEUED → RUNNING → COMPLETED / FAILED

### 21.2 Entities
- `Job` — id, name, Runnable task, scheduledTime, priority; supports recurring via interval
- `JobScheduler` — singleton; PriorityBlockingQueue + thread pool; polls ready jobs
- `JobCompletionListener` — observer callback for completed/failed jobs

### 21.3 Class Diagram
```
Job - id, name, task(Runnable), scheduledTime, priority, status, interval
JobScheduler (Singleton) - PriorityBlockingQueue, ExecutorService
ExecutionStrategy → OneTimeExecution, RecurringExecution
```

### 21.4 Java Code

> **How it works — read the comments line by line:**

```java
// ── Enums ──
public enum JobStatus { QUEUED, RUNNING, COMPLETED, FAILED }

// ── Job ──
// A Job wraps a Runnable (the actual work) with scheduling metadata.
// Implements Comparable so PriorityBlockingQueue knows how to sort jobs.
// Sort order: earliest scheduledTime first → then lowest priority number first.
public class Job implements Comparable<Job> {
    private String id;
    private String name;
    private Runnable task;          // the actual work — a lambda like () -> System.out.println("done")
    private long scheduledTimeMs;   // epoch millis — when this job should run
    private int priority;           // lower number = higher priority (0 = urgent, 10 = low)
    private JobStatus status;
    private boolean recurring;      // if true, job reschedules itself after completion
    private long intervalMs;        // gap between recurrences (e.g., 5000 = every 5 sec)

    public Job(String id, String name, Runnable task, long scheduledTimeMs, int priority) {
        this.id = id; this.name = name; this.task = task;
        this.scheduledTimeMs = scheduledTimeMs; this.priority = priority;
        this.status = JobStatus.QUEUED; this.recurring = false;
    }

    // Builder-style: call job.asRecurring(5000) to make it repeat every 5s
    public Job asRecurring(long intervalMs) {
        this.recurring = true;
        this.intervalMs = intervalMs;
        return this;  // returns this so you can chain: new Job(...).asRecurring(5000)
    }

    // Called by scheduler to check: "Is it time to run this job yet?"
    // Compares current wall-clock time against the job's scheduled time.
    public boolean isReady() { return System.currentTimeMillis() >= scheduledTimeMs; }

    // After a recurring job completes, create the NEXT occurrence:
    // same task, same priority, but scheduledTime shifted forward by intervalMs.
    // Returns a brand-new Job object (the old one stays COMPLETED).
    public Job nextOccurrence() {
        if (!recurring) return null;
        return new Job(UUID.randomUUID().toString(), name, task,
                scheduledTimeMs + intervalMs, priority).asRecurring(intervalMs);
    }

    // PriorityBlockingQueue calls this to decide ordering.
    // First compare by time (earlier = first). If same time, compare by priority (lower = first).
    @Override
    public int compareTo(Job other) {
        int timeCmp = Long.compare(this.scheduledTimeMs, other.scheduledTimeMs);
        if (timeCmp != 0) return timeCmp;
        return Integer.compare(this.priority, other.priority);
    }

    // Getters
    public String getId() { return id; }
    public String getName() { return name; }
    public Runnable getTask() { return task; }
    public long getScheduledTimeMs() { return scheduledTimeMs; }
    public int getPriority() { return priority; }
    public JobStatus getStatus() { return status; }
    public void setStatus(JobStatus s) { this.status = s; }
    public boolean isRecurring() { return recurring; }
}

// ── Job Completion Listener (Observer Pattern) ──
// Anyone who wants to know when a job finishes/fails implements this.
// The scheduler calls these callbacks after each job execution.
public interface JobCompletionListener {
    void onCompleted(Job job);
    void onFailed(Job job, Exception e);
}

// ── Job Scheduler ──
// The brain of the system. Three components:
//   1. PriorityBlockingQueue — sorted waiting area for jobs (Comparable decides order)
//   2. Scheduler thread     — single thread that checks "is any job ready to run?"
//   3. Worker thread pool   — N threads that actually execute jobs in parallel
//
// Flow: submit(job) → queue → scheduler thread polls → worker thread executes → listener notified
public class JobScheduler {
    private static volatile JobScheduler instance;       // Singleton — volatile for thread safety
    private final PriorityBlockingQueue<Job> queue;      // Auto-sorted by Job.compareTo()
    private final ExecutorService workerPool;            // Fixed thread pool (e.g., 4 threads)
    private final List<JobCompletionListener> listeners; // Observers notified on job complete/fail
    private volatile boolean running;                    // Flag to stop the scheduler gracefully

    // Private constructor — Singleton pattern. Creates queue + thread pool.
    private JobScheduler(int poolSize) {
        this.queue = new PriorityBlockingQueue<>();      // No capacity limit, auto-grows
        this.workerPool = Executors.newFixedThreadPool(poolSize); // e.g., 4 worker threads
        this.listeners = new ArrayList<>();
        this.running = false;
    }

    // Double-checked locking singleton. Thread-safe, lazy initialization.
    public static JobScheduler getInstance(int poolSize) {
        if (instance == null) {                          // First check — no lock (fast path)
            synchronized (JobScheduler.class) {
                if (instance == null) {                  // Second check — with lock (safe path)
                    instance = new JobScheduler(poolSize);
                }
            }
        }
        return instance;
    }

    // Register an observer to get callbacks when jobs complete or fail.
    public void addListener(JobCompletionListener listener) { listeners.add(listener); }

    // Add a job to the queue. PriorityBlockingQueue automatically inserts it
    // in the right position based on compareTo() (sorted by time, then priority).
    public void submit(Job job) {
        queue.offer(job);                                // offer() = non-blocking insert
        System.out.println("Job submitted: " + job.getName());
    }

    // Starts the scheduler loop on a background daemon thread.
    // This thread does NOT execute jobs — it only decides WHEN to hand them off to workers.
    public void start() {
        running = true;
        Thread schedulerThread = new Thread(() -> {
            while (running) {                            // Loop until stop() is called
                try {
                    Job job = queue.peek();              // Look at the first job WITHOUT removing it
                    if (job != null && job.isReady()) {  // Is current time >= job's scheduled time?
                        job = queue.poll();              // YES → remove from queue
                        executeJob(job);                 // Hand off to a worker thread
                    } else {
                        Thread.sleep(100);               // NO → wait 100ms, then check again
                        // Why 100ms? Trade-off: lower = more responsive, higher = less CPU waste.
                        // In production, use DelayQueue.take() which blocks until a job is due.
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();   // Restore interrupt flag
                    break;                               // Exit the loop
                }
            }
        });
        schedulerThread.setDaemon(true);                 // Daemon = dies when main thread dies
        schedulerThread.start();                         // Start the background loop
    }

    // Hands a job to the worker thread pool for actual execution.
    // The scheduler thread returns immediately — it does NOT wait for the job to finish.
    private void executeJob(Job job) {
        job.setStatus(JobStatus.RUNNING);
        workerPool.submit(() -> {                        // Runs on one of the N worker threads
            try {
                job.getTask().run();                     // ← THIS is where the actual work happens
                job.setStatus(JobStatus.COMPLETED);
                listeners.forEach(l -> l.onCompleted(job)); // Notify all observers: "job done"

                // If this is a recurring job, create the next occurrence and re-submit.
                // e.g., job ran at t=1000 with interval=5000 → next runs at t=6000
                if (job.isRecurring()) {
                    Job next = job.nextOccurrence();     // New Job with shifted scheduledTime
                    if (next != null) submit(next);      // Back into the queue → cycle repeats
                }
            } catch (Exception e) {
                job.setStatus(JobStatus.FAILED);
                listeners.forEach(l -> l.onFailed(job, e)); // Notify observers: "job failed"
                // Note: failed jobs are NOT retried. Add retry logic here if needed.
            }
        });
    }

    // Graceful shutdown: stop the scheduler loop + shut down worker threads.
    public void stop() {
        running = false;                                 // Scheduler thread will exit its while loop
        workerPool.shutdown();                           // No new tasks; finish in-progress ones
    }
}
        });
    }

    public void stop() {
        running = false;
        workerPool.shutdown();
    }
}
```

### 21.5 Service Layer

```java
public class JobSchedulerService {
    private final JobScheduler scheduler;

    public JobSchedulerService(int poolSize) {
        this.scheduler = JobScheduler.getInstance(poolSize);
        this.scheduler.addListener(new JobCompletionListener() {
            @Override
            public void onCompleted(Job job) {
                System.out.println("✓ Job completed: " + job.getName());
            }
            @Override
            public void onFailed(Job job, Exception e) {
                System.out.println("✗ Job failed: " + job.getName() + " - " + e.getMessage());
            }
        });
        scheduler.start();
    }

    public void scheduleOneTime(String name, Runnable task, long delayMs, int priority) {
        Job job = new Job(UUID.randomUUID().toString(), name, task,
                System.currentTimeMillis() + delayMs, priority);
        scheduler.submit(job);
    }

    public void scheduleRecurring(String name, Runnable task, long intervalMs, int priority) {
        Job job = new Job(UUID.randomUUID().toString(), name, task,
                System.currentTimeMillis(), priority).asRecurring(intervalMs);
        scheduler.submit(job);
    }

    public void shutdown() {
        scheduler.stop();
    }
}
```

---

## 22 · Rate Limiter 🟡
**Pattern: Strategy**

### 22.1 Functional Requirements
1. Limit requests per user/IP in a time window
2. Algorithms: Fixed Window, Sliding Window Log, Token Bucket
3. Return allow/deny per request
4. Configurable limit and window per API

### 22.2 Entities
- `RateLimitConfig` — maxRequests + window size in ms
- `RateLimitStrategy` — interface; returns allow/deny per clientId
- `FixedWindowStrategy` — counter per time window; resets on boundary
- `SlidingWindowLogStrategy` — Deque of timestamps; removes old, counts recent
- `TokenBucketStrategy` — tokens refill over time; each request consumes one
- `RateLimiter` — wraps a strategy; isAllowed(clientId)

### 22.3 Class Diagram
```
RateLimiter - strategy, configs
RateLimitStrategy (interface) → FixedWindow, SlidingWindowLog, TokenBucket
RateLimitConfig - maxRequests, windowSizeMs
```

### 22.4 Java Code

```java
// ── Config ──
public class RateLimitConfig {
    private int maxRequests;
    private long windowMs;

    public RateLimitConfig(int maxRequests, long windowMs) {
        this.maxRequests = maxRequests; this.windowMs = windowMs;
    }

    public int getMaxRequests() { return maxRequests; }
    public long getWindowMs() { return windowMs; }
}

// ── Strategy Interface ──
public interface RateLimitStrategy {
    boolean allowRequest(String clientId);
}

// ── Fixed Window ──
public class FixedWindowStrategy implements RateLimitStrategy {
    private final RateLimitConfig config;
    private final Map<String, long[]> windows; // clientId -> [windowStart, count]

    public FixedWindowStrategy(RateLimitConfig config) {
        this.config = config;
        this.windows = new ConcurrentHashMap<>();
    }

    @Override
    public boolean allowRequest(String clientId) {
        long now = System.currentTimeMillis();
        long[] window = windows.computeIfAbsent(clientId, k -> new long[]{now, 0});

        synchronized (window) {
            if (now - window[0] > config.getWindowMs()) {
                window[0] = now;
                window[1] = 0;
            }
            if (window[1] < config.getMaxRequests()) {
                window[1]++;
                return true;
            }
            return false;
        }
    }
}

// ── Sliding Window Log ──
public class SlidingWindowLogStrategy implements RateLimitStrategy {
    private final RateLimitConfig config;
    private final Map<String, Deque<Long>> logs;

    public SlidingWindowLogStrategy(RateLimitConfig config) {
        this.config = config;
        this.logs = new ConcurrentHashMap<>();
    }

    @Override
    public boolean allowRequest(String clientId) {
        long now = System.currentTimeMillis();
        Deque<Long> log = logs.computeIfAbsent(clientId, k -> new ConcurrentLinkedDeque<>());

        // Remove old entries
        while (!log.isEmpty() && now - log.peekFirst() > config.getWindowMs()) {
            log.pollFirst();
        }

        if (log.size() < config.getMaxRequests()) {
            log.addLast(now);
            return true;
        }
        return false;
    }
}

// ── Token Bucket ──
public class TokenBucketStrategy implements RateLimitStrategy {
    private final int maxTokens;
    private final long refillIntervalMs;
    private final Map<String, long[]> buckets; // [tokens, lastRefillTime]

    public TokenBucketStrategy(int maxTokens, long refillIntervalMs) {
        this.maxTokens = maxTokens;
        this.refillIntervalMs = refillIntervalMs;
        this.buckets = new ConcurrentHashMap<>();
    }

    @Override
    public boolean allowRequest(String clientId) {
        long now = System.currentTimeMillis();
        long[] bucket = buckets.computeIfAbsent(clientId,
                k -> new long[]{maxTokens, now});

        synchronized (bucket) {
            long elapsed = now - bucket[1];
            long tokensToAdd = elapsed / refillIntervalMs;
            bucket[0] = Math.min(maxTokens, bucket[0] + tokensToAdd);
            bucket[1] = now;

            if (bucket[0] > 0) {
                bucket[0]--;
                return true;
            }
            return false;
        }
    }
}

// ── Rate Limiter ──
public class RateLimiter {
    private final RateLimitStrategy strategy;

    public RateLimiter(RateLimitStrategy strategy) {
        this.strategy = strategy;
    }

    public boolean isAllowed(String clientId) {
        return strategy.allowRequest(clientId);
    }
}
```

### 22.5 Service Layer

```java
public class RateLimiterService {
    private final Map<String, RateLimiter> limiters; // api -> limiter

    public RateLimiterService() {
        this.limiters = new HashMap<>();
    }

    public void configureApi(String apiPath, RateLimitStrategy strategy) {
        limiters.put(apiPath, new RateLimiter(strategy));
    }

    public boolean checkRequest(String apiPath, String clientId) {
        RateLimiter limiter = limiters.get(apiPath);
        if (limiter == null) return true; // no limit configured

        boolean allowed = limiter.isAllowed(clientId);
        if (!allowed) {
            System.out.println("Rate limited: " + clientId + " on " + apiPath);
        }
        return allowed;
    }

    // Example setup
    public void setupDefaults() {
        // /api/search: 100 req per minute, fixed window
        configureApi("/api/search",
                new FixedWindowStrategy(new RateLimitConfig(100, 60_000)));

        // /api/login: 5 attempts per minute, token bucket
        configureApi("/api/login",
                new TokenBucketStrategy(5, 12_000)); // 1 token per 12 sec

        // /api/data: 50 req per minute, sliding window
        configureApi("/api/data",
                new SlidingWindowLogStrategy(new RateLimitConfig(50, 60_000)));
    }
}
```

---

## 23 · Splitwise (Expense Sharing) 🟡
**Pattern: Strategy**

### 23.1 Functional Requirements
1. Create groups, add members
2. Add expenses — split equally, by exact amount, or by percentage
3. Track balances (who owes whom)
4. Settle up / record payments
5. Expense history

### 23.2 Entities
- `SplitwiseUser` — id, name, email
- `SplitwiseGroup` — name, members, expenses
- `Expense` — description, amount, paidBy, splits list, split type
- `Split` — abstract (Equal/Exact/Percent); user + calculated amount
- `BalanceSheet` — nested map tracking who owes whom; update + settle + print

### 23.3 Class Diagram
```
Expense - id, description, amount, paidBy, splits[], group
Split (abstract) → EqualSplit, ExactSplit, PercentSplit
SplitStrategy → EqualSplitStrategy, ExactSplitStrategy, PercentSplitStrategy
BalanceSheet - Map<userId, Map<userId, Double>>
```

### 23.4 Java Code

```java
// ── Enums ──
public enum SplitType { EQUAL, EXACT, PERCENT }

// ── Split ──
public abstract class Split {
    protected SplitwiseUser user;
    protected double amount;

    public Split(SplitwiseUser user) { this.user = user; }

    public SplitwiseUser getUser() { return user; }
    public double getAmount() { return amount; }
    public void setAmount(double a) { this.amount = a; }
}

public class EqualSplit extends Split {
    public EqualSplit(SplitwiseUser user) { super(user); }
}

public class ExactSplit extends Split {
    public ExactSplit(SplitwiseUser user, double amount) {
        super(user);
        this.amount = amount;
    }
}

public class PercentSplit extends Split {
    private double percent;

    public PercentSplit(SplitwiseUser user, double percent) {
        super(user);
        this.percent = percent;
    }

    public double getPercent() { return percent; }
}

// ── SplitwiseUser ──
public class SplitwiseUser {
    private String id;
    private String name;
    private String email;

    public SplitwiseUser(String id, String name, String email) {
        this.id = id; this.name = name; this.email = email;
    }

    public String getId() { return id; }
    public String getName() { return name; }
}

// ── SplitwiseGroup ──
public class SplitwiseGroup {
    private String id;
    private String name;
    private List<SplitwiseUser> members;
    private List<Expense> expenses;

    public SplitwiseGroup(String id, String name) {
        this.id = id; this.name = name;
        this.members = new ArrayList<>();
        this.expenses = new ArrayList<>();
    }

    public void addMember(SplitwiseUser u) { members.add(u); }
    public void addExpense(Expense e) { expenses.add(e); }

    public String getId() { return id; }
    public List<SplitwiseUser> getMembers() { return members; }
    public List<Expense> getExpenses() { return expenses; }
}

// ── Expense ──
public class Expense {
    private String id;
    private String description;
    private double amount;
    private SplitwiseUser paidBy;
    private List<Split> splits;
    private SplitType splitType;

    public Expense(String id, String description, double amount,
                   SplitwiseUser paidBy, List<Split> splits, SplitType splitType) {
        this.id = id; this.description = description;
        this.amount = amount; this.paidBy = paidBy;
        this.splits = splits; this.splitType = splitType;
    }

    public String getId() { return id; }
    public double getAmount() { return amount; }
    public SplitwiseUser getPaidBy() { return paidBy; }
    public List<Split> getSplits() { return splits; }
    public SplitType getSplitType() { return splitType; }
}

// ── Balance Sheet ──
public class BalanceSheet {
    // balances[A][B] > 0 means A owes B that amount
    private Map<String, Map<String, Double>> balances;

    public BalanceSheet() {
        this.balances = new HashMap<>();
    }

    public void update(SplitwiseUser paidBy, SplitwiseUser owes, double amount) {
        String payerId = paidBy.getId();
        String owerId = owes.getId();
        if (payerId.equals(owerId)) return;

        // owes owes paidBy `amount`
        balances.computeIfAbsent(owerId, k -> new HashMap<>())
                .merge(payerId, amount, Double::sum);
        // Subtract reverse
        balances.computeIfAbsent(payerId, k -> new HashMap<>())
                .merge(owerId, -amount, Double::sum);
    }

    public void settle(String userId1, String userId2, double amount) {
        balances.getOrDefault(userId1, new HashMap<>())
                .merge(userId2, -amount, Double::sum);
        balances.getOrDefault(userId2, new HashMap<>())
                .merge(userId1, amount, Double::sum);
    }

    public void printBalances() {
        balances.forEach((user, owes) ->
                owes.forEach((other, amount) -> {
                    if (amount > 0.01) {
                        System.out.println(user + " owes " + other + ": ₹" +
                                String.format("%.2f", amount));
                    }
                }));
    }
}
```

### 23.5 Service Layer

```java
public class SplitwiseService {
    private Map<String, SplitwiseUser> users = new HashMap<>();
    private Map<String, SplitwiseGroup> groups = new HashMap<>();
    private BalanceSheet balanceSheet = new BalanceSheet();

    public SplitwiseUser registerUser(String id, String name, String email) {
        SplitwiseUser user = new SplitwiseUser(id, name, email);
        users.put(id, user);
        return user;
    }

    public SplitwiseGroup createGroup(String name, List<String> memberIds) {
        SplitwiseGroup group = new SplitwiseGroup(UUID.randomUUID().toString(), name);
        memberIds.forEach(id -> group.addMember(users.get(id)));
        groups.put(group.getId(), group);
        return group;
    }

    public void addExpense(String groupId, String paidById, double amount,
                           String description, SplitType splitType,
                           List<Split> splits) {
        SplitwiseUser paidBy = users.get(paidById);
        SplitwiseGroup group = groups.get(groupId);

        // Calculate splits based on type
        switch (splitType) {
            case EQUAL -> {
                double share = amount / splits.size();
                splits.forEach(s -> s.setAmount(share));
            }
            case PERCENT -> {
                for (Split s : splits) {
                    PercentSplit ps = (PercentSplit) s;
                    s.setAmount(amount * ps.getPercent() / 100);
                }
            }
            case EXACT -> {
                double total = splits.stream().mapToDouble(Split::getAmount).sum();
                if (Math.abs(total - amount) > 0.01) {
                    throw new RuntimeException("Exact splits don't add up to total");
                }
            }
        }

        Expense expense = new Expense(UUID.randomUUID().toString(),
                description, amount, paidBy, splits, splitType);
        group.addExpense(expense);

        // Update balances
        for (Split split : splits) {
            balanceSheet.update(paidBy, split.getUser(), split.getAmount());
        }

        System.out.println("Expense added: " + description + " ₹" + amount +
                " paid by " + paidBy.getName());
    }

    public void settleUp(String userId1, String userId2, double amount) {
        balanceSheet.settle(userId1, userId2, amount);
        System.out.println("Settlement recorded: ₹" + amount);
    }

    public void showBalances() {
        balanceSheet.printBalances();
    }
}
```

---

# HARD PROBLEMS (24-30)

---

## 24 · Ride Sharing (Uber/Ola) 🔴
**Pattern: Strategy, Observer, State**

### 24.1 Functional Requirements
1. Rider requests ride (pickup, drop)
2. Match nearest available driver
3. Driver accepts/rejects
4. Ride states: REQUESTED → ACCEPTED → IN_PROGRESS → COMPLETED → CANCELLED
5. Fare calculation (distance × rate + surge)
6. Rating system

### 24.2 Entities
- `Rider` — id, name, location, rating
- `Driver` — id, name, location, vehicle, availability, rating
- `Ride` — rider + driver + pickup/drop + fare + state; delegates to RideState
- `FareStrategy` — interface (Standard: base+rate×distance, Surge: wraps base×multiplier)
- `DriverMatchingStrategy` — interface (Nearest, HighestRated)
- `RideState` — interface (Requested→Accepted→InProgress→Completed/Cancelled)

### 24.3 Class Diagram
```
Rider - id, name, location, rating
Driver - id, name, location, vehicle, isAvailable, rating
Ride - id, rider, driver, pickup, drop, state, fare
FareStrategy → StandardFare, SurgeFare
MatchingStrategy → NearestDriver, HighestRatedDriver
RideState → Requested, Accepted, InProgress, Completed, Cancelled
```

### 24.4 Java Code

```java
// ── Enums ──
public enum RideStatus { REQUESTED, ACCEPTED, IN_PROGRESS, COMPLETED, CANCELLED }

// ── Rider ──
public class Rider {
    private String id;
    private String name;
    private Location location;
    private double rating;
    private int totalRides;

    public Rider(String id, String name, Location location) {
        this.id = id; this.name = name;
        this.location = location; this.rating = 5.0; this.totalRides = 0;
    }

    public void updateRating(double r) {
        totalRides++;
        rating = ((rating * (totalRides - 1)) + r) / totalRides;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public Location getLocation() { return location; }
    public double getRating() { return rating; }
}

// ── Driver ──
public class Driver {
    private String id;
    private String name;
    private Location location;
    private String vehicleNumber;
    private boolean isAvailable;
    private double rating;
    private int totalRides;

    public Driver(String id, String name, Location location, String vehicleNumber) {
        this.id = id; this.name = name;
        this.location = location; this.vehicleNumber = vehicleNumber;
        this.isAvailable = true; this.rating = 5.0; this.totalRides = 0;
    }

    public void setAvailable(boolean a) { this.isAvailable = a; }
    public void updateRating(double r) {
        totalRides++;
        rating = ((rating * (totalRides - 1)) + r) / totalRides;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public Location getLocation() { return location; }
    public void setLocation(Location l) { this.location = l; }
    public boolean isAvailable() { return isAvailable; }
    public double getRating() { return rating; }
}

// ── Fare Strategy ──
public interface FareStrategy {
    double calculate(Location pickup, Location drop);
}

public class StandardFareStrategy implements FareStrategy {
    private double ratePerKm;
    private double baseFare;

    public StandardFareStrategy(double baseFare, double ratePerKm) {
        this.baseFare = baseFare; this.ratePerKm = ratePerKm;
    }

    @Override
    public double calculate(Location pickup, Location drop) {
        double distance = pickup.distanceTo(drop);
        return baseFare + (distance * ratePerKm);
    }
}

public class SurgeFareStrategy implements FareStrategy {
    private FareStrategy baseFareStrategy;
    private double surgeMultiplier;

    public SurgeFareStrategy(FareStrategy base, double multiplier) {
        this.baseFareStrategy = base; this.surgeMultiplier = multiplier;
    }

    @Override
    public double calculate(Location pickup, Location drop) {
        return baseFareStrategy.calculate(pickup, drop) * surgeMultiplier;
    }
}

// ── Matching Strategy ──
public interface DriverMatchingStrategy {
    Driver findDriver(List<Driver> drivers, Location pickup);
}

public class NearestDriverStrategy implements DriverMatchingStrategy {
    @Override
    public Driver findDriver(List<Driver> drivers, Location pickup) {
        return drivers.stream()
                .filter(Driver::isAvailable)
                .min(Comparator.comparingDouble(d -> d.getLocation().distanceTo(pickup)))
                .orElseThrow(() -> new RuntimeException("No drivers available"));
    }
}

public class HighestRatedDriverStrategy implements DriverMatchingStrategy {
    private double maxDistance;

    public HighestRatedDriverStrategy(double maxDistance) { this.maxDistance = maxDistance; }

    @Override
    public Driver findDriver(List<Driver> drivers, Location pickup) {
        return drivers.stream()
                .filter(d -> d.isAvailable() && d.getLocation().distanceTo(pickup) <= maxDistance)
                .max(Comparator.comparingDouble(Driver::getRating))
                .orElseThrow(() -> new RuntimeException("No drivers in range"));
    }
}

// ── Ride State (State Pattern) ──
public interface RideState {
    void next(Ride ride);
    void cancel(Ride ride);
    String getStatus();
}

public class RequestedState implements RideState {
    @Override
    public void next(Ride ride) {
        ride.setState(new AcceptedState());
        System.out.println("Ride accepted by " + ride.getDriver().getName());
    }
    @Override
    public void cancel(Ride ride) {
        ride.setState(new CancelledState());
    }
    @Override public String getStatus() { return "REQUESTED"; }
}

public class AcceptedState implements RideState {
    @Override
    public void next(Ride ride) {
        ride.setState(new InProgressState());
        System.out.println("Ride started");
    }
    @Override
    public void cancel(Ride ride) { ride.setState(new CancelledState()); }
    @Override public String getStatus() { return "ACCEPTED"; }
}

public class InProgressState implements RideState {
    @Override
    public void next(Ride ride) {
        ride.setState(new CompletedState());
        ride.getDriver().setAvailable(true);
        System.out.println("Ride completed. Fare: ₹" + ride.getFare());
    }
    @Override
    public void cancel(Ride ride) {
        throw new RuntimeException("Cannot cancel ride in progress");
    }
    @Override public String getStatus() { return "IN_PROGRESS"; }
}

public class CompletedState implements RideState {
    @Override public void next(Ride ride) { throw new RuntimeException("Ride already completed"); }
    @Override public void cancel(Ride ride) { throw new RuntimeException("Ride already completed"); }
    @Override public String getStatus() { return "COMPLETED"; }
}

public class CancelledState implements RideState {
    @Override public void next(Ride ride) { throw new RuntimeException("Ride cancelled"); }
    @Override public void cancel(Ride ride) { throw new RuntimeException("Already cancelled"); }
    @Override public String getStatus() { return "CANCELLED"; }
}

// ── Ride ──
public class Ride {
    private String id;
    private Rider rider;
    private Driver driver;
    private Location pickup;
    private Location drop;
    private RideState state;
    private double fare;

    public Ride(String id, Rider rider, Driver driver, Location pickup,
                Location drop, double fare) {
        this.id = id; this.rider = rider; this.driver = driver;
        this.pickup = pickup; this.drop = drop;
        this.fare = fare; this.state = new RequestedState();
    }

    public void nextState() { state.next(this); }
    public void cancelRide() { state.cancel(this); }

    public void setState(RideState s) { this.state = s; }
    public String getId() { return id; }
    public Rider getRider() { return rider; }
    public Driver getDriver() { return driver; }
    public double getFare() { return fare; }
    public RideState getState() { return state; }
}
```

### 24.5 Service Layer

```java
public class RideService {
    private List<Driver> drivers = new ArrayList<>();
    private List<Rider> riders = new ArrayList<>();
    private Map<String, Ride> rides = new HashMap<>();
    private DriverMatchingStrategy matchingStrategy;
    private FareStrategy fareStrategy;

    public RideService(DriverMatchingStrategy matchingStrategy, FareStrategy fareStrategy) {
        this.matchingStrategy = matchingStrategy;
        this.fareStrategy = fareStrategy;
    }

    public void registerDriver(Driver d) { drivers.add(d); }
    public void registerRider(Rider r) { riders.add(r); }

    public Ride requestRide(String riderId, Location pickup, Location drop) {
        Rider rider = riders.stream().filter(r -> r.getId().equals(riderId))
                .findFirst().orElseThrow();
        Driver driver = matchingStrategy.findDriver(drivers, pickup);
        double fare = fareStrategy.calculate(pickup, drop);

        driver.setAvailable(false);
        Ride ride = new Ride(UUID.randomUUID().toString(), rider, driver, pickup, drop, fare);
        rides.put(ride.getId(), ride);

        System.out.println("Ride requested. Driver: " + driver.getName() +
                " | Fare: ₹" + String.format("%.2f", fare));
        return ride;
    }

    public void acceptRide(String rideId) { rides.get(rideId).nextState(); }
    public void startRide(String rideId) { rides.get(rideId).nextState(); }
    public void completeRide(String rideId) { rides.get(rideId).nextState(); }
    public void cancelRide(String rideId) { rides.get(rideId).cancelRide(); }

    public void rateDriver(String rideId, double rating) {
        rides.get(rideId).getDriver().updateRating(rating);
    }

    public void rateRider(String rideId, double rating) {
        rides.get(rideId).getRider().updateRating(rating);
    }

    public void enableSurge(double multiplier) {
        this.fareStrategy = new SurgeFareStrategy(fareStrategy, multiplier);
        System.out.println("Surge pricing enabled: " + multiplier + "x");
    }
}
```

---

## 25 · Cricket Scorecard (CricInfo) 🔴
**Pattern: Observer, Strategy**

### 25.1 Functional Requirements
1. Track match: teams, innings, overs, balls
2. Each ball: runs, wicket, extras (wide, no-ball)
3. Batsman stats: runs, balls faced, 4s, 6s, strike rate
4. Bowler stats: overs, runs given, wickets, economy
5. Live scorecard display, match result

### 25.2 Entities
- `CricketMatch` — two teams, innings list, determines result
- `Team` — name + player list
- `CricketPlayer` — name
- `Innings` — batting/bowling team, overs, runs, wickets, batsman/bowler score maps
- `Over` — over number, bowler, balls list; complete after 6 legal deliveries
- `Ball` — batsman, bowler, runs, wicket, extra type; knows if legal delivery
- `BatsmanScore` — runs, balls faced, 4s, 6s, strike rate, out status
- `BowlerScore` — balls bowled, runs conceded, wickets, economy

### 25.3 Class Diagram
```
Match - teams[2], innings[], result
Team - name, players[]
Innings - battingTeam, bowlingTeam, overs[], totalRuns, wickets
Over - number, balls[], bowler
Ball - runs, wicket, extra, batsman, bowler
Scorecard - batsmanScores, bowlerScores
```

### 25.4 Java Code

```java
// ── Enums ──
public enum ExtraType { NONE, WIDE, NO_BALL, BYE, LEG_BYE }

// ── Player ──
public class CricketPlayer {
    private String name;
    private boolean isBatsman;
    private boolean isBowler;

    public CricketPlayer(String name) { this.name = name; }

    public String getName() { return name; }
}

// ── Team ──
public class Team {
    private String name;
    private List<CricketPlayer> players;

    public Team(String name, List<CricketPlayer> players) {
        this.name = name; this.players = players;
    }

    public String getName() { return name; }
    public List<CricketPlayer> getPlayers() { return players; }
}

// ── Batsman Score ──
public class BatsmanScore {
    private CricketPlayer player;
    private int runs;
    private int ballsFaced;
    private int fours;
    private int sixes;
    private boolean isOut;

    public BatsmanScore(CricketPlayer player) {
        this.player = player;
    }

    public void addRuns(int r) {
        runs += r;
        ballsFaced++;
        if (r == 4) fours++;
        if (r == 6) sixes++;
    }

    public void faceDotBall() { ballsFaced++; }
    public void out() { this.isOut = true; }

    public double getStrikeRate() {
        return ballsFaced == 0 ? 0 : (runs * 100.0) / ballsFaced;
    }

    @Override
    public String toString() {
        return String.format("%s: %d(%d) [4s: %d, 6s: %d] SR: %.1f %s",
                player.getName(), runs, ballsFaced, fours, sixes,
                getStrikeRate(), isOut ? "OUT" : "NOT OUT");
    }

    public CricketPlayer getPlayer() { return player; }
    public int getRuns() { return runs; }
    public boolean isOut() { return isOut; }
}

// ── Bowler Score ──
public class BowlerScore {
    private CricketPlayer player;
    private int ballsBowled;
    private int runsConceded;
    private int wickets;

    public BowlerScore(CricketPlayer player) { this.player = player; }

    public void addBall(int runs, boolean wicket) {
        ballsBowled++;
        runsConceded += runs;
        if (wicket) wickets++;
    }

    public String getOvers() {
        return (ballsBowled / 6) + "." + (ballsBowled % 6);
    }

    public double getEconomy() {
        double overs = ballsBowled / 6.0;
        return overs == 0 ? 0 : runsConceded / overs;
    }

    @Override
    public String toString() {
        return String.format("%s: %s overs - %d/%d, Eco: %.1f",
                player.getName(), getOvers(), runsConceded, wickets, getEconomy());
    }

    public CricketPlayer getPlayer() { return player; }
}

// ── Ball ──
public class Ball {
    private CricketPlayer batsman;
    private CricketPlayer bowler;
    private int runs;
    private boolean isWicket;
    private ExtraType extra;

    public Ball(CricketPlayer batsman, CricketPlayer bowler, int runs,
                boolean isWicket, ExtraType extra) {
        this.batsman = batsman; this.bowler = bowler;
        this.runs = runs; this.isWicket = isWicket; this.extra = extra;
    }

    public CricketPlayer getBatsman() { return batsman; }
    public CricketPlayer getBowler() { return bowler; }
    public int getRuns() { return runs; }
    public boolean isWicket() { return isWicket; }
    public ExtraType getExtra() { return extra; }
    public boolean isLegalDelivery() { return extra != ExtraType.WIDE && extra != ExtraType.NO_BALL; }
}

// ── Over ──
public class Over {
    private int overNumber;
    private CricketPlayer bowler;
    private List<Ball> balls;

    public Over(int overNumber, CricketPlayer bowler) {
        this.overNumber = overNumber; this.bowler = bowler;
        this.balls = new ArrayList<>();
    }

    public void addBall(Ball ball) { balls.add(ball); }

    public boolean isComplete() {
        long legalDeliveries = balls.stream().filter(Ball::isLegalDelivery).count();
        return legalDeliveries >= 6;
    }

    public int getRunsInOver() { return balls.stream().mapToInt(Ball::getRuns).sum(); }
    public CricketPlayer getBowler() { return bowler; }
}

// ── Innings ──
public class Innings {
    private Team battingTeam;
    private Team bowlingTeam;
    private List<Over> overs;
    private int totalRuns;
    private int wickets;
    private int totalOvers;
    private Map<String, BatsmanScore> batsmanScores;
    private Map<String, BowlerScore> bowlerScores;

    public Innings(Team battingTeam, Team bowlingTeam, int totalOvers) {
        this.battingTeam = battingTeam; this.bowlingTeam = bowlingTeam;
        this.totalOvers = totalOvers; this.overs = new ArrayList<>();
        this.batsmanScores = new LinkedHashMap<>();
        this.bowlerScores = new LinkedHashMap<>();
    }

    public void recordBall(Ball ball) {
        // Update batsman score
        BatsmanScore bs = batsmanScores.computeIfAbsent(
                ball.getBatsman().getName(), k -> new BatsmanScore(ball.getBatsman()));
        if (ball.getRuns() > 0) bs.addRuns(ball.getRuns());
        else bs.faceDotBall();
        if (ball.isWicket()) { bs.out(); wickets++; }

        // Update bowler score
        BowlerScore bws = bowlerScores.computeIfAbsent(
                ball.getBowler().getName(), k -> new BowlerScore(ball.getBowler()));
        bws.addBall(ball.getRuns(), ball.isWicket());

        totalRuns += ball.getRuns();

        // Add to current over
        if (overs.isEmpty() || overs.get(overs.size() - 1).isComplete()) {
            overs.add(new Over(overs.size() + 1, ball.getBowler()));
        }
        overs.get(overs.size() - 1).addBall(ball);
    }

    public boolean isCompleted() {
        return wickets >= 10 || overs.size() >= totalOvers;
    }

    public void displayScorecard() {
        System.out.println("\n=== " + battingTeam.getName() + " Innings ===");
        System.out.println("Total: " + totalRuns + "/" + wickets);
        System.out.println("\nBatting:");
        batsmanScores.values().forEach(System.out::println);
        System.out.println("\nBowling:");
        bowlerScores.values().forEach(System.out::println);
    }

    public int getTotalRuns() { return totalRuns; }
    public int getWickets() { return wickets; }
    public Team getBattingTeam() { return battingTeam; }
}

// ── Match ──
public class CricketMatch {
    private Team team1;
    private Team team2;
    private List<Innings> innings;
    private int oversPerInnings;
    private String result;

    public CricketMatch(Team team1, Team team2, int oversPerInnings) {
        this.team1 = team1; this.team2 = team2;
        this.oversPerInnings = oversPerInnings;
        this.innings = new ArrayList<>();
    }

    public Innings startInnings(Team batting, Team bowling) {
        Innings inn = new Innings(batting, bowling, oversPerInnings);
        innings.add(inn);
        return inn;
    }

    public String determineResult() {
        if (innings.size() < 2) return "Match in progress";
        int team1Score = innings.get(0).getTotalRuns();
        int team2Score = innings.get(1).getTotalRuns();

        if (team1Score > team2Score) {
            result = innings.get(0).getBattingTeam().getName() + " wins by " +
                    (team1Score - team2Score) + " runs";
        } else if (team2Score > team1Score) {
            result = innings.get(1).getBattingTeam().getName() + " wins by " +
                    (10 - innings.get(1).getWickets()) + " wickets";
        } else {
            result = "Match tied";
        }
        return result;
    }

    public List<Innings> getInnings() { return innings; }
}
```

### 25.5 Service Layer

```java
public class CricketMatchService {
    private CricketMatch match;
    private Innings currentInnings;

    public void startMatch(Team team1, Team team2, int overs) {
        match = new CricketMatch(team1, team2, overs);
        System.out.println("Match: " + team1.getName() + " vs " + team2.getName() +
                " | " + overs + " overs");
    }

    public void startInnings(Team batting, Team bowling) {
        currentInnings = match.startInnings(batting, bowling);
        System.out.println("\n" + batting.getName() + " batting...");
    }

    public void recordBall(CricketPlayer batsman, CricketPlayer bowler,
                           int runs, boolean isWicket, ExtraType extra) {
        Ball ball = new Ball(batsman, bowler, runs, isWicket, extra);
        currentInnings.recordBall(ball);

        String desc = batsman.getName() + " → " + runs + " runs";
        if (isWicket) desc += " | WICKET!";
        System.out.println(desc);
    }

    public void showScorecard() {
        match.getInnings().forEach(Innings::displayScorecard);
    }

    public String getResult() {
        return match.determineResult();
    }
}
```

---

## 26 · LinkedIn 🔴
**Pattern: Observer**

### 26.1 Functional Requirements
1. User profile (experience, education, skills)
2. Connect / disconnect (connection requests)
3. Post content (text, image), like, comment
4. Feed: posts from connections sorted by time
5. Job posting, job application
6. Messaging

### 26.2 Entities
- `LinkedInUser` — id, name, profile, connections list, posts list
- `LinkedInProfile` — headline, experiences, education, skills
- `ConnectionRequest` — from/to users + status; accept() adds bidirectional connection
- `Post` — author, content, likes list, comments, timestamp
- `PostComment` — author + body + timestamp
- `JobPosting` — company, title, description, required skills, applications
- `JobApplication` — applicant + job + status (APPLIED→ACCEPTED/REJECTED)

### 26.3 Class Diagram
```
User - id, profile, connections[], posts[]
Profile - headline, experiences[], education[], skills[]
Connection - from, to, status(PENDING/ACCEPTED/REJECTED)
Post - id, author, content, likes[], comments[], timestamp
Job - id, company, title, description, applicants[]
```

### 26.4 Java Code

```java
// ── Enums ──
public enum ConnectionStatus { PENDING, ACCEPTED, REJECTED }
public enum ApplicationStatus { APPLIED, IN_REVIEW, ACCEPTED, REJECTED }

// ── Experience ──
public class Experience {
    private String title;
    private String company;
    private LocalDate from;
    private LocalDate to;

    public Experience(String title, String company, LocalDate from, LocalDate to) {
        this.title = title; this.company = company;
        this.from = from; this.to = to;
    }

    @Override
    public String toString() { return title + " at " + company; }
}

// ── Education ──
public class Education {
    private String degree;
    private String institution;
    private int year;

    public Education(String degree, String institution, int year) {
        this.degree = degree; this.institution = institution; this.year = year;
    }
}

// ── Profile ──
public class LinkedInProfile {
    private String headline;
    private List<Experience> experiences;
    private List<Education> educations;
    private List<String> skills;

    public LinkedInProfile(String headline) {
        this.headline = headline;
        this.experiences = new ArrayList<>();
        this.educations = new ArrayList<>();
        this.skills = new ArrayList<>();
    }

    public void addExperience(Experience e) { experiences.add(e); }
    public void addEducation(Education e) { educations.add(e); }
    public void addSkill(String skill) { skills.add(skill); }

    public String getHeadline() { return headline; }
    public List<Experience> getExperiences() { return experiences; }
    public List<String> getSkills() { return skills; }
}

// ── LinkedInUser ──
public class LinkedInUser {
    private String id;
    private String name;
    private String email;
    private LinkedInProfile profile;
    private List<LinkedInUser> connections;
    private List<Post> posts;

    public LinkedInUser(String id, String name, String email) {
        this.id = id; this.name = name; this.email = email;
        this.profile = new LinkedInProfile("");
        this.connections = new ArrayList<>();
        this.posts = new ArrayList<>();
    }

    public void addConnection(LinkedInUser user) { connections.add(user); }
    public void removeConnection(LinkedInUser user) { connections.remove(user); }
    public void addPost(Post post) { posts.add(post); }

    public String getId() { return id; }
    public String getName() { return name; }
    public LinkedInProfile getProfile() { return profile; }
    public List<LinkedInUser> getConnections() { return connections; }
    public List<Post> getPosts() { return posts; }
}

// ── Connection Request ──
public class ConnectionRequest {
    private String id;
    private LinkedInUser from;
    private LinkedInUser to;
    private ConnectionStatus status;
    private LocalDateTime createdAt;

    public ConnectionRequest(String id, LinkedInUser from, LinkedInUser to) {
        this.id = id; this.from = from; this.to = to;
        this.status = ConnectionStatus.PENDING;
        this.createdAt = LocalDateTime.now();
    }

    public void accept() {
        this.status = ConnectionStatus.ACCEPTED;
        from.addConnection(to);
        to.addConnection(from);
    }

    public void reject() { this.status = ConnectionStatus.REJECTED; }

    public String getId() { return id; }
    public LinkedInUser getFrom() { return from; }
    public LinkedInUser getTo() { return to; }
    public ConnectionStatus getStatus() { return status; }
}

// ── Post ──
public class Post {
    private String id;
    private LinkedInUser author;
    private String content;
    private List<LinkedInUser> likes;
    private List<PostComment> comments;
    private LocalDateTime timestamp;

    public Post(String id, LinkedInUser author, String content) {
        this.id = id; this.author = author; this.content = content;
        this.likes = new ArrayList<>(); this.comments = new ArrayList<>();
        this.timestamp = LocalDateTime.now();
    }

    public void addLike(LinkedInUser user) { likes.add(user); }
    public void addComment(PostComment comment) { comments.add(comment); }

    public String getId() { return id; }
    public LinkedInUser getAuthor() { return author; }
    public String getContent() { return content; }
    public int getLikeCount() { return likes.size(); }
    public LocalDateTime getTimestamp() { return timestamp; }
    public List<PostComment> getComments() { return comments; }
}

// ── PostComment ──
public class PostComment {
    private String id;
    private LinkedInUser author;
    private String body;
    private LocalDateTime timestamp;

    public PostComment(String id, LinkedInUser author, String body) {
        this.id = id; this.author = author; this.body = body;
        this.timestamp = LocalDateTime.now();
    }

    public String getBody() { return body; }
    public LinkedInUser getAuthor() { return author; }
}

// ── Job Posting ──
public class JobPosting {
    private String id;
    private String company;
    private String title;
    private String description;
    private List<String> requiredSkills;
    private List<JobApplication> applications;

    public JobPosting(String id, String company, String title,
                      String description, List<String> requiredSkills) {
        this.id = id; this.company = company;
        this.title = title; this.description = description;
        this.requiredSkills = requiredSkills;
        this.applications = new ArrayList<>();
    }

    public void addApplication(JobApplication app) { applications.add(app); }

    public String getId() { return id; }
    public String getTitle() { return title; }
    public String getCompany() { return company; }
    public List<String> getRequiredSkills() { return requiredSkills; }
    public List<JobApplication> getApplications() { return applications; }
}

// ── Job Application ──
public class JobApplication {
    private String id;
    private LinkedInUser applicant;
    private JobPosting job;
    private ApplicationStatus status;

    public JobApplication(String id, LinkedInUser applicant, JobPosting job) {
        this.id = id; this.applicant = applicant; this.job = job;
        this.status = ApplicationStatus.APPLIED;
    }

    public void setStatus(ApplicationStatus s) { this.status = s; }
    public LinkedInUser getApplicant() { return applicant; }
    public ApplicationStatus getStatus() { return status; }
}
```

### 26.5 Service Layer

```java
public class LinkedInService {
    private Map<String, LinkedInUser> users = new HashMap<>();
    private Map<String, ConnectionRequest> connectionRequests = new HashMap<>();
    private Map<String, Post> allPosts = new HashMap<>();
    private Map<String, JobPosting> jobs = new HashMap<>();

    public LinkedInUser registerUser(String id, String name, String email) {
        LinkedInUser user = new LinkedInUser(id, name, email);
        users.put(id, user);
        return user;
    }

    // ── Connection ──
    public ConnectionRequest sendConnectionRequest(String fromId, String toId) {
        LinkedInUser from = users.get(fromId);
        LinkedInUser to = users.get(toId);
        ConnectionRequest req = new ConnectionRequest(UUID.randomUUID().toString(), from, to);
        connectionRequests.put(req.getId(), req);
        System.out.println(from.getName() + " sent connection request to " + to.getName());
        return req;
    }

    public void respondToRequest(String requestId, boolean accept) {
        ConnectionRequest req = connectionRequests.get(requestId);
        if (accept) {
            req.accept();
            System.out.println(req.getTo().getName() + " accepted " + req.getFrom().getName());
        } else {
            req.reject();
        }
    }

    // ── Posts ──
    public Post createPost(String userId, String content) {
        LinkedInUser user = users.get(userId);
        Post post = new Post(UUID.randomUUID().toString(), user, content);
        user.addPost(post);
        allPosts.put(post.getId(), post);
        return post;
    }

    public void likePost(String userId, String postId) {
        allPosts.get(postId).addLike(users.get(userId));
    }

    public void commentOnPost(String userId, String postId, String body) {
        PostComment comment = new PostComment(UUID.randomUUID().toString(),
                users.get(userId), body);
        allPosts.get(postId).addComment(comment);
    }

    // ── Feed (posts from connections, sorted by time) ──
    public List<Post> getFeed(String userId) {
        LinkedInUser user = users.get(userId);
        return user.getConnections().stream()
                .flatMap(conn -> conn.getPosts().stream())
                .sorted(Comparator.comparing(Post::getTimestamp).reversed())
                .limit(50)
                .collect(Collectors.toList());
    }

    // ── Jobs ──
    public JobPosting postJob(String company, String title, String desc,
                              List<String> skills) {
        JobPosting job = new JobPosting(UUID.randomUUID().toString(),
                company, title, desc, skills);
        jobs.put(job.getId(), job);
        return job;
    }

    public JobApplication applyToJob(String userId, String jobId) {
        LinkedInUser user = users.get(userId);
        JobPosting job = jobs.get(jobId);
        JobApplication app = new JobApplication(UUID.randomUUID().toString(), user, job);
        job.addApplication(app);
        System.out.println(user.getName() + " applied to " + job.getTitle() +
                " at " + job.getCompany());
        return app;
    }

    public List<JobPosting> searchJobs(String keyword) {
        return jobs.values().stream()
                .filter(j -> j.getTitle().toLowerCase().contains(keyword.toLowerCase())
                        || j.getCompany().toLowerCase().contains(keyword.toLowerCase()))
                .collect(Collectors.toList());
    }
}
```

---

## 27 · Amazon Locker System 🔴
**Pattern: State, Strategy**

### 27.1 Functional Requirements
1. Locker sizes: Small, Medium, Large
2. Assign appropriate locker to package by size
3. Generate OTP for pickup
4. OTP expires in 3 days; package returned if uncollected
5. Locker freed after pickup or expiry

### 27.2 Entities
- `Locker` — id, size, locationId, state, current package, OTP; delegates to LockerState
- `LockerPackage` — id, orderId, size, deliveredAt
- `LockerState` — interface (Available/Occupied); handles assign/pickup/expire
- `LockerAssignmentStrategy` — interface; finds smallest available locker that fits

### 27.3 Class Diagram
```
LockerSystem - locations(Map<locationId, List<Locker>>)
Locker - id, size, location, state, package, otp
Package - id, orderId, size
LockerState → Available, Occupied, Expired
LockerAssignmentStrategy → SizeMatch, NearestAvailable
```

### 27.4 Java Code

```java
// ── Enums ──
public enum LockerSize { SMALL, MEDIUM, LARGE }

// ── LockerPackage ──
public class LockerPackage {
    private String id;
    private String orderId;
    private LockerSize size;
    private LocalDateTime deliveredAt;

    public LockerPackage(String id, String orderId, LockerSize size) {
        this.id = id; this.orderId = orderId; this.size = size;
    }

    public void markDelivered() { this.deliveredAt = LocalDateTime.now(); }

    public String getId() { return id; }
    public String getOrderId() { return orderId; }
    public LockerSize getSize() { return size; }
    public LocalDateTime getDeliveredAt() { return deliveredAt; }
}

// ── Locker State ──
public interface LockerState {
    void assignPackage(Locker locker, LockerPackage pkg);
    void pickupPackage(Locker locker, String otp);
    void expire(Locker locker);
    String getStateName();
}

public class AvailableLockerState implements LockerState {
    @Override
    public void assignPackage(Locker locker, LockerPackage pkg) {
        pkg.markDelivered();
        locker.setCurrentPackage(pkg);
        String otp = locker.generateOtp();
        locker.setState(new OccupiedLockerState());
        System.out.println("Package " + pkg.getId() + " assigned to locker " +
                locker.getId() + " | OTP: " + otp);
    }

    @Override
    public void pickupPackage(Locker locker, String otp) {
        System.out.println("Locker is empty");
    }

    @Override
    public void expire(Locker locker) {
        System.out.println("Locker is already available");
    }

    @Override public String getStateName() { return "AVAILABLE"; }
}

public class OccupiedLockerState implements LockerState {
    @Override
    public void assignPackage(Locker locker, LockerPackage pkg) {
        System.out.println("Locker is already occupied");
    }

    @Override
    public void pickupPackage(Locker locker, String otp) {
        if (!locker.getOtp().equals(otp)) {
            System.out.println("Invalid OTP");
            return;
        }
        System.out.println("Package picked up from locker " + locker.getId());
        locker.release();
    }

    @Override
    public void expire(Locker locker) {
        System.out.println("Locker " + locker.getId() + " expired. Returning package.");
        locker.release();
        locker.setState(new AvailableLockerState());
    }

    @Override public String getStateName() { return "OCCUPIED"; }
}

// ── Locker ──
public class Locker {
    private String id;
    private LockerSize size;
    private String locationId;
    private LockerState state;
    private LockerPackage currentPackage;
    private String otp;

    public Locker(String id, LockerSize size, String locationId) {
        this.id = id; this.size = size; this.locationId = locationId;
        this.state = new AvailableLockerState();
    }

    public String generateOtp() {
        this.otp = String.format("%06d", new Random().nextInt(999999));
        return otp;
    }

    public void release() {
        this.currentPackage = null;
        this.otp = null;
        this.state = new AvailableLockerState();
    }

    public boolean canFit(LockerSize packageSize) {
        return size.ordinal() >= packageSize.ordinal();
    }

    public boolean isAvailable() { return state.getStateName().equals("AVAILABLE"); }

    // Delegate to state
    public void assignPackage(LockerPackage pkg) { state.assignPackage(this, pkg); }
    public void pickupPackage(String otp) { state.pickupPackage(this, otp); }
    public void expirePackage() { state.expire(this); }

    // Getters/Setters
    public String getId() { return id; }
    public LockerSize getSize() { return size; }
    public String getLocationId() { return locationId; }
    public void setState(LockerState s) { this.state = s; }
    public LockerPackage getCurrentPackage() { return currentPackage; }
    public void setCurrentPackage(LockerPackage p) { this.currentPackage = p; }
    public String getOtp() { return otp; }
    public LockerState getState() { return state; }
}

// ── Assignment Strategy ──
public interface LockerAssignmentStrategy {
    Locker assign(List<Locker> lockers, LockerSize packageSize);
}

public class SizeMatchStrategy implements LockerAssignmentStrategy {
    @Override
    public Locker assign(List<Locker> lockers, LockerSize packageSize) {
        // Find smallest available locker that fits
        return lockers.stream()
                .filter(l -> l.isAvailable() && l.canFit(packageSize))
                .min(Comparator.comparingInt(l -> l.getSize().ordinal()))
                .orElseThrow(() -> new RuntimeException("No locker available"));
    }
}
```

### 27.5 Service Layer

```java
public class LockerService {
    private Map<String, List<Locker>> lockerLocations; // locationId -> lockers
    private Map<String, String> packageLockerMap;       // packageId -> lockerId
    private LockerAssignmentStrategy assignmentStrategy;
    private ScheduledExecutorService expiryService;

    public LockerService(LockerAssignmentStrategy strategy) {
        this.assignmentStrategy = strategy;
        this.lockerLocations = new HashMap<>();
        this.packageLockerMap = new HashMap<>();
        this.expiryService = Executors.newScheduledThreadPool(1);
    }

    public void addLockerLocation(String locationId, List<Locker> lockers) {
        lockerLocations.put(locationId, lockers);
    }

    public String assignLocker(LockerPackage pkg, String locationId) {
        List<Locker> lockers = lockerLocations.get(locationId);
        Locker locker = assignmentStrategy.assign(lockers, pkg.getSize());
        locker.assignPackage(pkg);
        packageLockerMap.put(pkg.getId(), locker.getId());

        // Schedule expiry after 3 days
        expiryService.schedule(() -> {
            if (!locker.isAvailable()) {
                locker.expirePackage();
            }
        }, 3, TimeUnit.DAYS);

        return locker.getOtp();
    }

    public boolean pickup(String lockerId, String otp) {
        for (List<Locker> lockers : lockerLocations.values()) {
            for (Locker l : lockers) {
                if (l.getId().equals(lockerId)) {
                    l.pickupPackage(otp);
                    return l.isAvailable(); // true if successfully picked up
                }
            }
        }
        return false;
    }

    public void displayStatus(String locationId) {
        lockerLocations.get(locationId).forEach(l ->
                System.out.println("Locker " + l.getId() + " [" + l.getSize() +
                        "] - " + l.getState().getStateName()));
    }
}
```

---

## 28 · Concert Ticket System 🔴
**Pattern: Observer, Strategy**

### 28.1 Functional Requirements
1. Events with venue, date, seat categories (VIP, Regular, Balcony)
2. Dynamic pricing (demand-based)
3. Seat hold (temporary lock) → confirm or release
4. Waitlist if sold out → notify when available
5. Bulk booking, refund on cancellation

### 28.2 Entities
- `Event` — name, venue, dateTime, seats by category; finds available seats
- `Venue` — id, name, city, capacity
- `ConcertSeat` — id, category, basePrice, status; synchronized hold()
- `ConcertBooking` — user + event + seats + total + status (HELD/CONFIRMED/CANCELLED)
- `ConcertPricingStrategy` — interface (Fixed, DemandBased: price rises as seats drop)
- `Waitlist` — per-category queue of observers; notifies next when seat freed

### 28.3 Class Diagram
```
Event - id, name, venue, dateTime, categories[]
SeatCategory - type, basePrice, seats[]
Seat - id, category, status(AVAILABLE/HELD/BOOKED)
ConcertBooking - id, user, seats[], total, status
PricingStrategy → FixedPricing, DemandBasedPricing
Waitlist (per event) - Queue<User>
```

### 28.4 Java Code

```java
// ── Enums ──
public enum ConcertSeatStatus { AVAILABLE, HELD, BOOKED }
public enum ConcertBookingStatus { HELD, CONFIRMED, CANCELLED }

// ── Venue ──
public class Venue {
    private String id;
    private String name;
    private String city;
    private int capacity;

    public Venue(String id, String name, String city, int capacity) {
        this.id = id; this.name = name;
        this.city = city; this.capacity = capacity;
    }

    public String getName() { return name; }
    public int getCapacity() { return capacity; }
}

// ── ConcertSeat ──
public class ConcertSeat {
    private String id;
    private String category;
    private double basePrice;
    private ConcertSeatStatus status;

    public ConcertSeat(String id, String category, double basePrice) {
        this.id = id; this.category = category;
        this.basePrice = basePrice;
        this.status = ConcertSeatStatus.AVAILABLE;
    }

    public synchronized boolean hold() {
        if (status != ConcertSeatStatus.AVAILABLE) return false;
        status = ConcertSeatStatus.HELD;
        return true;
    }

    public void book() { status = ConcertSeatStatus.BOOKED; }

    public void release() { status = ConcertSeatStatus.AVAILABLE; }

    public String getId() { return id; }
    public String getCategory() { return category; }
    public double getBasePrice() { return basePrice; }
    public ConcertSeatStatus getStatus() { return status; }
}

// ── Event ──
public class Event {
    private String id;
    private String name;
    private Venue venue;
    private LocalDateTime dateTime;
    private Map<String, List<ConcertSeat>> seatsByCategory;

    public Event(String id, String name, Venue venue, LocalDateTime dateTime) {
        this.id = id; this.name = name;
        this.venue = venue; this.dateTime = dateTime;
        this.seatsByCategory = new LinkedHashMap<>();
    }

    public void addSeats(String category, List<ConcertSeat> seats) {
        seatsByCategory.put(category, seats);
    }

    public List<ConcertSeat> getAvailableSeats(String category) {
        return seatsByCategory.getOrDefault(category, List.of()).stream()
                .filter(s -> s.getStatus() == ConcertSeatStatus.AVAILABLE)
                .collect(Collectors.toList());
    }

    public int getTotalAvailable() {
        return seatsByCategory.values().stream()
                .flatMap(List::stream)
                .filter(s -> s.getStatus() == ConcertSeatStatus.AVAILABLE)
                .mapToInt(s -> 1).sum();
    }

    public String getId() { return id; }
    public String getName() { return name; }
}

// ── ConcertPricingStrategy ──
public interface ConcertPricingStrategy {
    double calculatePrice(ConcertSeat seat, Event event);
}

public class FixedConcertPricing implements ConcertPricingStrategy {
    @Override
    public double calculatePrice(ConcertSeat seat, Event event) {
        return seat.getBasePrice();
    }
}

public class DemandBasedPricing implements ConcertPricingStrategy {
    @Override
    public double calculatePrice(ConcertSeat seat, Event event) {
        int available = event.getTotalAvailable();
        double demandMultiplier;
        if (available < 50) demandMultiplier = 2.0;
        else if (available < 200) demandMultiplier = 1.5;
        else demandMultiplier = 1.0;
        return seat.getBasePrice() * demandMultiplier;
    }
}

// ── Waitlist Observer ──
public interface WaitlistObserver {
    void onSeatAvailable(Event event, String category);
}

// ── Waitlist ──
public class Waitlist {
    private Map<String, Queue<WaitlistObserver>> waitlists; // category -> queue

    public Waitlist() { this.waitlists = new HashMap<>(); }

    public void addToWaitlist(String category, WaitlistObserver observer) {
        waitlists.computeIfAbsent(category, k -> new LinkedList<>()).add(observer);
        System.out.println("Added to waitlist for " + category);
    }

    public void notifyNext(Event event, String category) {
        Queue<WaitlistObserver> queue = waitlists.get(category);
        if (queue != null && !queue.isEmpty()) {
            WaitlistObserver next = queue.poll();
            next.onSeatAvailable(event, category);
        }
    }
}

// ── ConcertBooking ──
public class ConcertBooking {
    private String id;
    private User user;
    private Event event;
    private List<ConcertSeat> seats;
    private double totalAmount;
    private ConcertBookingStatus status;

    public ConcertBooking(String id, User user, Event event,
                          List<ConcertSeat> seats, double totalAmount) {
        this.id = id; this.user = user; this.event = event;
        this.seats = seats; this.totalAmount = totalAmount;
        this.status = ConcertBookingStatus.HELD;
    }

    public void confirm() { this.status = ConcertBookingStatus.CONFIRMED; }

    public void cancel() {
        this.status = ConcertBookingStatus.CANCELLED;
        seats.forEach(ConcertSeat::release);
    }

    public String getId() { return id; }
    public double getTotalAmount() { return totalAmount; }
    public ConcertBookingStatus getStatus() { return status; }
    public List<ConcertSeat> getSeats() { return seats; }
    public Event getEvent() { return event; }
}
```

### 28.5 Service Layer

```java
public class ConcertTicketService {
    private Map<String, Event> events = new HashMap<>();
    private Map<String, ConcertBooking> bookings = new HashMap<>();
    private Map<String, Waitlist> eventWaitlists = new HashMap<>();
    private ConcertPricingStrategy pricingStrategy;
    private ScheduledExecutorService holdTimer = Executors.newScheduledThreadPool(2);

    public ConcertTicketService(ConcertPricingStrategy pricingStrategy) {
        this.pricingStrategy = pricingStrategy;
    }

    public void addEvent(Event event) {
        events.put(event.getId(), event);
        eventWaitlists.put(event.getId(), new Waitlist());
    }

    public ConcertBooking holdSeats(User user, String eventId, String category, int count) {
        Event event = events.get(eventId);
        List<ConcertSeat> available = event.getAvailableSeats(category);

        if (available.size() < count) {
            throw new RuntimeException("Not enough seats. Available: " + available.size());
        }

        List<ConcertSeat> selected = available.subList(0, count);
        selected.forEach(ConcertSeat::hold);

        double total = selected.stream()
                .mapToDouble(s -> pricingStrategy.calculatePrice(s, event))
                .sum();

        ConcertBooking booking = new ConcertBooking(
                UUID.randomUUID().toString(), user, event, selected, total);
        bookings.put(booking.getId(), booking);

        // Auto-release after 10 minutes
        holdTimer.schedule(() -> {
            if (booking.getStatus() == ConcertBookingStatus.HELD) {
                booking.cancel();
                System.out.println("Hold expired for booking " + booking.getId());
            }
        }, 10, TimeUnit.MINUTES);

        System.out.println("Seats held. Total: ₹" + total + " | Confirm within 10 mins.");
        return booking;
    }

    public void confirmBooking(String bookingId, PaymentStrategy payment) {
        ConcertBooking booking = bookings.get(bookingId);
        payment.pay(booking.getTotalAmount());
        booking.confirm();
        booking.getSeats().forEach(ConcertSeat::book);
        System.out.println("Booking confirmed: " + bookingId);
    }

    public double cancelBooking(String bookingId) {
        ConcertBooking booking = bookings.get(bookingId);
        booking.cancel();

        // Notify waitlist
        Waitlist waitlist = eventWaitlists.get(booking.getEvent().getId());
        booking.getSeats().forEach(s ->
                waitlist.notifyNext(booking.getEvent(), s.getCategory()));

        double refund = booking.getTotalAmount() * 0.8; // 80% refund
        System.out.println("Cancelled. Refund: ₹" + refund);
        return refund;
    }

    public void joinWaitlist(String eventId, String category, WaitlistObserver observer) {
        eventWaitlists.get(eventId).addToWaitlist(category, observer);
    }
}
```

---

## 29 · Stock Exchange / Trading System 🔴
**Pattern: Observer, Strategy, Command**

### 29.1 Functional Requirements
1. Users place Buy/Sell orders (market, limit)
2. Order matching engine (price-time priority)
3. Order book per stock (buy heap, sell heap)
4. Execute matched trades
5. Portfolio tracking, trade history

### 29.2 Entities
- `Stock` — symbol, name, last traded price
- `StockOrder` — userId, stock, side (BUY/SELL), type (MARKET/LIMIT), price, qty; Comparable by price-time
- `OrderBook` — per stock; buy orders in MaxHeap, sell orders in MinHeap
- `MatchingEngine` — matches best buy ≥ best sell; creates Trades, handles partial fills
- `Trade` — buy order + sell order + execution price + qty + timestamp
- `Portfolio` — userId, holdings map (symbol→qty), realized P&L
- `TradeObserver` — notified on each trade execution

### 29.3 Class Diagram
```
Order - id, user, stock, side(BUY/SELL), type(MARKET/LIMIT), price, qty, timestamp
OrderBook (per stock) - buyOrders(MaxHeap), sellOrders(MinHeap)
MatchingEngine (Strategy) → PriceTimePriority
Trade - buyOrder, sellOrder, price, qty, executedAt
Portfolio - userId, holdings(Map<Stock, qty>), pnl
```

### 29.4 Java Code

```java
// ── Enums ──
public enum OrderSide { BUY, SELL }
public enum OrderType { MARKET, LIMIT }
public enum StockOrderStatus { OPEN, PARTIALLY_FILLED, FILLED, CANCELLED }

// ── Stock ──
public class Stock {
    private String symbol;
    private String name;
    private double lastTradedPrice;

    public Stock(String symbol, String name, double ltp) {
        this.symbol = symbol; this.name = name; this.lastTradedPrice = ltp;
    }

    public void updateLTP(double price) { this.lastTradedPrice = price; }
    public String getSymbol() { return symbol; }
    public double getLastTradedPrice() { return lastTradedPrice; }
}

// ── StockOrder ──
public class StockOrder implements Comparable<StockOrder> {
    private String id;
    private String userId;
    private Stock stock;
    private OrderSide side;
    private OrderType type;
    private double price;
    private int quantity;
    private int filledQuantity;
    private StockOrderStatus status;
    private LocalDateTime timestamp;

    public StockOrder(String id, String userId, Stock stock, OrderSide side,
                      OrderType type, double price, int quantity) {
        this.id = id; this.userId = userId; this.stock = stock;
        this.side = side; this.type = type;
        this.price = price; this.quantity = quantity;
        this.filledQuantity = 0; this.status = StockOrderStatus.OPEN;
        this.timestamp = LocalDateTime.now();
    }

    public void fill(int qty) {
        filledQuantity += qty;
        if (filledQuantity >= quantity) status = StockOrderStatus.FILLED;
        else status = StockOrderStatus.PARTIALLY_FILLED;
    }

    public int getRemainingQuantity() { return quantity - filledQuantity; }

    @Override
    public int compareTo(StockOrder other) {
        // For buy: higher price = higher priority (max-heap behavior)
        // For sell: lower price = higher priority (min-heap behavior)
        // Tie-break by time
        if (this.side == OrderSide.BUY) {
            int priceCmp = Double.compare(other.price, this.price); // reverse for max-heap
            return priceCmp != 0 ? priceCmp : this.timestamp.compareTo(other.timestamp);
        } else {
            int priceCmp = Double.compare(this.price, other.price);
            return priceCmp != 0 ? priceCmp : this.timestamp.compareTo(other.timestamp);
        }
    }

    public String getId() { return id; }
    public String getUserId() { return userId; }
    public Stock getStock() { return stock; }
    public OrderSide getSide() { return side; }
    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }
    public StockOrderStatus getStatus() { return status; }
}

// ── Trade ──
public class Trade {
    private String id;
    private StockOrder buyOrder;
    private StockOrder sellOrder;
    private double price;
    private int quantity;
    private LocalDateTime executedAt;

    public Trade(String id, StockOrder buyOrder, StockOrder sellOrder,
                 double price, int quantity) {
        this.id = id; this.buyOrder = buyOrder;
        this.sellOrder = sellOrder; this.price = price;
        this.quantity = quantity; this.executedAt = LocalDateTime.now();
    }

    @Override
    public String toString() {
        return String.format("Trade: %s %d @ ₹%.2f [%s ← %s]",
                buyOrder.getStock().getSymbol(), quantity, price,
                buyOrder.getUserId(), sellOrder.getUserId());
    }

    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }
    public StockOrder getBuyOrder() { return buyOrder; }
    public StockOrder getSellOrder() { return sellOrder; }
}

// ── OrderBook (per stock) ──
public class OrderBook {
    private Stock stock;
    private PriorityQueue<StockOrder> buyOrders;  // max-heap by price
    private PriorityQueue<StockOrder> sellOrders; // min-heap by price

    public OrderBook(Stock stock) {
        this.stock = stock;
        this.buyOrders = new PriorityQueue<>();
        this.sellOrders = new PriorityQueue<>();
    }

    public void addOrder(StockOrder order) {
        if (order.getSide() == OrderSide.BUY) buyOrders.add(order);
        else sellOrders.add(order);
    }

    public PriorityQueue<StockOrder> getBuyOrders() { return buyOrders; }
    public PriorityQueue<StockOrder> getSellOrders() { return sellOrders; }
    public Stock getStock() { return stock; }
}

// ── Matching Engine ──
public class MatchingEngine {
    public List<Trade> match(OrderBook book) {
        List<Trade> trades = new ArrayList<>();

        while (!book.getBuyOrders().isEmpty() && !book.getSellOrders().isEmpty()) {
            StockOrder buy = book.getBuyOrders().peek();
            StockOrder sell = book.getSellOrders().peek();

            // Match condition: buy price >= sell price
            if (buy.getPrice() >= sell.getPrice()) {
                int qty = Math.min(buy.getRemainingQuantity(), sell.getRemainingQuantity());
                double tradePrice = sell.getPrice(); // execute at sell price

                Trade trade = new Trade(UUID.randomUUID().toString(),
                        buy, sell, tradePrice, qty);
                trades.add(trade);

                buy.fill(qty);
                sell.fill(qty);
                book.getStock().updateLTP(tradePrice);

                if (buy.getStatus() == StockOrderStatus.FILLED) book.getBuyOrders().poll();
                if (sell.getStatus() == StockOrderStatus.FILLED) book.getSellOrders().poll();

                System.out.println(trade);
            } else {
                break; // no more matches possible
            }
        }
        return trades;
    }
}

// ── Portfolio ──
public class Portfolio {
    private String userId;
    private Map<String, Integer> holdings; // symbol -> qty
    private double realizedPnL;

    public Portfolio(String userId) {
        this.userId = userId;
        this.holdings = new HashMap<>();
        this.realizedPnL = 0;
    }

    public void addHolding(String symbol, int qty) {
        holdings.merge(symbol, qty, Integer::sum);
    }

    public void removeHolding(String symbol, int qty) {
        holdings.computeIfPresent(symbol, (k, v) -> v - qty <= 0 ? null : v - qty);
    }

    public void addPnL(double pnl) { this.realizedPnL += pnl; }

    public Map<String, Integer> getHoldings() { return holdings; }
    public double getRealizedPnL() { return realizedPnL; }
}

// ── Trade Observer ──
public interface TradeObserver {
    void onTrade(Trade trade);
}
```

### 29.5 Service Layer

```java
public class TradingService {
    private Map<String, OrderBook> orderBooks;     // symbol -> OrderBook
    private Map<String, Stock> stocks;
    private Map<String, Portfolio> portfolios;
    private List<Trade> tradeHistory;
    private MatchingEngine matchingEngine;
    private List<TradeObserver> observers;

    public TradingService() {
        this.orderBooks = new HashMap<>();
        this.stocks = new HashMap<>();
        this.portfolios = new HashMap<>();
        this.tradeHistory = new ArrayList<>();
        this.matchingEngine = new MatchingEngine();
        this.observers = new ArrayList<>();
    }

    public void addObserver(TradeObserver o) { observers.add(o); }

    public void registerStock(Stock stock) {
        stocks.put(stock.getSymbol(), stock);
        orderBooks.put(stock.getSymbol(), new OrderBook(stock));
    }

    public void registerUser(String userId) {
        portfolios.put(userId, new Portfolio(userId));
    }

    public StockOrder placeOrder(String userId, String symbol, OrderSide side,
                                 OrderType type, double price, int quantity) {
        Stock stock = stocks.get(symbol);
        StockOrder order = new StockOrder(UUID.randomUUID().toString(),
                userId, stock, side, type, price, quantity);

        OrderBook book = orderBooks.get(symbol);
        book.addOrder(order);
        System.out.println("Order placed: " + side + " " + quantity + " " +
                symbol + " @ ₹" + price);

        // Try to match
        List<Trade> newTrades = matchingEngine.match(book);
        tradeHistory.addAll(newTrades);

        // Update portfolios
        for (Trade trade : newTrades) {
            Portfolio buyerPortfolio = portfolios.get(trade.getBuyOrder().getUserId());
            Portfolio sellerPortfolio = portfolios.get(trade.getSellOrder().getUserId());

            buyerPortfolio.addHolding(symbol, trade.getQuantity());
            sellerPortfolio.removeHolding(symbol, trade.getQuantity());

            observers.forEach(o -> o.onTrade(trade));
        }

        return order;
    }

    public void showPortfolio(String userId) {
        Portfolio p = portfolios.get(userId);
        System.out.println("Portfolio for " + userId + ":");
        p.getHoldings().forEach((symbol, qty) ->
                System.out.println("  " + symbol + ": " + qty + " shares" +
                        " (LTP: ₹" + stocks.get(symbol).getLastTradedPrice() + ")"));
        System.out.println("  Realized P&L: ₹" + p.getRealizedPnL());
    }

    public void showOrderBook(String symbol) {
        OrderBook book = orderBooks.get(symbol);
        System.out.println("Order Book for " + symbol + ":");
        System.out.println("  Buy orders: " + book.getBuyOrders().size());
        System.out.println("  Sell orders: " + book.getSellOrders().size());
    }
}
```

---

## 30 · Task Management System (Jira-like) 🔴
**Pattern: State, Observer**

### 30.1 Functional Requirements
1. Create projects, create tasks/stories/bugs under projects
2. Task states: TODO → IN_PROGRESS → IN_REVIEW → DONE
3. Assign to users, set priority, sprint
4. Sub-tasks, dependencies (blocked-by)
5. Comments, activity log
6. Sprint board view

### 30.2 Entities
- `Project` — name, tasks, sprints, members
- `TaskItem` — title, type, state, priority, assignee, subtasks, dependencies, comments, activity log; checks deps before transition
- `TaskState` — interface (Todo→InProgress→InReview→Done); enforces valid transitions
- `Sprint` — name, date range, tasks, status (PLANNED/ACTIVE/COMPLETED)
- `TaskUser` — id, name, email
- `TaskComment` — author + body + timestamp
- `ActivityLog` — records who changed what field from old→new value + timestamp

### 30.3 Class Diagram
```
Project - id, name, tasks[], sprints[], members[]
Task - id, title, type(STORY/BUG/TASK), state, priority, assignee,
       sprint, subTasks[], dependencies[], comments[], log[]
Sprint - id, name, startDate, endDate, tasks[], status
TaskState → TodoState, InProgressState, InReviewState, DoneState
ActivityLog - user, action, field, oldValue, newValue, timestamp
```

### 30.4 Java Code

```java
// ── Enums ──
public enum TaskType { STORY, BUG, TASK, EPIC }
public enum TaskPriority { LOW, MEDIUM, HIGH, CRITICAL }
public enum SprintStatus { PLANNED, ACTIVE, COMPLETED }

// ── TaskState (State Pattern) ──
public interface TaskState {
    void moveForward(TaskItem task);
    void moveBackward(TaskItem task);
    String getStateName();
}

public class TodoState implements TaskState {
    @Override
    public void moveForward(TaskItem task) {
        task.setState(new InProgressState());
        System.out.println(task.getTitle() + " → IN PROGRESS");
    }

    @Override
    public void moveBackward(TaskItem task) {
        System.out.println("Already at initial state");
    }

    @Override public String getStateName() { return "TODO"; }
}

public class InProgressState implements TaskState {
    @Override
    public void moveForward(TaskItem task) {
        task.setState(new InReviewState());
        System.out.println(task.getTitle() + " → IN REVIEW");
    }

    @Override
    public void moveBackward(TaskItem task) {
        task.setState(new TodoState());
        System.out.println(task.getTitle() + " → TODO");
    }

    @Override public String getStateName() { return "IN_PROGRESS"; }
}

public class InReviewState implements TaskState {
    @Override
    public void moveForward(TaskItem task) {
        task.setState(new DoneState());
        System.out.println(task.getTitle() + " → DONE");
    }

    @Override
    public void moveBackward(TaskItem task) {
        task.setState(new InProgressState());
        System.out.println(task.getTitle() + " → IN PROGRESS");
    }

    @Override public String getStateName() { return "IN_REVIEW"; }
}

public class DoneState implements TaskState {
    @Override
    public void moveForward(TaskItem task) {
        System.out.println("Already at final state");
    }

    @Override
    public void moveBackward(TaskItem task) {
        task.setState(new InReviewState());
        System.out.println(task.getTitle() + " → IN REVIEW");
    }

    @Override public String getStateName() { return "DONE"; }
}

// ── TaskUser ──
public class TaskUser {
    private String id;
    private String name;
    private String email;

    public TaskUser(String id, String name, String email) {
        this.id = id; this.name = name; this.email = email;
    }

    public String getId() { return id; }
    public String getName() { return name; }
}

// ── ActivityLog ──
public class ActivityLog {
    private TaskUser user;
    private String action;
    private String field;
    private String oldValue;
    private String newValue;
    private LocalDateTime timestamp;

    public ActivityLog(TaskUser user, String action, String field,
                       String oldValue, String newValue) {
        this.user = user; this.action = action; this.field = field;
        this.oldValue = oldValue; this.newValue = newValue;
        this.timestamp = LocalDateTime.now();
    }

    @Override
    public String toString() {
        return String.format("[%s] %s %s: %s → %s (by %s)",
                timestamp.toLocalTime(), action, field, oldValue, newValue, user.getName());
    }
}

// ── TaskComment ──
public class TaskComment {
    private String id;
    private TaskUser author;
    private String body;
    private LocalDateTime timestamp;

    public TaskComment(String id, TaskUser author, String body) {
        this.id = id; this.author = author; this.body = body;
        this.timestamp = LocalDateTime.now();
    }

    public String getBody() { return body; }
    public TaskUser getAuthor() { return author; }
}

// ── TaskItem ──
public class TaskItem {
    private String id;
    private String title;
    private String description;
    private TaskType type;
    private TaskPriority priority;
    private TaskState state;
    private TaskUser assignee;
    private TaskUser reporter;
    private Sprint sprint;
    private List<TaskItem> subTasks;
    private List<TaskItem> dependencies; // blocked by these
    private List<TaskComment> comments;
    private List<ActivityLog> activityLog;

    public TaskItem(String id, String title, TaskType type, TaskUser reporter) {
        this.id = id; this.title = title; this.type = type;
        this.reporter = reporter; this.priority = TaskPriority.MEDIUM;
        this.state = new TodoState();
        this.subTasks = new ArrayList<>();
        this.dependencies = new ArrayList<>();
        this.comments = new ArrayList<>();
        this.activityLog = new ArrayList<>();
    }

    public void moveForward() {
        // Check dependencies
        boolean blocked = dependencies.stream()
                .anyMatch(d -> !d.getState().getStateName().equals("DONE"));
        if (blocked) {
            System.out.println(title + " is blocked by unfinished dependencies");
            return;
        }
        String oldState = state.getStateName();
        state.moveForward(this);
        logActivity(reporter, "status changed", "status", oldState, state.getStateName());
    }

    public void moveBackward() {
        String oldState = state.getStateName();
        state.moveBackward(this);
        logActivity(reporter, "status changed", "status", oldState, state.getStateName());
    }

    public void assign(TaskUser user) {
        String old = assignee != null ? assignee.getName() : "Unassigned";
        this.assignee = user;
        logActivity(user, "assigned", "assignee", old, user.getName());
    }

    public void addSubTask(TaskItem sub) { subTasks.add(sub); }
    public void addDependency(TaskItem dep) { dependencies.add(dep); }
    public void addComment(TaskComment c) { comments.add(c); }

    private void logActivity(TaskUser user, String action, String field,
                             String oldVal, String newVal) {
        activityLog.add(new ActivityLog(user, action, field, oldVal, newVal));
    }

    // Getters/Setters
    public String getId() { return id; }
    public String getTitle() { return title; }
    public TaskType getType() { return type; }
    public TaskState getState() { return state; }
    public void setState(TaskState s) { this.state = s; }
    public TaskPriority getPriority() { return priority; }
    public void setPriority(TaskPriority p) { this.priority = p; }
    public TaskUser getAssignee() { return assignee; }
    public Sprint getSprint() { return sprint; }
    public void setSprint(Sprint s) { this.sprint = s; }
    public List<TaskItem> getSubTasks() { return subTasks; }
    public List<ActivityLog> getActivityLog() { return activityLog; }
}

// ── Sprint ──
public class Sprint {
    private String id;
    private String name;
    private LocalDate startDate;
    private LocalDate endDate;
    private List<TaskItem> tasks;
    private SprintStatus status;

    public Sprint(String id, String name, LocalDate startDate, LocalDate endDate) {
        this.id = id; this.name = name;
        this.startDate = startDate; this.endDate = endDate;
        this.tasks = new ArrayList<>();
        this.status = SprintStatus.PLANNED;
    }

    public void addTask(TaskItem task) {
        tasks.add(task);
        task.setSprint(this);
    }

    public void start() { this.status = SprintStatus.ACTIVE; }
    public void complete() { this.status = SprintStatus.COMPLETED; }

    public String getId() { return id; }
    public String getName() { return name; }
    public List<TaskItem> getTasks() { return tasks; }
    public SprintStatus getStatus() { return status; }
}

// ── Project ──
public class Project {
    private String id;
    private String name;
    private List<TaskItem> tasks;
    private List<Sprint> sprints;
    private List<TaskUser> members;

    public Project(String id, String name) {
        this.id = id; this.name = name;
        this.tasks = new ArrayList<>();
        this.sprints = new ArrayList<>();
        this.members = new ArrayList<>();
    }

    public void addTask(TaskItem task) { tasks.add(task); }
    public void addSprint(Sprint sprint) { sprints.add(sprint); }
    public void addMember(TaskUser user) { members.add(user); }

    public String getId() { return id; }
    public String getName() { return name; }
    public List<TaskItem> getTasks() { return tasks; }
    public List<Sprint> getSprints() { return sprints; }
}
```

### 30.5 Service Layer

```java
public class TaskManagementService {
    private Map<String, Project> projects = new HashMap<>();
    private Map<String, TaskItem> allTasks = new HashMap<>();
    private Map<String, TaskUser> users = new HashMap<>();

    // ── User Management ──
    public TaskUser registerUser(String id, String name, String email) {
        TaskUser user = new TaskUser(id, name, email);
        users.put(id, user);
        return user;
    }

    // ── Project ──
    public Project createProject(String name) {
        Project project = new Project(UUID.randomUUID().toString(), name);
        projects.put(project.getId(), project);
        return project;
    }

    // ── Task CRUD ──
    public TaskItem createTask(String projectId, String title, TaskType type,
                               String reporterId) {
        TaskUser reporter = users.get(reporterId);
        TaskItem task = new TaskItem(UUID.randomUUID().toString(), title, type, reporter);
        allTasks.put(task.getId(), task);
        projects.get(projectId).addTask(task);
        System.out.println("Created: [" + type + "] " + title);
        return task;
    }

    public void assignTask(String taskId, String userId) {
        allTasks.get(taskId).assign(users.get(userId));
    }

    public void moveTaskForward(String taskId) {
        allTasks.get(taskId).moveForward();
    }

    public void moveTaskBackward(String taskId) {
        allTasks.get(taskId).moveBackward();
    }

    // ── Sub-tasks & Dependencies ──
    public void addSubTask(String parentId, String childId) {
        allTasks.get(parentId).addSubTask(allTasks.get(childId));
    }

    public void addDependency(String taskId, String blockedById) {
        allTasks.get(taskId).addDependency(allTasks.get(blockedById));
        System.out.println(allTasks.get(taskId).getTitle() + " blocked by " +
                allTasks.get(blockedById).getTitle());
    }

    // ── Comments ──
    public void addComment(String taskId, String userId, String body) {
        TaskComment comment = new TaskComment(UUID.randomUUID().toString(),
                users.get(userId), body);
        allTasks.get(taskId).addComment(comment);
    }

    // ── Sprint ──
    public Sprint createSprint(String projectId, String name,
                               LocalDate start, LocalDate end) {
        Sprint sprint = new Sprint(UUID.randomUUID().toString(), name, start, end);
        projects.get(projectId).addSprint(sprint);
        return sprint;
    }

    public void addTaskToSprint(String sprintId, String taskId, String projectId) {
        Project project = projects.get(projectId);
        Sprint sprint = project.getSprints().stream()
                .filter(s -> s.getId().equals(sprintId))
                .findFirst().orElseThrow();
        sprint.addTask(allTasks.get(taskId));
    }

    // ── Sprint Board View ──
    public void showSprintBoard(String sprintId, String projectId) {
        Project project = projects.get(projectId);
        Sprint sprint = project.getSprints().stream()
                .filter(s -> s.getId().equals(sprintId))
                .findFirst().orElseThrow();

        Map<String, List<TaskItem>> board = sprint.getTasks().stream()
                .collect(Collectors.groupingBy(t -> t.getState().getStateName()));

        System.out.println("\n═══ Sprint Board: " + sprint.getName() + " ═══");
        for (String state : List.of("TODO", "IN_PROGRESS", "IN_REVIEW", "DONE")) {
            System.out.println("\n── " + state + " ──");
            board.getOrDefault(state, List.of()).forEach(t ->
                    System.out.println("  [" + t.getType() + "] " + t.getTitle() +
                            (t.getAssignee() != null ?
                                    " → " + t.getAssignee().getName() : " (unassigned)")));
        }
    }

    // ── Activity Log ──
    public void showActivityLog(String taskId) {
        System.out.println("Activity log for: " + allTasks.get(taskId).getTitle());
        allTasks.get(taskId).getActivityLog().forEach(System.out::println);
    }
}
```

---

# PATTERN CHEAT SHEET

| Pattern | When to Use | Problems |
|---------|------------|----------|
| **Singleton** | One global instance | Parking Lot, Logger, KV Store, Job Scheduler |
| **Strategy** | Swap algorithms at runtime | Parking fees, Elevator scheduling, Payment, Fare, Split, Rate Limit, Pricing, Matching |
| **State** | Object behavior changes with state | Vending Machine, ATM, Traffic Signal, Elevator, Ride Sharing, Amazon Locker, Task Mgmt |
| **Observer** | Notify dependents on changes | Q&A, BookMyShow, Food Delivery, Chat, Cricket, LinkedIn, Waitlist, Stock Exchange |
| **Chain of Responsibility** | Pass request through handler chain | Logger, ATM Cash Dispenser |
| **Factory** | Create objects without specifying class | Notification Channels, Chess Pieces |
| **Composite** | Tree / part-whole hierarchies | In-Memory File System |
| **Mediator** | Centralized communication hub | Chat Application |
| **Command** | Encapsulate request as object | Stock Orders |

---

# IMPORTS REFERENCE

```java
import java.util.*;
import java.util.concurrent.*;
import java.util.stream.*;
import java.time.*;
import java.time.temporal.ChronoUnit;
```

---

> **End of LLD Handbook — 30 Complete Problems with Java Implementations**
