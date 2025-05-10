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

* **Good:** `Enter amount (5-20):`
* **Bad:** `Please specify the desired top-up amount in Kina.`

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

## A Sample Dynamic USSD Flow: Booking and Managing Dental Appointments

This flow demonstrates a dynamic USSD session where the app allows users to check past appointments, book new dental visits, and manage cancellations, with menus tailored to their history and real-time clinic availability.

1. **User dials `*123#`.**

    * App authenticates the user via the mobile number (MSISDN) and retrieves their profile from the backend.

    * App Response:
        ```
        Welcome, John!
        1. Check Appointments
        2. Book New Appointment
        3. Cancel Appointment
        4. Exit
        ```

2. **User selects “1” (Check Appointments).**

    * App queries the user’s past and upcoming dental appointments.

    * App Response:
        ```
        Your Dental Appointments: 
        1. Cleaning (10/03/2025, Koki Dental Clinic - Done) 
        2. Checkup (15/05/2025, Gerehu Dental Clinic - Upcoming) 
        3. Back
        ```

    * Alternative (No Appointments):
        ```
        No appointments found. 
        1. Book New Appointment 
        2. Exit
        ```

3. **User selects “2” (Book New Appointment) from the main menu.**

    * App retrieves a list of appointment types and checks clinic availability in real-time.

    * App Response:
        ```
        Select Appointment Type: 
        1. Cleaning 
        2. Checkup 
        3. Filling 
        4. Back
        ```

4. **User selects “2” (Checkup).**

    * App queries available clinics and time slots for a checkup.

    * App Response:
        ```
        Available Checkup Slots: 
        1. Koki Dental Clinic (10/05/2025, 9 AM) 
        2. Gerehu Dental Clinic (12/05/2025, 2 PM) 
        3. Back
        ```

5. **User selects “1” (Koki Dental Clinic, 10/05/2025, 9 AM).**

    * App prompts for confirmation.

    * App Response:
        ```
        Book Checkup at Koki Dental Clinic, 10/05/2025, 9 AM? 
        1. Confirm 
        2. Cancel
        ```

6. **User selects “1” (Confirm).**

    * App saves the appointment and sends an SMS reminder (assumed via a gateway).

    * App Response:
        ```
        Appointment confirmed! You'll get an SMS reminder. 
        1. Back to Main Menu
        ```

7. **Alternative Path: User selects “3” (Cancel Appointment) from the main menu.**

    * App retrieves upcoming appointments.

    * App Response:
        ```
        Select Appointment to Cancel: 
        1. Checkup (15/05/2025, Gerehu Dental Clinic) 
        2. Back
        ```

    * Alternative (No Upcoming Appointments):
        ```
        No upcoming appointments to cancel. 
        1. Book New Appointment 
        2. Exit
        ```

8. **User selects “1” (Checkup, 15/05/2025).**

    * App prompts for cancellation confirmation.

    * App Response:
        ```
        Cancel Checkup on 15/05/2025 at Gerehu Dental Clinic? 
        1. Confirm 
        2. Back
        ```

9. **User selects “1” (Confirm).**

    * App updates the appointment status to “Cancelled.”

    * App Response:
        ```
        Appointment cancelled successfully! 
        1. Back to Main Menu
        ```

10. **Error Handling: Invalid Option (e.g., “5” in step 1).**

    * App Response:
        ```
        Invalid choice. Please try again: 
        1. Check Appointments 
        2. Book New Appointment 
        3. Cancel Appointment 
        4. Exit
        ```

11. **Error Handling: No User Profile Found (e.g., new MSISDN).**

    * App Response:
        ```
        Welcome! No profile found.
        1. Register (Enter Name)
        2. Exit
        ```

    * If user selects "1", they're prompted to enter their name, and a new profile is created.

## Closing Thoughts
Dynamic USSD flows are essential for delivering fast, relevant, and user-friendly mobile services, especially in markets like Papua New Guinea. By understanding the fundamentals, you’re well on your way to creating impactful solutions. 

In Part 3, we’ll roll up our sleeves and dive into the open-source tech stack—using Python, Django, and the ussd-engine library by Francis Mwangi (@mwaaas), alongside Postgres for data management, Ubuntu for our coding environment, VS Code as our IDE, and tools like Postman or cURL to test API requests. We’ll set up the environment and prepare to build your first USSD menu, bringing these concepts to life. Stay tuned!

Have you used USSD services in PNG or elsewhere? What mobile solutions would you like to build with these tools? Share your thoughts or questions below, and get ready for Part 3 by installing Python and VS Code if you’d like to follow along!
#USSD #DigitalInclusion #PapuaNewGuinea #OpenSource
