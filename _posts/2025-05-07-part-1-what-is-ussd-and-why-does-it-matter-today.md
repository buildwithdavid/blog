
![Part1 banner image.]({{ site.baseurl }}/assets/images/part-1-banner.png)

*Build Your Own USSD App with Opensource Power*

In a world where mobile apps and high-speed internet dominate conversations about digital transformation, it's easy to overlook a technology that continues to serve as a digital lifeline for millions - **USSD (Unstructured Supplementary Service Data)**. As a telecommunications VAS engineer in Papua New Guinea (PNG), I see firsthand how vital USSD still is to the majority of our population.

Despite growing smartphone adoption, **most of PNG's population continue to use feature phones devices** that don't rely on apps, Wi-Fi, or 4G data. Even among smartphone users, access to affordable mobile data remains limited, especially outside major urban centers. This series will teach you to build USSD apps that transform lives, using open-source tools like Python and Django.

## What is USSD?
Have you ever dialed something like *120# on your phone to check your airtime balance or *777# to buy a data bundle? That’s USSD in action!

USSD stands for **Unstructured Supplementary Service Data**, sometimes called short codes/feature codes. It’s a simple, fast way to interact with your mobile network using short codes like *123#. Think of it as a text-based menu system that lets you access services directly on your phone—no internet or app required. Let’s break it down:

* **Unstructured:** USSD messages are plain text, sent back and forth in a free-form way. Unlike apps or websites with fixed formats, USSD is like texting simple commands to your network and getting instant replies.
* **Supplementary:** USSD is a built-in feature of your mobile network, just like making calls or sending SMS. It’s always there, ready to use, without needing extra software or data.
* **Service Data:** USSD lets you perform tasks like checking your account balance, topping up airtime, paying bills, transferring money, or subscribing to plans. You dial a code, navigate a menu, and get results in seconds.

## How Does USSD Work?
When you dial a USSD code (e.g. *131#), your phone starts a **real-time communication session** with your mobile network. You’ll typically see a simple text menu pop up on your screen with options like:
```
1. Check Balance
2. Buy Data
3. Transfer Credit
4. More Options
```
You respond by entering the number of your choice (e.g., 1), and the network immediately processes that input and sends back the next prompt or the requested information. This continues as a live session — similar to chatting in real time — but without needing the internet or any app.

## Behind the Scenes: A High-Level Flow
Imagine a farmer in PNG dialing *525# to check their Kinabank account balance. Here's how it works as shown in the diagram below

![Part1 banner image.]({{ site.baseurl }}/assets/images/part-1-flow)

### Explanation of the Flow:
1. **You Dial the Code:** The session begins when a user dials a USSD shortcode like *525#.
2. **Mobile Tower Relays:** The request travels from the phone through the nearest GSM cell tower.
3. **Network Switch Routes:** The Mobile Switching Center (MSC) acts as a traffic controller, forwarding the request to the USSD gateway.
4. **USSD Manager Handles:** The USSD Gateway (USSDGW) manages the session, passing the request to your app (see USSDGW in the diagram).
5. **Your App Responds:** Built with tools like Python and Django, your app processes the request—checking balances or generating menus—and sends a response (e.g., “Balance: K10”).
6. **User Continues:** The farmer sees the response and can reply to keep the session going.

This invisible back-and-forth happens in real time, and most users never realize the technical orchestration involved — they just enjoy a simple, fast experience.

## Why USSD Still Matters in 2025
In PNG and developing countries, USSD remains a powerful tool for digital access and inclusion. Even in 2025, where apps and high-speed networks dominate, USSD bridges the gap for millions who lack internet or smartphones. Its benefits make it indispensable:

* **No Internet Needed:** Works with just a basic mobile signal, no Wi-Fi or mobile data required.
* **Works on Any Phone:** From the simplest button phone to the latest smartphones.
* **Fast and Reliable:** Delivers near-instant responses through the mobile network.
* **Accessible to All:** Simple to use, even for people with limited digital literacy.

## How USSD Is Used in PNG Today
Currently, USSD powers essential services across PNG, connecting people to the digital world:

* 💳 **Mobile Banking & Wallets:** Send money, check bank account balances, or pay bills with codes like *131# (BSP Mobile Banking) and 525# (KinaBank USSD). Digicel’s 888# (CellMoni) offers a mobile wallet for payments.
* 📶 **Topping Up Credit:** Top up airtime or buy data bundles with *777# or *675# (network-specific options), *120# (balance check), *121# (top-up service), *126# (please call me), *127# (request credits), and *128# (transfer credits). These codes keep users connected, especially where data plans are out of reach, showcasing USSD’s everyday value.
* 📡 **Government Services:** Services like GAPMIS (Government Agriculture Price & Market Information Service, *16400#) let farmers check crop prices or market updates. Though underused due to low awareness, GAPMIS highlights USSD’s potential to deliver critical information—setting the stage for Part 2’s tips on better menu design.
## Untapped Potential for USSD in PNG
Beyond current uses, USSD has immense potential to expand digital inclusion, especially in remote areas with limited connectivity. Imagine these applications:

* 🏥 **Healthcare Outreach:** Send appointment reminders, track vaccinations, or share health tips in Tok Pisin (a widely spoken language in PNG) or other local languages. For example, a mother could dial *555# to confirm her child’s immunization schedule.
* 🎓 **Education Access:** Deliver short quizzes, school notices, or learning tips to students and parents. A student could dial *456# to receive a daily math problem.
* 📡 **Additional Government Services:** Enable citizens to register births, check National Identification (NID) status, or track passport applications via a USSD menu. For instance, *123# could guide users through a voter registration process.
* 📋 **Community Surveys:** Collect real-time feedback on public projects or disaster response. A village leader could dial *999# to report flooding impacts.
## Why USSD Shines in 2025
In PNG, slow internet rollout, high smartphone costs, and reliance on basic phones keep USSD relevant. It’s a low-cost, scalable solution that works where apps and websites can’t. However, challenges like network outages or complex menus for less literate users highlight the need for thoughtful design.

USSD isn’t just a technology—it’s a lifeline, connecting PNG’s people to banking, healthcare, education, and government services, no matter where they are. In 2025, it remains the unsung hero of digital inclusion, empowering communities reliably and affordably.

## Why I’m Writing This Series
As a Value-Added Services (VAS) Engineer working with USSD daily in Papua New Guinea, I’ve seen its transformative power—especially when services are designed with real users in mind, like farmers checking crop prices or parents accessing school updates. USSD isn’t just code; it’s a lifeline for communities with limited internet or smartphones.

I’m writing this series: to demystify USSD and show developers, designers, product leads, and even policymakers how to build smart, user-friendly USSD services using open-source tools like Python, Django, and the ussd-airflow-engine (a USSD workflow library). You don’t need to be a telecom expert to start. This series will guide you step-by-step, from idea to deployment, with practical tips to create services that make a difference.

## What You’ll Learn in This Series
This series is your hands-on guide to building USSD services that work for everyone, even on basic phones. HThis series is your hands-on guide to building USSD services that work for everyone, even on basic phones. Here’s what’s coming:

* **Part 1: What is USSD and Why It Matters Today** (you’re reading it now) – USSD’s role in digital inclusion.
* **Part 2: Designing a Dynamic USSD Flow - Key Concepts** – Learn to create interactive menus that adapt to user inputs, handle errors, and deliver seamless experience.
* **Part 3: The Open-Source Stack** – Explore tools like Python, Django, and ussd-airflow-engine to build robust USSD apps.
* **Part 4: Building the App** – Code a USSD service with session handling, menu navigation, and integration with APIs (e.g., for banking or weather data).
* **Part 5: Deployment, Testing, and Scaling** – Launch your app, test it with real users, and scale it for thousands of users.

Each post is practical, clear, and focused on helping you build responsive USSD services that meet real-world needs, like helping a villager check their bank balance with *123#.

## Closing Thoughts
USSD isn’t outdated—it’s *appropriate* tech for places like PNG where internet access and smartphones aren’t universal. It bridges the digital divide, connecting people to banking, education, healthcare, and public services through simple codes. But building great USSD services takes care—menus must be intuitive, and systems must handle diverse user inputs reliably.

Whether you’re a developer eager to code, a designer crafting user experiences, a product lead shaping services, a policymaker exploring digital inclusion, or just curious about mobile tech, this series is for you. By the end, you’ll have the tools to create USSD apps that empower communities.

How can a simple code like *16400# help a farmer check crop prices without internet? In Part 2, we’ll dive into designing dynamic USSD menus that adapt to users, handle mistakes, and work smoothly on any phone in Papua New Guinea. Don’t miss this step to building impactful apps!"

Let’s build digital services that work for *everyone*—See you in Part 2!