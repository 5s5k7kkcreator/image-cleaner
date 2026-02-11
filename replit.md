# replit.md

## Overview

**منظّف الصور الذكي** (Smart Image Cleaner) is a mobile application built with React Native / Expo that helps users find and delete duplicate images from their photo library. The app scans the device's media library, groups duplicate images by file size and optionally by content hash, lets users pick which image to keep from each duplicate group, and deletes the rest — freeing up storage space. The UI is in Arabic (RTL) with a dark theme.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend (Expo / React Native)

- **Framework**: Expo SDK 54 with React Native 0.81, using the new architecture (`newArchEnabled: true`)
- **Routing**: `expo-router` with file-based routing. The app has a simple single-screen layout (`app/index.tsx`) wrapped in a root layout (`app/_layout.tsx`)
- **State Management**: React Context (`ScanProvider` in `lib/scan-context.tsx`) manages all scan state — permissions, progress, duplicate groups, selection, and deletion. `@tanstack/react-query` is available for server-side data fetching but the core scanning logic is local/on-device
- **UI Components**: Custom components (`ScanButton`, `ProgressCard`, `DuplicateGroupCard`, `ResultSummary`) with animations via `react-native-reanimated`, haptic feedback via `expo-haptics`, and `expo-linear-gradient` for gradient styling
- **Fonts**: Inter font family (400, 500, 600, 700 weights) loaded via `@expo-google-fonts/inter`
- **Color Theme**: Dark theme defined in `constants/colors.ts` with a navy/blue palette (`#0A1628` background)
- **Image Scanning Engine** (`lib/image-scanner.ts`): Uses `expo-media-library` to enumerate photos, `expo-file-system` for file access, and `expo-crypto` for content hashing. Supports two duplicate detection modes: size-based grouping (fast) and hash-based grouping (accurate). Processes images in batches of 100

### Backend (Express)

- **Framework**: Express 5 with TypeScript, compiled via `esbuild` for production
- **Purpose**: Primarily serves as a lightweight API server. Currently has minimal routes (just the scaffolding in `server/routes.ts`)
- **CORS**: Configured to allow Replit domains and localhost origins for development
- **Static Serving**: In production, serves a static landing page and Expo web build from `dist/`
- **Storage Layer** (`server/storage.ts`): Uses an in-memory storage (`MemStorage`) with a `Map` for users. This is a placeholder — the interface (`IStorage`) is designed to be swapped for a database-backed implementation

### Database Schema

- **ORM**: Drizzle ORM with PostgreSQL dialect
- **Schema** (`shared/schema.ts`): Currently defines a single `users` table with `id` (UUID), `username`, and `password` fields
- **Validation**: Uses `drizzle-zod` for schema-to-Zod type generation
- **Note**: The database schema exists but the storage layer currently uses in-memory storage. The app's core functionality (image scanning) is entirely on-device and doesn't require a database. The Drizzle/Postgres setup is scaffolding for potential server-side features

### Development & Build

- **Dev workflow**: Two processes run concurrently — `expo:dev` for the Expo dev server and `server:dev` for the Express backend (via `tsx`)
- **Production build**: Expo static build (`expo:static:build`) + Express server build (`server:build`), served together via `server:prod`
- **Path aliases**: `@/*` maps to project root, `@shared/*` maps to `./shared/*`

## External Dependencies

### Core Platform
- **Expo SDK 54** — React Native development platform
- **React Native 0.81** — Mobile UI framework
- **React 19.1** — UI library

### Device APIs (Expo modules)
- `expo-media-library` — Access device photo library (core feature)
- `expo-file-system` — Read file data for hashing
- `expo-crypto` — Generate content hashes for duplicate detection
- `expo-haptics` — Tactile feedback on interactions
- `expo-image` — Optimized image rendering
- `expo-image-picker` — Image selection UI

### Server & Data
- **Express 5** — HTTP server
- **PostgreSQL** (`pg` driver) — Database (provisioned via Replit)
- **Drizzle ORM** — Type-safe database queries and schema management
- **drizzle-zod** — Schema validation bridge

### UI & UX
- `react-native-reanimated` — Smooth animations
- `react-native-gesture-handler` — Touch gesture handling
- `react-native-screens` — Native screen management
- `react-native-safe-area-context` — Safe area insets
- `react-native-keyboard-controller` — Keyboard-aware layouts
- `expo-linear-gradient` — Gradient backgrounds
- `@expo/vector-icons` — Icon sets (Ionicons, MaterialCommunityIcons, Feather)
- `@expo-google-fonts/inter` — Typography

### State & Networking
- `@tanstack/react-query` — Server state management
- `@react-native-async-storage/async-storage` — Local key-value storage

### Build Tools
- `esbuild` — Server bundling
- `tsx` — TypeScript execution for development
- `drizzle-kit` — Database migration tooling
- `patch-package` — Post-install patching