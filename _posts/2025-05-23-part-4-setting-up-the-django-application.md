---
title: "Part 4: Setting Up the Django Application"
date: 2025-05-28
---
![Part4 banner image.]({{ site.baseurl }}/assets/images/part-4-banner-v1.png)
*Build Your Own USSD APP with Opensource Power*

Welcome to **Part 4** of our Build Your Own USSD App Series! If you’re new, Parts 1-3 covered the theory behind USSD applications, and now we’re diving into the fun stuff: coding our Dental Appointment USSD app. This guide is ideal for developers with basic Python knowledge. In Part 3, I skipped mentioning Django to focus on architecture, but today I’ll introduce this powerful framework before we start coding. Let’s bring our app to life!
#### A Quick Intro to Django

Django is a high-level Python web framework that simplifies rapid development with features like an admin panel and ORM (Object-Relational Mapping). It’s perfect for USSD apps due to its scalability and security. Learn more at Django’s official docs.

### Let’s Dive In: Coding Our USSD App
I’m using Ubuntu for this tutorial and will provide Ubuntu commands. Where commands differ for Windows, I’ll note them explicitly. We’ll break this into three sections:

1. Preparing the Django Application
2. Creating the USSD Journey File, Models, API View, URLs, and Utility Functions
3. Simulating the USSD Service


## 1. Preparing Django Application
Let’s set up our Django environment to ensure everything runs smoothly.

1.  **Download the Starter Project**

    Start by downloading the starter project from my GitHub: https://github.com/jayskar/dental_ussd.  

    * **Option A (With Git)**: If you have Git installed (download from git-scm.com if needed), clone the repository:  
        ```bash
        git clone https://github.com/jayskar/dental_ussd.git
        ```

        ![Part4  image 1.]({{ site.baseurl }}/assets/images/p4-im01.png)


    * **Option B (Without Git)**: Click the green "Code" button on GitHub, select "Download ZIP," and extract using your preferred unzip tool (e.g., WinRAR or 7-Zip).


2. **Navigate to the Project Folder**

    After cloning or extracting, navigate into the project directory:  
    ```plain
    cd dental_ussd
    ```


3. **Create a Virtual Environment**
    
    A virtual environment isolates dependencies to avoid conflicts with your system Python. Create one:  
    ```bash
    python3 -m venv venv
    ```


4. **Activate the Virtual Environment**  

    * **Linux/MacOS**:  
        ```bash
        source venv/bin/activate
        ```


    * **Windows**:  
        ```
        venv\Scripts\activate
        ```




5. **Install Dependencies**

    With the virtual environment active, install the required packages:  
    ```bash
    pip install -r requirements.txt
    ```

    *Troubleshooting*: If you encounter errors, ensure `pip` is up-to-date ( `pip install --upgrade pip` ).

6. **Update configure.py for Compatibility**

    We need to tweak a file in the virtual environment to ensure compatibility with our app’s dependencies. Locate `configure.py` inside your virtual environment (e.g., `venv/lib/python3.x/site-packages/`). Update the following lines:  

    * **Replace**:  
        ```python
        from inspect import getargspec
        ```

        **With**:  
        ```python
        from inspect import argspec as getargspec
        ```


    *   **Replace**:  
        ```python
        from collections import MutableMapping, Mapping
        ```

        **With**:  
        ```python
        from collections.abc import MutableMapping, Mapping
        ```

    Save and exit. This ensures compatibility with older Python versions.

    ![Part4 configure update.]({{ site.baseurl }}/assets/images/part-4-configure-update.png)

7.  **Run Django Migrations**
    
    Apply the initial database setup:
    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```

    *Troubleshooting*: If makemigrations fails, check for syntax errors in your models (we’ll define them later).

8.  **Create an Admin User**

    Set up an admin user to access Django’s admin panel: 
    ```bash
    python manage.py createsuperuser
    ```

    Follow the prompts to enter a username, email, and password.

    ![Part4 createsuperuser.]({{ site.baseurl }}/assets/images/part-4-createsuperuser.png)

9.  **Test the Django App**

    Launch the Django development server:  
    ```bash
    python manage.py runserver
    ```

    ![Part4 runserver.]({{ site.baseurl }}/assets/images/part-4-runserver.png)

    Open your browser and visit `http://127.0.0.1:8000`. You should see Django’s welcome page.

    ![Part4 welcomepage.]({{ site.baseurl }}/assets/images/part-4-welcomepage.png)
    
    *Troubleshooting*: If the server doesn’t start, ensure port 8000 is free (`lsof -i :8000` on Linux, or use `python manage.py runserver 8001`).

10. **Access the Admin Site**

    Visit `http://127.0.0.1:8000/admin` and log in with the credentials from Step 8. You’ll see Django’s admin dashboard.

    ![Part4 djangoadmin.]({{ site.baseurl }}/assets/images/part-4-djangoadmin.png)


**Summary**: Your Django app is now set up! Let’s build the USSD logic next.

## 2. Creating the USSD Journey File, Models, API View, URLs, and Utility Functions
Now that Django is ready, let’s build the core components of our USSD app.
### Database Schema Overview

![Part4 db-schema.]({{ site.baseurl }}/assets/images/part-4-db-schema.png)
Our app needs three models:

* **Patient**: Stores name and phone number.
* **Appointment**: Tracks patient appointments.
* **ClinicAvailability**: Manages available slots per service (appointment_type).

### Steps to Build the USSD App

1. **Define Models**

    In `dental_ussd/models.py`, define the models to organize our data. Here’s a sample:
    ```python
    # dental_ussd/models.py
    from django.db import models
    from django.core.validators import MinValueValidator

    class Patient(models.Model):
        mobile_number = models.CharField(max_length=11, null=False, unique=True)
        name = models.CharField(max_length=100, null=False)
        created_at = models.DateTimeField(auto_now_add=True)
        updated_at = models.DateTimeField(auto_now=True)

        def __str__(self):
            return f"{self.name} ({self.mobile_number})"


    class Appointment(models.Model):
        patient = models.ForeignKey(Patient, on_delete=models.CASCADE, null=False)
        appointment_type = models.CharField(max_length=50, null=False)
        clinic_location = models.CharField(max_length=100, null=False)
        appointment_date = models.DateTimeField(null=False)
        status = models.CharField(
            max_length=20,
            default='scheduled',
            choices=[('scheduled', 'Scheduled'), ('done', 'Done'), ('cancelled', 'Cancelled')]
        )
        created_at = models.DateTimeField(auto_now_add=True)
        updated_at = models.DateTimeField(auto_now=True)

        def __str__(self) -> str:
            """Return a string representation of the appointment."""
            return f"""
            Type: {self.appointment_type}
            Location: {self.clinic_location}
            Date: {self.appointment_date.strftime('%Y-%m-%d %I:%M%p')}
            Status: {self.status}"""


    class ClinicAvailability(models.Model):
        clinic_location = models.CharField(max_length=100, null=False)
        appointment_type = models.CharField(
            max_length=20,
            default='checkup',
            choices=[('Checkup', 'Checkup'), ('Cleaning', 'Cleaning'), ('Filling', 'Filling'), ('Extraction', 'Extraction')]
        )
        available_slots = models.IntegerField(null=False, validators=[MinValueValidator(0)])
        appointment_date = models.DateTimeField(null=False)
        updated_at = models.DateTimeField(auto_now=True)

        class Meta:
            constraints = [
                models.CheckConstraint(
                    check=models.Q(available_slots__gte=0),
                    name='available_slots_non_negative')
            ]

        def __str__(self):
            return f"{self.appointment_type} at {self.clinic_location} on {self.appointment_date}"
    ```

    `models.py` code here: [dental_ussd/models.py](https://github.com/jayskar/dental_ussd/blob/code-part-4/dental_ussd/models.py){:target="_blank"}.

2. **Register Models in Admin**

    In `dental_ussd/admin.py`, add the models to the admin panel: 
    ```python
    # dental_ussd/admin.py
    from django.contrib import admin
    from .models import Patient, Appointment, ClinicAvailability

    @admin.register(Patient)
    class PatientAdmin(admin.ModelAdmin):
        list_display = ('name', 'mobile_number', 'created_at', 'updated_at')

    @admin.register(Appointment)
    class AppointmentAdmin(admin.ModelAdmin):
        list_display = ('patient', 'appointment_type', 'clinic_location', 'appointment_date', 'status')


    @admin.register(ClinicAvailability)
    class ClinicAvailibilityAdmin(admin.ModelAdmin):
        list_display = ('clinic_location', 'appointment_type', 'available_slots', 'appointment_date', 'updated_at')
    ```

    `admin.py` code here: [dental_ussd/admin.py](https://github.com/jayskar/dental_ussd/blob/code-part-4/dental_ussd/admin.py){:target="_blank"}


3. **Add App to `INSTALLED_APPS`**

    In `dental_app/settings.py`, ensure `dental_ussd` is in `INSTALLED_APPS`: 
    ```python 
    INSTALLED_APPS = [
        ...
        'dental_ussd',
    ]
    ```
    ![Part4 installedapps.]({{ site.baseurl }}/assets/images/part-4-installed-apps.png)


4. **Run Migrations Again**

    Update the database with the new models:  
    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```


5. **Verify Models in Admin**

    Launch the server:
    ```bash  
    python manage.py runserver
    ```

    Visit `http://127.0.0.1:8000/admin`, log in, and confirm you see the `DENTAL_USSD` app with the `Patient`, `ClinicAvailability`, and `Appointment` models.

    ![Part4 admin-dental-ussd.]({{ site.baseurl }}/assets/images/part-4-admin-dental-ussd.png)


6. **Load Sample Data**
    
    Add sample data to ClinicAvailability:
    ```bash 
    python manage.py loaddata fixtures/sample_data.json
    ```
    ![Part4 loaddata.]({{ site.baseurl }}/assets/images/part-4-loaddata.png)

    Check the admin portal (`http://127.0.0.1:8000/admin`) under `ClinicAvailability` to see the data.

    ![Part4 sampledata.]({{ site.baseurl }}/assets/images/part-4-sample-data.png)

    *Note*: For this tutorial, I have used above approach to load sample data to the database, however feel free to manually insert some data to the `ClinicAvilibility` model via the django admin.

7. **Create the API View**

    In `dental_ussd/views.py`, define the view to handle USSD requests. A simplified example:  

    ```python
    import os
    import yaml

    # Additional imported libraries

    class CustomUssdRequest(UssdRequest):
        # This class extends the base UssdRequest to handle USSD screen 
        # content from YAML files.

        def get_screens(self, screen_name=None):
            # Loads screen content from YAML journey files. It finds the specified 
            # screen using the screen name and returns its content. If the file or 
            # screen doesn't exist, it raises appropriate errors.

    @method_decorator(csrf_exempt, name='dispatch')  # Exempt this view from CSRF verification
    class DentalAppUssdGateway(APIView):
        def add_cors_headers(self, response, request):
            # helper function to allow simulator to communicate with 

        def options(self, request, *args, **kwargs):
            # This function handles what's called "preflight checks" - 
            # before making post request,

        def post(self, request):
            # The main method that processes incoming USSD requests:

            # Extracts the user's input from the request
            # Creates a CustomUssdRequest with the user's phone number and input
            # Processes the request through the UssdEngine
            # Returns a formatted response
            # note: full code ommited for breivity, please refer to github for full code

            ussd_request = CustomUssdRequest(
                phone_number=request.data['phoneNumber'].strip('+'),
                session_id=session_id,
                ussd_input=text,
                service_code = request.data['serviceCode'],
                language = request.data.get('language', 'en'),
                use_built_in_session_management=False,
                journey_name = "simple_patient_journey"
            )

            # Process the USSD request using the USSD engine
            ussd_engine = UssdEngine(ussd_request)
            ussd_response = ussd_engine.ussd_dispatcher()
            response = self.ussd_response_handler(ussd_response)

            self.add_cors_headers(response, request)
            return response

        def ussd_response_handler(self, ussd_response):
            # Formats the USSD engine's response into the required format with:

            # A success status
            # The message content
            # The message type (CON for continuing sessions, END for ending sessions)
    ```

    `views.py` code here: [dental_ussd/views.py](https://github.com/jayskar/dental_ussd/blob/code-part-4/dental_ussd/views.py){:target="_blank"}


8.  **Set Up URLs**  

    *   In `dental_ussd/urls.py`, add:  
    
        ```python
        # dental_ussd/urls.py
        from django.urls import path

        from .views import DentalAppUssdGateway

        urlpatterns = [
            path("dental_ussd_gw/", DentalAppUssdGateway.as_view(), name="dental_ussd_gw"),
        ]
        ```

        Code: [dental_ussd/urls.py](https://github.com/jayskar/dental_ussd/blob/code-part-4/dental_ussd/urls.py){:target="_blank"}


    *   In `dental_app/urls.py`, include the app’s URLs:  
        ```python
        from django.contrib import admin
        from django.urls import path, include

        urlpatterns = [
            path('admin/', admin.site.urls),
            path('dental_ussd/', include('dental_ussd.urls')),
        ]
        ```

        Code: [dental_app/urls.py](https://github.com/jayskar/dental_ussd/blob/code-part-4/dental_app/urls.py){:target="_blank"}


9.  **Define the USSD Journey File (YAML)***

    The USSD journey defines the interactive menu flow. First, let’s visualize it:
    ![Part4  ussd journey flow.]({{ site.baseurl }}/assets/images/flow-2.png)
    Now, here’s the YAML representation of that flow, saved as `dental_appointment_menu.yml` in the `journeys` directory:  

    ```yml
    # Initial Screen
    initial_screen: authenticate_user

    # Authentication
    authenticate_user:
    type: function_screen
    function: dental_ussd.utils.authenticate_user
    session_key: patient
    next_screen: router_1

    router_1:
    type: router_screen
    default_next_screen: authenticated_menu
    router_options:
        - expression: "{ { patient == None } }"
        next_screen: non_authenticated_menu

    # Non Authenticated Menu
    non_authenticated_menu:
    type: menu_screen
    text: |
        Welcome! No Profile found.
    options:
        - text: Register (Enter Name)
        next_screen: enter_name
        - text: Exit
        next_screen: end
        
    enter_name:
    type: input_screen
    text: Enter Full Name
    input_identifier: patient_name
    next_screen: register_user

    register_user:
    type: function_screen
    function: dental_ussd.utils.register_user
    session_key: is_registered
    next_screen: register_confirmation

    # Additional nodes excluded
    #

    end:
    type: quit_screen
    text: Thank you for using our service. Goodbye!

    ```
    Full `dental_appointment_menu.yml` here: [journeys/dental_appointment_menu.yml](https://github.com/jayskar/dental_ussd/blob/code-part-4/journeys/dental_appointment_menu.yml){:target="_blank"}


    Update `dental_app/settings.py` to use this file:  
    
    ```python
    DEFAULT_USSD_SCREEN_JOURNEY = os.path.join(BASE_DIR, 'journeys', 'dental_appointment_menu.yml')
    ```
    ![Part4 journey-update.]({{ site.baseurl }}/assets/images/part-4-journey-update.png)


10. **Add Utility Functions**

    In `dental_ussd/utils.py`, add helper functions (you may have to create if it doesn’t exist):  
    ```python
    def authenticate_user(ussd_request):
        resp = None
        phone_number = ussd_request.session.get('phone_number')
        print(phone_number)
        patient_reg = get_or_none(Patient, mobile_number=phone_number)
        print(patient_reg)
        if patient_reg is not None:
            resp = patient_reg
        print(f"[*] resp={resp}")
        return resp

    def register_user(ussd_request):
        resp = None
        patient_name = ussd_request.session.get('patient_name')
        phone_number = ussd_request.session.get('phone_number')
        patient = Patient.objects.get_or_create(
            mobile_number=phone_number,
            name=patient_name)
        if patient is not None:
            resp = patient
        else:
            resp = "Patient already registered"
        print(f"[*] resp={resp}")
        return resp
    ```

    Full utils here: [dental_app/dental_ussd/utils.py](https://github.com/jayskar/dental_ussd/blob/code-part-4/dental_ussd/utils.py){:target="_blank"}.

11. Generate an Authentication TokenIn the admin panel (`http://127.0.0.1:8000/admin`), go to `Tokens` (under `Authtoken`), click `Add Token`, select your user, and save. Copy the generated token (e.g., `05866ced4ed0900591a002cb9ef196b3eed2490a`).

    ![Part4 auth-token.]({{ site.baseurl }}/assets/images/part-4-auth-token.png)



    **Summary**: We’ve built the core USSD app components! Let’s test it next.

## 3. Simulating the USSD Service
Let’s simulate our USSD app using a custom simulator.

1.  **Download the Simulator**

    Clone my forked simulator repo:  
    ```bash
    git clone https://github.com/jayskar/ussd-simulator.git
    ```

    Navigate to the ussd-mock directory: 

    ```bash 
    cd ussd-simulator/ussd-mock
    ```


2.  **Set Up the Simulator**

    * **Install Node.js if needed (nodejs.org)**.  
    * **Install dependencies**:  
    
        ```bash
        npm install
        ```


    * **Edit the `.env` file in `ussd-mock/`**:
    
        Replace `<YOUR_API_TOKEN>` with your token:
        ```plain
        VUE_APP_AUTH_TOKEN=Token 05866ced4ed0900591a002cb9ef196b3eed2490a
        ```

3.  **Launch the Simulator** 

    ```bash
    npm run serve
    ```

    Once compiled, visit http://localhost:8081 in your browser. You’ll see a test phone interface.

    ![Part4 simulator interface.]({{ site.baseurl }}/assets/images/part-4-phone-simulator.png)


4.  **Configure CORS in Django**

    To allow the simulator to communicate with Django, update `dental_app/settings.py`:  

    ```python
    CORS_ALLOWED_ORIGINS = ['http://localhost:8081'] # Change port if your simulator is running on a different port
    CORS_ALLOW_METHODS = ['GET', 'POST']
    CORS_ALLOW_HEADERS = ['Authorization', 'Content-Type']
    CORS_ALLOW_CREDENTIALS = True
    ```

    *Note*: Ensure `django-cors-headers` is installed (`pip install django-cors-headers`) and added to `INSTALLED_APPS`.

5.  **Run Django Server**

    In the Django project directory: 
    ```bash 
    python manage.py runserver
    ```

6.  **Test with Simulator**

    In the simulator (http://localhost:8081), click the phone icon to open the dial pad.  
    Click the call button. You should see the following:  
    ![Part4 auth-token.]({{ site.baseurl }}/assets/images/part-4-simulator-dental-menu.png)

    If you get a prompt `Welcome to the Dental Appointment Service`, check if you have updated USSD Journey File n `dental_app/settings.py`, ensure the journey file path is set (refer to above 2.9): 

    ```python 
    DEFAULT_USSD_SCREEN_JOURNEY = os.path.join(BASE_DIR, 'journeys', 'dental_appointment_menu.yml')
    ```


8.  **Test the USSD Service**  

    *   In the simulator (http://localhost:8081), you can change phone number to any number you choose.  
    *   Dial `*123#` into the phone then click call button.
    *   Navigate through the menu to book appointments. 
    
    *Troubleshooting*: If it fails, verify the token, CORS settings, and journey file path.




## Conclusion
Congratulations! You’ve built and tested a Dental Appointment USSD app using Django. Join the discussion on GitHub Issues and share your feedback. In Part 5, we’ll focus on making the app production-ready with best practices, then move into deployment, and finally touch on scaling for production to handle real-world demand. Happy coding!
