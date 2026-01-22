# Webview Development

The Ascendara Webview is a Next.js web application that enables remote monitoring and control of Ascendara downloads. This document covers the architecture, development workflow, and integration with the desktop application.

## Architecture Overview

The webview operates as a standalone Next.js application that communicates with the Ascendara desktop app through a backend API:

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│  Desktop App    │      │   Backend API   │      │   Webview UI    │
│                 │◄────►│                 │◄────►│   (Next.js)     │
│  • Downloads    │      │  • Auth         │      │  • Dashboard    │
│  • Code Gen     │      │  • Session Mgmt │      │  • Controls     │
└─────────────────┘      └─────────────────┘      └─────────────────┘
```

### Connection Flow

```
User generates 6-digit code in desktop app
    │
    ▼
User enters code in webview
    │
    ▼
Backend verifies code and creates session
    │
    ▼
Webview receives session token
    │
    ▼
Dashboard polls for download updates
```

---

## Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Framework | Next.js 16 | React framework with App Router |
| Language | TypeScript | Type-safe development |
| Styling | Tailwind CSS v4 | Utility-first CSS |
| UI Components | shadcn/ui | Accessible component library |
| Icons | Lucide React | Icon system |
| PWA | Next.js PWA | Progressive Web App support |

---

## Project Structure

```
webview/
├── app/
│   ├── [code]/page.tsx        # Dynamic code verification route
│   ├── dashboard/page.tsx     # Main dashboard
│   ├── layout.tsx             # Root layout with providers
│   ├── page.tsx               # Connection page
│   └── manifest.ts            # PWA configuration
├── components/
│   ├── ui/                    # shadcn/ui base components
│   ├── code-input.tsx         # 6-digit code input
│   ├── download-card.tsx      # Download display with controls
│   └── theme-selector.tsx     # Theme picker
├── lib/
│   ├── api.ts                 # API client
│   ├── crypto.ts              # E2E encryption
│   ├── themes.ts              # Theme definitions
│   └── utils.ts               # Utilities
└── contexts/
    └── theme-context.tsx      # Theme provider
```

### Key Files

#### `lib/api.ts`
API client handling all backend communication:
```typescript
export const api = {
  verifyCode: (code: string) => Promise<Session>,
  getDownloads: (token: string) => Promise<Download[]>,
  pauseDownload: (id: string, token: string) => Promise<void>,
  resumeDownload: (id: string, token: string) => Promise<void>,
  cancelDownload: (id: string, token: string) => Promise<void>,
  checkConnection: (token: string) => Promise<boolean>
};
```

#### `lib/crypto.ts`
End-to-end encryption utilities for secure communication:
```typescript
export function encrypt(data: string, key: string): string;
export function decrypt(encrypted: string, key: string): string;
```

#### `app/dashboard/page.tsx`
Main dashboard component that:
- Polls for download updates every 30 seconds
- Displays download cards with progress
- Handles pause/resume/cancel actions
- Shows connection status

---

## Backend Communication

All communication between the webview and backend API is **end-to-end encrypted** and only functional in the production environment at [webview.ascendara.app](https://webview.ascendara.app/). 

The API client in `lib/api.ts` handles all backend requests, but these are encrypted and cannot be used during local development. For UI development and testing, use the built-in mock mode instead (see Development Workflow section).

---

## Development Workflow

### Setup

```bash
# Clone repository
git clone https://github.com/ascendara/webview.git
cd webview

# Install dependencies
npm install

# Start dev server
npm run dev
```

### Mock Mode

For development without a backend, use the built-in mock mode:

1. Start dev server: `npm run dev`
2. Navigate to `http://localhost:3000`
3. Enter code: `123456`
4. Access mock dashboard with simulated data

Mock mode features:
- Fake download data with realistic progress
- No real API calls (blocked automatically)
- Progress updates every 2 seconds
- Visual "DEVELOPMENT" badge
- Isolated localStorage keys

**Important**: Mock mode only works when `NODE_ENV === 'development'`

## Component Development

### Using shadcn/ui

Add new components:
```bash
npx shadcn-ui@latest add [component-name]
```

Example usage:
```typescript
import { Button } from '@/components/ui/button';
import { Card } from '@/components/ui/card';

export function MyComponent() {
  return (
    <Card>
      <Button variant="default">Click Me</Button>
    </Card>
  );
}
```

### Component Pattern

```typescript
import { cn } from '@/lib/utils';

interface ComponentProps {
  className?: string;
  children: React.ReactNode;
}

export function Component({ className, children }: ComponentProps) {
  return (
    <div className={cn('base-styles', className)}>
      {children}
    </div>
  );
}
```

---

## State Management

### Local Storage

Session persistence:
```typescript
// Store session
localStorage.setItem('session-token', token);
localStorage.setItem('session-id', sessionId);

// Retrieve session
const token = localStorage.getItem('session-token');

// Clear session
localStorage.removeItem('session-token');
localStorage.removeItem('session-id');
```

### React Context

Theme management via context:
```typescript
import { useTheme } from '@/contexts/theme-context';

function Component() {
  const { theme, setTheme } = useTheme();
  
  return (
    <button onClick={() => setTheme('dark')}>
      Toggle Theme
    </button>
  );
}
```

---

## Theme System

### Theme Structure

```typescript
// lib/themes.ts
export interface Theme {
  name: string;
  colors: {
    background: string;
    foreground: string;
    primary: string;
    secondary: string;
    accent: string;
    muted: string;
  };
}
```

### Adding Themes

1. Define theme in `lib/themes.ts`:
```typescript
export const customTheme: Theme = {
  name: 'custom',
  colors: {
    background: '#000000',
    foreground: '#ffffff',
    primary: '#3b82f6',
    secondary: '#64748b',
    accent: '#8b5cf6',
    muted: '#1e293b',
  }
};
```

2. Add to themes array:
```typescript
export const themes = [defaultTheme, darkTheme, customTheme];
```

---

## PWA Configuration

### Manifest

PWA settings in `app/manifest.ts`:
```typescript
export default function manifest(): MetadataRoute.Manifest {
  return {
    name: 'Ascendara Monitor',
    short_name: 'Ascendara',
    description: 'Remote monitoring for Ascendara downloads',
    start_url: '/',
    display: 'standalone',
    background_color: '#000000',
    theme_color: '#000000',
    icons: [
      { src: '/icon-192.png', sizes: '192x192', type: 'image/png' },
      { src: '/icon-512.png', sizes: '512x512', type: 'image/png' }
    ]
  };
}
```

### Install Prompt

```typescript
import { InstallPrompt } from '@/components/install-prompt';

export function Layout() {
  return (
    <>
      <InstallPrompt />
      {children}
    </>
  );
}
```

---

## Building & Deployment

### Production Build

```bash
npm run build
npm start
```

### Docker Deployment

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
EXPOSE 3000
CMD ["npm", "start"]
```

---

## Security Considerations

### End-to-End Encryption

All sensitive data is encrypted using `lib/crypto.ts`:
```typescript
import { encrypt, decrypt } from '@/lib/crypto';

// Before sending
const encrypted = encrypt(JSON.stringify(data), sessionKey);

// After receiving
const decrypted = JSON.parse(decrypt(encrypted, sessionKey));
```

### Session Management

- Session tokens stored in localStorage
- Tokens validated on each API request
- Automatic logout on token expiration
- Clear sensitive data on disconnect

### Best Practices

- Never expose API keys in client code
- Always use HTTPS in production
- Validate all user input
- Implement proper CORS policies
- Use secure session tokens

---

## Testing

### Development Testing

```bash
npm run dev
# Use mock mode with code: 123456
```

### Production Testing

```bash

# Build and test
npm run build
npm start
```

---

## Troubleshooting

### Connection Issues

**Code not working**
- Verify 6-digit code is correct
- Check backend API is running
- Ensure code hasn't expired
- Verify network connectivity

**Downloads not updating**
- Check 30-second auto-refresh is active
- Verify session token is valid
- Check browser console for errors
- Confirm backend is responding

### Theme Issues

**Theme not persisting**
- Verify localStorage is enabled
- Check for browser extensions blocking storage
- Clear cache and retry

### PWA Issues

**Install prompt not showing**
- Ensure HTTPS is enabled
- Verify manifest.ts configuration
- Check service worker registration
- Test on supported browser

---

## Related Documentation

- [Developer Overview](/docs/developer/overview)
- [API Reference](/docs/api-reference/overview)
- [System Design](/docs/developer/system-design)
- [Contributing Guide](/docs/getting-started/contributing)

---

## Resources

- **Repository**: [github.com/ascendara/webview](https://github.com/ascendara/webview)
- **Production**: [webview.ascendara.app](https://webview.ascendara.app/)
- **Discord**: [ascendara.app/discord](https://ascendara.app/discord)