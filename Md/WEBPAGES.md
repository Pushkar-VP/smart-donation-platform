

🌐 Smart Donation Platform — Website Pages

We'll divide the website into 4 groups:

SMART DONATION PLATFORM
│
├── 🌍 Public Pages
├── 👤 Donor Pages
├── 🏢 Organization Pages
└── 👨‍💼 Admin Pages

### public pages

1. 🌍 PUBLIC PAGES

These are accessible to visitors.

1. Home Page

File:

index.html

Purpose:

Introduce the platform and encourage users to explore causes.

Sections:

Navbar
    ↓
Hero Section
    ↓
Featured Campaigns
    ↓
How It Works
    ↓
Platform Statistics
    ↓
Why Choose Us
    ↓
Call to Action
    ↓
Footer

Main buttons:

[ Explore Causes ]
[ Start a Campaign ]
[ Login ]


2. Browse Campaigns

File:

campaigns.html

Purpose:

Allow users to discover donation campaigns.

Features:

Search
Category filter
Sort
Campaign cards
Campaign progress
Verified organization badge

Flow:

Campaigns
    ↓
Search / Filter
    ↓
Select Campaign
    ↓
Campaign Details

3. Campaign Details

File:

campaign.html

This page should show:

Campaign Image

Campaign Title
Organization ✓

Goal
Amount Raised
Progress Bar
Days Remaining

Description

Campaign Updates

[ DONATE NOW ]

***^^This is one of the most important pages.***

4. About Us

File:

about.html

Explain:

Problem you're solving
Purpose of platform
How the platform works
Transparency
Smart features


5. How It Works

File:

how-it-works.html

For example:

1. Find a Cause
       ↓
2. Choose Amount
       ↓
3. Donate
       ↓
4. Track Impact

***^^ We can have separate explanations for donors and organizations.***

6. Contact

File:

contact.html

Contains:

Name
Email
Subject
Message

[Send Message]

The form can eventually send the message to the backend/admin.

###  AUTHENTICATION PAGES

2. 🔐 AUTHENTICATION PAGES

These are shared by different users.

7. Login

File:

login.html
Email
Password

[ Login ]

Forgot Password?

Don't have an account?
[ Register ]

After login:

       LOGIN
         ↓
   Flask checks role
         ↓
 ┌───────┼────────┐
 ↓       ↓        ↓
Donor   Org      Admin
 ↓       ↓        ↓
Dashboard


8. Registration

We could have:

register.html

Instead of immediately showing a huge form, I'd make it:

        Create Account

     What are you?

 ┌────────────┐ ┌────────────────┐
 │   👤       │ │      🏢        │
 │   Donor    │ │ Organization   │
 │            │ │                │
 │  [Select]  │ │   [Select]     │
 └────────────┘ └────────────────┘

Then show the appropriate form.

This keeps the UI cleaner.

### DONOR PAGES
 
3. 👤 DONOR PAGES

After login, the donor enters their dashboard.

9. Donor Dashboard

File:

donor-dashboard.html

This is the donor's main page.

Sidebar
│
├── Dashboard
├── Explore Campaigns
├── My Donations
├── Recommended
├── Profile
└── Logout

Dashboard content:

Welcome 👋

Total Donated
₹5,500

Campaigns Supported
8

Recent Donations

Recommended For You 🤖

10. My Donations

File:

my-donations.html

Example:

MY DONATIONS

Campaign              Amount       Date
─────────────────────────────────────────
Education Support      ₹500       01/09/26
Food Drive             ₹1000      25/08/26
Medical Support        ₹500       15/08/26

Buttons:

[View Receipt]
11. Recommended Campaigns 🤖

File:

recommendations.html

This is our first Smart Feature page.

          🤖 Recommended For You

Based on your donation history

┌───────────────┐
│ Education     │
│ Match: 92%    │
│ [View]        │
└───────────────┘

┌───────────────┐
│ Medical       │
│ Match: 78%    │
│ [View]        │
└───────────────┘

The recommendations will come from Python/Flask, not be hardcoded into HTML.

12. Donate Page

File:

donate.html

This page handles the donation process.

Campaign
    ↓
Donation Amount

Suggested Amount 💡
₹500

[ ₹100 ] [ ₹500 ] [ ₹1000 ]

Other Amount
[________]

[ Continue ]

The Smart Amount Suggestion will appear here.

13. Donation Confirmation

We can either make this a separate page or use a modal.

I'd prefer a modal to avoid unnecessary pages:

Confirm Donation

Campaign: Education Support
Amount: ₹500

[ Cancel ]    [ Confirm ]

Then send the request to Flask.

14. Donation Success / Receipt

File:

donation-success.html
        ✅ Donation Successful!

Thank you for supporting this campaign.

Amount: ₹500
Campaign: Education Support
Transaction ID: DON123456

[ View Receipt ]
[ Download PDF ]


### ORGANIZATION PAGES

4. 🏢 ORGANIZATION PAGES
15. Organization Dashboard

File:

organization-dashboard.html

Sidebar:

Dashboard
My Campaigns
Create Campaign
Donations
Campaign Updates
Profile
Logout

Dashboard:

Total Raised
₹2,50,000

Active Campaigns
5

Total Donors
420
16. Create Campaign

File:

create-campaign.html

Form:

Campaign Title
Category
Description
Target Amount
End Date
Campaign Image

[ Submit Campaign ]

After submission:

Submitted
    ↓
Pending Admin Approval
17. My Campaigns

File:

my-campaigns.html

Shows:

Campaign                  Status
──────────────────────────────────
Education Support         ✓ Live
Medical Support           ⏳ Pending
Food Drive                ✓ Live
18. Campaign Management

File:

manage-campaign.html

Organization can:

See donation progress
See donors/counts
Post updates
Edit allowed campaign information
Monitor goal progress
19. Organization Donations

File:

organization-donations.html

Shows:

DONATIONS

Campaign          Amount       Date
─────────────────────────────────────
Education          ₹500        01/09
Education          ₹1000       01/09
Medical            ₹500        31/08
20. Campaign Updates

We could either use a separate page or integrate this into campaign management.

I'd recommend integrating it into manage-campaign.html rather than creating another page.

### ADMIN PAGES

5. 👨‍💼 ADMIN PAGES

The admin section can have a completely different layout.

21. Admin Dashboard

File:

admin-dashboard.html
ADMIN PANEL

Total Users             2450
Organizations             85
Campaigns                120
Total Donations        ₹25L

        Donation Chart

        Recent Activity

        ⚠️ Alerts
        
22. Manage Users

File:

admin-users.html

Admin can:

View users
Search users
Disable accounts
View user details
23. Organization Verification

File:

admin-organizations.html

Shows:

Pending Organizations

ABC Foundation
Status: Pending

[ Review ]

Review page/modal:

Organization Details
Registration Details
Documents

[ Approve ] [ Reject ]

24. Campaign Approval

File:

admin-campaigns.html

Shows:

Pending Campaigns

Education Support
ABC Foundation

[ Review ]

Then:

[ Approve ]
[ Reject ]

25. Suspicious Activity 🚨

File:

admin-alerts.html

This is our second Smart Feature.

⚠️ Suspicious Activities

Alert #001
Campaign: XYZ
User: User123
Amount: ₹10,000

Reason:
Unusual donation pattern

[ Review ]

26. Analytics

File:

admin-analytics.html

Shows:

Donation trends
Campaign performance
Popular categories
Donor statistics
Organization statistics


📋 Final Page List

Let's keep track of everything:

🌍 Public
1. index.html
2. campaigns.html
3. campaign.html
4. about.html
5. how-it-works.html
6. contact.html
🔐 Authentication
7. login.html
8. register.html
👤 Donor
9. donor-dashboard.html
10. my-donations.html
11. recommendations.html
12. donate.html
13. donation-success.html
🏢 Organization
14. organization-dashboard.html
15. create-campaign.html
16. my-campaigns.html
17. manage-campaign.html
18. organization-donations.html
👨‍💼 Admin
19. admin-dashboard.html
20. admin-users.html
21. admin-organizations.html
22. admin-campaigns.html
23. admin-alerts.html
24. admin-analytics.html

So we're looking at 24 logical pages, although some actions—like confirmation, campaign review, and organization review—can be modals/components instead of separate HTML files.

🧩 One important architecture decision

Don't make every page completely independent.

For example, all donor pages can share:

              DONOR LAYOUT
                  │
        ┌─────────┴─────────┐
        │                   │
     Sidebar              Content
                            │
                ┌───────────┼───────────┐
                ↓           ↓           ↓
             Dashboard  Donations  Recommendations

Later, Flask can serve the pages/templates and JavaScript can fetch dynamic data through APIs.

📁 Our eventual project structure

Something like:

smart-donation-platform/
│
├── frontend/
│   ├── public/
│   │   ├── index.html
│   │   ├── campaigns.html
│   │   ├── campaign.html
│   │   ├── about.html
│   │   ├── how-it-works.html
│   │   └── contact.html
│   │
│   ├── auth/
│   │   ├── login.html
│   │   └── register.html
│   │
│   ├── donor/
│   │   ├── dashboard.html
│   │   ├── donations.html
│   │   ├── recommendations.html
│   │   └── donate.html
│   │
│   ├── organization/
│   │   ├── dashboard.html
│   │   ├── create-campaign.html
│   │   ├── campaigns.html
│   │   └── manage-campaign.html
│   │
│   └── admin/
│       ├── dashboard.html
│       ├── users.html
│       ├── organizations.html
│       ├── campaigns.html
│       ├── alerts.html
│       └── analytics.html
│
├── backend/
│   └── ...
│
└── documentation/
    └── ...

One correction before we move on: because we're using Flask, we may eventually reorganize the frontend into Flask's templates/ and static/ structure rather than keeping it exactly like this. We don't need to decide that until we design the backend.