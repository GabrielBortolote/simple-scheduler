# Simple Physiotherapy Scheduler API

This is a Simple Scheduler API project, done using DJango framework, entirely implemented in Python. The idea of this project is to create a tutorial about how to develop API's applying the clean-code best practices, Test Driven Development (TDD) and documentation.

In this project I'm going to show you how:

1. Raise the project requirements;
2. Planning and structure the database;
3. Planning views;
4. Create the tests based on the requirements;
5. Planning routes using RESTful API standards;
6. Implement views;
7. Create solid documentation.

## 1. Raise requirements

First of all we need to understand what our project is, so let's define it:

### System definition

"The system is a scheduler system where patients can schedule appointments with physiotherapists."

Here we can notice that there are two different types of users in our system: patients and physiotherapists.

### Requirements / Stories

#### Patient Stories

- Patient Story 1. I, as Patient, would like to login in the system.

- Patient Story 2. I, as Patient, once logged in the system, would like to see available .physiotherapists.

- Patient Story 3. I, as Patient, once logged in the system, would like to filter physiotherapists by specialty.

- Patient Story 4. I, as Patient, once logged in the system, would like to select one physiotherapist and see its profile.

- Patient Story 5. I, as Patient, once logged in the system, would like to see the availability of a physiotherapist.

- Patient Story 6. I, as Patient, once logged in the system, would like to create an appointment with a selected physiotherapist.

- Patient Story 7. I, as Patient, once logged in the system, would like to see a list of appointment waiting for physiotherapist confirmation.

- Patient Story 8. I, as Patient, once logged in the system, would like to see a list of appointment confirmed for the physiotherapists.

- Patient Story 9. I, as Patient, once logged in the system, would like to my appoints in a calendar view.

- Patient Story 10. I, as Patient, once logged in the system, would like to cancel an appointment.

#### Physiotherapist Stories

- Physiotherapist Story 1. I, as Physiotherapists, would like to register on the system.

- Physiotherapist Story 2. I, as Physiotherapists, when registering in the system, would like to register my specialties.

- Physiotherapist Story 3. I, as Physiotherapist, would like to login in the system.

- Physiotherapist Story 4. I, as Physiotherapist, once logged in the system, would like to see list of appointments to confirm.

- Physiotherapist Story 5. I, as Physiotherapist, once logged in the system, would like to see the details about a particular appointment that is waiting for confirmation.

- Physiotherapist Story 6. I, as Physiotherapist, once logged in the system, would like to confirm an appointment.

- Physiotherapist Story 7. I, as Physiotherapist, once logged in the system, would like to see a list of confirmed appointments.

- Physiotherapist Story 8. I, as Physiotherapist, once logged in the system, would like to see the details about a particular appointment already confirmed.

- Physiotherapist Story 9. I, as Physiotherapist, once logged in the system, would like to cancel an appointment.

Now we have a clear idea about what the system is and how it works, we can now structure our database.

## 2. Structure database

Patient:
    - name: str
    - age: int
    - address: foreign key to Address
    - gender: int

Physiotherapist:
    - name: str
    - age: int
    - address: foreign key to Address
    - gender: int
    - specialties: n2n relation with Specialty

Specialty:
    - name: str

Address:
    - country: str
    - state: str
    - city: str
    - neighborhood: str
    - street: str
    - number: str
    - apartment: str
    - latitude: float
    - longitude: float

Appointment:
    - timestamp: datetime
    - location: foreign key to Address
    - cancellation: foreign key to AppointmentCancellation

AppointmentCancellation:
    - appointment: foreign key to Appointment
    - timestamp: datetime
    - reason: str
    - done_by_patient: bool
    - done_by_physiotherapist: bool

## 3. Planning views

On this application we are going to use DRF ([Django Rest Framework](https://www.django-rest-framework.org/)), this framework uses REST Full standards to build API's. In REST, resources are the key abstractions. Everything is treated as a resource, and each resource is uniquely identified by a URI. Resources can represent data, objects, services, or any other concept. With this in mind we can define our resources to the API, they are:

### Resources

1. Patients
2. Physiotherapists
3. Specialties
4. Addresses
5. Appointments

Now we can start the implementation. First thin we need to install django and DRF. You need a Python installed (please use always the latest stable version). Create a virtual environment to store the dependencies and activate it in the terminal:

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`
```

Install the libraries:

```bash
pip install django
pip install djangorestframework
```

Set up a new project with a single application, this is a shortcut command to create django basics structure and configuration:

```bash
django-admin startproject simple_scheduler .  # Note the trailing '.' character
cd simple_scheduler
django-admin startapp scheduler
cd ..
```

A lot of files were created by the django `startproject` and `startapp` commands. The standard created **database** was a *sqlite3* database, this database is the simpler choice, it is a local file stored in the app's root directory. A lot of files and configurations was created, so we need to apply this changes to our database, let's to this suing this command:

```bash
python manage.py migrate
```

We also need a super user access our system:

```bash
python manage.py createsuperuser --email <your_email> --username admin
```

The command will ask you to define a password for the user. Please write down the password in your mind, we are going to need this password further in this tutorial.
