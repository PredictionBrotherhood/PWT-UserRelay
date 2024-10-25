
# Directus-Wix Integration Plan for Predict View

## Overview

This document outlines the strategy for integrating **Directus** with **Wix** to build a SaaS platform, Predict View, which provides users with access to prediction markets. This setup includes user signup, login, restricted content access, and a framework for dynamically delivering prediction market data from your Polymarket API-fed database. Future considerations include **Directus-managed payments and subscriptions** for scaling user membership levels.

## Objectives

- **User Management**: Enable secure user signup, login, and role-based access to restricted areas.
- **Waitlist Access**: Allow only authenticated users to join the waitlist.
- **Dynamic Content Delivery**: Use Directus to manage and display prediction markets on Wix, drawing data from Polymarket's API-fed database.
- **Subscription Management**: As Predict View scales, use Directus to handle payments and subscriptions.

## Implementation Plan

---

### 1. User Signup and Registration

The user registration process allows new users to create accounts, providing the foundation for accessing restricted areas and joining the waitlist.

1. **Directus Setup**:
   - Set up a `users` collection to store user data (e.g., name, email, role, password).
   - Create roles like `Basic User`, `Premium User`, and `Admin`, and configure role-based permissions.

2. **Signup Form on Wix**:
   - Build a signup form on Wix where users can provide their information.
   - Form submissions will be sent to a Velo backend function, which securely creates user accounts in Directus and assigns them the `Basic User` role.

3. **API Security**:
   - Use Directus’s **Static Access Token** or **JWT** to ensure secure communication between Velo and Directus.

---

### 2. User Login and Authentication

To view restricted content, users need to log in. The authentication process will use Directus’s JWT system for secure access management.

1. **Login Form on Wix**:
   - Implement a login form on Wix where users enter their credentials.
   - Upon successful login, a Velo backend function authenticates with Directus, retrieves a JWT token, and stores it in session storage for user session management.

2. **Session Management**:
   - Use the stored JWT token to maintain the user’s session across pages.
   - Implement logout functionality to clear session storage, requiring users to log in again to access protected content.

3. **Error Feedback**:
   - Provide meaningful feedback for failed logins, invalid credentials, and session expirations.

---

### 3. User-Restricted Pages and Role-Based Access

Some content, including the waitlist signup and prediction market data, will only be available to logged-in users. Role-based access will allow you to segment access further.

1. **Protected Pages on Wix**:
   - Create restricted pages on Wix, using Velo backend checks to confirm the user’s authentication status.
   - On page load, Velo checks session storage for a JWT token; if missing or expired, users are redirected to the login page.

2. **Role-Based Content Control**:
   - Implement logic in Velo to display content sections based on user roles. For example:
     - Basic Users can view general prediction market data.
     - Premium Users access advanced data, analytics, or additional prediction insights.
     - Admins have full access to all content and control features.

---

### 4. Waitlist Signup for Predict View

Authenticated users can join a waitlist for early access to certain prediction features or premium content.

1. **Waitlist Collection in Directus**:
   - Set up a `waitlist` collection with fields such as `user_id`, `interest`, `comments`, and `signup_date`.

2. **Waitlist Form on Wix**:
   - Build a form on the restricted waitlist page in Wix.
   - On form submission, a Velo backend function sends the data to Directus’s `waitlist` collection using the JWT token for secure access.

3. **User Feedback**:
   - Display a success message on submission or provide error messages if submission fails.

---

### 5. Dynamic Content Delivery: Prediction Markets from Polymarket

To create a compelling, data-driven user experience, use Directus to manage and dynamically render prediction market data from Polymarket’s API.

#### 5.1 Setting Up Prediction Market Collections in Directus

Create collections in Directus to structure prediction market data. This data can be updated via Polymarket’s API and synced with Directus regularly.

- **Collections Examples**:
  - `markets`: Stores data on prediction markets, such as `market_id`, `title`, `description`, `current_odds`, `outcome_options`, etc.
  - `outcomes`: Stores individual outcome details for each market (e.g., `yes` or `no` probabilities).
  - `market_updates`: Tracks market updates and changes in probabilities or outcomes.

#### 5.2 Velo Backend Functions to Fetch Prediction Market Data

Use Velo backend functions to retrieve prediction market data from Directus. This enables dynamic rendering of prediction market content directly on Wix pages.

- **Example**: A Velo backend function (`getPredictionMarkets.jsw`) retrieves current market data from the `markets` collection and returns it to be displayed on the prediction markets page.

#### 5.3 Role-Based Data Display for Prediction Markets

Different users may access different levels of prediction data:
- **Basic Users**: See general data, such as market titles and basic odds.
- **Premium Users**: Access additional data fields, such as deeper analytics or historical trends.
- **Admins**: Have full visibility and editing capabilities.

---

### 6. Future Expansion: Payments and Subscriptions

As Predict View scales, Directus will manage payments and subscriptions for premium access.

#### 6.1 Subscription Collection in Directus

Set up a `subscriptions` collection in Directus to manage user subscriptions and payment status:
- **Fields**:
  - `user_id` (Reference to the user in `users`)
  - `subscription_type` (Type of plan, e.g., Basic, Premium)
  - `start_date` (Subscription start date)
  - `end_date` (Subscription end date for time-limited access)
  - `status` (Active, Pending, Canceled)

#### 6.2 Payment and Subscription Processing

- **Integration with Payment Gateway**:
   - Use a payment gateway like Stripe, PayPal, or others. Webhooks can trigger updates to Directus when payments are completed, updating the `subscriptions` collection with the appropriate status.
   
- **Role-Based Access Control Based on Subscription Status**:
   - Velo backend functions check the user’s subscription status in Directus to ensure they’re allowed access to premium content.
   - If a user’s subscription expires or payment fails, adjust their role or restrict access until payment is resolved.

---

### 7. Directus-Managed Content Blocks for Predict View

As Predict View grows, Directus can support more advanced content management, with dynamically generated blocks displayed on Wix.

1. **Directus-Managed Content Blocks**:
   - Define additional content types, such as blog posts, news updates, or market analysis reports in Directus.
   - Each content type has its own collection (e.g., `blog_posts`, `market_analysis`, `updates`).

2. **Backend Fetching for Dynamic Content Display**:
   - Use Velo backend functions to pull content blocks from Directus and display them in relevant sections on Wix pages.
   - Example: Retrieve market analysis posts for a news feed or blog section on the Predict View platform.

---

## Additional Considerations

- **Data Security**: 
   - Store JWT tokens securely in session storage and manage token expiration. Use Wix Secrets Manager for API token storage.
   
- **Subscription Management**:
   - Centralize subscription handling in Directus, keeping user plans and payment statuses organized.
   - Regularly sync subscription status with the payment gateway and Directus.

- **Error Handling and User Feedback**:
   - Implement thorough error handling to manage failed submissions, expired tokens, and network errors.
   - Provide users with clear feedback, such as alerts for login errors or notifications on form submission.

---

## Summary

This Directus-Wix integration plan for Predict View provides a secure and scalable approach for managing users, handling subscriptions, and dynamically displaying prediction market data. Directus serves as the backend, storing user, subscription, and prediction market data, while Wix Velo handles frontend interaction and middleware processes.

### Key Features

1. **User Registration and Authentication**: Secure signup and login, with role-based access to restricted areas.
2. **Waitlist Signup for Restricted Features**: Enable authenticated users to join the waitlist for early access.
3. **Dynamic Display of Prediction Market Data**: Source market data from Directus and display it dynamically on Wix.
4. **Subscription and Payment Management**: Directus handles subscription plans and payment status for premium access as the platform scales.
5. **Content Blocks for Future Flexibility**: Use Directus as a central CMS to power various types of content on the Predict View platform.

