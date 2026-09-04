# ALGO SALON — Supabase Requirements and Implementation Guide

**Status:** This application is currently a **local-preview React prototype**. Its mock data and browser storage must not be treated as a production database or authentication system. The included migration provides the production data model, guarded booking workflows, private media storage, and row-level access controls required before live use.

> **Security principle:** Supabase secures its platform infrastructure; the application owner remains responsible for correct Row Level Security policies, API-key handling, and application access controls.

## 1. Recommended Setup

Create one Supabase project for each environment—**development**, **staging**, and **production**—and deploy the migration through the Supabase CLI so the schema remains reproducible.

| Requirement | Decision for ALGO SALON | Reason |
| --- | --- | --- |
| Authentication | Supabase Auth using email magic link or OTP, with optional Google OAuth | Replaces the current hard-coded, client-side “App Code” and simulated Google/email verification. |
| Authorization | Postgres RLS plus `salon_members` roles | Separates customer data from salon staff and management access at the database layer. |
| Booking writes | Protected `create_booking` database RPC | Derives price/duration from live records and stops staff-time overlaps in a single transaction. |
| Status changes | Protected `set_appointment_status` RPC | Allows only valid customer or manager actions and creates an audit event. |
| Media | Private `avatars` and `salon-media` buckets with signed URLs | Restricts uploads and reads by authenticated identity and salon membership. |
| Payments | Payment provider integration through a server-side function | Keeps payment secrets and card data out of the React application and public schema. |
| Notifications | Server-triggered inserts / trusted function | Prevents browser users from creating messages for other people. |
