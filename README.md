# Telegram Ads Platform

A platform for buying ad campaigns in Telegram channels. Advertisers build a campaign in a **Telegram Mini App**, an operator reviews it in a **web admin panel**, and approved campaigns are submitted to Telegram Ads by browser automation.

> The source code of this project is private (client work). This page describes what it does and how it is built.

## Components
- **Telegram bot:** entry point with a "dashboard" button that opens the Mini App, and a balance top-up link to a companion Bale bot.
- **Mini App backend (FastAPI):**
  - Verifies Telegram Mini App `initData` with an **HMAC signature check**, so users cannot be spoofed.
  - Creates users and balances, and caches profile avatars.
  - **Campaign builder:** title, ad text and media upload, target channels or a link, daily limit per user, impression target and plan.
  - **Channel catalogue:** loads channel lists from Excel files, cleans and de-duplicates them and filters banned categories. Channels are grouped into about 15 categories with search, "similar channels", and local targeting by Iranian province and city.
  - **Pricing:** CPM in TON, TON-to-IRR rate, plan multiplier and cost estimates.
  - **AI ad-copy suggestions:** three short Persian ad texts from an OpenAI model, returned as structured JSON.
  - **Link preview:** resolves a channel or bot link into a name, avatar and call-to-action preview.
- **Web admin panel (FastAPI):** login with signed, expiring session cookies. Operators search campaigns, filter by status, approve, reject with a reason, or submit. Campaigns move through a status pipeline: `pending_support_review` -> `approved` -> `queued_for_rpa` -> `rpa_running` -> `submitted_to_telegram` (or `rejected` / `failed`).
- **RPA submission:** a headless browser script fills in the Telegram Ads form from the stored campaign, runs in a background thread with a timeout and a persistent browser profile, and saves a **screenshot** that the admin can check. Each run's status and errors are stored.
- **Payment and balance:** each user has a balance, and the bot links to a companion Bale bot for top-ups.

## Stack
Python, FastAPI, SQLAlchemy and SQLite, pandas, python-telegram-bot, requests, OpenAI API, headless browser automation, HTML/JavaScript front end.

## How it is run
- Runs on a Linux server behind Nginx over HTTPS, as separate services for the bot, Mini App and admin panel, with scheduled jobs and monitoring.
- Delivered and handed over with project documentation.

## Context
Built at Arika (2024 - 2026) as one of five production services I delivered and handed over.

## Contact
Amir Hossein Fotoohi - ahfotoohi@gmail.com - [LinkedIn](https://linkedin.com/in/amir-hossein-fotoohi-411429390)
