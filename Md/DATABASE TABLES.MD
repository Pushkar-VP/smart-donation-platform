
  ### Smart Donation Platform — Database Design
🗄️  Smart Donation Platform — Database Design

We'll use MySQL for the database.

At a high level, our database will look like: 
                         DATABASE
                            │
       ┌────────────────────┼────────────────────┐
       │                    │                    │
       ▼                    ▼                    ▼
     USERS            ORGANIZATIONS          CAMPAIGNS
       │                    │                    │
       │                    │                    │
       └──────────┐         │         ┌──────────┘
                  ▼         ▼         ▼
                       DONATIONS
                            │
                            ▼
                         PAYMENTS

Additional tables:
├── Campaign_Updates
├── Verification_Documents
├── Suspicious_Activities
└── Donor_Interests

### table-by-table for everything

But let's go table-by-table.

1. 👤 users

This will be our main user table.

Instead of creating completely separate login tables for donors, organizations, and admins, we'll keep common account information here.

users
| Column          | Type     | Purpose                      |
| --------------- | -------- | ---------------------------- |
| `user_id`       | INT      | Primary key                  |
| `full_name`     | VARCHAR  | User's name                  |
| `email`         | VARCHAR  | Login email                  |
| `password_hash` | VARCHAR  | Hashed password              |
| `role`          | ENUM     | donor / organization / admin |
| `phone`         | VARCHAR  | Contact number               |
| `status`        | ENUM     | active / suspended           |
| `created_at`    | DATETIME | Registration date            |


Example:

user_id | full_name | email              | role
------------------------------------------------
1       | Pushkar   | pushkar@gmail.com  | donor
2       | ABC NGO   | abc@gmail.com      | organization
3       | Admin     | admin@platform.com | admin
Why have role?

Because Flask needs to know:

Who logged in?
       ↓
What role do they have?
       ↓
Which dashboard should they see?

2. 🏢 organizations

Organizations need information that normal donors don't have.

organizations
| Column                | Type     | Purpose                       |
| --------------------- | -------- | ----------------------------- |
| `organization_id`     | INT      | Primary key                   |
| `user_id`             | INT      | Links to users                |
| `organization_name`   | VARCHAR  | Organization name             |
| `registration_number` | VARCHAR  | Official registration number  |
| `address`             | TEXT     | Address                       |
| `description`         | TEXT     | About organization            |
| `verification_status` | ENUM     | pending / verified / rejected |
| `verified_at`         | DATETIME | Verification date             |


Relationship:

users
  │
  │ 1
  │
  │
  │ 1
  ▼
organizations

An organization account is also a user account.

3. 📄 verification_documents

Organizations may need to submit documents for verification.

| Column            | Type     |
| ----------------- | -------- |
| `document_id`     | INT      |
| `organization_id` | INT      |
| `document_type`   | VARCHAR  |
| `document_path`   | VARCHAR  |
| `uploaded_at`     | DATETIME |
| `status`          | ENUM     |


Example:

organization
      │
      ├── Registration Certificate
      ├── Identity Document
      └── Other Supporting Document

This allows the admin to review submitted documents.

4. 📢 campaigns

This is one of the most important tables.

| Column            | Type     | Purpose                                 |
| ----------------- | -------- | --------------------------------------- |
| `campaign_id`     | INT      | Primary key                             |
| `organization_id` | INT      | Campaign owner                          |
| `title`           | VARCHAR  | Campaign title                          |
| `description`     | TEXT     | Campaign description                    |
| `category_id`     | INT      | Campaign category                       |
| `goal_amount`     | DECIMAL  | Target donation                         |
| `raised_amount`   | DECIMAL  | Current amount                          |
| `image_path`      | VARCHAR  | Campaign image                          |
| `start_date`      | DATE     | Start date                              |
| `end_date`        | DATE     | End date                                |
| `status`          | ENUM     | pending / active / rejected / completed |
| `created_at`      | DATETIME | Creation date                           |


Relationship:

Organization
      │
      │ creates
      ▼
   Campaign

One organization can create many campaigns.

So:

ORGANIZATION 1 ───────────< CAMPAIGN

The < means "many."

5. 🏷️ categories

Instead of storing category names directly inside campaigns, we'll create a separate table.

| Column          | Type    |
| --------------- | ------- |
| `category_id`   | INT     |
| `category_name` | VARCHAR |


Example:

1 → Education
2 → Medical
3 → Food
4 → Disaster Relief
5 → Environment
6 → Animals

Relationship:

CATEGORY 1 ───────────< CAMPAIGN

One category can have many campaigns.

6. 💰 donations

This is the heart of the platform.

| Column        | Type     | Purpose                       |
| ------------- | -------- | ----------------------------- |
| `donation_id` | INT      | Primary key                   |
| `donor_id`    | INT      | User who donated              |
| `campaign_id` | INT      | Campaign receiving donation   |
| `amount`      | DECIMAL  | Donation amount               |
| `status`      | ENUM     | pending / successful / failed |
| `donated_at`  | DATETIME | Donation date                 |


Relationship:

DONOR
  │
  │ makes
  ▼
DONATION
  │
  │ goes to
  ▼
CAMPAIGN

So:

USER 1 ───────────< DONATION >─────────── 1 CAMPAIGN

A donor can make many donations.

A campaign can receive many donations.

7. 💳 payments

If we eventually integrate a real payment gateway, we'll need payment information.

| Column           | Type     |
| ---------------- | -------- |
| `payment_id`     | INT      |
| `donation_id`    | INT      |
| `transaction_id` | VARCHAR  |
| `payment_method` | VARCHAR  |
| `amount`         | DECIMAL  |
| `status`         | ENUM     |
| `paid_at`        | DATETIME |


Relationship:

DONATION 1 ─────────── 1 PAYMENT

For a prototype, we can keep payment processing simulated and still maintain this table.

8. 📢 campaign_updates

Organizations can post updates about their campaigns.

| Column        | Type     |
| ------------- | -------- |
| `update_id`   | INT      |
| `campaign_id` | INT      |
| `title`       | VARCHAR  |
| `content`     | TEXT     |
| `image_path`  | VARCHAR  |
| `created_at`  | DATETIME |


Relationship:

CAMPAIGN 1 ───────────< CAMPAIGN_UPDATE

Example:

Campaign
   │
   ├── Update 1: 50 students helped
   ├── Update 2: School supplies delivered
   └── Update 3: Goal reached

9. ❤️ donor_interests

This helps our Smart Recommendation System.

We need to know what categories a donor is interested in.
| Column             | Type    |
| ------------------ | ------- |
| `interest_id`      | INT     |
| `donor_id`         | INT     |
| `category_id`      | INT     |
| `preference_score` | DECIMAL |


Example:

Donor: Pushkar

Education → 0.90
Medical   → 0.60
Food      → 0.20

The score can change based on the donor's activity.

10. 🤖 recommendations

We can store generated recommendations if we want to track them.

| Column              | Type     |
| ------------------- | -------- |
| `recommendation_id` | INT      |
| `donor_id`          | INT      |
| `campaign_id`       | INT      |
| `score`             | DECIMAL  |
| `reason`            | VARCHAR  |
| `created_at`        | DATETIME |


Example:

Donor
  ↓
Recommendation Engine
  ↓
Campaign
  ↓
Score = 0.92
  ↓
"Recommended because you frequently support Education"

This is useful for demonstrating your smart system.

11. 🚨 suspicious_activities

This is for our Smart Detection System.

| Column        | Type     |
| ------------- | -------- |
| `alert_id`    | INT      |
| `user_id`     | INT      |
| `donation_id` | INT      |
| `reason`      | TEXT     |
| `risk_score`  | DECIMAL  |
| `status`      | ENUM     |
| `created_at`  | DATETIME |
| `reviewed_at` | DATETIME |


Example:

Donation
   ↓
Python Detection System
   ↓
Risk Score = 87%
   ↓
Suspicious Activity
   ↓
Admin Review

Notice that we're storing a risk score, not declaring the user fraudulent.

12. 💡 What about Smart Amount Suggestion?

Here's an important design decision.

We do not necessarily need a separate suggested_amounts table.

Why?

Because the suggested amount can be calculated dynamically.

For example:

Previous donations
        +
Average donation
        +
Campaign information
        ↓
Python algorithm
        ↓
Suggested amount

So:

Donor's previous donations:
₹500
₹1000
₹500

Average = ₹667

Python might suggest:
₹500 or ₹700

The recommendation can be generated when the donor opens the donation page.

This avoids storing unnecessary data.

🔥 COMPLETE ER DIAGRAM

Now let's connect everything.

┌─────────────────────┐
│       USERS         │
├─────────────────────┤
│ PK user_id          │
│ full_name           │
│ email               │
│ password_hash       │
│ role                │
│ phone               │
│ status              │
└──────────┬──────────┘
           │
           │ 1
           │
     ┌─────┴──────────────┐
     │                    │
     │ 1                  │
     ▼                    ▼
┌───────────────┐   ┌──────────────────┐
│ ORGANIZATIONS │   │     DONATIONS    │
├───────────────┤   ├──────────────────┤
│ PK org_id     │   │ PK donation_id   │
│ FK user_id    │   │ FK donor_id      │
│ name          │   │ FK campaign_id   │
│ reg_number    │   │ amount           │
│ address       │   │ status           │
│ verify_status │   │ donated_at       │
└───────┬───────┘   └───────┬──────────┘
        │                   │
        │ 1                 │
        │                   │
        ▼                   │
┌──────────────────┐        │
│    CAMPAIGNS     │◄───────┘
├──────────────────┤
│ PK campaign_id   │
│ FK org_id        │
│ FK category_id   │
│ title            │
│ description      │
│ goal_amount      │
│ raised_amount    │
│ status           │
└───────┬──────────┘
        │
        │ 1
        ├───────────────────────┐
        │                       │
        ▼                       ▼
┌──────────────────┐     ┌────────────────────┐
│CAMPAIGN_UPDATES  │     │     CATEGORIES     │
├──────────────────┤     ├────────────────────┤
│ PK update_id     │     │ PK category_id     │
│ FK campaign_id   │     │ category_name      │
│ title            │     └────────────────────┘
│ content          │
└──────────────────┘


┌──────────────────────┐
│ VERIFICATION_DOCS    │
├──────────────────────┤
│ PK document_id       │
│ FK organization_id   │
│ document_type        │
│ document_path        │
│ status               │
└──────────┬───────────┘
           │
           ▼
     ORGANIZATIONS


DONATIONS
    │
    │ 1
    ▼
┌──────────────────────┐
│      PAYMENTS        │
├──────────────────────┤
│ PK payment_id        │
│ FK donation_id       │
│ transaction_id       │
│ payment_method       │
│ amount               │
│ status               │
└──────────────────────┘


USERS
  │
  │
  ├───────────────┐
  │               │
  ▼               ▼
DONOR        SUSPICIOUS_ACTIVITIES
  │               │
  ▼               │
DONOR_INTERESTS   │
  │               │
  ▼               │
CATEGORIES        │
                  │
USERS ────────────┘


DONOR
  │
  ▼
RECOMMENDATIONS
  │
  ▼
CAMPAIGN

### Relationships in simple form

🧩 Relationships in simple form

Here's the relationship map you should understand for your viva:

USERS
 │
 ├── 1 : 1 ── ORGANIZATIONS
 │
 ├── 1 : N ── DONATIONS
 │
 ├── 1 : N ── DONOR_INTERESTS
 │
 ├── 1 : N ── RECOMMENDATIONS
 │
 └── 1 : N ── SUSPICIOUS_ACTIVITIES


ORGANIZATIONS
 │
 ├── 1 : N ── CAMPAIGNS
 │
 └── 1 : N ── VERIFICATION_DOCUMENTS


CATEGORIES
 │
 ├── 1 : N ── CAMPAIGNS
 └── 1 : N ── DONOR_INTERESTS


CAMPAIGNS
 │
 ├── 1 : N ── DONATIONS
 ├── 1 : N ── CAMPAIGN_UPDATES
 └── 1 : N ── RECOMMENDATIONS


DONATIONS
 │
 ├── 1 : 1 ── PAYMENTS
 └── 1 : N ── SUSPICIOUS_ACTIVITIES
One small improvement

I would actually make the admin a role inside users, rather than creating a separate admins table. Admins don't currently have additional profile data that justifies a separate entity.

🎯 Our database is now tied directly to our features

| Feature                   | Database tables                                   |
| ------------------------- | ------------------------------------------------- |
| Login/Register            | `users`                                           |
| Organization verification | `organizations`, `verification_documents`         |
| Campaign creation         | `campaigns`, `categories`                         |
| Donations                 | `donations`, `payments`                           |
| Donation history          | `donations`                                       |
| Campaign updates          | `campaign_updates`                                |
| Smart recommendations     | `donor_interests`, `recommendations`, `donations` |
| Smart amount suggestion   | `donations`, `campaigns`                          |
| Suspicious detection      | `donations`, `suspicious_activities`              |
| Admin analytics           | Almost all transaction tables                     |


So we're not adding database tables just because they sound impressive. Every table has a purpose connected to a feature.

### Finalized Requirements

✅ Smart Donation Platform — Finalized Requirements

1. 👤 Donor

We'll collect:

Full name
Email
Password
Phone number
Profile photo
City/Location
Preferred donation categories

Donors can:

Register/login
Browse campaigns
Search/filter campaigns
View campaign details
Donate
Choose anonymous donation
Add a donor message
View donation history
Download receipts
Get campaign recommendations
Get smart donation amount suggestions
Manage their profile

2. 🏢 Organization

We'll collect:

Organization name
Email
Phone
Address
City
Description
Registration number
Registration certificate
Authorized person's name
Verification status
Website
Social media links

Organizations can:

Register
Submit verification documents
Wait for admin verification
Create campaigns
Submit campaigns for approval
View their campaigns
Monitor donations
Post campaign updates
Track campaign progress

3. 🏷️ Campaign Categories

We'll keep all 10:

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

4. 📢 Campaign Information

Every campaign will contain:

Campaign title
Description
Category
Target amount
Campaign image
Start date
End date
Location
Beneficiary information
Reason/need for funds

Optional supporting documents can also be included.

5. 💰 Donations

Each donation will support:

Donor
Campaign
Amount
Date/time
Status
Transaction ID
Payment method
Anonymous donation option
Donor message

Flow:

Campaign
    ↓
Donate
    ↓
Smart Amount Suggestion 💡
    ↓
Donor chooses amount
    ↓
Confirm
    ↓
Payment
    ↓
Donation recorded
    ↓
Receipt

6. 🧾 Donation Receipt

We'll implement a proper receipt containing:

Platform name
Receipt ID
Transaction ID
Donor name / Anonymous
Campaign name
Organization name
Amount
Donation date
Payment method
Thank-you message

And:

✅ PDF download

7. 📢 Campaign Updates

Organizations can publish:

Update title
Update description
Image
Date

Example:

Campaign: Education for Rural Children

Update:
"School supplies distributed to 50 students."

[Image]

September 4, 2026

8. 🤖 Smart Recommendation System

We'll use:

Previous donations
Preferred categories
Campaign category
Campaign relevance
Campaign urgency

Conceptually:

Donor History
      +
Preferred Categories
      +
Campaign Information
      +
Urgency
      ↓
Python Recommendation Engine
      ↓
Recommendation Score
      ↓
Top Recommended Campaigns

Example:

🎯 Recommended For You

Education Support for Rural Students

Match Score: 92%

[View Campaign]

9. 💡 Smart Donation Amount Suggestion

We'll use all four factors:

Previous donation amounts
Average donation
Recent donation behavior
Campaign progress

Concept:

Previous Donations
       ↓
Average Amount
       +
Recent Behavior
       +
Campaign Progress
       ↓
Python Algorithm
       ↓
Suggested Amount

Example:

Your previous average: ₹650

💡 Suggested donation

        ₹750

[ ₹100 ] [ ₹500 ] [ ₹750 ] [ ₹1000 ]

The donor always has the freedom to choose another amount.

10. 🚨 Suspicious Activity Detection

We'll use the four recommended rules:

Rule 1

High-frequency donations

Rule 2

Unusually high donation amount

Rule 3

Repeated identical transactions

Rule 4

Multiple failed payments

The system will calculate/assign a risk score and flag the activity.

Donation
   ↓
Detection System
   ↓
Analyze Pattern
   ↓
Normal ─────────→ Continue
   │
   └─ Suspicious
          ↓
      Risk Score
          ↓
    Admin Review

⚠️ The system does not automatically call someone a fraudster or ban them.

11. 🔐 Security

We'll implement:

Password hashing
Secure authentication
Role-based authorization
Donor/organization/admin access control
Secure file uploads
File type restrictions
File size restrictions
Safe filenames
Backend validation
Input validation

12. 👨‍💼 Admin

Admin will be able to:

Users
View users
Search users
Suspend/reactivate users
Organizations
Review organizations
View verification documents
Approve organizations
Reject organizations
Campaigns
Review campaigns
Approve campaigns
Reject campaigns
Disable/remove campaigns when necessary
Donations
View donations
Search/filter donations
Monitor donation activity
Smart Detection
View suspicious activity
See risk score
See reason for flag
Review the activity
Analytics
Total users
Total organizations
Active campaigns
Total donations
Popular categories
Donation trends

🏆 We've now got the actual project

Our system is no longer just an idea.

We have:

                  SMART DONATION PLATFORM
                           │
       ┌───────────────────┼───────────────────┐
       ↓                   ↓                   ↓
     DONOR            ORGANIZATION           ADMIN
       │                   │                   │
       ↓                   ↓                   ↓
  Find Campaign       Create Campaign     Manage System
       │                   │                   │
       ↓                   ↓                   ↓
 Recommendation       Verification        Approvals
       │                   │                   │
       ↓                   ↓                   ↓
Amount Suggestion     Campaign Approval    Detection
       │                   │                   │
       └──────────────┬────┴───────────────────┘
                      ↓
                  DONATIONS
                      │
            ┌─────────┼─────────┐
            ↓         ↓         ↓
  Recommendation  Detection  Receipt
      Engine        Engine
            │           │
            └─────┬─────┘
                  ↓
             PYTHON/FLASK
                  │
                  ↓
                MYSQL
   ### Smart Donation Platform Final Database Schema v1.0             
🗄️ Smart Donation Platform — Final Database Schema v1.0

We'll use MySQL.

Our final database will have 11 tables:

1.  users
2.  organizations
3.  verification_documents
4.  categories
5.  campaigns
6.  campaign_updates
7.  donations
8.  payments
9.  donor_interests
10. recommendations
11. suspicious_activities


1. 👤 users

This is the central authentication table.

| Column          | Data Type    | Key    | NULL | Description                  |
| --------------- | ------------ | ------ | ---- | ---------------------------- |
| `user_id`       | INT          | PK     | NO   | Unique user ID               |
| `full_name`     | VARCHAR(100) |        | NO   | User's name                  |
| `email`         | VARCHAR(150) | UNIQUE | NO   | Login email                  |
| `password_hash` | VARCHAR(255) |        | NO   | Hashed password              |
| `role`          | ENUM         |        | NO   | donor / organization / admin |
| `phone`         | VARCHAR(20)  |        | YES  | Phone number                 |
| `profile_photo` | VARCHAR(255) |        | YES  | Photo path                   |
| `city`          | VARCHAR(100) |        | YES  | User's city                  |
| `status`        | ENUM         |        | NO   | active / suspended           |
| `created_at`    | DATETIME     |        | NO   | Registration time            |
| `updated_at`    | DATETIME     |        | NO   | Last update                  |

Example:

user_id | full_name | email | role | status
-------------------------------------------------
1       | Pushkar   | ...   | donor | active
2       | ABC NGO   | ...   | organization | active
3       | Admin     | ...   | admin | active

Important:
We don't need separate login tables for donors, organizations, and admins.

The:
role column tells Flask what type of account it is.

2. 🏢 organizations

Stores organization-specific information.

| Column                   | Data Type    | Key        | NULL | Description                     |
| ------------------------ | ------------ | ---------- | ---- | ------------------------------- |
| `organization_id`        | INT          | PK         | NO   | Unique organization ID          |
| `user_id`                | INT          | FK, UNIQUE | NO   | Associated user account         |
| `organization_name`      | VARCHAR(150) |            | NO   | Organization name               |
| `registration_number`    | VARCHAR(100) | UNIQUE     | NO   | Official registration number    |
| `phone`                  | VARCHAR(20)  |            | NO   | Organization phone              |
| `address`                | TEXT         |            | NO   | Full address                    |
| `city`                   | VARCHAR(100) |            | NO   | City                            |
| `description`            | TEXT         |            | YES  | Organization description        |
| `authorized_person_name` | VARCHAR(100) |            | NO   | Authorized person               |
| `website_url`            | VARCHAR(255) |            | YES  | Website                         |
| `social_media_links`     | TEXT         |            | YES  | Social media links              |
| `verification_status`    | ENUM         |            | NO   | pending / verified / rejected   |
| `verification_reason`    | TEXT         |            | YES  | Admin's rejection/review reason |
| `verified_at`            | DATETIME     |            | YES  | Verification date               |
| `created_at`             | DATETIME     |            | NO   | Registration date               |

Relationship
users 1 ───────── 1 organizations

The UNIQUE constraint on user_id means one user account can represent only one organization.

3. 📄 verification_documents

Stores documents uploaded by organizations.

| Column            | Data Type    | Key | NULL |
| ----------------- | ------------ | --- | ---- |
| `document_id`     | INT          | PK  | NO   |
| `organization_id` | INT          | FK  | NO   |
| `document_type`   | VARCHAR(100) |     | NO   |
| `document_path`   | VARCHAR(255) |     | NO   |
| `status`          | ENUM         |     | NO   |
| `uploaded_at`     | DATETIME     |     | NO   |
| `reviewed_at`     | DATETIME     |     | YES  |
| `review_notes`    | TEXT         |     | YES  |

Possible document types:

Registration Certificate
Organization ID
Authorized Person ID
Other Supporting Document

Relationship:
organization 1 ───────── N verification_documents

3. 📄 verification_documents

Stores documents uploaded by organizations.

| Column            | Data Type    | Key | NULL |
| ----------------- | ------------ | --- | ---- |
| `document_id`     | INT          | PK  | NO   |
| `organization_id` | INT          | FK  | NO   |
| `document_type`   | VARCHAR(100) |     | NO   |
| `document_path`   | VARCHAR(255) |     | NO   |
| `status`          | ENUM         |     | NO   |
| `uploaded_at`     | DATETIME     |     | NO   |
| `reviewed_at`     | DATETIME     |     | YES  |
| `review_notes`    | TEXT         |     | YES  |

Possible document types:

Registration Certificate
Organization ID
Authorized Person ID
Other Supporting Document

Relationship:
organization 1 ───────── N verification_documents

4. 🏷️ categories

Stores our 10 campaign categories.

| Column          | Data Type    | Key    |
| --------------- | ------------ | ------ |
| `category_id`   | INT          | PK     |
| `category_name` | VARCHAR(100) | UNIQUE |
| `description`   | TEXT         |        |
| `is_active`     | BOOLEAN      |        |
| `created_at`    | DATETIME     |        |

Initial data:

1  Education
2  Medical
3  Food & Hunger
4  Disaster Relief
5  Environment
6  Animal Welfare
7  Child Welfare
8  Elderly Care
9  Community Development
10 Other

Admin can later deactivate a category without deleting historical data.

5. 📢 campaigns

This is our main campaign table.

| Column                | Data Type     | Key | NULL |
| --------------------- | ------------- | --- | ---- |
| `campaign_id`         | INT           | PK  | NO   |
| `organization_id`     | INT           | FK  | NO   |
| `category_id`         | INT           | FK  | NO   |
| `title`               | VARCHAR(200)  |     | NO   |
| `description`         | TEXT          |     | NO   |
| `goal_amount`         | DECIMAL(12,2) |     | NO   |
| `raised_amount`       | DECIMAL(12,2) |     | NO   |
| `campaign_image`      | VARCHAR(255)  |     | YES  |
| `start_date`          | DATE          |     | NO   |
| `end_date`            | DATE          |     | NO   |
| `location`            | VARCHAR(150)  |     | YES  |
| `beneficiary_info`    | TEXT          |     | NO   |
| `fund_usage_reason`   | TEXT          |     | NO   |
| `supporting_document` | VARCHAR(255)  |     | YES  |
| `status`              | ENUM          |     | NO   |
| `rejection_reason`    | TEXT          |     | YES  |
| `created_at`          | DATETIME      |     | NO   |
| `updated_at`          | DATETIME      |     | NO   |

Status:

pending
active
rejected
completed
closed

Relationship:
organization 1 ───────── N campaigns

category 1 ───────── N campaigns

6. 📢 campaign_updates

Stores updates posted by organizations.

| Column        | Data Type    | Key |
| ------------- | ------------ | --- |
| `update_id`   | INT          | PK  |
| `campaign_id` | INT          | FK  |
| `title`       | VARCHAR(200) |     |
| `content`     | TEXT         |     |
| `image_path`  | VARCHAR(255) |     |
| `created_at`  | DATETIME     |     |
| `updated_at`  | DATETIME     |     |

Relationship:

campaign 1 ───────── N campaign_updates

Example:
Campaign
   │
   ├── Update 1
   ├── Update 2
   └── Update 3

7. 💰 donations

This is the most important transaction table.

| Column           | Data Type     | Key    | NULL |
| ---------------- | ------------- | ------ | ---- |
| `donation_id`    | INT           | PK     | NO   |
| `donor_id`       | INT           | FK     | NO   |
| `campaign_id`    | INT           | FK     | NO   |
| `amount`         | DECIMAL(12,2) |        | NO   |
| `is_anonymous`   | BOOLEAN       |        | NO   |
| `donor_message`  | VARCHAR(500)  |        | YES  |
| `status`         | ENUM          |        | NO   |
| `transaction_id` | VARCHAR(100)  | UNIQUE | YES  |
| `donated_at`     | DATETIME      |        | NO   |

Status

pending
successful
failed
refunded

Relationships
users 1 ───────── N donations

campaigns 1 ───── N donations

This allows:

One donor → many donations

One campaign → many donations

8. 💳 payments

Stores payment-related information.

| Column                   | Data Type     | Key        |
| ------------------------ | ------------- | ---------- |
| `payment_id`             | INT           | PK         |
| `donation_id`            | INT           | FK, UNIQUE |
| `payment_method`         | VARCHAR(50)   |            |
| `gateway_transaction_id` | VARCHAR(150)  | UNIQUE     |
| `amount`                 | DECIMAL(12,2) |            |
| `status`                 | ENUM          |            |
| `paid_at`                | DATETIME      |            |
	

Relationship:

donation 1 ───────── 1 payment

The UNIQUE constraint on donation_id ensures one donation has one payment record.

9. ❤️ donor_interests

This table supports our recommendation engine.

| Column             | Data Type    | Key |
| ------------------ | ------------ | --- |
| `interest_id`      | INT          | PK  |
| `donor_id`         | INT          | FK  |
| `category_id`      | INT          | FK  |
| `preference_score` | DECIMAL(5,2) |     |
| `updated_at`       | DATETIME     |     |

Example:

Donor: Pushkar

Education       90.00
Medical         60.00
Food            20.00
Environment     10.00

Relationships:

user 1 ───────── N donor_interests

category 1 ───── N donor_interests

10. 🤖 recommendations

Stores recommendations generated by our smart system.

| Column              | Data Type    | Key |
| ------------------- | ------------ | --- |
| `recommendation_id` | INT          | PK  |
| `donor_id`          | INT          | FK  |
| `campaign_id`       | INT          | FK  |
| `score`             | DECIMAL(5,2) |     |
| `reason`            | VARCHAR(500) |     |
| `created_at`        | DATETIME     |     |
| `is_viewed`         | BOOLEAN      |     |

Example:

Donor
  ↓
Recommendation Engine
  ↓
Campaign #25
  ↓
Score = 92.50
  ↓
Reason =
"Matches your interest in Education"

Relationships:

user 1 ───────── N recommendations

campaign 1 ───── N recommendations

11. 🚨 suspicious_activities

This stores alerts generated by our detection system.

| Column        | Data Type    | Key |
| ------------- | ------------ | --- |
| `alert_id`    | INT          | PK  |
| `user_id`     | INT          | FK  |
| `donation_id` | INT          | FK  |
| `risk_score`  | DECIMAL(5,2) |     |
| `reason`      | TEXT         |     |
| `status`      | ENUM         |     |
| `created_at`  | DATETIME     |     |
| `reviewed_at` | DATETIME     |     |
| `admin_notes` | TEXT         |     |	

Status:

pending
reviewed
dismissed
confirmed

Example:

Donation
   ↓
Python Detection
   ↓
Risk Score = 87
   ↓
Suspicious Activity
   ↓
Admin Review

💡 What about Smart Amount Suggestion?

As we decided earlier, we don't need a separate table.

Python calculates it dynamically from:

donations
     +
campaigns
     ↓
Smart Amount Algorithm
     ↓
Suggested Amount

For example:

Previous donations:
₹500
₹1000
₹500
₹750

Average = ₹687.50

Campaign progress = 40%

             ↓

Suggested amount = ₹750

The value can simply be displayed to the donor. We don't need to permanently store every suggestion.

🔗 FINAL RELATIONSHIP MAP

Now our complete database relationship structure is:

                         ┌─────────────────┐
                         │      USERS      │
                         │─────────────────│
                         │ PK user_id      │
                         │ full_name       │
                         │ email           │
                         │ password_hash   │
                         │ role            │
                         └────────┬────────┘
                                  │
                    ┌─────────────┼──────────────┐
                    │             │              │
                   1│            1│             1│
                    │             │              │
                    ▼             │              ▼
          ┌────────────────┐      │     ┌───────────────────┐
          │ ORGANIZATIONS  │      │     │ DONOR_INTERESTS   │
          └───────┬────────┘      │     └─────────┬─────────┘
                  │               │               │
                 1│               │              N│
                  │               │               │
                N ▼               │               ▼
          ┌───────────────┐       │        ┌─────────────┐
          │   CAMPAIGNS   │       │        │ CATEGORIES  │
          └───────┬───────┘       │        └──────┬──────┘
                  │               │               │
             ┌────┼────┐          │              1│
             │    │    │          │               │
            N│   N│   N│          │              N│
             │    │    │          │               │
             ▼    ▼    ▼          │               ▼
       UPDATES  DONATIONS ◄────────┘         CAMPAIGNS
                  │
                  │ 1
                  ▼
              PAYMENTS


ORGANIZATIONS
      │
      │ 1
      ▼
VERIFICATION_DOCUMENTS


USERS
  │
  ├───────────────► RECOMMENDATIONS ◄──────── CAMPAIGNS
  │
  └───────────────► SUSPICIOUS_ACTIVITIES ◄── DONATIONS

📌 Relationship Summary

Relationship	                            Type

User → Organization	                      1 : 1
Organization → Campaign	                  1 : N
Category → Campaign	                      1 : N
Organization → Verification Documents	    1 : N
Campaign → Campaign Updates	              1 : N
User/Donor → Donations	                  1 : N
Campaign → Donations	                    1 : N
Donation → Payment	                      1 : 1
User/Donor → Donor Interests	            1 : N
Category → Donor Interests	              1 : N
User/Donor → Recommendations	            1 : N
Campaign → Recommendations	              1 : N
User → Suspicious Activities	            1 : N
Donation → Suspicious Activities	        1 : N


⚠️ One database design point we'll handle carefully

The users table contains both donors and organizations.

So technically:

users
 ├── donor
 ├── organization
 └── admin

But donor_interests and donations.donor_id should only reference users whose role = 'donor'.

MySQL foreign keys alone won't automatically enforce that role. Flask's backend validation will enforce it.

Similarly, campaigns.organization_id will reference an actual organization record.

This is a good example of why we need both:

Database constraints + backend validation.

![alt text](image.png)