# Complete Project Implementation Guide
## Restaurant Project — Phase 1 to Phase 6

---

# Phase 1 — Foundation (Weeks 1–2)

## Step 1: Project Setup
1. Create a GitHub organization and add all 4 members
2. Create two repositories — `restaurant-frontend` and `restaurant-backend`
3. Set up branch protection on main — no direct pushes, PR required
4. Create a shared `.env.example` file so everyone knows what environment variables are needed
5. Set up a project board (GitHub Projects or Notion) with tasks divided by member

## Step 2: Database Design
1. Open a whiteboard tool (Excalidraw or Figma) and design all tables together as a team before writing any code
2. Define these tables — `users`, `menu_items`, `categories`, `orders`, `order_items`, `reviews`, `user_preferences`, `coupons`, `reservations`
3. Draw all relationships — one user has many orders, one order has many order items, one menu item belongs to one category
4. Agree on column names and data types before anyone writes a model
5. Save this diagram in the repo as `docs/db-schema.png`

## Step 3: Backend Project Setup
1. Create a virtual environment and activate it
2. Install FastAPI, Uvicorn, SQLAlchemy, Alembic, Pydantic, python-jose, passlib, python-multipart, psycopg2
3. Save all dependencies to `requirements.txt`
4. Create folder structure — `routers/`, `models/`, `schemas/`, `services/`, `utils/`, `core/`
5. Set up `database.py` with SQLAlchemy engine and session factory
6. Set up `config.py` to read all environment variables using Pydantic BaseSettings
7. Set up `main.py` with the FastAPI app instance and include all routers
8. Run Alembic init and configure it to point to your models
9. Create all SQLAlchemy models based on the schema designed in Step 2
10. Run first migration to create all tables
11. Test the connection by running the server and hitting `/docs`

## Step 4: Frontend Project Setup
1. Create React app using Vite
2. Install Tailwind CSS and configure it
3. Install React Router, Axios, React Query, React Hot Toast
4. Create folder structure — `pages/`, `components/`, `hooks/`, `services/`, `context/`, `assets/`
5. Set up React Router with placeholder pages for all routes — home, menu, cart, orders, login, signup, profile, admin
6. Set up Axios instance with base URL and auth token interceptor
7. Create an `AuthContext` that holds the current user and token globally

## Step 5: Authentication — Backend
1. Create `users` model with fields — id, name, email, hashed password, role (user/admin), created at
2. Write password hashing utility using passlib bcrypt
3. Write JWT token creation and verification using python-jose
4. Create `/auth/signup` endpoint — validate input, hash password, save user, return token
5. Create `/auth/login` endpoint — check email, verify password, return token
6. Create a `get_current_user` dependency that reads the token from the Authorization header and returns the user
7. Create a `require_admin` dependency that checks if the current user has admin role
8. Test both endpoints in Swagger docs

## Step 6: Authentication — Frontend
1. Build signup page with form — name, email, password, confirm password
2. Build login page with form — email, password
3. On successful login/signup, save the JWT token to localStorage
4. Store user info in AuthContext
5. Create a `ProtectedRoute` component that redirects to login if no token found
6. Add logout button that clears token and redirects to home
7. Test full auth flow end to end

## Step 7: Landing and Static Pages
1. Build landing page — hero section, featured dishes section, why choose us section, footer
2. Build about page — restaurant story, team, location
3. Build contact page — contact form (just UI, no backend needed yet)
4. Make all pages mobile responsive using Tailwind breakpoints

---

# Phase 2 — Core Features (Weeks 3–4)

## Step 1: Menu — Backend
1. Create `categories` model and `menu_items` model
2. Create endpoint to get all categories
3. Create endpoint to get all menu items with optional filters — category, veg only, search query
4. Add pagination to the menu items endpoint using skip and limit parameters
5. Seed the database with at least 20 sample menu items across 4–5 categories for testing

## Step 2: Menu — Frontend
1. Build menu page that fetches and displays all menu items in a grid
2. Add category filter buttons at the top — clicking filters items by category
3. Add a search bar that filters items by name
4. Add a veg/non-veg toggle filter
5. Show item name, image, description, price, and an add to cart button on each card

## Step 3: Cart — Frontend (no backend needed)
1. Create `CartContext` that holds an array of cart items globally
2. Add to cart — if item already in cart, increase quantity; otherwise add new entry
3. Remove from cart — decrease quantity or remove entirely
4. Calculate total price dynamically in the context
5. Build cart sidebar or cart page showing all items, quantities, and total
6. Persist cart to localStorage so it survives page refresh

## Step 4: Order Placement — Backend
1. Create `orders` model with fields — id, user id, total amount, status, created at
2. Create `order_items` model with fields — id, order id, menu item id, quantity, price at time of order
3. Create `POST /orders` endpoint — accepts cart items, creates order and order items, returns order id
4. Create `GET /orders/my-orders` endpoint — returns all orders for the logged in user
5. Create `GET /orders/{order_id}` endpoint — returns single order with all items
6. Create `PATCH /orders/{order_id}/status` endpoint — admin only, updates order status

## Step 5: Order Placement — Frontend
1. Build checkout page that shows cart summary and a place order button
2. On clicking place order, call the create order API
3. On success, clear the cart and redirect to order confirmation page
4. Build order history page that lists all past orders with status badges
5. Build single order detail page showing items, quantities, total, and current status

## Step 6: User Preferences
1. Add `user_preferences` table — user id, veg only, spice level, favorite categories
2. Create `GET /users/preferences` and `PUT /users/preferences` endpoints
3. Build a preferences section on the user profile page with toggles and dropdowns
4. Save preferences on form submit

## Step 7: Reviews — Backend
1. Create `reviews` model with fields — id, user id, menu item id, rating (1–5), comment, created at
2. Create `POST /reviews` endpoint — authenticated users only
3. Create `GET /reviews/item/{menu_item_id}` endpoint — returns all reviews for a dish
4. Add average rating calculation to the menu item response

## Step 8: Reviews — Frontend
1. Build review form component — star rating selector and comment text area
2. Show review form on the menu item detail page only if user is logged in
3. Show list of all reviews below the review form
4. Show average rating and total review count on each menu card

---

# Phase 3 — Payment and Admin (Weeks 5–6)

## Step 1: Razorpay Integration — Backend
1. Create a Razorpay account and switch to test mode
2. Install the Razorpay Python SDK
3. Save your test key id and key secret in the `.env` file
4. Create `POST /payments/create-order` endpoint — creates a Razorpay order with the amount and returns the order id
5. Create `POST /payments/verify` endpoint — receives payment id, order id, and signature from frontend; verify the signature using HMAC SHA256; if valid, mark the restaurant order as paid; if invalid, mark as failed
6. Store transaction id and payment status against the order in the database

## Step 2: Razorpay Integration — Frontend
1. Add the Razorpay checkout script to your HTML
2. On the checkout page, when user clicks pay, first call your backend to create a Razorpay order
3. Open the Razorpay payment popup with the order id and key id
4. On payment success, Razorpay returns payment id, order id, and signature — send these to your backend verify endpoint
5. On verify success, show success page; on failure, show failure page with retry option
6. Use these test card numbers during development — 4111 1111 1111 1111 for success, and test UPI handles provided by Razorpay

## Step 3: Admin Dashboard — Backend
1. Create `GET /admin/orders` endpoint — returns all orders with user info, paginated
2. Create `GET /admin/users` endpoint — returns all users
3. Create `GET /admin/analytics/top-dishes` endpoint — counts order items grouped by menu item, returns top 10
4. Create `GET /admin/analytics/sales` endpoint — returns daily sales totals for the past 30 days
5. All admin endpoints must use the `require_admin` dependency

## Step 4: Admin Dashboard — Frontend
1. Create a separate admin layout with a sidebar — menu items, orders, users, analytics sections
2. Build menu management page — table of all menu items with edit and delete buttons; add new item form with image upload
3. Build orders page — table of all orders with status dropdown to update status inline
4. Build users page — table of all users with basic info
5. Build analytics page — bar chart of top dishes, line chart of daily sales using Recharts library
6. Protect all admin routes using your `require_admin` role check

## Step 5: Image Upload
1. Choose Cloudinary for image hosting — create a free account
2. Install Cloudinary Python SDK in the backend
3. Create `POST /upload/image` endpoint — accepts a file, uploads it to Cloudinary, returns the image URL
4. In the admin menu item form, allow image file selection; on form submit, first upload image and get URL, then save menu item with that URL

---

# Phase 4 — AI Integration (Weeks 7–8)

## Step 1: Prepare Data Pipeline
1. Export all menu items from the database as structured text — format each item as "Name: X. Category: X. Description: X. Price: X. Veg: X"
2. Export all reviews similarly — format as "Item: X. Rating: X. Review: X"
3. Save these as text files in a `data/` folder in the backend

## Step 2: Set Up Vector Store
1. Install LangChain, OpenAI, FAISS-cpu, and tiktoken
2. Save your OpenAI API key in `.env`
3. Create `ai/embeddings.py` — load the menu text file, split it into chunks using LangChain's text splitter, generate embeddings using OpenAI embeddings, and store in a FAISS index
4. Save the FAISS index to disk so you do not regenerate it on every server start
5. Create a function to reload the index — call this whenever menu items are added or updated in admin

## Step 3: RAG Pipeline — Menu Assistant
1. Create `ai/chat.py` — load the FAISS index, create a retriever from it
2. Build a LangChain RAG chain — retriever fetches relevant menu chunks, passes them as context to GPT, GPT answers the user's question
3. Write a clear system prompt — tell the AI it is a restaurant assistant, it should only answer food related questions, and it should recommend dishes based on the context provided
4. Create `POST /ai/chat` endpoint — accepts user message and optional chat history, runs the RAG chain, returns AI response

## Step 4: Chat UI — Frontend
1. Build a floating chat button on the menu page — clicking it opens a chat panel
2. Build the chat panel — message list showing user and AI messages, input box at bottom, send button
3. On send, call `/ai/chat` with the message and the last 5 messages as history
4. Show a typing indicator while waiting for the response
5. Display the AI response when it arrives and scroll to the bottom

## Step 5: Sentiment Analysis — Backend
1. Install the HuggingFace transformers library or use the OpenAI API
2. Create `ai/sentiment.py` — accepts a review text, returns positive, neutral, or negative with a confidence score
3. Option A (free) — use a pre-trained HuggingFace model like `distilbert-base-uncased-finetuned-sst-2-english` locally
4. Option B (simpler) — call OpenAI with a short prompt asking to classify the sentiment
5. Create a background task that runs sentiment analysis on every new review when it is submitted and stores the result in the reviews table
6. Create `GET /admin/analytics/sentiment` endpoint — returns counts of positive, neutral, and negative reviews grouped by menu item

## Step 6: AI Insights — Admin Dashboard
1. Add a sentiment overview section to the admin analytics page
2. Show a donut chart of overall positive vs negative vs neutral reviews using Recharts
3. Show a table of menu items sorted by most negative reviews so admin can identify problem dishes
4. Add a summary card that calls OpenAI with the last 50 reviews and asks it to write a 3-sentence summary of customer feedback — show this summary at the top of the insights section

---

# Phase 5 — Polish and Deploy (Weeks 9–10)

## Step 1: Mobile Responsiveness
1. Go through every page and test on a 375px wide viewport
2. Fix any layouts that break — use Tailwind's sm and md breakpoints
3. Make the navigation collapse into a hamburger menu on mobile
4. Ensure the cart, checkout, and payment flow all work on mobile
5. Test the admin dashboard on a tablet viewport

## Step 2: Error Handling and Loading States
1. Add a global error boundary in React that catches unexpected crashes
2. Add loading spinners or skeleton screens on every page that fetches data
3. Show toast notifications for all success and error actions — order placed, payment failed, item added to cart
4. Handle 401 errors globally in your Axios interceptor — redirect to login if token expires
5. Add proper HTTP error responses in all FastAPI endpoints with clear messages

## Step 3: Security
1. Add rate limiting to your FastAPI app using `slowapi` — limit login to 5 attempts per minute
2. Make sure all passwords are hashed — never store plain text
3. Validate all inputs using Pydantic schemas — reject unexpected fields
4. Add CORS settings in FastAPI — only allow your frontend domain
5. Make sure all admin endpoints are protected with the admin role check

## Step 4: API Documentation
1. Add descriptions to all FastAPI endpoints using docstrings
2. Add example request and response bodies to all schemas
3. Test every endpoint in the auto-generated Swagger UI at `/docs`
4. Export the OpenAPI JSON and save it in the repo as `docs/api.json`

## Step 5: Deploy Backend
1. Create a `Procfile` or `Dockerfile` for the backend
2. Push backend to Railway or Render — connect your GitHub repo
3. Set all environment variables in the Railway/Render dashboard
4. Use Neon or Supabase for the hosted PostgreSQL database
5. Run Alembic migrations on the production database
6. Test all endpoints on the live URL

## Step 6: Deploy Frontend
1. Set your production API base URL as an environment variable in the frontend
2. Push frontend to Vercel — connect your GitHub repo
3. Set the environment variable in the Vercel dashboard
4. Test the full user flow on the live Vercel URL

## Step 7: Seed Production Data
1. Write a seed script that adds sample categories and menu items
2. Create one admin user manually via the database or a one-time script
3. Add sample reviews so the AI sentiment and recommendations have data to work with
4. Test the AI chat with real menu data on production

---

# Phase 6 — Standout Features (Weeks 11–13)

## Feature 1: Real-time Order Updates (WebSockets)

### Backend
1. Install the `websockets` package
2. Create `websocket_manager.py` with a `ConnectionManager` class that holds a dictionary mapping order id to a list of active WebSocket connections
3. Add three methods — `connect` to accept and register the socket, `disconnect` to remove it, and `broadcast` to send a JSON message to all sockets for a given order id
4. Add a WebSocket route `/ws/order/{order_id}` — on connect register the socket, keep it alive in a loop by waiting for incoming messages, on disconnect clean up
5. In your existing order status update endpoint, after saving the new status to the database, call `broadcast` with the order id and new status

### Frontend
1. Create a custom hook `useOrderStatus` that accepts an order id, opens a native WebSocket connection to the backend, listens for messages, updates a local status state, and closes the connection on unmount
2. Build an `OrderTracker` component that takes the order id and uses the hook — display 4 steps (Placed, Confirmed, Preparing, Ready) as a horizontal progress bar
3. Highlight the current step and all previous steps in a different color
4. Add a smooth CSS transition so the progress bar animates forward when status changes
5. Render the OrderTracker component on the order confirmation page immediately after successful payment

---

## Feature 2: Personalized Recommendations

### Backend
1. Use the existing `order_items` and `user_preferences` tables — no new tables needed
2. Create `GET /recommendations/{user_id}` endpoint
3. Inside the endpoint — fetch all past order items for this user, extract the menu item ids, query those items to find their categories, count the most frequent categories, then query menu items in those top categories that the user has NOT ordered before, apply veg filter if the user has veg only preference, return up to 6 items
4. Optionally enhance this with OpenAI — pass the user's order history and preferences as a prompt and ask the model to pick the best 5 item ids from the available menu, then return those items

### Frontend
1. Create a `Recommendations` component that fetches from `/recommendations/{userId}` on mount
2. Display the results in a horizontally scrollable row of menu cards
3. Show it on the homepage below the hero section and on the menu page below the main listing
4. Label it clearly as "You might also like" — only render the section if recommendations are returned

---

## Feature 3: Offer / Coupon System

### Backend
1. Add a `coupons` table to your schema — fields are id, code, discount type (percent or flat), discount value, minimum order value, max uses, used count, is active, expires at
2. Run an Alembic migration to create the table
3. Create `POST /coupons/validate` endpoint — accepts coupon code and order total, checks if coupon exists, is active, has not exceeded max uses, has not expired, and the order total meets the minimum — return the discount amount if valid, return a clear error message if not
4. In your order creation endpoint, accept an optional coupon code, validate it, apply the discount to the total, and increment the coupon's used count after successful payment
5. Create admin endpoints to create, edit, and deactivate coupons

### Frontend
1. Add a coupon input field on the cart or checkout page with an Apply button
2. On clicking Apply, call the validate endpoint with the code and current cart total
3. If valid, show the discount amount and deduct it from the displayed total
4. If invalid, show the error message returned by the backend
5. Pass the coupon code along with the order when placing it
6. In the admin dashboard add a coupon management section — table of all coupons with create and deactivate options

---

## Feature 4: Table Reservation System

### Backend
1. Add a `reservations` table — fields are id, user id, name, phone, date, time slot, number of guests, status (pending, confirmed, cancelled), created at
2. Run an Alembic migration
3. Create `POST /reservations` endpoint — authenticated users only, accepts date, time slot, guest count, and contact details, checks if that slot already has too many bookings (set a max per slot like 5 tables), creates the reservation with pending status
4. Create `GET /reservations/my-reservations` endpoint — returns all reservations for the logged in user
5. Create admin endpoints to view all reservations by date and to confirm or cancel them

### Frontend
1. Build a reservation page — date picker, time slot selector (dropdown of available slots like 12pm, 2pm, 4pm, 7pm, 9pm), guest count selector, name and phone fields, submit button
2. On submit, call the create reservation endpoint and show a confirmation message with the reservation details
3. Add a My Reservations section in the user profile page — list upcoming reservations with their status badge and a cancel button
4. In the admin dashboard add a reservations section — show a calendar or date-filtered table of all reservations with confirm and cancel buttons

---

# Final Checklist Before Submission

1. All pages are mobile responsive
2. All API endpoints return proper error messages
3. Admin dashboard is fully functional with real data
4. AI chat works with actual menu data
5. Payment flow works end to end in Razorpay test mode
6. App is live on a public URL
7. README has setup instructions, live link, team members, and feature list
8. All environment variables are documented in `.env.example`
9. No API keys or secrets are committed to GitHub
10. Seed data is added so reviewers can test without signing up
