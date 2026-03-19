# LLD Handbook — 30 Problems (Quick Reference)

> Each problem: **Functional Requirements → Entities → Class Diagram → Pattern → Service Layer**

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