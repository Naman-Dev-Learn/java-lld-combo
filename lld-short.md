# LLD Handbook — 30 Problems (Complete Interview Reference)

> Each problem: **Functional Requirements → Entities → Class Diagram → Pattern → Key Code → Service Layer → Driver Code → Interview Tips**

---

# 📑 Index (Click to Navigate)

### Framework & Theory
- [PART 0 · Interview Approach (10-Min Framework)](#part-0--how-to-approach-lld-in-interviews-the-10-minute-framework)
- [PART 0.5 · SOLID Principles](#part-05--solid-principles-reference-every-problem-against-these)
- [PART 1 · Design Patterns Deep Dive](#part-1--design-patterns-deep-dive) — [Singleton](#pattern-1-singleton) · [Strategy](#pattern-2-strategy) · [State](#pattern-3-state) · [Observer](#pattern-4-observer) · [Chain of Resp](#pattern-5-chain-of-responsibility) · [Factory](#pattern-6-factory) · [Composite](#pattern-7-composite) · [Mediator](#pattern-8-mediator) · [Command](#pattern-9-command)
- [PART 1.5 · Reusable Building Blocks](#part-15--common-building-blocks-reusable-across-problems)

### 🟢 Easy (1-7)
| # | Problem | Jump |
|---|---------|------|
| 1 | Parking Lot | [→ Go](#1--parking-lot-) |
| 2 | Logger System | [→ Go](#2--logger-system-) |
| 3 | Vending Machine | [→ Go](#3--vending-machine-) |
| 4 | Stack Overflow (Q&A) | [→ Go](#4--stack-overflow-qa-platform-) |
| 5 | Library Management | [→ Go](#5--library-management-) |
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
| 17 | Hotel Booking | [→ Go](#17--hotel-booking-) |
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
| 27 | Amazon Locker | [→ Go](#27--amazon-locker-) |
| 28 | Concert Tickets | [→ Go](#28--concert-ticket-system-) |
| 29 | Stock Exchange | [→ Go](#29--stock-exchange--trading-system-) |
| 30 | Task Management (Jira) | [→ Go](#30--task-management-jira-like-) |

### Reference
- [Pattern Cheat Sheet](#pattern-cheat-sheet) · [PART 3 · Revision Tables](#part-3--quick-revision-tables) · [PART 4 · Top 10 Mistakes](#part-4--top-10-interview-mistakes-in-lld) · [PART 5 · Similarity Map](#part-5--problem-similarity-map-if-you-know-x-you-can-solve-y)

---

# PART 0 · How to Approach LLD in Interviews (The 10-Minute Framework)

## Step-by-Step (follow this EVERY time)

| Min | Step | What to Do |
|-----|------|-----------|
| 0-2 | **Clarify** | Ask 3-5 questions. Scope it down. "Do we need multi-threading?" "Single or multi-server?" "Payment needed?" |
| 2-4 | **Requirements** | List 5-7 functional requirements. Write them on the board. Get interviewer's nod. |
| 4-5 | **Entities** | Identify core nouns → these become your classes. |
| 5-7 | **Class Diagram** | Draw entities + relationships. Show key attributes + methods. Identify interfaces. |
| 7-9 | **Code** | Write the core classes + one service method. Use design patterns where natural. |
| 9-10 | **Extend** | Mention how you'd handle: concurrency, scaling, persistence, edge cases. |

## Clarification Questions Cheat Sheet (ask these!)
- **Users**: How many concurrent users? Single or multi-tenant?
- **Scale**: In-memory only? Or persist to DB?
- **Concurrency**: Multi-threaded access? Locking strategy?
- **Scope**: Which features are must-have vs nice-to-have?
- **Real-time**: Do we need notifications / live updates?

## Red Flags (avoid these)
- ❌ Jumping to code without requirements
- ❌ Using design patterns just to show off (use them when they FIT)
- ❌ Ignoring edge cases when asked
- ❌ God class / putting everything in one class
- ❌ Public fields everywhere / no encapsulation
- ❌ Tight coupling between classes

## Green Flags (interviewer loves these)
- ✅ Start with requirements, get confirmation
- ✅ Identify the right pattern and explain WHY
- ✅ Program to interfaces, not implementations
- ✅ Mention thread safety where relevant
- ✅ Discuss trade-offs (e.g., "I chose Strategy over if-else because...")
- ✅ Bring up edge cases proactively

---

# PART 0.5 · SOLID Principles (Reference Every Problem Against These)

| Principle | Rule | Example |
|-----------|------|---------|
| **S** — Single Responsibility | Each class does ONE thing | `ParkingSpot` only manages spot state; `FeeStrategy` only calculates fees |
| **O** — Open/Closed | Open for extension, closed for modification | Add `SurgeFareStrategy` without changing `RideService` |
| **L** — Liskov Substitution | Subclass must be usable wherever parent is used | Any `Vehicle` subclass works with `ParkingSpot.canFit(Vehicle)` |
| **I** — Interface Segregation | Don't force classes to implement unused methods | `Readable` and `Writable` interfaces instead of one fat `FileOperations` |
| **D** — Dependency Inversion | Depend on abstractions, not concretions | `BookingService` depends on `PaymentStrategy` interface, not `CardPayment` directly |

### Quick SOLID Violations to Watch For
```
❌ class OrderService { // does ordering, payment, notification, inventory → violates S
❌ if (type == "CARD") ... else if (type == "UPI") ... → violates O (add new type = modify code)
❌ class Square extends Rectangle { setWidth() { also sets height } } → violates L
❌ interface Worker { work(); eat(); sleep(); } → violates I for robots
❌ class ReportGenerator { MySQLDatabase db = new MySQLDatabase(); } → violates D
```

---

# PART 1 · Design Patterns Deep Dive

## Pattern 1: Singleton
**When**: Exactly one instance needed globally (DB connection pool, Logger, Config)
**Watch out**: Makes unit testing harder. Consider DI as alternative.
```java
public class Singleton {
    private static volatile Singleton instance;       // volatile for thread safety
    private Singleton() {}                             // private constructor
    public static Singleton getInstance() {
        if (instance == null) {                        // first check (no lock)
            synchronized (Singleton.class) {
                if (instance == null) {                // second check (with lock)
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
**Interview tip**: Always use double-checked locking + `volatile`. Explain why both checks are needed.

---

## Pattern 2: Strategy
**When**: Multiple algorithms that are interchangeable at runtime.
**Red flag to use it**: You see `if-else` or `switch` on type to choose behavior.
```java
// Instead of:
if (payType == "CARD") { ... }
else if (payType == "UPI") { ... }

// Use:
interface PaymentStrategy { void pay(double amount); }
class CardPayment implements PaymentStrategy { ... }
class UPIPayment implements PaymentStrategy { ... }

// Client just calls:
paymentStrategy.pay(amount); // strategy injected at runtime
```
**SOLID link**: Satisfies Open/Closed — add new strategy without modifying existing code.

---

## Pattern 3: State
**When**: Object behavior changes based on its internal state. You see a state machine.
**Red flag to use it**: Multiple `if (state == X)` scattered in methods.
```java
interface State {
    void handle(Context ctx);
}
class IdleState implements State {
    void handle(Context ctx) {
        // do idle stuff
        ctx.setState(new ActiveState()); // transition
    }
}
```
**Key**: State transitions are defined INSIDE state classes, not in the main class.

---

## Pattern 4: Observer
**When**: One-to-many dependency. When one object changes, all dependents are notified.
```java
interface Observer { void update(Event event); }
interface Observable {
    void subscribe(Observer o);
    void unsubscribe(Observer o);
    void notifyAll(Event e);
}
class EventSource implements Observable {
    List<Observer> observers = new ArrayList<>();
    void notifyAll(Event e) { observers.forEach(o -> o.update(e)); }
}
```
**Use in**: Notifications, live feeds, real-time updates, waitlists

---

## Pattern 5: Chain of Responsibility
**When**: Request passes through a chain of handlers. Each handler decides: process or forward.
```java
abstract class Handler {
    Handler next;
    void handle(Request req) {
        if (canHandle(req)) process(req);
        else if (next != null) next.handle(req);
    }
    abstract boolean canHandle(Request req);
    abstract void process(Request req);
}
```
**Use in**: Logger (level filtering), ATM (cash denominations), Middleware, Validation chains

---

## Pattern 6: Factory
**When**: Create objects without exposing creation logic. Caller doesn't know concrete class.
```java
class ChannelFactory {
    static NotificationChannel create(ChannelType type) {
        return switch (type) {
            case EMAIL -> new EmailChannel();
            case SMS   -> new SMSChannel();
            case PUSH  -> new PushChannel();
        };
    }
}
```

---

## Pattern 7: Composite
**When**: Part-whole hierarchies (tree structures). Treat individual and group objects uniformly.
```java
abstract class Entry { abstract int getSize(); }
class File extends Entry { int getSize() { return content.length(); } }
class Directory extends Entry {
    List<Entry> children;
    int getSize() { return children.stream().mapToInt(Entry::getSize).sum(); }
}
```
**Use in**: File system, UI components, Org chart

---

## Pattern 8: Mediator
**When**: Many objects communicate with each other → centralize via mediator to reduce coupling.
```java
class ChatMediator {
    void sendMessage(User sender, String msg, Group group) {
        group.getMembers().stream()
            .filter(u -> !u.equals(sender))
            .forEach(u -> u.receive(msg));
    }
}
```

---

## Pattern 9: Command
**When**: Encapsulate a request as an object. Supports undo, queuing, logging.
```java
interface Command { void execute(); void undo(); }
class PlaceOrderCommand implements Command {
    void execute() { orderBook.add(order); }
    void undo() { orderBook.remove(order); }
}
```
**Use in**: Stock orders, text editor undo/redo, macro recording

---

# PART 1.5 · Common Building Blocks (Reusable Across Problems)

### Payment Module
```java
interface PaymentStrategy { boolean pay(double amount); }
class CardPayment implements PaymentStrategy { ... }
class UPIPayment implements PaymentStrategy { ... }
class WalletPayment implements PaymentStrategy { ... }
// → Used in: BookMyShow, Shopping Cart, Food Delivery, Hotel, Ride Sharing, Concert
```

### Location / Distance
```java
class Location {
    double lat, lng;
    double distanceTo(Location other) {
        return Math.sqrt(Math.pow(lat-other.lat,2) + Math.pow(lng-other.lng,2));
    }
}
// → Used in: Food Delivery, Ride Sharing, Amazon Locker
```

### Notification Module
```java
interface NotificationChannel { void send(String userId, String msg); }
// → Used in: BookMyShow, Food Delivery, Hotel, Amazon Locker, Concert, Task Mgmt
```

### Observer Module
```java
interface Observer<T> { void onEvent(T event); }
interface Observable<T> {
    void subscribe(Observer<T> o);
    void unsubscribe(Observer<T> o);
    void notifyObservers(T event);
}
// → Used in: 15+ problems
```

### Status / State Machine
```java
enum Status { CREATED, ACTIVE, COMPLETED, CANCELLED }
// Common pattern: can only transition forward (or to CANCELLED)
// → Used in: Orders, Rides, Bookings, Tasks, Loans
```

---

## Table of Contents

| # | Problem | Difficulty | Pattern(s) |
|---|---------|-----------|------------|
| 1 | Parking Lot | Easy | Strategy, Singleton |
| 2 | Logger System | Easy | Singleton, Chain of Responsibility |
| 3 | Vending Machine | Easy | State |
| 4 | Stack Overflow (Q&A) | Easy | Observer |
| 5 | Library Management | Easy | — |
| 6 | Tic-Tac-Toe | Easy | — |
| 7 | Traffic Signal | Easy | State |
| 8 | Elevator System | Medium | Strategy, State, Observer |
| 9 | In-Memory Key-Value Store | Medium | Singleton |
| 10 | LRU Cache | Medium | — |
| 11 | Movie Ticket Booking (BookMyShow) | Medium | Strategy, Observer |
| 12 | Chess Game | Medium | Strategy, Factory |
| 13 | Snake & Ladder | Medium | — |
| 14 | ATM Machine | Medium | State, Chain of Responsibility |
| 15 | Online Shopping Cart | Medium | Strategy, Observer |
| 16 | Food Delivery (Swiggy/Zomato) | Medium | Strategy, Observer |
| 17 | Hotel Booking | Medium | Strategy, Observer |
| 18 | File System (In-Memory) | Medium | Composite |
| 19 | Notification Service | Medium | Observer, Strategy, Factory |
| 20 | Chat Application | Medium | Observer, Mediator |
| 21 | In-Memory Job Scheduler | Medium | Strategy, Observer |
| 22 | Rate Limiter | Medium | Strategy |
| 23 | Splitwise (Expense Sharing) | Medium | Strategy |
| 24 | Ride Sharing (Uber/Ola) | Hard | Strategy, Observer, State |
| 25 | Cricket Scorecard (CricInfo) | Hard | Observer, Strategy |
| 26 | LinkedIn | Hard | Observer |
| 27 | Amazon Locker | Hard | State, Strategy |
| 28 | Concert Ticket System | Hard | Observer, Strategy |
| 29 | Stock Exchange / Trading System | Hard | Observer, Strategy, Command |
| 30 | Task Management (Jira-like) | Hard | State, Observer |

---

## Difficulty Legend
- **Easy** — Straightforward entities, 1-2 patterns, ≤5 classes
- **Medium** — Multiple interacting entities, 2-3 patterns, 5-10 classes
- **Hard** — Complex flows, concurrency concerns, 3+ patterns, 10+ classes

> Full Java code for every problem → see **lld-code.md**

---

## 1 · Parking Lot 🟢

### Functional Requirements
1. Multiple floors, each with multiple spots (Compact, Large, Handicapped)
2. Vehicle types: Car, Truck, Motorcycle → park in compatible spots
3. Assign nearest available spot on entry
4. Free spot on exit, calculate fee by duration
5. Display available spots per floor

### Entities
`ParkingLot` · `Floor` · `ParkingSpot` · `Vehicle` · `Ticket` · `FeeStrategy`

### Class Diagram
```
ParkingLot (singleton)
 └── List<Floor>
      └── List<ParkingSpot>
           - spotId, spotType, isOccupied, vehicle

Vehicle (abstract)
 ├── Car
 ├── Truck
 └── Motorcycle
     - licensePlate, vehicleType

Ticket
 - ticketId, vehicle, spot, entryTime, exitTime

FeeStrategy (interface)
 └── HourlyFeeStrategy
 └── FlatFeeStrategy
```

### Pattern: **Strategy** (fee calculation), **Singleton** (ParkingLot)

### Key Code Sketch
```java
public class ParkingLot {
    private static ParkingLot instance;
    private List<Floor> floors;
    private FeeStrategy feeStrategy;

    public Ticket parkVehicle(Vehicle v) { ... }
    public double unparkVehicle(Ticket t) { ... }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class ParkingLotDemo {
    public static void main(String[] args) {
        // 1. Setup
        ParkingLot lot = ParkingLot.getInstance();
        lot.setFeeStrategy(new HourlyFeeStrategy());
        lot.addFloor(new Floor(1, createSpots(10, SpotType.COMPACT)));
        lot.addFloor(new Floor(2, createSpots(5, SpotType.LARGE)));

        // 2. Vehicle enters → get ticket
        Vehicle car = new Car("KA-01-1234");
        Ticket ticket = lot.parkVehicle(car);
        // → Finds floor 1, spot C-01, marks occupied, returns ticket

        // 3. Display availability
        lot.displayAvailability();
        // → Floor 1: COMPACT: 9 available | Floor 2: LARGE: 5 available

        // 4. Vehicle exits → pay fee
        double fee = lot.unparkVehicle(ticket.getTicketId());
        // → Calculates hours parked × rate, releases spot
        System.out.println("Fee: ₹" + fee);
    }
}
```
**Flow**: `Vehicle enters` → `ParkingLot.parkVehicle()` → `Floor.findAvailableSpot()` → `Spot.occupy()` → `return Ticket` → ... → `unparkVehicle(ticketId)` → `FeeStrategy.calculateFee(ticket)` → `Spot.release()`

### 🎯 Interview Tips
- **Why Strategy?** "Fee calculation varies — hourly, flat, dynamic. Strategy lets us swap without touching ParkingLot."
- **Why Singleton?** "Only one parking lot instance system-wide. But mention DI as alternative."
- **Follow-up: Concurrency** — Multiple vehicles entering simultaneously → `synchronized` on `findAvailableSpot()` or use `ConcurrentHashMap` for spot status
- **Follow-up: Nearest spot** — BFS from entrance or maintain a sorted data structure per floor
- **Edge cases**: Lot full, vehicle already parked, invalid ticket, power failure mid-transaction
- **Design decision**: Vehicle → Spot compatibility as a method in `ParkingSpot.canFit(Vehicle)` keeps logic close to data
- **Extend**: EV charging spots, reserved/VIP spots, multi-entry/exit gates

---

## 2 · Logger System 🟢

### Functional Requirements
1. Log levels: DEBUG, INFO, WARN, ERROR, FATAL
2. Each logger level can forward to the next level (chain)
3. Output to Console, File, or Database (pluggable sinks)
4. Global singleton access
5. Thread-safe logging

### Entities
`Logger` · `LogLevel` · `LogHandler` · `LogMessage`

### Class Diagram
```
Logger (singleton)
 - logLevel, handlerChain

LogHandler (abstract) → Chain of Responsibility
 ├── DebugHandler
 ├── InfoHandler
 ├── WarnHandler
 ├── ErrorHandler
 └── FatalHandler
     - nextHandler

LogMessage
 - level, message, timestamp
```

### Pattern: **Singleton** (Logger), **Chain of Responsibility** (handlers)

### Key Code Sketch
```java
public abstract class LogHandler {
    protected LogHandler nextHandler;
    protected LogLevel level;

    public void log(LogMessage msg) {
        if (msg.getLevel().ordinal() >= level.ordinal()) {
            write(msg);
        }
        if (nextHandler != null) nextHandler.log(msg);
    }
    protected abstract void write(LogMessage msg);
}
```

### 🚀 Driver Code (Full Flow)
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
        logger.info("User logged in");   // → ConsoleHandler prints, FileHandler skips (INFO < ERROR)
        logger.error("DB connection lost"); // → ConsoleHandler prints AND FileHandler writes to file
        logger.debug("Cache hit ratio: 92%"); // → ConsoleHandler prints
    }
}
```
**Flow**: `logger.info(msg)` → creates `LogMessage(INFO, msg)` → `handlerChain.handle(msg)` → `ConsoleHandler: severity >= DEBUG? YES → write()` → `next.handle(msg)` → `FileHandler: severity >= ERROR? NO → skip`

### 🎯 Interview Tips
- **Why Chain of Responsibility?** "Each handler decides if it processes the log AND forwards to next. Adding a new level/sink doesn't change existing handlers → Open/Closed."
- **Thread safety**: Use `volatile` for singleton + double-checked locking. Log writes should be thread-safe (synchronized or use a ConcurrentLinkedQueue buffer).
- **Follow-up: Async logging** — Use a blocking queue + dedicated writer thread to avoid log calls blocking the main thread
- **Follow-up: Log rotation** — FileHandler can rotate by size/date
- **Edge cases**: Null handler chain, log in extremely hot path (perf), circular chain
- **Real-world**: SLF4J, Log4j use similar patterns. Mention this familiarity.

---

## 3 · Vending Machine 🟢

### Functional Requirements
1. Display products with price and quantity
2. Accept coins/notes, track inserted amount
3. States: Idle → HasMoney → Dispensing → ReturnChange
4. Dispense product if enough money; else return
5. Admin can refill products

### Entities
`VendingMachine` · `Product` · `Inventory` · `Coin` · `State`

### Class Diagram
```
VendingMachine
 - currentState, inventory, currentBalance

State (interface)
 ├── IdleState
 ├── HasMoneyState
 ├── DispensingState
 └── ReturnChangeState
     + insertMoney(), selectProduct(), dispense(), returnChange()

Inventory
 - Map<Product, Integer>

Product
 - name, price, code
```

### Pattern: **State**

### Key Code Sketch
```java
public interface State {
    void insertMoney(VendingMachine vm, double amount);
    void selectProduct(VendingMachine vm, String code);
    void dispense(VendingMachine vm);
    void returnChange(VendingMachine vm);
}
```

### 🚀 Driver Code (Full Flow)
```java
public class VendingMachineDemo {
    public static void main(String[] args) {
        // 1. Setup machine + stock products
        VendingMachine vm = new VendingMachine();
        vm.getInventory().addProduct(new Product("A1", "Coke", 40), 5);
        vm.getInventory().addProduct(new Product("B1", "Chips", 20), 10);

        // 2. User flow: insert money → select → dispense
        vm.insertMoney(50);         // State: Idle → HasMoney (balance=50)
        vm.selectProduct("A1");     // State: HasMoney → Dispensing → ReturnChange
        // Output: "Dispensed: Coke" then "Returning change: ₹10"
        // State: ReturnChange → Idle

        // 3. Insufficient money flow
        vm.insertMoney(10);
        vm.selectProduct("A1");     // "Insufficient balance. Need ₹30 more"
        vm.returnChange();          // "Returning ₹10", State → Idle
    }
}
```
**Flow**: `insertMoney()` → `IdleState.insertMoney()` → sets balance, transitions to `HasMoneyState` → `selectProduct()` → validates stock + balance → transitions to `DispensingState` → `dispense()` → reduces inventory, calculates change → `ReturnChangeState` → back to `IdleState`

### 🎯 Interview Tips
- **Why State pattern?** "Each state has different behavior for the same action (insertMoney, selectProduct). Without State pattern, you'd have messy if-else chains in every method."
- **State transition diagram** — Draw this first! Idle → HasMoney → Dispensing → ReturnChange → Idle
- **Follow-up: Concurrency** — Two users at the same machine? Unlikely physically, but in code, make state transitions atomic.
- **Edge cases**: Out of stock after money inserted, exact change only, multiple products in one session, coin jam
- **Design decision**: States hold no data — VendingMachine holds balance/selectedProduct. States just define behavior.
- **Extend**: Support multiple currencies, card payment, admin dashboard

---

## 4 · Stack Overflow (Q&A Platform) 🟢

### Functional Requirements
1. Users post questions, answer questions
2. Upvote/downvote questions and answers
3. Tags on questions, search by tag
4. Accept answer, reputation system
5. Comment on questions and answers

### Entities
`User` · `Question` · `Answer` · `Comment` · `Tag` · `Vote`

### Class Diagram
```
User
 - userId, name, email, reputation

Question
 - questionId, title, body, author, tags, answers, votes, comments

Answer
 - answerId, body, author, votes, isAccepted, comments

Comment
 - commentId, body, author, createdAt

Vote
 - voteType (UP/DOWN), user
```

### Pattern: **Observer** (notify user when answer posted)

### Key Code Sketch
```java
public class Question {
    private List<Observer> observers; // watchers
    public void addAnswer(Answer a) {
        answers.add(a);
        notifyObservers(a);
    }
}
```

### 🚀 Driver Code (Full Flow)
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

        // 3. Bob posts answer → Alice gets notified (Observer)
        Answer a = service.postAnswer("u2", q.getId(), "It uses array of linked lists...");
        // Output: "Notification to Alice: New answer on 'How does HashMap work?' by Bob"

        // 4. Alice upvotes Bob's answer → Bob gets +10 reputation
        service.upvoteAnswer(q.getId(), a.getId(), "u1");
        // Bob.reputation: 0 → 10

        // 5. Alice accepts Bob's answer → Bob gets +15 reputation
        service.acceptAnswer(q.getId(), a.getId());
        // Bob.reputation: 10 → 25

        // 6. Search by tag
        List<Question> javaQs = service.searchByTag("java"); // returns [q]
    }
}
```
**Flow**: `postQuestion()` → creates Question + subscribes author as Observer → `postAnswer()` → adds Answer to Question → `question.notifyObservers()` → all watchers get callback → `upvote()` → adds Vote + updates author reputation

### 🎯 Interview Tips
- **Votable interface** — Both Question and Answer support voting → extract a `Votable` interface for DRY
- **Reputation system**: Upvote on answer = +10 rep, upvote on question = +5, accepted answer = +15. Track this on User.
- **Follow-up: Search** — By title, body, tag. Mention inverted index for full-text search.
- **Follow-up: Ranking** — Sort answers by votes, with accepted answer pinned at top.
- **Edge cases**: Self-voting, duplicate questions, edit history, spam detection
- **Concurrency**: Vote counts under concurrent upvotes → use AtomicInteger or synchronized

---

## 5 · Library Management 🟢

### Functional Requirements
1. Add/remove books; track copies
2. Members search, borrow, return books
3. Max 5 books per member, 14-day return window
4. Fine for overdue books
5. Reservation if all copies lent out

### Entities
`Library` · `Book` · `BookItem` · `Member` · `Loan` · `Reservation` · `Fine`

### Class Diagram
```
Book
 - isbn, title, author, subject

BookItem
 - barcode, book, isAvailable, dueDate

Member
 - memberId, name, activeLoans, reservations

Loan
 - loanId, bookItem, member, issueDate, dueDate, returnDate

Reservation
 - reservationId, book, member, status
```

### Pattern: None specific (clean OOP)

### Key Code Sketch
```java
public class LibraryService {
    public Loan borrowBook(Member m, String barcode) { ... }
    public Fine returnBook(Loan loan) { ... }
    public Reservation reserveBook(Member m, String isbn) { ... }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class LibraryDemo {
    public static void main(String[] args) {
        LibraryService lib = new LibraryService();

        // 1. Setup: add books with copies
        Book java = new Book("ISBN-001", "Effective Java", "Bloch", "Programming");
        lib.addBook(java, 2); // 2 physical copies
        lib.registerMember(new Member("M1", "Alice"));
        lib.registerMember(new Member("M2", "Bob"));

        // 2. Alice borrows copy 1
        Loan loan1 = lib.borrowBook("M1", "ISBN-001");
        // → BookItem(barcode=xxx) checked out, dueDate = today+14

        // 3. Bob borrows copy 2
        Loan loan2 = lib.borrowBook("M2", "ISBN-001");

        // 4. Charlie tries to borrow — no copies left → reservation created
        lib.registerMember(new Member("M3", "Charlie"));
        lib.borrowBook("M3", "ISBN-001");
        // Output: "All copies lent. Reservation created for Charlie"

        // 5. Alice returns (late) → fine + Charlie's reservation auto-fulfilled
        Fine fine = lib.returnBook(loan1.getLoanId());
        // → If overdue: Fine of ₹X. Then: "Reservation fulfilled for Charlie"
    }
}
```
**Flow**: `borrowBook()` → checks `member.canBorrow()` (max 5) → finds available `BookItem` → `bookItem.checkout(dueDate)` → creates `Loan` → if no copy: creates `Reservation` | `returnBook()` → `loan.markReturned()` → `bookItem.returnItem()` → check reservation queue → calculate fine if overdue

### 🎯 Interview Tips
- **Book vs BookItem**: Book is metadata (ISBN, title). BookItem is a physical copy (barcode). One Book can have many BookItems. This is the key design insight.
- **Reservation queue**: When a book is returned, check reservation queue → auto-fulfill the first waiting reservation.
- **Follow-up**: Fine calculation = (daysPastDue × finePerDay). Cap at book price.
- **Edge cases**: Renewing a loan, lost book, member with outstanding fines trying to borrow, reservation expiry
- **Concurrency**: Two members trying to borrow the last copy → lock on BookItem
- **Extend**: Multi-branch library, inter-library loan, digital books

---

## 6 · Tic-Tac-Toe 🟢

### Functional Requirements
1. 3×3 board, two players (X and O)
2. Players take turns placing marks
3. Detect win (row/col/diagonal) or draw
4. Support replaying

### Entities
`Game` · `Board` · `Player` · `Cell` · `Piece`

### Class Diagram
```
Game
 - board, players[], currentTurnIndex

Board
 - Cell[][] grid, size

Cell
 - row, col, piece

Player
 - name, piece(X/O)
```

### Pattern: None specific (clean OOP)

### Key Code Sketch
```java
public class Board {
    private Cell[][] grid;
    public boolean placePiece(int row, int col, Piece p) { ... }
    public boolean checkWin(Piece p) { ... }
    public boolean isFull() { ... }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class TicTacToeDemo {
    public static void main(String[] args) {
        TicTacToeService game = new TicTacToeService();
        game.startGame("Alice", "Bob");
        // "Game started! Alice (X) vs Bob (O)"

        game.playTurn(0, 0); // Alice places X at (0,0)
        game.playTurn(1, 1); // Bob places O at (1,1)
        game.playTurn(0, 1); // Alice places X at (0,1)
        game.playTurn(2, 2); // Bob places O at (2,2)
        game.playTurn(0, 2); // Alice places X at (0,2) → row 0 complete!
        // Output: "Alice wins!"

        System.out.println(game.getResult()); // "Alice wins!"
    }
}
```
**Flow**: `playTurn(row, col)` → `game.makeMove(row, col)` → `board.placePiece(row, col, currentPlayer.piece)` → `board.checkWin(row, col, piece)` → checks row/col/diagonals → if win: set status=WIN | if `board.isFull()`: DRAW | else: switch turn

### 🎯 Interview Tips
- **Win check optimization**: Only check row/col/diagonals that include the last placed piece — O(n) instead of O(n²)
- **Follow-up: NxN board** — Generalize to NxN. Win condition = N in a row.
- **Follow-up: Bot player** — Strategy pattern for player (HumanPlayer, BotPlayer). Bot uses minimax.
- **Edge cases**: Place on occupied cell, play after game over
- **Design decision**: Piece as enum (X, O) or class? Enum is simpler here — use class only if pieces have behavior.

---

## 7 · Traffic Signal System 🟢

### Functional Requirements
1. Multiple roads at an intersection
2. Each signal cycles: Green → Yellow → Red
3. Configurable duration per state
4. Emergency override to force green on one road

### Entities
`Intersection` · `Road` · `TrafficSignal` · `SignalState` · `SignalController`

### Class Diagram
```
TrafficSignal
 - currentState, road

SignalState (interface)
 ├── GreenState
 ├── YellowState
 └── RedState
     + next(), getDuration()

SignalController
 - signals[], schedule transitions
```

### Pattern: **State**

### Key Code Sketch
```java
public interface SignalState {
    SignalState next();
    int getDurationSeconds();
    String getColor();
}
```

### 🚀 Driver Code (Full Flow)
```java
public class TrafficSignalDemo {
    public static void main(String[] args) {
        // 1. Setup intersection
        Intersection intersection = new Intersection("Main St & 5th Ave");
        intersection.addSignal(new TrafficSignal(new Road("Main St"), new GreenState(30)));
        intersection.addSignal(new TrafficSignal(new Road("5th Ave"), new RedState(30)));

        TrafficSignalService controller = new TrafficSignalService(intersection);

        // 2. Normal cycle
        controller.startCycle();  // Main St: GREEN→YELLOW, 5th Ave: RED→GREEN
        controller.displayStatus();

        // 3. Emergency override
        controller.emergencyOverride("5th Ave");
        // → 5th Ave forced GREEN(60s), Main St forced RED(60s)
    }
}
```
**Flow**: `startCycle()` → for each signal: `signal.transition()` → `currentState.next()` → Green returns Yellow, Yellow returns Red, Red returns Green | `emergencyOverride(road)` → target road: `forceState(GreenState)`, all others: `forceState(RedState)`

### 🎯 Interview Tips
- **State Pattern fits perfectly**: Green → Yellow → Red → Green. Each state knows its duration and its next state.
- **Controller coordination**: When road A is green, all other roads must be red. Controller orchestrates.
- **Follow-up: Emergency override** — Force one road to green, all others to red. Return to normal cycle after.
- **Edge cases**: Sensor malfunction, stuck in one state, pedestrian signal, left-turn signals
- **Concurrency**: Use `ScheduledExecutorService` for timed transitions

---

## 8 · Elevator System 🟡

### Functional Requirements
1. N floors, M elevators
2. Request from floor (up/down) or inside elevator (go to floor)
3. Scheduling: nearest elevator assignment
4. Elevator states: Moving, Idle, DoorOpen
5. Direction-aware: serves requests in current direction first

### Entities
`Building` · `Elevator` · `Request` · `Scheduler` · `ElevatorState` · `Direction`

### Class Diagram
```
Building
 - elevators[], scheduler

Elevator
 - id, currentFloor, direction, state, requests(PQ)

Request
 - sourceFloor, destinationFloor, direction

Scheduler (Strategy)
 ├── NearestElevatorScheduler
 └── ZoneBasedScheduler

ElevatorState
 ├── MovingState
 ├── IdleState
 └── DoorOpenState
```

### Pattern: **Strategy** (scheduling), **State** (elevator), **Observer** (floor display)

### Key Code Sketch
```java
public interface Scheduler {
    Elevator assignElevator(List<Elevator> elevators, Request req);
}

public class NearestElevatorScheduler implements Scheduler {
    public Elevator assignElevator(List<Elevator> elevators, Request req) {
        return elevators.stream()
            .min(Comparator.comparingInt(e -> Math.abs(e.getCurrentFloor() - req.getSourceFloor())))
            .orElseThrow();
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class ElevatorDemo {
    public static void main(String[] args) {
        // 1. Setup building: 10 floors, 3 elevators, nearest scheduler
        Building building = new Building(10, 3, new NearestElevatorScheduler());
        ElevatorService service = new ElevatorService(building);

        // 2. User on floor 3 wants to go to floor 7
        service.requestElevator(3, 7);
        // → Scheduler picks nearest elevator (e.g., Elevator 0 at floor 0)
        // → Adds stops: floor 3 (pickup) and floor 7 (destination)

        // 3. Another request: floor 5 → floor 1
        service.requestElevator(5, 1);

        // 4. Simulate steps — elevator moves one stop at a time
        service.step(); // Elevator 0 → floor 3 (pickup)
        service.step(); // Elevator 0 → floor 7 (drop)
        service.step(); // Elevator 1 → floor 5 (pickup)

        service.displayStatus();
        // Elevator 0: Floor 7 | IDLE | Elevator 1: Floor 5 | DOWN
    }
}
```
**Flow**: `requestElevator(src, dest)` → `scheduler.assignElevator(elevators, request)` → picks nearest idle/same-direction → `elevator.addRequest(src)` + `elevator.addRequest(dest)` → `step()` → elevator pops next stop from TreeSet → moves → `notifyObservers()` for floor display

### 🎯 Interview Tips
- **Three patterns, three concerns**: Strategy (which elevator?), State (elevator behavior), Observer (floor display updates)
- **SCAN Algorithm**: Serve all requests in current direction before reversing — like a disk arm. Use two TreeSets (upStops, downStops).
- **Follow-up: Scheduling Strategies** — Nearest, Zone-based (elevators assigned to floor ranges), Load-balanced
- **Follow-up: VIP/freight elevator** — Separate scheduling pool
- **Concurrency**: Multiple floor buttons pressed simultaneously → thread-safe request queue (PriorityBlockingQueue)
- **Edge cases**: All elevators busy, maintenance mode, overweight, door obstruction
- **Data Structure**: TreeSet for stops (sorted, O(log n) insert/remove), or PriorityQueue

---

## 9 · In-Memory Key-Value Store 🟡

### Functional Requirements
1. GET, PUT, DELETE operations
2. Optional TTL (time-to-live) per key
3. Thread-safe reads/writes
4. Support snapshots for persistence

### Entities
`KVStore` · `Entry` · `ExpiryManager`

### Class Diagram
```
KVStore (singleton)
 - ConcurrentHashMap<String, Entry> store
 - ExpiryManager expiryManager

Entry
 - key, value, createdAt, ttl

ExpiryManager
 - ScheduledExecutorService to evict expired keys
```

### Pattern: **Singleton**

### Key Code Sketch
```java
public class KVStore {
    private final ConcurrentHashMap<String, Entry> store = new ConcurrentHashMap<>();

    public void put(String key, String value, long ttlMs) { ... }
    public String get(String key) { ... }
    public void delete(String key) { ... }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class KVStoreDemo {
    public static void main(String[] args) throws InterruptedException {
        KVStore store = KVStore.getInstance();

        // 1. Basic CRUD
        store.put("user:1", "Alice");
        store.put("user:2", "Bob");
        System.out.println(store.get("user:1")); // "Alice"
        store.delete("user:2");

        // 2. TTL — key expires after 2 seconds
        store.put("session:abc", "token123", 2000);
        System.out.println(store.get("session:abc")); // "token123"
        Thread.sleep(3000);
        System.out.println(store.get("session:abc")); // null (expired)

        // 3. Snapshot for persistence
        store.put("config:timeout", "30");
        Map<String, String> snapshot = store.snapshot();
        // → {"user:1": "Alice", "config:timeout": "30"} (expired keys excluded)
    }
}
```
**Flow**: `put(key, val, ttl)` → creates `Entry(key, val, ttl)` → stores in `ConcurrentHashMap` | `get(key)` → retrieves Entry → `entry.isExpired()?` YES→remove+return null, NO→return value | Background `ScheduledExecutorService` runs `evictExpired()` every 1s

### 🎯 Interview Tips
- **TTL eviction strategies**: Lazy (check on GET) vs Active (background thread scans periodically) vs Both (hybrid like Redis)
- **Thread safety**: `ConcurrentHashMap` for O(1) thread-safe ops. But compound operations (check-then-act) need synchronization.
- **Follow-up: Snapshots** — Copy-on-write Map for consistent snapshots without locking readers
- **Follow-up: Persistence** — Append-only log (AOF) or periodic RDB-style dump
- **Edge cases**: Key expiry during read (lazy cleanup), snapshot during writes, max memory limit
- **Think Redis**: If interviewer says "design Redis", use this as base + add data structures (List, Set, SortedSet)

---

## 10 · LRU Cache 🟡

### Functional Requirements
1. Fixed capacity cache
2. GET returns value and marks as recently used
3. PUT inserts; evicts least-recently-used if full
4. O(1) for both GET and PUT

### Entities
`LRUCache` · `DoublyLinkedList` · `Node`

### Class Diagram
```
LRUCache<K,V>
 - capacity, map(HashMap<K, Node>), doublyLinkedList

Node<K,V>
 - key, value, prev, next

DoublyLinkedList<K,V>
 - head(dummy), tail(dummy)
 + addToFront(Node), remove(Node), removeLast()
```

### Pattern: None (data structure problem)

### Key Code Sketch
```java
public class LRUCache<K, V> {
    private final int capacity;
    private final Map<K, Node<K,V>> map;
    private final DoublyLinkedList<K,V> dll;

    public V get(K key) {
        Node<K,V> node = map.get(key);
        if (node == null) return null;
        dll.moveToFront(node);
        return node.value;
    }

    public void put(K key, V value) {
        if (map.containsKey(key)) { /* update + move front */ }
        else {
            if (map.size() == capacity) { /* evict last */ }
            /* add new node to front */
        }
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class LRUCacheDemo {
    public static void main(String[] args) {
        LRUCache<String, String> cache = new LRUCache<>(3); // capacity=3

        // 1. Fill cache
        cache.put("a", "1");  // DLL: [a]
        cache.put("b", "2");  // DLL: [b, a]
        cache.put("c", "3");  // DLL: [c, b, a]

        // 2. Access "a" → moves to front
        cache.get("a");       // DLL: [a, c, b]

        // 3. Insert "d" → cache full → evicts LRU ("b")
        cache.put("d", "4");  // DLL: [d, a, c] — "b" evicted!

        System.out.println(cache.get("b")); // null (evicted)
        System.out.println(cache.get("a")); // "1" (still there)

        // 4. Update existing key
        cache.put("c", "33"); // DLL: [c, a, d] — "c" updated + moved to front
    }
}
```
**Flow**: `get(key)` → `map.get(key)` → if found: `dll.moveToFront(node)` + return value | `put(key, val)` → if exists: update + moveToFront | if new: check `size == capacity?` → YES: `dll.removeLast()` + `map.remove(evicted.key)` → `dll.addToFront(newNode)` + `map.put(key, node)`

### 🎯 Interview Tips
- **Key insight**: HashMap gives O(1) lookup, DLL gives O(1) move-to-front and remove-last. Together → O(1) for everything.
- **Dummy nodes**: Use dummy head + dummy tail in DLL to avoid null checks on insert/remove.
- **Follow-up: Thread-safe LRU** — Use `ReadWriteLock` or `synchronized` on get/put. Or `ConcurrentLinkedHashMap`.
- **Follow-up: LFU Cache** — Maintain frequency count per key + min-frequency tracking
- **Follow-up: TTL support** — Add expiry time to Node, lazy evict on get()
- **Edge cases**: Capacity = 0, update existing key value, remove from empty cache
- **Why not LinkedHashMap?** In interview, they want to see you build it. But mention Java's `LinkedHashMap(capacity, 0.75f, true)` with `removeEldestEntry()` as production shortcut.

---

## 11 · Movie Ticket Booking (BookMyShow) 🟡

### Functional Requirements
1. Browse movies by city, theatre, showtime
2. View seat map; select seats
3. Lock seats during booking (5 min timeout)
4. Payment; confirm or release seats
5. Notification on booking confirmation

### Entities
`Movie` · `Theatre` · `Screen` · `Show` · `Seat` · `Booking` · `Payment` · `User`

### Class Diagram
```
Movie - id, title, genre, duration
Theatre - id, name, city, screens[]
Screen - id, seats[]
Show - id, movie, screen, startTime
Seat - id, seatType, row, number
Booking - id, user, show, seats[], status, payment
Payment - id, amount, method, status
```

### Pattern: **Strategy** (payment), **Observer** (notification)

### Key Code Sketch
```java
public class BookingService {
    public Booking initiateBooking(User u, Show s, List<Seat> seats) {
        lockSeats(seats); // 5 min lock
        return new Booking(u, s, seats, BookingStatus.PENDING);
    }
    public void confirmBooking(Booking b, PaymentStrategy ps) {
        ps.pay(b.getTotalAmount());
        b.setStatus(BookingStatus.CONFIRMED);
        notifyUser(b);
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class BookMyShowDemo {
    public static void main(String[] args) {
        BookingService bookingService = new BookingService();

        // 1. Setup
        Movie movie = new Movie("M1", "Inception", "Sci-Fi", 148);
        Screen screen = new Screen("S1", createSeats(100)); // 100 seats
        Show show = new Show("SH1", movie, screen, LocalDateTime.of(2026, 3, 20, 18, 0));

        // 2. User selects seats
        User user = new User("U1", "Alice", "alice@email.com");
        List<Seat> selected = List.of(show.getAvailableSeats().get(0),
                                      show.getAvailableSeats().get(1));

        // 3. Initiate booking → seats LOCKED for 5 min
        Booking booking = bookingService.initiateBooking(user, show, selected);
        // → Seats status: AVAILABLE → LOCKED. Timer starts (5 min auto-release)

        // 4. Confirm with payment
        bookingService.confirmBooking(booking.getId(), new UPIPayment("alice@upi"));
        // → Paid ₹500 via UPI. Seats: LOCKED → BOOKED. Notification sent.

        // 5. If user doesn't pay → auto-release after 5 min
        // bookingService.cancelBooking(booking.getId()); // manual cancel also works
    }
}
```
**Flow**: `initiateBooking()` → `show.lockSeats(seats)` [synchronized] → schedule 5-min auto-release → `confirmBooking()` → `paymentStrategy.pay(total)` → `show.bookSeats()` → LOCKED→BOOKED → notify user

### 🎯 Interview Tips
- **Seat locking is the CORE challenge**: Temporary hold (5 min) prevents double-booking. Use `ScheduledExecutorService` for auto-release.
- **Concurrency**: `synchronized` block on seat locking — two users clicking the same seat at the same time. Use optimistic locking or `compareAndSet`.
- **Follow-up: Seat map** — Show available vs locked vs booked in real-time (Observer pattern to push updates)
- **Follow-up: Dynamic pricing** — Price varies by showtime, day (weekend), seat fill rate
- **Edge cases**: Payment fails after locking → must release. User closes browser mid-booking. Show cancelled.
- **Design decision**: Show holds the seat status map (not individual Seat objects) for centralized locking.

---

## 12 · Chess Game 🟡

### Functional Requirements
1. 8×8 board, 2 players (White/Black)
2. Each piece type has unique movement rules
3. Validate move legality (including check/checkmate)
4. Turn-based play; detect game-over conditions
5. Support undo

### Entities
`Game` · `Board` · `Cell` · `Piece` · `Player` · `Move`

### Class Diagram
```
Piece (abstract)
 ├── King, Queen, Rook, Bishop, Knight, Pawn
 │    + canMove(Board, Cell from, Cell to): boolean
 - color, killed

Board
 - Cell[8][8] grid

Move
 - player, piece, from, to, killedPiece

Game
 - board, players[], moves[], status(ACTIVE/CHECK/CHECKMATE/STALEMATE)
```

### Pattern: **Strategy/Polymorphism** (each piece's move logic), **Factory** (create pieces)

### Key Code Sketch
```java
public abstract class Piece {
    protected Color color;
    protected boolean killed;
    public abstract boolean canMove(Board board, Cell from, Cell to);
}

public class Knight extends Piece {
    public boolean canMove(Board board, Cell from, Cell to) {
        int dx = Math.abs(from.getX() - to.getX());
        int dy = Math.abs(from.getY() - to.getY());
        return (dx == 2 && dy == 1) || (dx == 1 && dy == 2);
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class ChessDemo {
    public static void main(String[] args) {
        ChessGameService service = new ChessGameService();
        service.startGame("Alice", "Bob");

        // Alice (White) moves knight
        service.playMove(7, 1, 5, 2); // Knight from (7,1) to (5,2)
        // Bob (Black) moves pawn
        service.playMove(1, 4, 3, 4); // Pawn from (1,4) to (3,4)
        // Alice moves bishop
        service.playMove(7, 2, 4, 5); // Bishop from (7,2) to (4,5)

        // Invalid move example:
        service.playMove(0, 0, 5, 5); // "Invalid move for ♜" (Rook can't move diagonal)
    }
}
```
**Flow**: `playMove(fromX, fromY, toX, toY)` → get Cell from/to → validate piece color == currentTurn → `piece.canMove(board, from, to)` [polymorphic dispatch: Knight/Rook/etc.] → if valid: move piece, capture if occupied → add to `moveHistory` → switch turn

### 🎯 Interview Tips
- **Polymorphism over Strategy here**: Each piece IS a different type with different `canMove()`. This is natural polymorphism.
- **Path blocking**: Rook/Bishop/Queen need `isPathClear()` — iterate step-by-step between from → to checking for obstacles. Knight JUMPS (no path check).
- **Follow-up: Check/Checkmate** — After each move, check if opponent's King is under attack. Checkmate = King in check + no legal moves.
- **Follow-up: Castling, En Passant** — Special move rules. Track if King/Rook have moved.
- **Follow-up: Undo** — Store Move objects in a stack. Move stores killedPiece for restoration.
- **Edge cases**: Move into check (illegal), stalemate (not in check but no legal moves), pawn promotion
- **Design decision**: Board.isPathClear() utility used by Rook, Bishop, Queen — avoids duplication.

---

## 13 · Snake & Ladder 🟡

### Functional Requirements
1. N×N board with snakes and ladders
2. 2-4 players roll dice, move forward
3. Snake head → move down to tail; ladder bottom → move up to top
4. First to reach/exceed last cell wins
5. Configurable board

### Entities
`Game` · `Board` · `Player` · `Dice` · `Snake` · `Ladder` · `Cell`

### Class Diagram
```
Board
 - size, snakes(Map<Int,Int>), ladders(Map<Int,Int>)

Player
 - name, position

Dice
 - numberOfDice
 + roll(): int

Game
 - board, players(Queue), dice, winner
```

### Pattern: None specific (clean OOP)

### Key Code Sketch
```java
public class Game {
    public void play() {
        while (winner == null) {
            Player p = players.poll();
            int rolled = dice.roll();
            int newPos = p.getPosition() + rolled;
            newPos = board.getfinalPosition(newPos); // check snake/ladder
            p.setPosition(newPos);
            if (newPos >= board.getSize()) { winner = p; }
            else { players.offer(p); }
        }
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class SnakeLadderDemo {
    public static void main(String[] args) {
        Board board = new Board(10); // 10x10 = 100 cells
        board.addSnake(99, 10);  // nearly won? back to 10!
        board.addSnake(52, 28);
        board.addLadder(2, 38);  // early ladder boost
        board.addLadder(28, 84);

        List<Player> players = List.of(new Player("Alice"), new Player("Bob"));
        Dice dice = new Dice(1, 6); // 1 die, 6 faces

        SnakeLadderGame game = new SnakeLadderGame(board, players, dice);
        game.play();
        // Output: "Alice rolled 4 | 0 → 4"
        //         "Bob rolled 2 | 0 → 2"  "  Climbed ladder! From 2 to 38"
        //         ... "Alice WINS!"
    }
}
```
**Flow**: `play()` → loop while no winner → `players.poll()` → `dice.roll()` → `newPos = pos + rolled` → `board.getFinalPosition(newPos)` [checks snakes Map then ladders Map] → if exceeds board: stay → if == 100: winner! → else `players.offer(player)`

### 🎯 Interview Tips
- **Board design**: Use `Map<Integer, Integer>` for both snakes (head→tail) and ladders (bottom→top). `getFinalPosition()` checks both maps.
- **Queue for players**: Natural turn-order with `poll()` and `offer()`.
- **Follow-up**: Configurable — board size, number of dice, number of snakes/ladders all parameterized.
- **Edge cases**: Roll exceeds board → stay in place. Snake at position 99 (near end). Snake and ladder at same cell (invalid config).
- **Extend**: Power-ups, multiple dice, reverse movement

---

## 14 · ATM Machine 🟡

### Functional Requirements
1. Authenticate via card + PIN
2. Operations: Withdraw, Deposit, Balance Inquiry
3. Cash dispenser: dispense in denominations (₹500, ₹200, ₹100)
4. State transitions: Idle → CardInserted → Authenticated → Transaction → Idle
5. Insufficient funds / cash handling

### Entities
`ATM` · `Card` · `Account` · `Transaction` · `CashDispenser` · `State`

### Class Diagram
```
ATM
 - currentState, cashDispenser, bankService

State (interface)
 ├── IdleState
 ├── CardInsertedState
 ├── AuthenticatedState
 └── TransactionState

CashDispenser — Chain of Responsibility
 ├── FiveHundredHandler
 ├── TwoHundredHandler
 └── HundredHandler

Transaction
 - type(WITHDRAW/DEPOSIT/BALANCE), amount, timestamp
```

### Pattern: **State** (ATM flow), **Chain of Responsibility** (cash dispensing)

### Key Code Sketch
```java
public abstract class CashHandler {
    protected CashHandler next;
    protected int denomination;

    public void dispense(int amount) {
        int count = amount / denomination;
        int remainder = amount % denomination;
        if (count > 0) System.out.println(count + " x ₹" + denomination);
        if (remainder > 0 && next != null) next.dispense(remainder);
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class ATMDemo {
    public static void main(String[] args) {
        // 1. Setup bank + ATM
        BankService bank = new BankService();
        bank.addAccount(new Account("ACC001", 50000, "1234"));
        ATM atm = new ATM(bank); // cash: 100x₹500, 100x₹200, 100x₹100

        Card card = new Card("CARD-001", "ACC001");

        // 2. Withdrawal flow
        atm.insertCard(card);   // State: Idle → CardInserted
        atm.enterPin("1234");   // State: CardInserted → Authenticated
        atm.withdraw(1700);     // Dispensing: 3x₹500 + 1x₹200
        // Output: "3 x ₹500" "1 x ₹200" "Remaining balance: ₹48300"
        atm.ejectCard();        // State: Authenticated → Idle

        // 3. Wrong PIN flow
        atm.insertCard(card);
        atm.enterPin("0000");   // "Invalid PIN." State → Idle, card ejected
    }
}
```
**Flow**: State machine: `Idle` →[insertCard]→ `CardInserted` →[enterPin+validate]→ `Authenticated` →[withdraw]→ checks balance + ATM cash → `CashHandler chain: ₹500Handler.dispense(1700)` → dispenses 3×500=1500, remainder=200 → `₹200Handler.dispense(200)` → 1×200 → done → [ejectCard] → `Idle`

### 🎯 Interview Tips
- **Two patterns, two problems**: State = ATM flow (insert card → enter PIN → transact). Chain of Resp = cash dispensing (₹500 → ₹200 → ₹100).
- **Cash handler tracks available notes**: Each handler has a `count` field. Dispense `min(needed, available)`, forward remainder.
- **Follow-up: Max withdrawal limit** per transaction, daily limit per account
- **Follow-up: Multiple card types** — credit vs debit → different transaction rules
- **Edge cases**: Wrong PIN (3 attempts → block card), cash insufficient in ATM, power failure mid-transaction, partial dispense
- **Concurrency**: ATM is physically single-user, but bank backend needs concurrency (account balance)

---

## 15 · Online Shopping Cart 🟡

### Functional Requirements
1. Browse products by category, search
2. Add/remove items to cart, update quantity
3. Apply coupon/discount codes
4. Checkout with payment strategy (Card, UPI, Wallet)
5. Order tracking, notifications

### Entities
`User` · `Product` · `Cart` · `CartItem` · `Order` · `Payment` · `Coupon`

### Class Diagram
```
Cart
 - userId, items(List<CartItem>)
 + addItem(), removeItem(), getTotal()

CartItem
 - product, quantity

Order
 - orderId, user, items[], totalAmount, status, payment

PaymentStrategy (interface)
 ├── CardPayment
 ├── UPIPayment
 └── WalletPayment

Coupon
 - code, discountPercent, validUntil
```

### Pattern: **Strategy** (payment), **Observer** (order status notifications)

### Key Code Sketch
```java
public class CheckoutService {
    public Order checkout(Cart cart, PaymentStrategy ps, String couponCode) {
        double total = cart.getTotal();
        if (couponCode != null) total = applyCoupon(total, couponCode);
        ps.pay(total);
        Order order = new Order(cart, total, OrderStatus.CONFIRMED);
        notifyUser(order);
        return order;
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class ShoppingCartDemo {
    public static void main(String[] args) {
        ShoppingService shop = new ShoppingService();

        // 1. Setup catalog + coupons
        shop.addToCatalog(new Product("P1", "Laptop", 50000, "Electronics"));
        shop.addToCatalog(new Product("P2", "Mouse", 500, "Electronics"));
        shop.addCoupon(new Coupon("SAVE10", 10.0, LocalDate.of(2026, 12, 31)));

        // 2. Add to cart
        shop.addToCart("user1", "P1", 1);  // 1 laptop
        shop.addToCart("user1", "P2", 2);  // 2 mice

        // 3. Checkout with coupon + UPI
        Order order = shop.checkout("user1",
                new UPIPayment("user@upi"), "SAVE10");
        // Cart total: 50000 + 1000 = 51000
        // Coupon SAVE10 (10%): 51000 → 45900
        // "Paid ₹45900 via UPI"
        // "Order placed: ORD-xxx"
    }
}
```
**Flow**: `addToCart()` → `cart.addItem(product, qty)` [merge if exists] → `checkout()` → `cart.getTotal()` → if coupon: `coupon.isValid()` + `coupon.apply(total)` → `paymentStrategy.pay(discountedTotal)` → create `Order(items, total)` → `cart.clear()` → `notifyUser()`

### 🎯 Interview Tips
- **Cart is a temporary object** — Order is the permanent record. Cart → Order at checkout.
- **Coupon validation**: Check expiry, usage limits, min order value. Strategy pattern for different discount types (flat, percent, BOGO).
- **Follow-up: Inventory management** — Deduct stock on order, return on cancel. Use pessimistic locking for hot items.
- **Follow-up: Order states** — PLACED → CONFIRMED → SHIPPED → DELIVERED → (optional CANCELLED/RETURNED)
- **Concurrency**: Flash sale — 100 people buying last 1 item. Use `synchronized` or database-level optimistic locking.
- **Edge cases**: Empty cart checkout, coupon already used, item out of stock between add-to-cart and checkout

---

## 16 · Food Delivery (Swiggy/Zomato) 🟡

### Functional Requirements
1. User browses restaurants by location
2. Add items to cart from one restaurant
3. Place order → assign delivery agent
4. Track order status: Placed → Preparing → PickedUp → Delivered
5. Payment, ratings

### Entities
`User` · `Restaurant` · `MenuItem` · `Cart` · `Order` · `DeliveryAgent` · `Payment` · `Rating`

### Class Diagram
```
Restaurant - id, name, location, menu(List<MenuItem>), isOpen
MenuItem - id, name, price, isAvailable
Order - id, user, restaurant, items[], agent, status, payment
DeliveryAgent - id, name, location, isAvailable

AgentAssignmentStrategy (interface)
 ├── NearestAgentStrategy
 └── RoundRobinAgentStrategy
```

### Pattern: **Strategy** (agent assignment), **Observer** (order tracking)

### Key Code Sketch
```java
public class OrderService {
    private AgentAssignmentStrategy agentStrategy;

    public Order placeOrder(User u, Cart c, PaymentStrategy ps) {
        ps.pay(c.getTotal());
        Order order = new Order(u, c);
        DeliveryAgent agent = agentStrategy.assign(order);
        order.setAgent(agent);
        order.setStatus(OrderStatus.PLACED);
        return order;
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class FoodDeliveryDemo {
    public static void main(String[] args) {
        FoodDeliveryService service = new FoodDeliveryService(new NearestAgentStrategy());

        // 1. Register restaurants + agents
        Restaurant r = new Restaurant("R1", "Pizza Palace", new Location(12.9, 77.6));
        r.addMenuItem(new MenuItem("M1", "Margherita", 250));
        r.addMenuItem(new MenuItem("M2", "Garlic Bread", 150));
        service.registerRestaurant(r);
        service.registerAgent(new DeliveryAgent("A1", "Ravi", new Location(12.91, 77.61)));

        // 2. User places order
        FoodOrder order = service.placeOrder(user, "R1",
                List.of("M1", "M2"), new UPIPayment("user@upi"));
        // → Paid ₹400. Agent Ravi assigned (nearest). Status: PLACED

        // 3. Track order status transitions
        service.updateOrderStatus(order.getId(), FoodOrderStatus.PREPARING);
        service.updateOrderStatus(order.getId(), FoodOrderStatus.PICKED_UP);
        service.updateOrderStatus(order.getId(), FoodOrderStatus.DELIVERED);
        // → Agent Ravi marked available again

        // 4. Rate the agent
        service.rateAgent(order.getId(), 4.5);
    }
}
```
**Flow**: `placeOrder()` → `restaurant.getMenu()` filter by itemIds → `paymentStrategy.pay(total)` → `agentStrategy.assign(agents, restaurantLocation)` [picks nearest available] → `agent.assignOrder()` → create FoodOrder(PLACED) | Status transitions: PLACED→PREPARING→PICKED_UP→DELIVERED → agent freed

### 🎯 Interview Tips
- **Two Strategy decisions**: (1) Agent assignment (nearest, round-robin, load-balanced). (2) Payment method.
- **Order status is key**: Placed → Preparing → PickedUp → Delivered. Each transition notifies all observers (user, restaurant, agent dashboard).
- **Follow-up: ETA calculation** — Based on distance + preparation time.
- **Follow-up: Restaurant ranking** — By distance, rating, delivery time, promoted listings.
- **Concurrency**: Multiple orders competing for same agent → lock on agent.isAvailable check.
- **Edge cases**: Restaurant closes mid-order, agent cancels, no agents available (queue order), user cancels after preparing started (partial refund)
- **Design decision**: Cart restricted to one restaurant. Multi-restaurant = multiple orders.

---

## 17 · Hotel Booking 🟡

### Functional Requirements
1. Search rooms by date range, room type, city
2. Room types: Single, Double, Suite
3. Book room → block dates → payment
4. Cancel booking (refund policy)
5. Notifications

### Entities
`Hotel` · `Room` · `RoomType` · `Booking` · `Guest` · `Payment`

### Class Diagram
```
Hotel - id, name, city, rooms[]
Room - id, roomType, pricePerNight, bookings[]
Booking - id, guest, room, checkIn, checkOut, status, payment
Guest - id, name, email, phone
```

### Pattern: **Strategy** (pricing — peak/off-peak), **Observer** (booking confirmation)

### Key Code Sketch
```java
public class BookingService {
    public Booking bookRoom(Guest g, Room r, LocalDate in, LocalDate out, PaymentStrategy ps) {
        if (!r.isAvailable(in, out)) throw new RoomNotAvailableException();
        double total = pricingStrategy.calculate(r, in, out);
        ps.pay(total);
        Booking b = new Booking(g, r, in, out);
        r.addBooking(b);
        notifyGuest(b);
        return b;
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class HotelBookingDemo {
    public static void main(String[] args) {
        HotelBookingService service = new HotelBookingService(new StandardPricing());

        // 1. Setup hotel + rooms
        Hotel hotel = new Hotel("H1", "Grand Hyatt", "Mumbai");
        hotel.addRoom(new Room("R101", RoomType.DOUBLE, 5000));
        hotel.addRoom(new Room("R102", RoomType.SUITE, 12000));
        service.registerHotel(hotel);

        // 2. Search available rooms
        List<Room> rooms = service.searchRooms("Mumbai", RoomType.DOUBLE,
                LocalDate.of(2026, 4, 10), LocalDate.of(2026, 4, 12));
        // → [Room R101 - DOUBLE - ₹5000/night]

        // 3. Book room
        Guest guest = new Guest("G1", "Alice", "alice@email.com");
        HotelBooking booking = service.bookRoom(guest, rooms.get(0),
                LocalDate.of(2026, 4, 10), LocalDate.of(2026, 4, 12),
                new CardPayment("4111111111111111"));
        // → Paid ₹10000 (2 nights × 5000). Booking confirmed.

        // 4. Cancel → 50% refund
        double refund = service.cancelBooking(booking.getId());
        // → "Refund: ₹5000"
    }
}
```
**Flow**: `searchRooms(city, type, in, out)` → filter hotels by city → `room.isAvailable(in, out)` [check no booking overlap] → `bookRoom()` → `pricingStrategy.calculate(room, in, out)` → `paymentStrategy.pay(total)` → `room.addBooking()` → notify guest

### 🎯 Interview Tips
- **Room availability is a DATE RANGE problem**: `isAvailable(checkIn, checkOut)` = no existing booking overlaps with this range. Overlap: `newCheckIn < existingCheckOut && newCheckOut > existingCheckIn`.
- **Pricing Strategy**: StandardPricing vs PeakSeasonPricing vs WeekendPricing. Swap at runtime.
- **Follow-up: Overbooking** — Some hotels overbook by 5-10%. Handle spillover to partner hotel.
- **Follow-up: Cancellation policy** — Full refund >7 days, 50% 3-7 days, no refund <3 days.
- **Edge cases**: Check-in at 2PM, checkout at 11AM (partial day), room upgrade, extending stay
- **Concurrency**: Two users booking same room for same dates → first-come-first-served with locking.

---

## 18 · In-Memory File System 🟡

### Functional Requirements
1. Create files and directories
2. Navigate (cd, ls, pwd)
3. Read/write file content
4. Delete files/directories (recursive)
5. Search by name

### Entities
`FileSystem` · `Entry` · `File` · `Directory`

### Class Diagram
```
Entry (abstract)      ← Composite Pattern
 ├── File
 │    - content, size
 └── Directory
      - children(Map<String, Entry>)
 - name, createdAt, parent

FileSystem
 - root(Directory), currentDir
```

### Pattern: **Composite**

### Key Code Sketch
```java
public abstract class Entry {
    protected String name;
    protected Directory parent;
    public abstract int getSize();
}

public class Directory extends Entry {
    private Map<String, Entry> children = new HashMap<>();
    public int getSize() {
        return children.values().stream().mapToInt(Entry::getSize).sum();
    }
    public void addEntry(Entry e) { children.put(e.getName(), e); e.parent = this; }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class FileSystemDemo {
    public static void main(String[] args) {
        FileSystemService fs = new FileSystemService();

        // 1. Create directory structure
        fs.createDirectory("home");
        fs.navigate("home");          // pwd: /home
        fs.createDirectory("documents");
        fs.createFile("readme.txt", "Hello World!");

        // 2. List contents
        fs.listContents();
        // → Contents of /home:
        //     documents/
        //     readme.txt

        // 3. Navigate + read
        System.out.println(fs.readFile("readme.txt")); // "Hello World!"

        // 4. Search from anywhere
        List<String> found = fs.search("readme.txt");
        // → ["/home/readme.txt"]

        // 5. Delete
        fs.navigate(".."); // back to /
        fs.delete("home");  // deletes /home and everything inside (recursive)
    }
}
```
**Flow**: `mkdir(name)` → `currentDir.addEntry(new Directory(name))` | `touch(name)` → `currentDir.addEntry(new File(name))` | `cd(path)` → if "..": go to parent, if "/": go to root, else: `currentDir.getEntry(path)` cast to Directory | `getSize()` → recursive: File returns content.length, Directory sums children

### 🎯 Interview Tips
- **Composite is THE pattern here**: File and Directory both extend Entry. Directory contains Entry children (which can be File or Directory). `getSize()` is recursive.
- **pwd() implementation**: Walk up parent chain to root → build path string. Use `getFullPath()` recursive method.
- **Follow-up: Path resolution** — Handle absolute paths ("/a/b/c") and relative ("../b"). Split by "/" and navigate.
- **Follow-up: Permissions** — Read/Write/Execute per entry per user. Check on every operation.
- **Edge cases**: Circular symlinks, delete dir with contents (recursive), file name conflicts, max depth
- **Design decision**: `children` as `Map<String, Entry>` gives O(1) lookup by name. LinkedHashMap preserves insert order.

---

## 19 · Notification Service 🟡

### Functional Requirements
1. Send notifications via Email, SMS, Push
2. User preferences for channel selection
3. Priority levels: Low, Medium, High, Critical
4. Template-based messages
5. Retry on failure

### Entities
`NotificationService` · `Notification` · `Channel` · `Template` · `UserPreference`

### Class Diagram
```
Notification
 - id, userId, message, channel, priority, status

Channel (interface) ← Strategy
 ├── EmailChannel
 ├── SMSChannel
 └── PushChannel
     + send(Notification): boolean

NotificationFactory  ← Factory
 + createChannel(type): Channel

Observable (users subscribe to topics)
```

### Pattern: **Observer**, **Strategy**, **Factory**

### Key Code Sketch
```java
public class NotificationService {
    private ChannelFactory factory;

    public void notify(String userId, String templateId, ChannelType type, Priority p) {
        Channel ch = factory.createChannel(type);
        Notification n = buildFromTemplate(templateId, userId);
        n.setPriority(p);
        boolean sent = ch.send(n);
        if (!sent) retryQueue.enqueue(n);
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class NotificationDemo {
    public static void main(String[] args) {
        NotificationService service = new NotificationService();

        // 1. Register template
        service.registerTemplate(new Template("T1", "OrderUpdate",
                "Hi {name}, your order {orderId} is {status}"));

        // 2. Set user preference: Email + Push
        service.setUserPreference(new UserPreference("U1",
                List.of(ChannelType.EMAIL, ChannelType.PUSH)));

        // 3. Send notification → goes to both Email and Push
        service.send("U1", "T1",
                Map.of("name", "Alice", "orderId", "ORD-123", "status", "shipped"),
                Priority.HIGH);
        // Output:
        //   EMAIL → [HIGH] Hi Alice, your order ORD-123 is shipped
        //   PUSH  → [HIGH] Hi Alice, your order ORD-123 is shipped

        // 4. If send fails → goes to retry queue
        service.processRetryQueue(); // retries failed notifications
    }
}
```
**Flow**: `send(userId, templateId, params, priority)` → `template.render(params)` [replaces {placeholders}] → get user's preferred channels → for each channel: `channelFactory.createChannel(type)` → `channel.send(notification)` → if fails: add to retryQueue with incrementRetry()

### 🎯 Interview Tips
- **Three patterns, three concerns**: Strategy (which channel), Factory (create channel), Observer (users subscribe to topics).
- **Template rendering**: `"Hello {name}, your order {orderId} is {status}"` → replace placeholders with params map.
- **Retry with backoff**: On failure, retry with exponential backoff (1s, 2s, 4s, 8s...) up to MAX_RETRIES.
- **Follow-up: Priority queue** — CRITICAL notifications skip the queue. Use PriorityBlockingQueue.
- **Follow-up: DND/Rate limiting** — Don't spam users. Max 3 notifications per hour per user.
- **Edge cases**: Channel failure (SMS provider down → fallback to email), user without email, template missing
- **Concurrency**: High throughput → use async processing with thread pool + message queue.

---

## 20 · Chat Application 🟡

### Functional Requirements
1. 1-to-1 and group messaging
2. Online/offline status
3. Message history per conversation
4. Typing indicator, read receipts
5. Media sharing (text, image, file)

### Entities
`User` · `Message` · `Conversation` · `GroupConversation` · `MessageType`

### Class Diagram
```
User - id, name, status(ONLINE/OFFLINE), conversations[]
Message - id, sender, content, type(TEXT/IMAGE/FILE), timestamp, status(SENT/DELIVERED/READ)
Conversation (abstract)
 ├── OneToOneConversation - user1, user2
 └── GroupConversation - participants[], admin
     - messages[]
```

### Pattern: **Observer** (new message push), **Mediator** (ChatMediator routes messages)

### Key Code Sketch
```java
public class ChatMediator {
    public void sendMessage(Message msg, Conversation conv) {
        conv.addMessage(msg);
        conv.getParticipants().stream()
            .filter(u -> !u.equals(msg.getSender()))
            .forEach(u -> u.receive(msg));
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class ChatAppDemo {
    public static void main(String[] args) {
        ChatService chat = new ChatService();

        // 1. Register users + go online
        ChatUser alice = chat.registerUser("U1", "Alice");
        ChatUser bob = chat.registerUser("U2", "Bob");
        ChatUser charlie = chat.registerUser("U3", "Charlie");
        alice.goOnline(); bob.goOnline();

        // 2. Start 1-to-1 conversation
        Conversation dm = chat.startOneToOne("U1", "U2");
        chat.sendMessage("U1", dm.getId(), "Hey Bob!", MessageType.TEXT);
        // Output: [Bob] received: Hey Bob! (from Alice)

        // 3. Create group chat
        GroupConversation group = chat.createGroup("Weekend Plans", "U1");
        chat.addToGroup(group.getId(), "U2");
        chat.addToGroup(group.getId(), "U3");

        charlie.goOnline();
        chat.sendMessage("U1", group.getId(), "Hiking on Saturday?", MessageType.TEXT);
        // Output: [Bob] received: Hiking on Saturday? (from Alice)
        //         [Charlie] received: Hiking on Saturday? (from Alice)

        // 4. Message history
        List<Message> history = chat.getHistory(dm.getId()); // all DM messages
    }
}
```
**Flow**: `sendMessage(senderId, convId, content, type)` → creates `Message(sender, content, type)` → `chatMediator.sendMessage(msg, conversation)` → `conversation.addMessage(msg)` → for each participant (except sender): `user.receive(msg)` → if ONLINE: mark DELIVERED

### 🎯 Interview Tips
- **Mediator is key here**: Users don't communicate directly. ChatMediator routes messages → reduces N×N connections to N×1.
- **Message status flow**: SENT → DELIVERED (server received, user online) → READ (user opened). Track per-message.
- **Follow-up: Offline messages** — Store in queue, deliver when user comes online.
- **Follow-up: Typing indicator** — Broadcast "user X is typing" to other participants. Debounce to avoid spam.
- **Follow-up: Group admin features** — Add/remove members, promote admin, group settings.
- **Concurrency**: Multiple messages in same conversation simultaneously. Message ordering → use timestamp + sequence number.
- **Edge cases**: User blocked by another, max group size, message size limits, media upload failures

---

## 21 · In-Memory Job Scheduler 🟡

### Functional Requirements
1. Submit jobs with execution time / cron expression
2. One-time and recurring jobs
3. Priority-based execution
4. Configurable thread pool
5. Job states: QUEUED → RUNNING → COMPLETED / FAILED

### Entities
`JobScheduler` · `Job` · `JobType` · `ExecutionStrategy` · `JobQueue`

### Class Diagram
```
Job
 - id, name, task(Runnable), scheduledTime, priority, status, cronExpr

JobScheduler (singleton)
 - PriorityBlockingQueue<Job>, ExecutorService

ExecutionStrategy (interface)
 ├── OneTimeExecution
 └── RecurringExecution
```

### Pattern: **Strategy** (execution type), **Observer** (job completion callback)

### Key Code Sketch
```java
public class JobScheduler {
    private final PriorityBlockingQueue<Job> queue;
    private final ExecutorService executor;

    public void submit(Job job) { queue.offer(job); }

    public void start() {
        executor.submit(() -> {
            while (true) {
                Job job = queue.take();
                if (job.isReady()) {
                    job.setStatus(RUNNING);
                    job.getTask().run();
                    job.setStatus(COMPLETED);
                } else { queue.offer(job); }
            }
        });
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class JobSchedulerDemo {
    public static void main(String[] args) throws InterruptedException {
        JobSchedulerService scheduler = new JobSchedulerService(4); // 4 worker threads

        // 1. One-time job (runs after 2 seconds)
        scheduler.scheduleOneTime("SendReport",
                () -> System.out.println("Report sent!"), 2000, 1);

        // 2. Recurring job (every 5 seconds)
        scheduler.scheduleRecurring("CleanupCache",
                () -> System.out.println("Cache cleaned!"), 5000, 2);

        // 3. High priority job (runs first if same time)
        scheduler.scheduleOneTime("AlertAdmin",
                () -> System.out.println("Admin alerted!"), 0, 0); // priority=0 = highest

        // Output timeline:
        // t=0s: "Admin alerted!" "\u2713 Job completed: AlertAdmin"
        // t=2s: "Report sent!" "\u2713 Job completed: SendReport"
        // t=5s: "Cache cleaned!" (then re-scheduled for t=10s)
        // t=10s: "Cache cleaned!" (recurring)

        Thread.sleep(12000);
        scheduler.shutdown();
    }
}
```
**Flow**: `scheduleOneTime()` → creates Job(scheduledTime=now+delay) → `queue.offer(job)` | Scheduler thread: `queue.peek()` → `job.isReady()?` → YES: `executor.submit(job.task)` → `onCompleted()` callback → if recurring: `job.nextOccurrence()` → re-submit

### 🎯 Interview Tips
- **PriorityBlockingQueue is the backbone**: Jobs sorted by (scheduledTime, priority). `take()` blocks when empty.
- **Recurring jobs**: After completion, calculate next occurrence and re-submit to queue.
- **Follow-up: Cron expression** — Parse "0/5 * * * *" → next execution time. Use a CronExpression parser.
- **Follow-up: Job dependencies** — Job B runs only after Job A completes. DAG-based scheduling.
- **Concurrency**: Thread pool size is configurable. Separate scheduler thread from worker threads.
- **Edge cases**: Job throws exception → catch, mark FAILED, don't crash scheduler. Job takes too long → timeout.
- **Design decision**: `isReady()` checks `System.currentTimeMillis() >= scheduledTime`. Not-ready jobs go back to queue.

---

## 22 · Rate Limiter 🟡

### Functional Requirements
1. Limit requests per user/IP in a time window
2. Algorithms: Fixed Window, Sliding Window, Token Bucket
3. Return 429 if limit exceeded
4. Configurable limit and window per API

### Entities
`RateLimiter` · `RateLimitStrategy` · `RequestInfo` · `RateLimitConfig`

### Class Diagram
```
RateLimiter
 - strategy, configMap(Map<String, RateLimitConfig>)

RateLimitStrategy (interface)
 ├── FixedWindowStrategy
 ├── SlidingWindowStrategy
 └── TokenBucketStrategy

RateLimitConfig
 - maxRequests, windowSizeMs
```

### Pattern: **Strategy**

### Key Code Sketch
```java
public class TokenBucketStrategy implements RateLimitStrategy {
    private final Map<String, TokenBucket> buckets = new ConcurrentHashMap<>();

    public boolean allowRequest(String clientId) {
        TokenBucket bucket = buckets.computeIfAbsent(clientId, k -> new TokenBucket(config));
        return bucket.tryConsume();
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class RateLimiterDemo {
    public static void main(String[] args) {
        RateLimiterService service = new RateLimiterService();

        // 1. Configure: /api/search = 3 requests per 10 seconds (Token Bucket)
        service.configureApi("/api/search", new TokenBucketStrategy(3, 3333));

        // 2. Simulate requests
        String clientId = "user-123";
        System.out.println(service.checkRequest("/api/search", clientId)); // true (token 3→2)
        System.out.println(service.checkRequest("/api/search", clientId)); // true (token 2→1)
        System.out.println(service.checkRequest("/api/search", clientId)); // true (token 1→0)
        System.out.println(service.checkRequest("/api/search", clientId)); // false! Rate limited
        // Output: "Rate limited: user-123 on /api/search"

        // 3. After waiting, tokens refill
        Thread.sleep(4000); // ~1 token refilled
        System.out.println(service.checkRequest("/api/search", clientId)); // true again
    }
}
```
**Flow**: `checkRequest(api, clientId)` → `rateLimiter.isAllowed(clientId)` → `strategy.allowRequest(clientId)` → TokenBucket: calculate `tokensToAdd = elapsed / refillInterval` → `tokens = min(max, tokens + toAdd)` → if tokens > 0: `tokens--`, return true | else: return false

### 🎯 Interview Tips
- **Know all 3 algorithms deeply**:
  - **Fixed Window**: Simple counter per time window. Problem: burst at window boundary.
  - **Sliding Window Log**: Track timestamp of each request. Remove old ones. Precise but memory-heavy.
  - **Token Bucket**: Tokens refill at constant rate. Each request consumes a token. Allows controlled bursts. **Most commonly asked.**
- **Token Bucket math**: `tokensToAdd = (now - lastRefill) / refillInterval`. Cap at maxTokens.
- **Follow-up: Distributed rate limiter** — Use Redis with `INCR` + `EXPIRE` (Fixed Window) or Redis Sorted Sets (Sliding Window).
- **Follow-up: Per-API config** — Different limits for different endpoints. Map<apiPath, RateLimitConfig>.
- **Concurrency**: `synchronized` block inside `tryConsume()` per client. Or use `AtomicLong` for token count.
- **Edge cases**: Clock skew in distributed systems, sudden traffic spike, whitelist IPs

---

## 23 · Splitwise (Expense Sharing) 🟡

### Functional Requirements
1. Create groups, add members
2. Add expenses — split equally, by exact amount, or by percentage
3. Track balances (who owes whom)
4. Settle up / record payments
5. Expense history

### Entities
`User` · `Group` · `Expense` · `Split` · `SplitStrategy` · `Balance`

### Class Diagram
```
Expense
 - id, description, amount, paidBy(User), splits[], group

Split (abstract)
 ├── EqualSplit
 ├── ExactSplit(amount)
 └── PercentSplit(percent)

SplitStrategy (interface)
 ├── EqualSplitStrategy
 ├── ExactSplitStrategy
 └── PercentSplitStrategy

BalanceSheet
 - Map<UserId, Map<UserId, Double>> balances
```

### Pattern: **Strategy** (split type)

### Key Code Sketch
```java
public class ExpenseService {
    public void addExpense(Expense e) {
        List<Split> splits = e.getSplitStrategy().split(e);
        for (Split s : splits) {
            balanceSheet.update(e.getPaidBy(), s.getUser(), s.getAmount());
        }
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class SplitwiseDemo {
    public static void main(String[] args) {
        SplitwiseService service = new SplitwiseService();

        // 1. Register users
        service.registerUser("U1", "Alice", "alice@email.com");
        service.registerUser("U2", "Bob", "bob@email.com");
        service.registerUser("U3", "Charlie", "charlie@email.com");

        // 2. Create group
        SplitwiseGroup group = service.createGroup("Trip", List.of("U1", "U2", "U3"));

        // 3. Alice pays ₹900 for dinner → split equally
        service.addExpense(group.getId(), "U1", 900, "Dinner", SplitType.EQUAL,
                List.of(new EqualSplit(users.get("U1")),
                        new EqualSplit(users.get("U2")),
                        new EqualSplit(users.get("U3"))));
        // Each owes ₹300. Bob owes Alice ₹300, Charlie owes Alice ₹300

        // 4. Bob pays ₹600 for cab → split by percentage
        service.addExpense(group.getId(), "U2", 600, "Cab", SplitType.PERCENT,
                List.of(new PercentSplit(alice, 50),   // ₹300
                        new PercentSplit(bob, 25),     // ₹150
                        new PercentSplit(charlie, 25))); // ₹150

        // 5. Show balances (netted)
        service.showBalances();
        // Alice owes Bob: ₹300 - ₹300 = ₹0  (net: nothing)
        // Charlie owes Alice: ₹300, Charlie owes Bob: ₹150

        // 6. Settle up
        service.settleUp("U3", "U1", 300); // Charlie pays Alice ₹300
    }
}
```
**Flow**: `addExpense(paidBy, amount, splits)` → based on SplitType: EQUAL→amount/N each, PERCENT→amount×%/100 each, EXACT→as given → validate splits sum == total → for each split: `balanceSheet.update(paidBy, owes, splitAmount)` → netted in Map<userId, Map<userId, Double>>

### 🎯 Interview Tips
- **Three split types, one Strategy**: Equal (amount / N), Exact (user provides each share), Percent (user provides %). Validate: shares must add up to total.
- **Balance sheet design**: `Map<userId, Map<userId, Double>>` where `balances[A][B] > 0` means A owes B. Simplify by netting: if A owes B ₹100 and B owes A ₹30, net = A owes B ₹70.
- **Follow-up: Minimize transactions** — Settle debts with minimum number of transfers. This is an NP-hard problem; greedy works for simple cases (match max creditor with max debtor).
- **Follow-up: Multi-currency** — Convert to base currency using exchange rates.
- **Edge cases**: Expense with only one person (self-expense), 0 amount split, person not in group, rounding errors (use `BigDecimal` in production)
- **Concurrency**: Two expenses added to same group simultaneously → synchronize balance updates.

---

## 24 · Ride Sharing (Uber/Ola) 🔴

### Functional Requirements
1. Rider requests ride (pickup, drop)
2. Match nearest available driver
3. Driver accepts/rejects
4. Ride states: REQUESTED → ACCEPTED → IN_PROGRESS → COMPLETED → CANCELLED
5. Fare calculation (distance × rate + surge)
6. Rating system, payment

### Entities
`Rider` · `Driver` · `Ride` · `Location` · `FareStrategy` · `MatchingStrategy` · `Payment` · `Rating`

### Class Diagram
```
Rider - id, name, location, rating
Driver - id, name, location, vehicleDetails, isAvailable, rating
Ride - id, rider, driver, pickup, drop, status, fare, payment

FareStrategy (interface)
 ├── StandardFareStrategy
 └── SurgeFareStrategy

MatchingStrategy (interface)
 ├── NearestDriverStrategy
 └── HighestRatedDriverStrategy

RideState (interface)
 ├── RequestedState → AcceptedState → InProgressState → CompletedState
 └── CancelledState
```

### Pattern: **Strategy** (fare, matching), **Observer** (ride updates), **State** (ride lifecycle)

### Key Code Sketch
```java
public class RideService {
    private MatchingStrategy matchingStrategy;
    private FareStrategy fareStrategy;

    public Ride requestRide(Rider rider, Location pickup, Location drop) {
        Driver driver = matchingStrategy.findDriver(rider, pickup);
        double fare = fareStrategy.calculate(pickup, drop);
        Ride ride = new Ride(rider, driver, pickup, drop, fare);
        ride.setState(new RequestedState());
        return ride;
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class RideSharingDemo {
    public static void main(String[] args) {
        RideService service = new RideService(
                new NearestDriverStrategy(),
                new StandardFareStrategy(50, 12)); // base ₹50 + ₹12/km

        // 1. Register
        service.registerDriver(new Driver("D1", "Ravi", new Location(12.9, 77.6), "KA-01-1234"));
        service.registerRider(new Rider("R1", "Alice", new Location(12.91, 77.61)));

        // 2. Request ride
        Ride ride = service.requestRide("R1",
                new Location(12.91, 77.61),  // pickup
                new Location(12.95, 77.65)); // drop
        // → "Ride requested. Driver: Ravi | Fare: ₹120.00"

        // 3. State transitions
        service.acceptRide(ride.getId());   // REQUESTED → ACCEPTED
        service.startRide(ride.getId());    // ACCEPTED → IN_PROGRESS
        service.completeRide(ride.getId()); // IN_PROGRESS → COMPLETED, driver freed

        // 4. Enable surge pricing (2x)
        service.enableSurge(2.0);
        // Next ride: fare = (50 + 12*distance) × 2.0

        // 5. Rate
        service.rateDriver(ride.getId(), 5.0);
        service.rateRider(ride.getId(), 4.5);
    }
}
```
**Flow**: `requestRide()` → `matchingStrategy.findDriver(drivers, pickup)` → `fareStrategy.calculate(pickup, drop)` → driver.setAvailable(false) → create Ride(RequestedState) | State machine: `nextState()` delegates to current state → `RequestedState.next()` → sets AcceptedState → ... → `CompletedState`: driver freed

### 🎯 Interview Tips
- **This is a pattern-heavy problem — use 3 patterns**: Strategy (fare + driver matching), State (ride lifecycle), Observer (live tracking). Explain WHY each.
- **Surge pricing**: `SurgeFareStrategy` wraps a base strategy with a multiplier. Decorator-like. Triggered by demand/supply ratio.
- **State transitions**: REQUESTED → ACCEPTED → IN_PROGRESS → COMPLETED. Each state validates what transitions are allowed. Cannot complete from REQUESTED.
- **Follow-up: Driver matching** — Beyond nearest: consider driver rating, acceptance rate, vehicle type.
- **Follow-up: ETA** — Based on distance + traffic. Update in real-time.
- **Concurrency**: Multiple riders requesting same driver → only one should get matched. Lock on `driver.isAvailable`.
- **Edge cases**: Driver rejects → re-match. Both cancel at same time. GPS inaccuracy. Driver goes offline mid-ride.

---

## 25 · Cricket Scorecard (CricInfo) 🔴

### Functional Requirements
1. Track match: teams, innings, overs, balls
2. Each ball: runs, wicket, extras (wide, no-ball)
3. Batsman stats: runs, balls faced, 4s, 6s, strike rate
4. Bowler stats: overs, runs given, wickets, economy
5. Live scorecard display, match result

### Entities
`Match` · `Team` · `Player` · `Innings` · `Over` · `Ball` · `Scorecard` · `BatsmanScore` · `BowlerScore`

### Class Diagram
```
Match - teams[2], innings[], result
Team - name, players[]
Innings - battingTeam, bowlingTeam, overs[], totalRuns, wickets
Over - overNumber, balls[], bowler
Ball - ballNumber, batsman, bowler, runsScored, wicket, extra

ScoreCard
 - batsmanScores(Map<Player, BatsmanScore>)
 - bowlerScores(Map<Player, BowlerScore>)

BatsmanScore - runs, ballsFaced, fours, sixes
BowlerScore - oversBowled, runsConceded, wickets
```

### Pattern: **Observer** (live score updates), **Strategy** (scoring rules)

### Key Code Sketch
```java
public class MatchService {
    public void recordBall(Ball ball) {
        innings.getCurrentOver().addBall(ball);
        scorecard.updateBatsman(ball.getBatsman(), ball.getRunsScored());
        scorecard.updateBowler(ball.getBowler(), ball.getRunsScored(), ball.isWicket());
        notifyObservers(scorecard);
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class CricketDemo {
    public static void main(String[] args) {
        CricketMatchService service = new CricketMatchService();

        Team india = new Team("India", List.of(
                new CricketPlayer("Rohit"), new CricketPlayer("Kohli") /*...*/));
        Team aus = new Team("Australia", List.of(
                new CricketPlayer("Smith"), new CricketPlayer("Starc") /*...*/));

        // 1. Start match
        service.startMatch(india, aus, 20); // T20: 20 overs

        // 2. India bats first
        service.startInnings(india, aus);
        service.recordBall(rohit, starc, 4, false, ExtraType.NONE);  // Rohit hits 4
        service.recordBall(rohit, starc, 0, false, ExtraType.WIDE);  // Wide! +1 run, re-bowl
        service.recordBall(rohit, starc, 0, true, ExtraType.NONE);   // WICKET! Rohit out
        service.recordBall(kohli, starc, 6, false, ExtraType.NONE);  // Kohli hits 6!

        // 3. Show scorecard
        service.showScorecard();
        // === India Innings ===
        // Total: 11/1
        // Batting: Rohit: 4(2) [4s:1, 6s:0] SR:200.0 OUT
        //          Kohli: 6(1) [4s:0, 6s:1] SR:600.0 NOT OUT
        // Bowling: Starc: 0.4 overs - 11/1, Eco: 16.5

        // 4. After both innings
        System.out.println(service.getResult()); // "India wins by 20 runs"
    }
}
```
**Flow**: `recordBall(batsman, bowler, runs, isWicket, extra)` → create Ball → `innings.recordBall(ball)` → update BatsmanScore (runs, fours, sixes) → update BowlerScore (balls, runs, wickets) → add to current Over → if `over.isComplete()`: start new Over → `notifyObservers(scorecard)` for live display

### 🎯 Interview Tips
- **Entity hierarchy matters**: Match → Innings → Over → Ball. Each level aggregates stats from the level below.
- **Extras handling**: Wide/No-ball → runs counted but ball NOT counted in over (re-bowl). `isLegalDelivery()` check.
- **Strike rate**: `(runs × 100) / ballsFaced`. Economy: `runsConceded / oversBowled`.
- **Follow-up: Live updates** — Observer pattern pushes scorecard to all connected displays/apps on every ball.
- **Follow-up: Match types** — ODI (50 overs), T20 (20 overs), Test (unlimited overs, 2 innings each). Configurable.
- **Edge cases**: Super over, DLS method (rain), batsman retired hurt, bowler max overs in limited format
- **Design decision**: Separate BatsmanScore and BowlerScore classes keep stats clean + independently testable.

---

## 26 · LinkedIn 🔴

### Functional Requirements
1. User profile (experience, education, skills)
2. Connect / disconnect (connection request)
3. Post content (text, image), like, comment
4. Feed: posts from connections
5. Job posting, job application
6. Messaging

### Entities
`User` · `Profile` · `Connection` · `Post` · `Comment` · `Job` · `Application` · `Message`

### Class Diagram
```
User - id, profile, connections[], posts[]
Profile - headline, experience[], education[], skills[]
Connection - from, to, status(PENDING/ACCEPTED/REJECTED)
Post - id, author, content, likes[], comments[], timestamp
Job - id, company, title, description, applicants[]
Application - id, user, job, status, resumeUrl
```

### Pattern: **Observer** (feed updates, notifications)

### Key Code Sketch
```java
public class FeedService {
    public List<Post> getFeed(User user) {
        return user.getConnections().stream()
            .flatMap(c -> c.getPosts().stream())
            .sorted(Comparator.comparing(Post::getTimestamp).reversed())
            .limit(50)
            .collect(Collectors.toList());
    }
}

public class ConnectionService {
    public void sendConnectionRequest(User from, User to) {
        Connection c = new Connection(from, to, ConnectionStatus.PENDING);
        to.notify(new ConnectionRequestNotification(from));
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class LinkedInDemo {
    public static void main(String[] args) {
        LinkedInService service = new LinkedInService();

        // 1. Register users + build profiles
        LinkedInUser alice = service.registerUser("U1", "Alice", "alice@email.com");
        alice.getProfile().addExperience(new Experience("SDE-2", "Microsoft",
                LocalDate.of(2022, 1, 1), null));
        alice.getProfile().addSkill("Java");
        LinkedInUser bob = service.registerUser("U2", "Bob", "bob@email.com");

        // 2. Connection flow
        ConnectionRequest req = service.sendConnectionRequest("U1", "U2");
        service.respondToRequest(req.getId(), true); // Bob accepts
        // → Alice.connections now includes Bob (and vice versa)

        // 3. Posts + engagement
        Post post = service.createPost("U1", "Excited to join Microsoft!");
        service.likePost("U2", post.getId());
        service.commentOnPost("U2", post.getId(), "Congrats!");

        // 4. Feed (posts from connections, reverse chronological)
        List<Post> feed = service.getFeed("U2"); // sees Alice's post

        // 5. Jobs
        JobPosting job = service.postJob("Microsoft", "SDE-3",
                "Backend role", List.of("Java", "Distributed Systems"));
        service.applyToJob("U2", job.getId());
        // → "Bob applied to SDE-3 at Microsoft"
    }
}
```
**Flow**: `sendConnectionRequest()` → create Connection(PENDING) → `respondToRequest(accept)` → `from.addConnection(to)` + `to.addConnection(from)` | `getFeed(userId)` → stream user's connections → flatMap their posts → sort by timestamp desc → limit(50) | `applyToJob()` → create JobApplication → add to job's applicant list

### 🎯 Interview Tips
- **This is the most entity-rich problem**: User, Profile, Connection, Post, Comment, Job, Application, Message. Start with User → Profile, then expand.
- **Connection is bidirectional**: Accept → both users add each other. Use a Connection entity with status.
- **Feed algorithm**: Start simple (reverse-chronological from connections). Mention weighted ranking (engagement, relevance) as extension.
- **Follow-up: Job recommendation** — Match user skills to job requiredSkills. Score by overlap.
- **Follow-up: "People you may know"** — 2nd degree connections (friends of friends). Graph BFS.
- **Edge cases**: Withdraw pending request, block user, connection limit, post visibility (public/connections-only)
- **Design decision**: Separate services for each concern — FeedService, ConnectionService, JobService, MessagingService.

---

## 27 · Amazon Locker 🔴

### Functional Requirements
1. Locker sizes: Small, Medium, Large
2. Assign appropriate locker to package based on size
3. Generate OTP for pickup
4. OTP expires in 3 days; package returned if uncollected
5. Locker freed after pickup or expiry

### Entities
`LockerSystem` · `Locker` · `LockerSize` · `Package` · `Order` · `OTP` · `Notification`

### Class Diagram
```
LockerSystem
 - lockerLocations(Map<LocationId, List<Locker>>)

Locker
 - id, size, location, isOccupied, currentPackage, otp

Package
 - id, orderId, size, assignedLocker, deliveredAt

LockerAssignmentStrategy (interface)
 ├── SizeMatchStrategy
 └── NearestAvailableStrategy

LockerState (interface)
 ├── AvailableState
 ├── OccupiedState
 └── ExpiredState
```

### Pattern: **State** (locker lifecycle), **Strategy** (assignment)

### Key Code Sketch
```java
public class LockerService {
    public String assignLocker(Package pkg, String locationId) {
        Locker locker = assignmentStrategy.assign(pkg.getSize(), locationId);
        locker.setPackage(pkg);
        locker.setState(new OccupiedState());
        String otp = generateOTP();
        locker.setOtp(otp);
        notifyCustomer(pkg.getOrderId(), otp, locker.getId());
        return otp;
    }

    public boolean pickupPackage(String lockerId, String otp) {
        Locker locker = getLocker(lockerId);
        if (locker.getOtp().equals(otp) && !isExpired(locker)) {
            locker.release();
            return true;
        }
        return false;
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class AmazonLockerDemo {
    public static void main(String[] args) {
        LockerService service = new LockerService(new SizeMatchStrategy());

        // 1. Setup locker location
        List<Locker> lockers = List.of(
                new Locker("L1", LockerSize.SMALL, "LOC-1"),
                new Locker("L2", LockerSize.MEDIUM, "LOC-1"),
                new Locker("L3", LockerSize.LARGE, "LOC-1"));
        service.addLockerLocation("LOC-1", lockers);

        // 2. Assign locker to package (small package → smallest fitting locker)
        LockerPackage pkg = new LockerPackage("PKG-1", "ORD-100", LockerSize.SMALL);
        String otp = service.assignLocker(pkg, "LOC-1");
        // → "Package PKG-1 assigned to locker L1 | OTP: 482910"

        // 3. Customer picks up with OTP
        service.pickup("L1", otp);   // "Package picked up from locker L1"
        // Locker L1 now AVAILABLE again

        // 4. If not picked up in 3 days → expired (handled by scheduled task)
        service.displayStatus("LOC-1");
        // Locker L1 [SMALL] - AVAILABLE
        // Locker L2 [MEDIUM] - AVAILABLE
        // Locker L3 [LARGE] - AVAILABLE
    }
}
```
**Flow**: `assignLocker(pkg, locationId)` → `strategy.assign(lockers, pkgSize)` [finds smallest available locker that fits] → `locker.assignPackage(pkg)` → state: Available→Occupied → generate 6-digit OTP → schedule 3-day expiry timer → notify customer | `pickup(lockerId, otp)` → `locker.pickupPackage(otp)` → validate OTP → `locker.release()` → state: Occupied→Available

### 🎯 Interview Tips
- **Size matching is the core algorithm**: Assign smallest locker that fits the package → minimize waste. Strategy pattern for different assignment policies.
- **OTP security**: 6-digit random, expires in 3 days. After expiry → return to sender, locker freed.
- **State transitions**: Available → Occupied (package delivered) → Available (picked up) or Expired (3 days)
- **Follow-up: Return package** — User drops off return in locker. Assign locker, generate return label.
- **Follow-up: Multi-package** — One customer, multiple packages → multiple lockers with same OTP? Or one large locker?
- **Concurrency**: Two packages assigned to same locker → lock on locker.isAvailable check.
- **Edge cases**: All lockers full at a location, wrong OTP, expired OTP, package too large for any locker

---

## 28 · Concert Ticket System 🔴

### Functional Requirements
1. Events with venue, date, seat categories (VIP, Regular, Balcony)
2. Dynamic pricing (demand-based)
3. Seat selection and hold (temporary lock)
4. Waitlist if sold out → notify when available
5. Bulk booking, refund on cancellation

### Entities
`Event` · `Venue` · `Seat` · `SeatCategory` · `Booking` · `User` · `Waitlist` · `PricingStrategy`

### Class Diagram
```
Event - id, name, venue, dateTime, seatCategories[]
SeatCategory - type, basePrice, seats[]
Seat - id, category, status(AVAILABLE/HELD/BOOKED)
Booking - id, user, event, seats[], totalAmount, status

PricingStrategy (interface)
 ├── FixedPricingStrategy
 └── DemandBasedPricingStrategy

Waitlist (per event+category)
 - Queue<User>
```

### Pattern: **Observer** (waitlist notification), **Strategy** (pricing)

### Key Code Sketch
```java
public class TicketService {
    public Booking holdSeats(User u, Event e, List<Seat> seats) {
        seats.forEach(s -> { s.hold(); scheduleRelease(s, 10, MINUTES); });
        double total = pricingStrategy.calculate(seats);
        return new Booking(u, e, seats, total, BookingStatus.HELD);
    }

    public void confirmBooking(Booking b, PaymentStrategy ps) {
        ps.pay(b.getTotal());
        b.getSeats().forEach(Seat::book);
        b.setStatus(BookingStatus.CONFIRMED);
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class ConcertTicketDemo {
    public static void main(String[] args) {
        ConcertTicketService service = new ConcertTicketService(new DemandBasedPricing());

        // 1. Setup event
        Venue venue = new Venue("V1", "Madison Square", "NYC", 5000);
        Event event = new Event("E1", "Coldplay Live", venue,
                LocalDateTime.of(2026, 6, 15, 19, 0));
        event.addSeats("VIP", createSeats(100, "VIP", 5000));    // 100 VIP seats @ ₹5000 base
        event.addSeats("Regular", createSeats(400, "Regular", 1500)); // 400 regular
        service.addEvent(event);

        // 2. Hold seats (locked for 10 min)
        User alice = new User("U1", "Alice", "alice@email.com");
        ConcertBooking booking = service.holdSeats(alice, "E1", "VIP", 2);
        // → 2 VIP seats held. Demand-based price: ₹5000×1.0 = ₹10000 total

        // 3. Confirm + pay
        service.confirmBooking(booking.getId(), new CardPayment("4111...1111"));
        // → "Paid ₹10000 via Card. Booking confirmed."

        // 4. If sold out → join waitlist
        service.joinWaitlist("E1", "VIP", (evt, category) ->
                System.out.println("VIP seat available! Book now."));

        // 5. Cancel → refund + notify waitlist
        double refund = service.cancelBooking(booking.getId());
        // → "Cancelled. Refund: ₹8000" (80%). Waitlist notified.
    }
}
```
**Flow**: `holdSeats()` → `event.getAvailableSeats(category)` → `seat.hold()` [synchronized] → schedule 10-min auto-release → `pricingStrategy.calculatePrice()` [DemandBased checks remaining count] → create Booking(HELD) | `confirmBooking()` → pay → seat.book() | `cancelBooking()` → seat.release() → `waitlist.notifyNext()`

### 🎯 Interview Tips
- **Similar to BookMyShow but with**: Dynamic pricing + waitlist. Two extra dimensions of complexity.
- **Demand-based pricing**: More seats sold → higher price for remaining. `DemandBasedPricing` checks available count: <50 left = 2x, <200 = 1.5x.
- **Waitlist with Observer**: When seats released (cancellation/hold expiry), notify first person on waitlist for that category.
- **Follow-up: Bulk booking** — Lock N seats atomically. All-or-nothing (no partial booking).
- **Follow-up: Refund tiers** — >7 days = 100%, 3-7 = 50%, <3 = 0%. Based on event date minus cancel date.
- **Concurrency**: Heavy concurrency during ticket drops (10K users, 500 seats). Queue-based or `synchronized` seat holding.
- **Edge cases**: Event cancelled → full refund all. Venue changed. Date rescheduled.

---

## 29 · Stock Exchange / Trading System 🔴

### Functional Requirements
1. Users place Buy/Sell orders (market, limit)
2. Order matching engine (price-time priority)
3. Order book per stock (buy heap, sell heap)
4. Execute matched trades
5. Portfolio tracking, trade history

### Entities
`User` · `Stock` · `Order` · `OrderType` · `OrderBook` · `Trade` · `Portfolio` · `MatchingEngine`

### Class Diagram
```
Order
 - id, user, stock, type(BUY/SELL), orderType(MARKET/LIMIT), price, quantity, timestamp, status

OrderBook (per stock)
 - buyOrders (MaxHeap by price, then time)
 - sellOrders (MinHeap by price, then time)

MatchingEngine (Strategy)
 ├── PriceTimePriorityStrategy
 └── ProRataStrategy

Trade
 - id, buyOrder, sellOrder, price, quantity, executedAt

Portfolio
 - userId, holdings(Map<Stock, Integer>), realizedPnL
```

### Pattern: **Observer** (trade execution notification), **Strategy** (matching), **Command** (order placement)

### Key Code Sketch
```java
public class MatchingEngine {
    public List<Trade> match(OrderBook book) {
        List<Trade> trades = new ArrayList<>();
        while (!book.getBuyOrders().isEmpty() && !book.getSellOrders().isEmpty()) {
            Order buy = book.getBuyOrders().peek();
            Order sell = book.getSellOrders().peek();
            if (buy.getPrice() >= sell.getPrice()) {
                int qty = Math.min(buy.getQuantity(), sell.getQuantity());
                trades.add(new Trade(buy, sell, sell.getPrice(), qty));
                // adjust quantities or remove filled orders
            } else break;
        }
        return trades;
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class StockExchangeDemo {
    public static void main(String[] args) {
        TradingService service = new TradingService();

        // 1. Setup
        Stock reliance = new Stock("RELIANCE", "Reliance Industries", 2500);
        service.registerStock(reliance);
        service.registerUser("alice");
        service.registerUser("bob");

        // 2. Alice places BUY order: 10 shares @ ₹2500
        service.placeOrder("alice", "RELIANCE", OrderSide.BUY,
                OrderType.LIMIT, 2500, 10);
        // → Added to buy heap. No sell orders yet → no match.

        // 3. Bob places SELL order: 5 shares @ ₹2480
        service.placeOrder("bob", "RELIANCE", OrderSide.SELL,
                OrderType.LIMIT, 2480, 5);
        // → Match! buyPrice(2500) >= sellPrice(2480)
        // → Trade: RELIANCE 5 @ ₹2480 [alice ← bob]
        // → Alice's order partially filled: 5/10 remaining

        // 4. Charlie sells 5 more @ ₹2490
        service.registerUser("charlie");
        service.placeOrder("charlie", "RELIANCE", OrderSide.SELL,
                OrderType.LIMIT, 2490, 5);
        // → Match! 2500 >= 2490 → Trade: 5 @ ₹2490. Alice fully filled.

        // 5. Show portfolio
        service.showPortfolio("alice");
        // → RELIANCE: 10 shares (LTP: ₹2490)
    }
}
```
**Flow**: `placeOrder()` → create StockOrder → `orderBook.addOrder(order)` [buy→MaxHeap, sell→MinHeap] → `matchingEngine.match(book)` → while bestBuy.price >= bestSell.price: `Trade(buy, sell, sellPrice, min(buyQty, sellQty))` → fill orders (partial or full) → update LTP → update portfolios → notify observers

### 🎯 Interview Tips
- **Order Book is the heart**: Per stock: buy orders in MaxHeap (highest price first), sell orders in MinHeap (lowest price first). Match when `bestBuy >= bestSell`.
- **Price-Time Priority**: Same price → earlier order gets matched first. Use `Comparable` with price + timestamp.
- **Partial fills**: Buy 100 shares, only 60 available → fill 60, keep remaining 40 in order book.
- **Follow-up: Market vs Limit orders** — Market = execute at best available price (always matches). Limit = only at specified price or better.
- **Follow-up: Stop-loss** — Trigger a market sell when price drops below threshold. Stored separately, activated on price change.
- **Concurrency**: This is CRITICAL here. High-frequency trading → millions of orders/sec. Lock per stock's order book. Or lock-free concurrent queues.
- **Edge cases**: Cancel partially filled order, order for non-existent stock, market opens/closes (no matching during off-hours)
- **Portfolio updates**: On each trade, update buyer's and seller's holdings + P&L.

---

## 30 · Task Management (Jira-like) 🔴

### Functional Requirements
1. Create projects, create tasks/stories/bugs under projects
2. Task states: TODO → IN_PROGRESS → IN_REVIEW → DONE
3. Assign to users, set priority and sprint
4. Sub-tasks, dependencies (blocked-by)
5. Comments, attachments, activity log
6. Sprint board view

### Entities
`Project` · `Task` · `TaskType` · `Sprint` · `User` · `Comment` · `Attachment` · `ActivityLog`

### Class Diagram
```
Project - id, name, tasks[], sprints[], members[]
Task - id, title, description, type(STORY/BUG/TASK), status, priority, assignee,
       sprint, subTasks[], dependencies[], comments[], attachments[], activityLog[]
Sprint - id, name, startDate, endDate, tasks[], status(PLANNED/ACTIVE/COMPLETED)

TaskState (interface) ← State Pattern
 ├── TodoState
 ├── InProgressState
 ├── InReviewState
 └── DoneState

Comment - id, author, body, timestamp
ActivityLog - id, user, action, field, oldValue, newValue, timestamp
```

### Pattern: **State** (task lifecycle), **Observer** (activity notifications)

### Key Code Sketch
```java
public class TaskService {
    public Task createTask(Project p, String title, TaskType type, User assignee) {
        Task t = new Task(title, type, new TodoState());
        t.setAssignee(assignee);
        p.addTask(t);
        logActivity(t, "created");
        return t;
    }

    public void transition(Task t, TaskState newState) {
        t.getState().next(t); // validates transition
        logActivity(t, "status changed to " + newState);
        notifyAssignee(t);
    }
}
```

### 🚀 Driver Code (Full Flow)
```java
public class JiraDemo {
    public static void main(String[] args) {
        TaskManagementService service = new TaskManagementService();

        // 1. Setup
        TaskUser alice = service.registerUser("U1", "Alice", "alice@email.com");
        TaskUser bob = service.registerUser("U2", "Bob", "bob@email.com");
        Project project = service.createProject("Payment Service");

        // 2. Create sprint
        Sprint sprint = service.createSprint(project.getId(), "Sprint 1",
                LocalDate.of(2026, 3, 20), LocalDate.of(2026, 4, 3));

        // 3. Create tasks
        TaskItem task1 = service.createTask(project.getId(), "Implement Stripe API",
                TaskType.STORY, "U1");
        TaskItem task2 = service.createTask(project.getId(), "Fix payment timeout",
                TaskType.BUG, "U1");

        // 4. Assign + add to sprint
        service.assignTask(task1.getId(), "U1"); // assigned to Alice
        service.assignTask(task2.getId(), "U2"); // assigned to Bob
        service.addTaskToSprint(sprint.getId(), task1.getId(), project.getId());
        service.addTaskToSprint(sprint.getId(), task2.getId(), project.getId());

        // 5. Add dependency: task2 blocked by task1
        service.addDependency(task2.getId(), task1.getId());

        // 6. State transitions
        service.moveTaskForward(task1.getId()); // TODO → IN_PROGRESS
        service.moveTaskForward(task2.getId()); // BLOCKED! "task2 blocked by unfinished dependencies"
        service.moveTaskForward(task1.getId()); // IN_PROGRESS → IN_REVIEW
        service.moveTaskForward(task1.getId()); // IN_REVIEW → DONE
        service.moveTaskForward(task2.getId()); // NOW works: TODO → IN_PROGRESS

        // 7. Sprint board
        service.showSprintBoard(sprint.getId(), project.getId());
        // ═══ Sprint Board: Sprint 1 ═══
        // ── DONE ──
        //   [STORY] Implement Stripe API → Alice
        // ── IN_PROGRESS ──
        //   [BUG] Fix payment timeout → Bob

        // 8. Activity log
        service.showActivityLog(task1.getId());
        // [10:00] status changed status: TODO → IN_PROGRESS (by Alice)
        // [10:05] status changed status: IN_PROGRESS → IN_REVIEW (by Alice)
        // [10:10] status changed status: IN_REVIEW → DONE (by Alice)
    }
}
```
**Flow**: `createTask()` → Task(TodoState) → `assignTask()` → sets assignee + logs activity | `moveTaskForward()` → check dependencies (all DONE?) → `state.moveForward(task)` → TodoState creates InProgressState → logs ActivityLog(old, new) → notify assignee | `showSprintBoard()` → group sprint tasks by state → display kanban columns

### 🎯 Interview Tips
- **State pattern enforces valid transitions**: Each state knows what transitions are allowed. `TodoState.moveForward()` → InProgressState only. Cannot jump to DoneState.
- **Activity log is an audit trail**: Every change (status, assignee, priority) creates an ActivityLog entry with old/new values.
- **Dependencies are important**: Task A blocked by Task B → A cannot move to IN_PROGRESS until B is DONE. Check before transition.
- **Follow-up: Sprint board** — Group tasks by state for a sprint. `Map<state, List<Task>>`. The classic kanban view.
- **Follow-up: Story points & velocity** — Track points per sprint to predict team velocity.
- **Follow-up: Custom workflows** — Different task types might have different state machines (Bug: TODO → IN_PROGRESS → TESTING → DONE).
- **Concurrency**: Two users editing same task simultaneously → last-write-wins or optimistic locking with version number.
- **Edge cases**: Cyclic dependencies, orphan tasks (project deleted), reassign task during IN_REVIEW, sprint overlap

---

## Pattern Cheat Sheet

| Pattern | When to Use | Problems Using It |
|---------|------------|-------------------|
| **Singleton** | One global instance | Parking Lot, Logger, KV Store, Job Scheduler |
| **Strategy** | Swap algorithms at runtime | Parking Lot fees, Elevator scheduling, Payment, Fare, Split, Rate Limit, Pricing, Matching |
| **State** | Object behavior changes with state | Vending Machine, ATM, Traffic Signal, Elevator, Ride, Amazon Locker, Task Mgmt |
| **Observer** | Notify dependents of changes | Q&A, BookMyShow, Food Delivery, Chat, Cricket, LinkedIn, Waitlist, Stock |
| **Chain of Responsibility** | Pass request through handlers | Logger levels, ATM cash dispenser |
| **Factory** | Create objects without specifying class | Notification channels, Chess pieces |
| **Composite** | Tree structures | File System (File/Directory) |
| **Mediator** | Centralized communication | Chat application |
| **Command** | Encapsulate requests | Stock orders, Undo operations |

---

> 📖 **Full Java implementations with Service layers → lld-code.md**

---

# PART 3 · Quick Revision Tables

## Which Pattern for Which Scenario?

| You See This In Requirements... | Use This Pattern |
|---------------------------------|-----------------|
| "Only one instance", "global access" | **Singleton** |
| "Multiple algorithms", "swap behavior", "types of X" | **Strategy** |
| "Object behavior changes based on state", "state machine" | **State** |
| "Notify when something changes", "real-time updates" | **Observer** |
| "Chain of handlers", "pass to next if can't handle" | **Chain of Responsibility** |
| "Create objects without knowing concrete class" | **Factory** |
| "Tree structure", "files and folders", "part-whole" | **Composite** |
| "Centralize communication between objects" | **Mediator** |
| "Undo/redo", "queue operations", "log commands" | **Command** |
| "Wrap with extra behavior", "add features dynamically" | **Decorator** |

## Concurrency Cheat Sheet

| Scenario | Solution |
|----------|----------|
| Thread-safe map | `ConcurrentHashMap` |
| Thread-safe singleton | Double-checked locking + `volatile` |
| Producer-consumer queue | `BlockingQueue` / `PriorityBlockingQueue` |
| Read-heavy, write-rare | `ReadWriteLock` |
| Atomic counter | `AtomicInteger` / `AtomicLong` |
| Timed tasks | `ScheduledExecutorService` |
| Seat locking / resource hold | `synchronized` block + timer for auto-release |
| Batch processing | `ExecutorService` with fixed thread pool |

## Data Structure Choices

| Need | Use | Why |
|------|-----|-----|
| O(1) lookup by key | `HashMap` | Constant time get/put |
| O(1) lookup + insertion order | `LinkedHashMap` | Maintains insertion order |
| Sorted access | `TreeMap` / `TreeSet` | O(log n) sorted operations |
| Priority-based processing | `PriorityQueue` | O(log n) insert, O(1) peek |
| Thread-safe priority queue | `PriorityBlockingQueue` | Blocking + priority |
| O(1) cache eviction | `HashMap` + `DoublyLinkedList` | LRU Cache pattern |
| FIFO processing | `Queue` / `LinkedList` | Player turns, waitlists |
| Stack/undo operations | `Deque` / `Stack` | LIFO access |

---

# PART 4 · Top 10 Interview Mistakes in LLD

| # | Mistake | Fix |
|---|---------|-----|
| 1 | Jump straight to code | Spend 2-3 min on requirements first |
| 2 | God class (one class does everything) | Split by Single Responsibility |
| 3 | Using `if-else` for types/modes | Use Strategy or State pattern |
| 4 | Public fields everywhere | Use private fields + getters |
| 5 | Ignoring concurrency | At least MENTION thread safety where relevant |
| 6 | Not using interfaces | Program to interface, not implementation |
| 7 | Hardcoding values | Use enums, config objects, constants |
| 8 | No service layer | Separate business logic from model classes |
| 9 | Over-engineering simple problems | Tic-Tac-Toe doesn't need 5 patterns |
| 10 | Not discussing trade-offs | "I chose X because Y. Alternative was Z." |

---

# PART 5 · Problem Similarity Map (If you know X, you can solve Y)

```
Parking Lot ──→ Hotel Booking (slot management by type + time)
                └──→ Amazon Locker (slot assignment by size)
                └──→ Concert Tickets (seat management)

BookMyShow ──→ Concert Tickets (+ dynamic pricing + waitlist)
             └──→ Hotel Booking (date-range availability)

Vending Machine ──→ ATM Machine (both are state machines with physical I/O)
                  └──→ Traffic Signal (state cycling)

Food Delivery ──→ Ride Sharing (agent/driver matching + tracking)
               └──→ Online Shopping Cart (cart + checkout + payment)

Splitwise ──→ can be asked as "simplify debts" algorithm follow-up

Logger ──→ Notification Service (chain of handlers / multi-channel dispatch)

LRU Cache ──→ KV Store (add TTL on top of LRU)

File System ──→ any tree/composite problem

Chat App ──→ Notification Service (message routing + delivery)

Stock Exchange ──→ unique (order book matching engine is specialized)

LinkedIn ──→ most entity-rich; covers: connections, posts, jobs, messaging

Task Management ──→ most state-machine-rich; covers: states, dependencies, sprints
```

---

> 📖 **Full Java implementations with Service layers → lld-code.md**