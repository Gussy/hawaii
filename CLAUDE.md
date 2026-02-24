# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Hawaii trip itinerary web app — a single-page static site with no build step or framework. The entire app lives in `public/index.html` (HTML, CSS, and JavaScript all inline).

## Architecture

- **Static site**: No bundler, no npm, no dependencies. Just a single HTML file.
- **Styling**: Inline `<style>` block with CSS custom properties. Supports dark/light themes via `[data-theme]` attribute.
- **JavaScript**: Inline `<script>` block. Contains itinerary data as JS objects and renders tabs for each day, maps, packing lists, etc.
- **External resources**: Google Fonts (DM Sans, Playfair Display) and Google Maps Embed API.

## Deployment

- **Hosted on Cloudflare Pages** — auto-deploys from `main` branch.
- **Build command**: none (static files served directly).
- **Build output directory**: `public/`
- **Access control**: Cloudflare Zero Trust (email-based OTP). See `DEPLOY.md` for full setup.

## Development

Open `public/index.html` directly in a browser — no dev server needed. Hermit is configured but only provides `github-token-auth`.
