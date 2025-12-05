## Veda Logistics – React Native Logistics & Delivery Tracking App

This is a mobile Logistics & Delivery Tracking application built with **React Native (0.82)**.  
It implements a complete multi‑role flow for **Customer**, **Driver**, and **Admin**, entirely backed by **mock APIs / static in‑memory data**.

### Tech stack
- **React Native 0.82** (CLI template)
- **Navigation**: `@react-navigation/native`, `@react-navigation/native-stack`, `@react-navigation/bottom-tabs`
- **State management**: React **Context API** (`src/state/AppContext.tsx`)
- **Mock backend**: in‑memory store in `src/mockApi.ts`
- **Icons**: `react-native-vector-icons/MaterialIcons`

---

## Running the app

From the project root:

```sh
# Install dependencies
npm install

# Start Metro
npm start

# In another terminal:
npm run android   # or: npm run ios
```

> Make sure your React Native environment is set up (`Android Studio` / Xcode, emulators, etc.).

---

## Authentication & Roles

Auth is **mocked** and uses static / auto‑generated users. There is no real backend.

- **Screen**: `src/components/AuthScreens.tsx` (`AuthFlow`)
- **Supported roles**: `customer`, `driver`, `admin`
- **Flow**:
  1. Choose role (Customer / Driver / Admin)
  2. Enter email + password (any password works)
  3. Mock login via `mockLogin` in `src/mockApi.ts`
  4. OTP verification step (always succeeds via `mockVerifyOtp`)
  5. Forgot password flow (mocked via `mockForgotPassword`)
- **Session**:
  - Stored via a small in‑memory storage wrapper in `AppContext` (simulating LocalStorage/SessionStorage)
  - Restored on app launch using `SESSION_KEY = 'mock_session_user'`

Example emails created on first login:
- Customer: `alice@example.com`
- Driver: `bob@example.com`
- Admin: `admin@example.com`

---

## Navigation & Screens

Top‑level app wiring:
- `App.tsx` – wraps the app with:
  - `SafeAreaProvider`
  - `AppProvider` (Context)
  - `NavigationContainer`
  - `AppNavigator`

### Navigation

- `src/navigation/AppNavigator.tsx`
  - **Stack**:
    - `Auth` → `AuthFlow` (unauthenticated)
    - `Main` → role‑based tab navigator (authenticated)
  - **Tabs (per role)**:
    - **Customer**: Home, New Order, Orders, Profile
    - **Driver**: Deliveries, Orders, Profile
    - **Admin**: Overview, Orders, Profile
  - Each tab screen uses consistent styling and bottom tab icons (MaterialIcons).

### Screens

All screens live in `src/screens/`:

- `CustomerHome.tsx`
  - Stats: **live deliveries**, **pending**, **in transit**, **delivered**
  - Recent orders list
- `OrderCreate.tsx`
  - Shipping address
  - Delivery options: **Standard / Express / Same‑day**
  - Payment methods (mock): **Card / UPI / Cash on Delivery**
  - Mock payment (80% success) → success / failure UI
- `OrdersList.tsx`
  - Master–detail view of all orders
  - Right‑side order detail panel (`OrderDetails`)
- `DriverDashboard.tsx`
  - Shows orders assigned to the driver
  - Progresses order status:
    - `pending → in_transit → delivered`
  - When marking **delivered**, attaches mock picture proof
- `AdminPanel.tsx`
  - View all orders
  - Assign orders to drivers
  - Change status (e.g., cancel)
  - Delete / remove orders
- `ProfileScreen.tsx`
  - Profile info (name, email, role)
  - Delivery history:
    - Customer: delivered customer orders
    - Driver: delivered driver orders
    - Admin: all orders

Authentication screen:
- `src/components/AuthScreens.tsx` – login + OTP + forgot password

Shared layout & components:
- `src/components/Layout.tsx`
  - `ScreenContainer` – dark header + light rounded body
  - `AppButton` – primary/secondary/danger/ghost
  - `StatPill` – KPI pills on dashboards
- `src/components/shared/OrderComponents.tsx`
  - `OrderCards`, `OrderCardItem`, `OrderDetails`, `Chip`

---

## State Management & Mock API

### Context (`src/state/AppContext.tsx`)

Global state contains:
- `auth` – current user + loading state
- `orders` – list of orders visible for current role
- `notifications` – simple notification items
- `allDrivers` – list of driver users

Exposed actions:
- `login`, `verifyOtp`, `forgotPassword`, `logout`
- `refreshOrders`, `createNewOrder`, `changeOrderStatus`, `removeOrder`, `assignOrder`
- `refreshNotifications`, `markNotificationAsRead`

### Mock API (`src/mockApi.ts`)

In‑memory arrays simulate a backend:
- `users` – seeded with one Customer, one Driver, one Admin
- `orders` – few sample orders with timelines
- `notifications` – created when admin assigns an order

Key functions:
- `mockLogin`, `mockVerifyOtp`, `mockForgotPassword`
- `fetchOrdersForUser(user)`
- `createOrder(customer, address, option, paymentMethod)`
- `updateOrderStatus(orderId, status, {proofImage?})`
- `assignOrderToDriver(orderId, driver)`
- `deleteOrder(orderId)`
- `fetchAllDrivers()`
- `getNotifications(userId)`, `markNotificationRead(id)`

All functions are asynchronous and include small timeouts to simulate network latency.

---

## Feature–Requirement Mapping

- **User Authentication**
  - Sign up / log in / log out: `AuthFlow` + `mockLogin` (auto‑creates new users)
  - Roles: **Admin**, **Customer**, **Driver**
  - OTP verification: `verifyOtp` (mock success)
  - Forgot password: `forgotPassword` (mock)
  - Session: stored via simulated LocalStorage in `AppContext`

- **Home Page (Customer)**
  - Live deliveries, Pending, In transit, Delivered counts – `CustomerHome`
  - Order cards show Order ID + current status

- **Order Creation & Checkout**
  - Shipping address
  - Delivery options: Standard / Express / Same‑day
  - Payment methods: Card / UPI / Cash on Delivery (mock)
  - Success / failure screens after payment – `OrderCreate`

- **Order Tracking**
  - View all orders – `OrdersList`
  - Status timeline: Placed → Shipped (in transit) → Delivered – `OrderDetails.timeline`
  - Picture proof of delivery (mock image) if uploaded by driver

- **Driver Dashboard**
  - Driver‑only view of assigned deliveries – `DriverDashboard`
  - Accept / progress delivery status; upload mock picture proof on delivered

- **Admin Panel**
  - View all orders – `AdminPanel`
  - Assign orders to drivers
  - Change order status (e.g., cancel)
  - Delete / remove orders

- **Notifications**
  - Created when admin assigns an order to a driver – `mockApi.createNotification`
  - Stored per‑user; unread count shown in header badge

- **Profile Page**
  - Profile info (name, email, role)
  - Delivery history per role – `ProfileScreen`




