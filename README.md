# Repetz                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                  
  > Production SaaS platform for pet shop management with active paying customers.                                                                                                                                                                                                
                                                                                                                                                                                                                                                                                  
  🐾 **[repetz.com.br](https://repetz.com.br)**                                                                                                                                                                                                                                   
                  
  ---                                                                                                                                                                                                                                                                             
                  
  ## Overview

  Repetz is a production SaaS platform I co-founded and built end-to-end for pet shop management, currently serving real customers across Brazil. Launched in late 2025, the platform centralizes everything a pet shop needs to operate: intelligent scheduling, client and pet  
  records, point of sale with cashier shifts, inventory control, monthly service plans, financial reporting with AI, WhatsApp automation, and a public booking page for clients — all in a single multi-tenant system.
                                                                                                                                                                                                                                                                                  
  > 🔒 This repository is private. This README documents the architecture and technical decisions behind the product.                                                                                                                                                             
   
  ---                                                                                                                                                                                                                                                                             
                  
  ## Live Product

  - 14-day free trial, no credit card required                                                                                                                                                                                                                                    
  - Monthly and annual plans available
  - Active paying customers since launch                                                                                                                                                                                                                                          
                  
  ---

  ## What Repetz Does                                                                                                                                                                                                                                                             
   
  **Scheduling**                                                                                                                                                                                                                                                                  
  Intelligent appointment management with availability rules per professional. Clients can self-schedule directly via a shareable public booking page — no login required.
                                                                                                                                                                                                                                                                                  
  **Point of Sale & Cashier**                                                                                                                                                                                                                                                     
  Full POS with cashier shift control, split payment support, and pending sale tracking.                                                                                                                                                                                          
                                                                                                                                                                                                                                                                                  
  **Monthly Service Plans**
  Recurring grooming packages per pet, with automatic billing and usage tracking.                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                                  
  **Client & Pet Management**
  Complete records for each pet and owner — allergies, grooming preferences, behavioral notes, visit history.                                                                                                                                                                     
                                                                                                                                                                                                                                                                                  
  **Inventory Control**
  Stock management with automatic alerts for low inventory and expiration dates.                                                                                                                                                                                                  
                                                                                                                                                                                                                                                                                  
  **Financial Analytics with AI**
  Revenue tracking, detailed financial reports with service × product breakdown, and AI-powered insights to optimize profitability.                                                                                                                                               
                                                                                                                                                                                                                                                                                  
  **WhatsApp Automation**                                                                                                                                                                                                                                                         
  Automated reminders for appointments, pet-ready notifications, and inactive client re-engagement via Meta Cloud API.                                                                                                                                                            
                                                                                                                                                                                                                                                                                  
  **Commission Tracking**
  Automatic commission calculation integrated into sales reports.                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                                  
  **Service Types Supported**
  Grooming (fixed location), Mobile Grooming, Pet Hotel & Daycare, Dog Walking / Pet Sitting.                                                                                                                                                                                     
                                                                                                                                                                                                                                                                                  
  ---
                                                                                                                                                                                                                                                                                  
  ## Tech Stack   

  | Layer | Technology |
  |---|---|
  | Frontend | Next.js 15 (App Router), React 19, TypeScript |                                                                                                                                                                                                                    
  | Styling | Tailwind CSS v4, shadcn/ui, Radix UI |
  | Database | PostgreSQL (Neon — serverless) |                                                                                                                                                                                                                                   
  | Auth | Custom session-based (bcryptjs + httpOnly cookies) |                                                                                                                                                                                                                   
  | Payments | Stripe (checkout, webhooks, billing portal) |                                                                                                                                                                                                                      
  | Email | Resend |                                                                                                                                                                                                                                                              
  | WhatsApp | Meta Cloud API |                                                                                                                                                                                                                                                   
  | Rate limiting | Upstash Redis |                                                                                                                                                                                                                                               
  | AI | Vercel AI SDK + AI Gateway |                                                                                                                                                                                                                                             
  | Storage | Vercel Blob |
  | Infrastructure | Vercel |                                                                                                                                                                                                                                                     
  | Testing | Jest (335 tests across node + jsdom projects) |
                                                                                                                                                                                                                                                                                  
  ---             
                                                                                                                                                                                                                                                                                  
  ## Development Approach

  Repetz was built using the [ai-dev-pipeline](https://github.com/rmb34/ai-dev-pipeline) — a multi-agent development workflow where specialized AI agents handle everything from technical spec writing to implementation, testing, and automated Git commits.                    
   
  This approach enabled a small team to build and ship a full-featured SaaS from zero to production with paying customers, maintaining architectural consistency and high code quality throughout.                                                                                
                  
  ---                                                                                                                                                                                                                                                                             
                  
  ## Author                                                                                                                                                                                                                                                                       
   
  **Lucas da Silva Santos**                                                                                                                                                                                                                                                       
  Full Stack Developer | Co-founder at Repetz
  [LinkedIn](https://linkedin.com/in/lucas-da-silva-santos-a46879285) · [repetz.com.br](https://repetz.com.br)
