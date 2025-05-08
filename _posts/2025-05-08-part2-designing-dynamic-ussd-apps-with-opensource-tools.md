---
title: "Part 2: Designing Dynamic USSD Apps with Open-Source Tools"
date: 2025-05-08
---
![Part2 banner image.]({{ site.baseurl }}/assets/images/part-2-banner.jpg)
*Build Your Own USSD APP with Opensource Power*

In Part 1, we explored why USSD remains vital in Papua New Guinea, powering essential services without internet access. Now, let’s dive into how USSD apps work and how you can create dynamic, user-friendly experiences for developers, businesses, or telecom enthusiasts.
## Why Build Dynamic USSD Flows?
Dynamic USSD flows improve flexibility, personalization, and scalability, transforming basic menus into intelligent services. Here’s why they matter:

* **Flexibility:** Update services or menus without altering your app’s core code.
* **Personalization:** Offer menus tailored to user profiles, like suggesting data bundles based on low balances.
* **Efficiency:** Minimize downtime when rolling out new offers.
* **Scalability:** Manage growing services with ease.

In short, dynamic USSD delivers better user experiences and faster innovation.
## Static vs. Dynamic USSD Menus
USSD apps fall into two categories:

1. **Static USSD Menus**
    Hard-coded menus with fixed content, ideal for simple tasks like balance checks. Example:
    ```
    1. Check Balance 
    2. Call Support
    ```

2. **Dynamic USSD Menus**
    Menus created in real-time based on user input, account data, or promotions. 
    Example: When you subscribe to Digicel RedClub, the `*675#` menu is updated dynamically to cater for your subscription and available balance


Dynamic menus enable smarter, personalized services.
## Key Concepts Behind a Dynamic USSD App
Dynamic USSD apps rely on a few core concepts. Here’s what you need to know:
### Session Management
USSD is session-based, like a phone call—your app must track each interaction using a unique session ID. If the session is lost, the user’s experience breaks.
### State Machine Design
Each menu is a “state” (or screen) in a choose-your-own-adventure flow. Your app must:

* Track the user’s current state.
* Show available options.
* Handle invalid inputs, like redirecting users who enter wrong choices.

### Menu Retrieval from Backend
Instead of hardcoding menus, your app pulls them from a backend (e.g., a Django server) based on:

* The user’s session state.
* Their mobile number (MSISDN).
* Live data like balance or active promotions.This lets you update offers without rewriting the app.

### Handling Timeouts and Errors
USSD sessions can time out in as little as 180 seconds. To avoid disruptions:

* Guide users quickly with clear options.
* Validate inputs and recover from errors, e.g., `Invalid amount. Try again.`

### Short, Clear Prompts
Most users are on small feature phone screens. Prompts must be concise:

* **Good:** “Enter amount (5-20):”
* **Bad:** “Please specify the desired top-up amount in Kina.”

Fortunately, the open-source `ussd-engine` Python library simplifies session management, state handling, and error processing, letting you focus on designing great user flows.
## USSD Flow Basics *(recap)*
A dynamic USSD session is a conversation between the user and your app. Here’s the flow:

1. **User Input:** User dials a shortcode like `*123#`.
2. **Gateway Request:** The telecom’s USSD gateway sends a request to your app (via HTTP).
3. **App Sends Menu:** Your app responds with a menu, e.g., 
    ```
    1. Buy Data Bundle 
    2. Check Balance
    ```
4. **User Chooses:** The user selects an option, and the gateway sends their choice back.
5. **App Processes Input:** Your app uses logic, databases, or APIs to send the next menu.
6. **Repeat or End:** The process continues until the session ends or times out. If a user doesn’t respond, they’ll need to dial again.

## A Sample Dynamic USSD Flow: Personalized Data Bundle Purchase
This flow demonstrates a dynamic USSD session where the app tailors options based on the user’s balance, usage, and a time-sensitive promotion.

1. **User dials `*123#`**.
2. **App checks the user’s balance and recent data usage in real-time.** 
    App:

    ```
    Welcome! Your balance: K5. Choose an option:  
    1. Buy Data Bundle  
    2. Check Balance  
    3. Special Offer (Today Only!)”
    ```
3. **User selects “1” (Buy Data Bundle).**
    App detects low balance (K5) and tailors bundle options:

    ```
    Low balance detected! Select a data bundle:  
    1. 100MB for K3  
    2. 50MB for K2  
    3. Back
    ```
4. **User selects “1” (100MB for K3).**
    App:

    ```
    Buy 100MB for K3?  
    1. Confirm  
    2. Cancel
    ```
5. **User selects “1” (Confirm).** 
    App processes payment and responds:

    ```
    Success! 100MB added. New balance: K2.
    ```
6. **Alternative Path: If user selects “3” (Special Offer) in step 2.**
    App checks the current date for a time-limited promotion:

    ```
    Today’s Offer: 500MB for K10 (50% bonus data)!  
    1. Buy Now (requires K10 balance)  
    2. Back”
    ```
    If user selects “1” but has insufficient balance (K5 < K10):

    ```
    Insufficient balance (K5). Top up and try again.  
    1. Back”
    ```
7. **Error Handling: If user enters an invalid option (e.g., “4” in step 2):** 
    App:

    ```
    Invalid choice. Please try again:  
    1. Buy Data Bundle  
    2. Check Balance  
    3. Special Offer (Today Only!)
    ```

## Closing Thoughts
Dynamic USSD flows are essential for delivering fast, relevant, and user-friendly mobile services, especially in markets like Papua New Guinea. With open-source tools like `ussd-engine`, you can build modern USSD apps with confidence.

In **Part 3**, we’ll set up `ussd-engine` and create your first USSD menu. Stay tuned!

Have you used USSD services in PNG or elsewhere? Share your thoughts or questions below!
#USSD #DigitalInclusion #PapuaNewGuinea #OpenSource
