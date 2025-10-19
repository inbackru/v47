# Overview

This Flask-based real estate platform, InBack/Clickback, provides cashback services for new construction property purchases in the Krasnodar region, connecting buyers with developers. It offers property listings, streamlines user applications, and includes client relationship management tools. Key features are smart property search, residential complex comparisons with interactive map, user favorites, a manager dashboard for client and cashback tracking, and robust notification and document generation capabilities. The platform aims to capture market share through unique cashback incentives and an intuitive user experience.

## Recent Updates (Oct 17, 2025)

- **50/50 Mobile Split on /complexes-map**: Replaced tab-based navigation with simultaneous 50/50 vertical split (list top 50vh, map bottom 50vh) on mobile devices. Removed mobile tab switcher, both views always visible for better UX following Avito/CIAN patterns
- **Fixed Search Bar Z-Index**: Reduced search section z-index from z-30 to z-10 to prevent overlapping InBack logo in header (header uses z-index 100000+)
- **Auto-Center Map on Markers**: Added map.invalidateSize() before fitBounds() in updateMarkers() to ensure proper map dimensions and auto-centering on all residential complex markers, especially on mobile with new 50/50 split
- **Homepage Cashback Display Fix**: Corrected "Эксклюзивные предложения" section to use residential_complexes.cashback_rate instead of non-existent fields. Implemented proper fallback logic (NULL→5%, 0%→0%, positive values preserved) to handle all cashback scenarios correctly

## Previous Updates (Oct 16, 2025)

- **Yandex Maps Migration**: Migrated all mini-maps from OpenStreetMap/Leaflet to Yandex Maps API for better Russian address coverage and user familiarity. Updated /properties and /residential-complexes pages with Yandex Maps integration (ymaps.ready()), blue cluster markers, and disabled interaction (200px height static maps). API key stored in YANDEX_MAPS_API_KEY environment variable
- **Z-Index Fix for /complexes-map**: Fixed mega-menu dropdowns (Недвижимость, Ипотека) appearing behind page filters. Removed duplicate z-index rules and implemented clean hierarchy: header/mega-menu (100000-100003) > page filters (51-52) > map (1). Used specific selectors (#filtersDesktop, header .dropdown) to prevent conflicts
- **Real Database Coordinates on Mini Maps**: Migrated mini-maps from fake coordinates to real database data. Created API endpoints /api/mini-map/properties (354 properties from excel_properties.address_position_lat/lon) and /api/mini-map/complexes (11 complexes with AVG coordinates). JavaScript now fetches real coordinates via DOMContentLoaded, displays cluster markers, and redirects to /complexes-map on click
- **Avito-Style Mini Maps**: Added static mini-maps on /properties and /residential-complexes with cluster markers (200px height). Maps show preview with numbers in blue circles, clickable for navigation to /complexes-map. Maps with disabled interaction (no zoom/drag), auto-initialize on page load
- **Clickable Complex Cards on /complexes-map**: Removed "Смотреть ЖК" button and made entire residential complex card clickable with full keyboard accessibility (role="link", tabindex, Enter/Space support). "Показать на карте" button properly isolated with event.stopPropagation()
- **Fixed ImportError in smart_search**: Replaced incorrect `super_search` imports with `smart_search` throughout app.py, eliminating "cannot import name 'super_search'" errors
- **Mobile Map Experience**: Implemented Avito/CIAN-style mobile interface for /complexes-map with List/Map toggle, full-width filters, and optimized map display
- **Chaport Integration**: Replaced Jivosite with Chaport chat widget (appId: 68f0c90bff74d8504910745a)
- **Dynamic Cashback**: Fixed hardcoded 3.5% cashback on /complexes-map to use real values from residential_complexes.cashback_rate field
- **Street URL System**: Complete transliteration and 301 redirects from `/streets/<encoded>` to `/street/<slug>`, regenerated sitemap with 1,641 street URLs

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Frontend Architecture

The frontend uses server-side rendered HTML with Jinja2 and TailwindCSS (CDN-based). Interactivity is provided by vanilla JavaScript, employing a modular, component-based approach. The design emphasizes a mobile-first, responsive experience with progressive enhancement, including features like smart search, real-time filtering, Yandex Maps integration, property comparison, and PDF generation.

## Backend Architecture

The backend is built with Flask 2.3.3, following an MVC pattern with blueprints. SQLAlchemy serves as the ORM for PostgreSQL. Key architectural decisions include Flask-Login for session management and role-based access control (RBAC), robust security (password hashing, CSRF protection), and custom parsing for Russian address formats integrated with OpenStreetMap/Nominatim. The system supports phone verification and manager-to-client presentation delivery.

## Data Storage Solutions

PostgreSQL, hosted on Neon Database (serverless) and managed via SQLAlchemy 2.0.32, is the primary database. The schema includes Users, Managers, Properties, Residential Complexes, Developers, Districts, Favorites, Saved Searches, Applications, Deals, and Recommendations. Caching uses Flask-Caching, and database indexes are optimized for performance.

## Authentication & Authorization

The system supports Regular Users, Managers, and Demo Users. Authentication uses email/password (Flask-Login), Telegram ID integration, and email verification. Authorization is based on role-based access control (RBAC) with decorator-based route protection, enabling manager-specific dashboards and client-manager assignment.

## Project Structure

The project follows a standard Flask structure with `app.py` as the entry point, `models.py` for SQLAlchemy models, and dedicated folders for deployment/utility scripts, static assets, Jinja2 templates, data files, and user-uploaded assets.

# External Dependencies

## Third-Party APIs

-   **SendGrid**: Transactional emails and notifications.
-   **OpenAI**: Smart search and content generation.
-   **Telegram Bot API**: User notifications and communication.
-   **Yandex Maps API**: Interactive maps, geocoding, and location visualization with Russian address support.
-   **OpenStreetMap/Nominatim**: Geocoding and address parsing (legacy/fallback).
-   **Google Analytics**: User behavior tracking.
-   **LaunchDarkly**: Feature flagging.
-   **SMS.ru, SMSC.ru**: Russian SMS services for phone verification.
-   **Chaport**: Chat widget for user communication.

## Web Scraping Infrastructure

-   `selenium`, `playwright`, `beautifulsoup4`, `undetected-chromedriver`: Used for automated data collection.

## PDF Generation

-   `weasyprint`, `reportlab`: Used for generating property detail sheets, comparison reports, and cashback calculations.

## Image Processing

-   `Pillow`: Used for image resizing, compression, WebP conversion, and QR code generation.

## Additional Services

-   **Replit Infrastructure**: Development and hosting.
-   **reCAPTCHA**: Spam and bot prevention.