Act as a Senior Product Manager and Lead Full-Stack Architect specializing in peer-to-peer (P2P) marketplace applications. 

I am building "Micro-Lease," a hyper-local neighborhood tool and equipment rental app. The platform allows users to list idle household equipment (e.g., pressure washers, carpet cleaners, lawn aerators, high-end tools) for daily rental, while neighbors rent them nearby instead of purchasing expensive items they rarely use.

Please help me build the foundation of this app by providing:

1. CORE USER FLOWS & UX:
   - Outline the streamlined 4-step user journey for a "Lender" (listing an item).
   - Outline the 4-step user journey for a "Renter" (discovering, booking, picking up, and returning an item).
   - Detail a simple, low-friction pick-up and return verification process (e.g., QR code scanning + timestamped photo proof of condition).

2. DATABASE SCHEMA (PostgreSQL / Supabase style):
   - Design essential database tables with relationships:
     * Users (Lenders/Renters)
     * Items (Title, Category, Daily Price, Condition, Location Geopoint, Availability Status)
     * Bookings (Start/End Date, Total Cost, Deposit, Status: Pending, Active, Completed, Disputed)
     * Reviews/Ratings

3. RISK MANAGEMENT & TRUST ARCHITECTURE:
   - Provide a plain-language micro-copy explanation for a $50–$150 refundable security deposit hold.
   - Outline an automated resolution tree for three key edge cases:
     1. Late returns
     2. Item returned damaged
     3. No-show at agreed pickup time

4. UI / LANDING PAGE COPY:
   - Provide a high-converting hero headline, sub-headline, and key value propositions targeting both Lenders (monetize unused tools) and Renters (save money and storage space).

Ensure all suggestions prioritize safety, low friction, fast local transactions, and scalability using standard micro-SaaS tech stacks (e.g., Stripe Connect for payouts/holds, Supabase, FlutterFlow/React Native).
# Micro-lease
Hyper-local neighborhood tool &amp; equipment rental app.
