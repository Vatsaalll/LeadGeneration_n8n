# Automated Lead Generation & Outreach System

[![n8n Workflow](https://img.shields.io/badge/n8n-Workflow-276EF1?logo=n8n)](https://n8n.io/)
[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

This n8n workflow automates **industry-specific lead generation** and **personalized cold email outreach** using Apollo.io data, Apify scraping, and AI-powered email personalization. The system sources qualified leads based on custom criteria (industry, company size, job titles) and generates hyper-personalized emails at scale.

![Workflow Diagram](workflow-diagram.png) <!-- Add screenshot of your n8n workflow -->

## ✨ Key Features
- **Targeted Lead Sourcing**: Filter by industry (retail, real estate, etc.), company size (1-10 employees), job titles, and location
- **Data Validation**: Auto-removes invalid emails and duplicates
- **AI-Powered Personalization**: Generates unique icebreakers using OpenAI based on company keywords/products
- **Google Sheets Integration**: Centralized lead database with automatic email preparation
- **Compliance-Focused**: Respects email sending limits (50-100/day) to avoid spam flags

## 🔧 How It Works
### Phase 1: Lead Sourcing
1. **Apollo.io Search Configuration**  
   - Define filters (industry, company size, job titles)
   - Generate Apollo search URLs (e.g., `https://app.apollo.io/#/search?industry=retail&size=1-10`)
   
2. **Apify Scraping**  
   - Uses [Apollo Scraper actor](https://apify.com/drobnikj/apollo-scraper) to extract leads
   - Processes 100 leads/search URL (free tier limitation)
   - Retrieves: Name, Title, Email, Company Size, LinkedIn, Website, Keywords

3. **Data Cleaning**  
   - Filters invalid emails
   - Removes duplicates by email
   - Maps data to Google Sheets columns

### Phase 2: Email Personalization
1. **Dynamic Content Generation**  
   - Uses OpenAI (GPT-4 mini) to create personalized icebreakers:
     ```text
     "I love how [Company] brings [Keyword] to life with such passion!"
     ```
   - Maintains fixed pitch structure while inserting industry-specific hooks

2. **Google Sheets Integration**  
   - Populates "email_prepared" column with ready-to-send emails
   - Only processes leads without existing emails

3. **Compliance Safeguards**  
   - Scheduled triggers limit daily email volume (configurable per mailbox)

## ⚙️ Setup Guide
### Prerequisites
1. [Apollo.io account](https://www.apollo.io/) (free tier available)
2. [Apify account](https://apify.com/) ($5 free monthly credit)
3. [OpenAI API key](https://platform.openai.com/)
4. Google Sheets API access
5. n8n (v1.0+)

### Configuration Steps
1. **Generate Apollo Search URLs**  
   - In Apollo: `People` → Set filters → Copy URL from address bar
   - Add to `searchURL` array in n8n "Set" node

2. **Configure Apify**  
   - Get Apify API token: `Account` → `API` → `Token`
   - Update HTTP Request node with:
     ```json
     {
       "searchUrls": ["YOUR_APOLLO_URL_1", "YOUR_APOLLO_URL_2"],
       "totalRecords": 100,
       "getWorkEmails": true,
       "getPersonalEmails": true
     }
     ```

3. **Set Up Google Sheets**  
   - Create sheet with columns:
     `first_name, last_name, title, email, company_size, linkedin, organization, org_linkedin, org_site, industry, email_prepared, keywords`
   - Configure Google Sheets credentials in n8n

4. **Personalization Settings**  
   - Edit LLM prompt in "OpenAI" node:
     - Adjust icebreaker rules (max 15 words)
     - Modify fixed pitch content
     - Update personalization constraints

## 🚀 Usage
1. **Run Lead Sourcing**  
   Trigger workflow → Generates leads → Populates Google Sheets

2. **Generate Emails**  
   Run email workflow → Processes new leads → Updates "email_prepared" column

3. **Send Outreach**  
   *Extend workflow with
