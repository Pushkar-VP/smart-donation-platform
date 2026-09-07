🧩 First understand how your 3 parts connect

Your project will basically work like this:

              USER
                ↓
        ┌───────────────┐
        │   FRONTEND    │
        │ HTML CSS JS   │
        │   Aaditya     │
        └───────┬───────┘
                │
             fetch()
                │
                ↓
        ┌───────────────┐
        │    FLASK      │
        │ Python Backend │
        │   Shreyash    │
        └───────┬───────┘
                │
                ↓
        ┌───────────────┐
        │    MySQL      │
        │   Database    │
        │   Shreyash    │
        └───────────────┘

For example, suppose Aaditya makes a Login page.

User enters:

Email: pushkar@gmail.com
Password: 123456

JavaScript sends:

POST /api/login

to Flask.

Flask checks MySQL.

If correct:

Login successful
Role = donor

Flask sends that response back.

JavaScript then takes the user to:

donor-dashboard.html

That's the basic concept you three need to understand.

🚨 MOST IMPORTANT RULE

For every feature, follow this exact order:

1. Decide what feature we're building
             ↓
2. Aaditya makes the frontend
             ↓
3. Shreyash makes database + API
             ↓
4. Connect frontend to API
             ↓
5. Test it
             ↓
6. Mark feature COMPLETE
             ↓
7. Move to next feature

Don't move to the next feature until the current one basically works.

🏗️ COMPLETE BEGINNER DEVELOPMENT ROADMAP

I'm going to give you the order I recommend for your exact project.

PHASE 0 — Setup

Before making any webpage, all three of you should set up the project.

Folder

Create:

Smart-Donation-Platform/
│
├── frontend/
│
├── backend/
│
├── database/
│
└── documentation/

Initially:

Aaditya

Works inside:

frontend/
Shreyash

Works inside:

backend/
database/
You

Works inside:

documentation/

You can also work on frontend with Aaditya.

PHASE 1 — Make the HOME PAGE
👨‍🎨 Aaditya's work

First webpage:

index.html

Make a simple home page containing:

Navbar
    Home
    Campaigns
    About
    Login
    Register

Hero section

"Make a Difference"

Browse Campaigns button

How It Works

Categories

Featured Campaigns

Footer
Important

Don't connect anything yet.

Buttons can simply point to pages:

<a href="campaigns.html">Browse Campaigns</a>

etc.

👨‍💻 Shreyash's work

Nothing complicated yet.

He should set up Flask.

For example:

backend/
│
├── app.py
├── database.py
└── requirements.txt

His first goal:

Flask running successfully

When he runs it, you should be able to open something like:

http://127.0.0.1:5000

and get:

Hello Smart Donation Platform
👤 Your work

You document:

Phase 1 – Project Setup

and:

Technology Used

Don't spend too much time writing documentation yet.

PHASE 2 — DATABASE SETUP

Now Shreyash starts the actual database.

Shreyash

Create:

smart_donation

database in MySQL.

Then create the 11 tables we designed:

users
organizations
verification_documents
categories
campaigns
campaign_updates
donations
payments
donor_interests
recommendations
suspicious_activities

He should first get:

MySQL
   ↓
Flask
   ↓
Connection

working.

Insert categories

He should insert:

Education
Medical
Food & Hunger
Disaster Relief
Environment
Animal Welfare
Child Welfare
Elderly Care
Community Development
Other
PHASE 3 — REGISTRATION

This should be your first real connected feature.

Because everything else depends on users being able to exist.

🎨 Aaditya

Make:

register.html

Initially make two choices:

I am a Donor
I am an Organization

Then registration form.

Donor
Full Name
Email
Password
Phone
City
Profile Photo
Preferred Categories
Organization
Organization Name
Email
Password
Phone
Address
City
Description
Registration Number
Authorized Person
Website
Social Media
Documents

Don't worry about making it perfect.

👨‍💻 Shreyash

He needs:

POST /api/register

This API receives the registration information.

For example:

{
    "full_name": "Pushkar Valvi",
    "email": "pushkar@gmail.com",
    "password": "123456",
    "role": "donor"
}

Flask then:

Receive data
     ↓
Validate data
     ↓
Check email already exists?
     ↓
Hash password
     ↓
Insert into users
     ↓
Return success
🔌 How Aaditya connects to Shreyash

Aaditya's JavaScript:

fetch("http://127.0.0.1:5000/api/register", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        full_name: name,
        email: email,
        password: password,
        role: role
    })
})
.then(response => response.json())
.then(data => {
    console.log(data);
});

Shreyash's Flask:

@app.route("/api/register", methods=["POST"])
def register():

    data = request.get_json()

    # get data
    # validate
    # save in database

    return {
        "message": "Registration successful"
    }

That's your first frontend-backend connection.

You should personally understand this connection very well because you'll be coordinating the project.

🧪 Test #1

You three should test:

Open register.html
       ↓
Enter information
       ↓
Click Register
       ↓
JavaScript sends data
       ↓
Flask receives data
       ↓
MySQL stores user
       ↓
Success message

Then open MySQL and check:

users

You should see the new user.

🎉 Registration is COMPLETE.

Only then move on.

PHASE 4 — LOGIN

Now do login.

🎨 Aaditya

Create:

login.html

Fields:

Email
Password

[ Login ]

Don't have account?
Register
👨‍💻 Shreyash

Create:

POST /api/login

Flow:

Email + password
       ↓
Flask
       ↓
Find user in MySQL
       ↓
Check password
       ↓
Find role
       ↓
Create login session
       ↓
Return success

Response could be:

{
    "message": "Login successful",
    "role": "donor"
}
🔌 Connection

Aaditya:

fetch("/api/login", {
    method: "POST",
    headers: {
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        email: email,
        password: password
    })
})

Shreyash:

@app.route("/api/login", methods=["POST"])
def login():

    data = request.get_json()

    # find user
    # check password

    return {
        "message": "Login successful",
        "role": "donor"
    }

Then JavaScript:

if(data.role === "donor"){
    window.location.href = "donor-dashboard.html";
}
PHASE 5 — DONOR DASHBOARD

Now Aaditya makes:

donor-dashboard.html

At first, use dummy data.

Example:

Welcome Pushkar 👋

Your Donations
₹5,000

Campaigns Supported
4

Recommended Campaigns

[Education Campaign]
[Medical Campaign]
[Food Campaign]
Shreyash

Now make APIs for dashboard.

For example:

GET /api/donor/dashboard

Response:

{
    "total_donated": 5000,
    "campaigns_supported": 4
}

Later Aaditya connects it.

PHASE 6 — CAMPAIGN LIST

Now you build the actual heart of the website.

🎨 Aaditya

Make:

campaigns.html

Display cards:

--------------------------------
| Campaign Image               |
| Help Children Get Education  |
| Education                    |
| ₹65,000 / ₹1,00,000          |
|                              |
| [View Campaign]              |
--------------------------------

Initially:

Use dummy campaign data.

👨‍💻 Shreyash

Create:

GET /api/campaigns

Flask gets active campaigns from MySQL.

Example response:

[
    {
        "campaign_id": 1,
        "title": "Help Children Get Education",
        "category": "Education",
        "goal_amount": 100000,
        "raised_amount": 65000
    },
    {
        "campaign_id": 2,
        "title": "Medical Support",
        "category": "Medical",
        "goal_amount": 200000,
        "raised_amount": 120000
    }
]
🔌 Then connect them

Aaditya:

fetch("/api/campaigns")

Then:

data.forEach(campaign => {
    // create campaign card
});

Now instead of dummy data, the website gets campaigns from MySQL.

🎉 This is your second major connection.
PHASE 7 — CAMPAIGN DETAILS

When someone clicks:

View Campaign

they should see:

Campaign image

Title

Organization

Category

₹65,000 raised
of ₹1,00,000

Progress bar

Description

Beneficiary information

Why money is needed

Location

Campaign dates

Updates

[ Donate Now ]
Shreyash

API:

GET /api/campaigns/<campaign_id>

Example:

GET /api/campaigns/1

returns campaign 1.

Aaditya

JavaScript gets campaign ID:

campaign.html?id=1

Then:

fetch("/api/campaigns/1")

and displays the information.

PHASE 8 — ORGANIZATION SIDE

Now build the organization functionality.

🎨 Aaditya

Create:

organization-dashboard.html

Show:

Total Campaigns
Active Campaigns
Total Donations

[Create Campaign]

My Campaigns
Shreyash

API:

GET /api/organization/dashboard
PHASE 9 — CREATE CAMPAIGN

This is another important feature.

🎨 Aaditya

Create:

create-campaign.html

Form:

Campaign Title
Category
Description
Goal Amount
Start Date
End Date
Location
Beneficiary Information
Fund Usage Reason
Campaign Image
Supporting Document

[Submit Campaign]
👨‍💻 Shreyash

Create:

POST /api/campaigns

Backend:

Receive form
      ↓
Validate
      ↓
Check logged-in organization
      ↓
Save campaign
      ↓
status = pending
PHASE 10 — ADMIN CAMPAIGN APPROVAL

Now you need the admin.

🎨 Aaditya

Create:

admin-dashboard.html

and:

admin-campaigns.html

Display:

Pending Campaigns

Campaign 1
[View]
[Approve]
[Reject]

Campaign 2
[View]
[Approve]
[Reject]
👨‍💻 Shreyash

APIs:

GET /api/admin/campaigns/pending

Approve:

PUT /api/admin/campaigns/<id>/approve

Reject:

PUT /api/admin/campaigns/<id>/reject

Flow:

Organization creates campaign
           ↓
status = pending
           ↓
Admin sees it
           ↓
Approve
           ↓
status = active
           ↓
Campaign appears on campaigns.html

Now you have your complete organization → admin → donor flow.

PHASE 11 — DONATION

Only now start donation.

🎨 Aaditya

Create:

donate.html

Show:

Campaign Name

Smart Suggested Amount

₹750

[ ₹500 ]
[ ₹750 ]
[ ₹1000 ]

OR

Enter amount: ₹____

☐ Donate anonymously

Message:
_________________

[ Continue ]
👨‍💻 Shreyash

First create:

GET /api/donation/suggestion/<campaign_id>

This returns:

{
    "suggested_amount": 750
}

Initially he can make a simple calculation.

Later improve it using:

Previous donations
Average donation
Recent behaviour
Campaign progress
PHASE 12 — PAYMENT

Don't start with a real payment gateway.

For your beginner prototype, do:

[Pay ₹750]

Then show:

Payment Processing...

Payment Successful
Shreyash

API:

POST /api/donations

Backend:

Receive donation
      ↓
Create donation
      ↓
Payment simulation
      ↓
status = successful
      ↓
Create transaction ID
      ↓
Update campaign raised_amount
      ↓
Return success
🔥 Very important donation operation

Suppose:

Campaign goal = ₹100,000
Raised = ₹65,000

User donates:

₹750

Database should become:

Raised = ₹65,750

And:

donations

gets a new record.

This is one of the things you should test carefully.

PHASE 13 — DONATION SUCCESS

Aaditya:

donation-success.html

Display:

🎉 Donation Successful!

Thank you for your contribution.

Amount: ₹750
Campaign: Help Children Get Education
Transaction ID: TXN123456

[Download Receipt]
PHASE 14 — DONATION HISTORY

Aaditya:

my-donations.html

Example:

My Donations

Education       ₹750     Successful
Medical         ₹500     Successful
Food            ₹1000    Successful

Shreyash:

GET /api/donor/donations

returns the logged-in donor's donations.

PHASE 15 — RECEIPT

Shreyash generates the PDF.

API:

GET /api/donations/<id>/receipt

Aaditya:

[ Download Receipt ]

clicks the API.

PHASE 16 — CAMPAIGN UPDATES

Organization:

manage-campaign.html

Add:

Update Title
Update Description
Image

[Post Update]

Shreyash:

POST /api/campaigns/<id>/updates

Donor campaign page:

GET /api/campaigns/<id>/updates
PHASE 17 — RECOMMENDATIONS

Now you finally have enough data for your smart feature.

🎨 Aaditya

Create:

recommendations.html

Cards:

Recommended For You ❤️

Education Campaign
92% Match

Medical Campaign
81% Match

Food Campaign
73% Match
👨‍💻 Shreyash

Create:

GET /api/recommendations

His Python algorithm uses:

Donor's previous donations
        +
Preferred categories
        +
Campaign category
        +
Campaign urgency
        ↓
Score

Then return recommendations.

PHASE 18 — SUSPICIOUS ACTIVITY

Now do admin alerts.

Shreyash

Create the detection logic.

Check:

High-frequency donations
Unusually high amount
Repeated identical transactions
Multiple failed payments

If suspicious:

suspicious_activities

gets a record.

API:

GET /api/admin/alerts
🎨 Aaditya

Create:

admin-alerts.html

Display:

Suspicious Activity

User: XYZ
Risk Score: 82

Reason:
Multiple failed payments

[Review]
[Dismissing]
PHASE 19 — ADMIN ANALYTICS

Finally:

admin-analytics.html

Show:

Total Users
Total Organizations
Total Campaigns
Total Donations
Total Amount Donated

Campaigns by Category
Donations over time

Shreyash provides:

GET /api/admin/analytics

Aaditya displays the data.

🧭 So your EXACT DEVELOPMENT ORDER is this

Save this somewhere. This is the roadmap you should give your team.

START
  │
  ├── 1. Project setup
  │
  ├── 2. Flask setup
  │
  ├── 3. MySQL database
  │
  ├── 4. Home page
  │
  ├── 5. Registration
  │      ├── Frontend
  │      ├── Backend
  │      ├── Database
  │      └── Connect
  │
  ├── 6. Login
  │      ├── Frontend
  │      ├── Backend
  │      ├── Database
  │      └── Connect
  │
  ├── 7. Donor Dashboard
  │
  ├── 8. Campaign Listing
  │      ├── Frontend
  │      ├── API
  │      └── Connect
  │
  ├── 9. Campaign Details
  │
  ├── 10. Organization Dashboard
  │
  ├── 11. Create Campaign
  │
  ├── 12. Admin Campaign Approval
  │
  ├── 13. Donation
  │
  ├── 14. Payment Simulation
  │
  ├── 15. Donation Success
  │
  ├── 16. Donation History
  │
  ├── 17. Receipt
  │
  ├── 18. Campaign Updates
  │
  ├── 19. Recommendations
  │
  ├── 20. Suspicious Activity
  │
  ├── 21. Admin Analytics
  │
  ├── 22. Testing
  │
  ├── 23. Deployment
  │
  └── FINAL PROJECT
👥 What they should do at the same time

Here's the part that will make your teamwork much easier.

When you're on Registration
Aaditya
register.html
CSS
JavaScript
form validation
Shreyash
users table
POST /api/register
password hashing
validation
You
Check requirements
Coordinate both
Connect frontend + backend
Test registration
Document it
When you're on Login
Aaditya
login.html
JavaScript
Shreyash
POST /api/login
password verification
session
role
You
Connect
Test
Document
When you're on Campaigns
Aaditya
campaigns.html
campaign.html
campaign cards
filters
Shreyash
GET /api/campaigns
GET /api/campaigns/<id>
campaign database queries
You
Connect
Test
Document
🔌 The API concept Shreyash must understand

Tell Shreyash that an API is basically a door between frontend and backend.

For example:

                  API
Frontend  ─────────────────→ Backend
           POST /api/login

Frontend  ←───────────────── Backend
             JSON response

Different doors for different jobs:

Feature	Method	API
Register	POST	/api/register
Login	POST	/api/login
Get campaigns	GET	/api/campaigns
Get campaign	GET	/api/campaigns/1
Create campaign	POST	/api/campaigns
Approve campaign	PUT	/api/admin/campaigns/1/approve
Donate	POST	/api/donations
My donations	GET	/api/donor/donations
Recommendation	GET	/api/recommendations
Donation suggestion	GET	/api/donation/suggestion/1
Campaign updates	POST	/api/campaigns/1/updates
Get updates	GET	/api/campaigns/1/updates
Admin alerts	GET	/api/admin/alerts
Analytics	GET	/api/admin/analytics

They don't need to memorize all of this now. Build one API at a time.

🧑‍🏫 And this is how YOU should manage them

Don't give Shreyash:

"Make the backend."

That's too big.

Instead, every morning/every work session give him one small task.

For example:

Day 1

Shreyash, today only set up Flask and make sure the server runs.

Day 2

Today connect Flask to MySQL.

Day 3

Today create the users table and test inserting one user.

Day 4

Today make /api/register.

Day 5

Today we'll connect Aaditya's registration form to your API.

That's much easier for beginners.

⭐ Your first 7 tasks

If you're starting right now, don't worry about recommendations, donations, admin etc.

Tell them:

TODAY

Aaditya:

Make index.html and basic website navbar/home page.

Shreyash:

Install Flask, create app.py, run the Flask server successfully.

You:

Create the project folder structure and start the project documentation. Also make the task tracker.

NEXT

Aaditya:

Complete Home page.

Shreyash:

Connect Flask to MySQL.

You:

Finalize/check database schema.

NEXT

Aaditya:

Create register.html.

Shreyash:

Create users table and /api/register.

You:

Understand the API and help connect them.

NEXT

Aaditya:

Finish registration UI + JavaScript.

Shreyash:

Finish registration API + password hashing.

You:

Connect → test → fix → document.

NEXT

Move to:

LOGIN

Then:

DONOR DASHBOARD

Then:

CAMPAIGN LIST

Then:

CAMPAIGN DETAILS

And continue down the roadmap.

🚫 One thing I strongly recommend

Don't let Aaditya make all 20+ pages completely before connecting anything.

And don't let Shreyash make 30 APIs without connecting them.

You'll end up with:

Frontend ❌ ───────── ❌ Backend

Instead:

Registration
    ↓
Frontend + Backend + Database
    ↓
TEST ✅

Login
    ↓
Frontend + Backend + Database
    ↓
TEST ✅

Campaign
    ↓
Frontend + Backend + Database
    ↓
TEST ✅

Donation
    ↓
Frontend + Backend + Database
    ↓
TEST ✅

This approach is much easier for three beginners, and if something breaks, you'll know exactly which feature caused it.

Your first milestone should therefore be very small:

Milestone 1: Home page + Flask running + MySQL connected.

Then:

Milestone 2: Registration completely working from HTML → JavaScript → Flask → MySQL.

Once you achieve that second milestone, you will have understood the fundamental architecture of your entire project. Everything else is basically building the same pattern with more features.