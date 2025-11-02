# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Flutter web application for the Digimon Card Game, providing a comprehensive deck builder and collection manager. The app is primarily designed for web deployment and includes features for deck building, card collection tracking, game simulation, and user authentication.

## Development Commands

### Core Flutter Commands
- `flutter run -d chrome --web-port=50000` - Run the web app on port 50000
- `flutter build web` - Build for web production
- `flutter test` - Run all tests
- `flutter analyze` - Static code analysis using flutter_lints
- `flutter pub get` - Install dependencies
- `flutter pub upgrade` - Update dependencies

### Code Generation
- `dart run build_runner build` - Generate auto_route and JSON serialization code
- `dart run build_runner build --delete-conflicting-outputs` - Force regenerate all generated files

### Mobile Web Testing
- Use `run_mobile_web_test.bat` for Android emulator testing
- Access via `http://10.0.2.2:50000` in emulator Chrome browser

## Architecture

### State Management
- **Provider pattern** with multiple specialized providers:
  - `UserProvider` - Authentication and user state
  - `DeckProvider` - Deck building and management
  - `CollectProvider` - Card collection tracking (ChangeNotifierProxyProvider dependent on UserProvider)
  - `LimitProvider` - Format limitations and restrictions
  - `LocaleProvider` - Language/locale management
  - `NoteProvider` - Notes and annotations
  - `FormatDeckCountProvider` - Deck count tracking by format
  - `HeaderToggleProvider`, `DeckSortProvider`, `TextSimplifyProvider` - UI state
- **Important**: `CollectProvider` auto-initializes when user logs in and clears on logout via proxy provider pattern

### Routing
- **AutoRoute** for declarative routing with nested routes
- Route guards for deck-specific pages (`DeckGuard` - redirects to deck-builder if required args missing)
- Main shell page (`MainRoute`) with nested child routes for tab-based navigation
- Standalone routes: `/deck-image`, `/game`, `/login/kakao`, `/logout/kakao`, `/qr`
- Nested routes under `/`: `deck-builder`, `deck-list`, `collect`, `info/*`

### Data Layer
- **Dio** HTTP client with centralized auth error handling
- Local JSON assets for card data (`assets/data/`)
- API services in `/api/` directory
- Model classes with JSON serialization

### UI Architecture
- **Responsive design** with breakpoints: 0-480 (MOBILE), 481-800 (TABLET), 801-1920 (DESKTOP), 1920+ (4K)
- Material 3 design system with custom blue theme (primary: #2563EB)
- Custom fonts: JalnanGothic (primary), MPLUSC (secondary)
- Service layer for UI concerns (DialogService, OrientationService, ColorService, SizeService)
- Widget composition with specialized directories

## Key Directories

### `/lib/`
- `/api/` - HTTP API service classes
- `/model/` - Data models and DTOs with JSON serialization
- `/page/` - Top-level page widgets corresponding to routes
- `/provider/` - State management providers
- `/service/` - Business logic and utility services
- `/widget/` - Reusable UI components organized by feature
- `/theme/` - Design system and theming
- `/util/` - Utilities (Dio client configuration)

### `/assets/`
- `/data/` - Static JSON data files (cards, keywords, notes)
- `/fonts/` - Custom fonts (JalnanGothic, MPLUSC)
- `/images/` - App icons and images

## Technical Details

### App Initialization Flow
1. `CardDataService().initialize()` - Load card data from JSON assets before app starts
2. All providers initialized via MultiProvider in main.dart
3. OAuth message listeners registered for login/logout window communication
4. `DioClient` auth error handler connected to `UserProvider.unAuth()`
5. `LimitProvider` initialized from remote data
6. `UserSettingService` loads and applies saved user preferences

### Authentication
- OAuth-based login system with window message listeners (`html.window.addEventListener`)
- Login/logout handled via popup windows that post messages back to main window
- User session persistence and auth error handling via DioClient callback
- Kakao OAuth integration with dedicated login/logout routes
- `UserProvider.unAuth()` triggered on 401/403 responses from API

### Data Management
- Card data loaded synchronously on app startup (`CardDataService`)
- User settings persisted via `UserSettingService` (locale, deck sort, text simplify, etc.)
- Collection data synced with backend when logged in
- Real-time updates between providers via ChangeNotifierProxyProvider pattern

### Code Generation
- Auto-generated routes (`router.gr.dart`)
- JSON serialization for model classes
- Build runner integration for development workflow

## Development Notes

### Testing
- Widget tests in `/test/` directory
- Basic smoke test exists but may need updating for actual app functionality
- Use `flutter test` to run tests

### Code Style
- Flutter lints enabled in `analysis_options.yaml`
- Korean comments and UI text throughout codebase
- Consistent Material 3 theming with custom color scheme

### Dependencies
- Core: Flutter SDK, Provider, AutoRoute, Dio
- UI: ResponsiveFramework, ColorPicker, QR code generation
- Platform: Web-specific packages for image handling and storage