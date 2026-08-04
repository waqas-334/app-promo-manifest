# App Promo Manifest

This repository hosts the cross-promotion manifest used by Android apps to display promotional banners and "More Apps" screens.

## Manifest URL

```
https://raw.githubusercontent.com/waqas-334/app-promo-manifest/main/promo-manifest.json
```

## How It Works

1. Apps fetch this manifest on launch (cached for 24 hours)
2. The **first app** in the list is displayed in the home page banner
3. **All apps** are displayed in the "More Apps" section
4. Apps automatically filter themselves out of the list

## Manifest Structure

```json
{
  "updatedAt": "2026-08-01",
  "apps": [
    {
      "packageName": "com.example.app",
      "name": "App Name",
      "titleColor": "#FF5722",
      "tagline": [
        {
          "en": "First tagline in English",
          "es": "Primera frase en español"
        },
        {
          "en": "Second tagline in English",
          "es": "Segunda frase en español"
        }
      ],
      "iconUrl": "https://example.com/icon.webp",
      "storeUrl": "https://play.google.com/store/apps/details?id=com.example.app",
      "weight": 1
    }
  ]
}
```

## Field Reference

### Root Fields

| Field | Type | Description |
|-------|------|-------------|
| `updatedAt` | String | Date of last update (for reference only, not used by app) |
| `apps` | Array | List of apps to promote |

### App Object Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `packageName` | String | Yes | Android package name (e.g., `com.example.app`). Used to filter out the current app from its own promo list. |
| `name` | String | Yes | Display name shown in the banner and More Apps list. |
| `titleColor` | String | No | Hex color code for the app name text, card border, and download icon (e.g., `#FF5722`). If not specified, uses default theme color. |
| `tagline` | Array | Yes | Array of localized tagline objects. Each tagline cycles in the banner every 3 seconds, followed by "Download Now". |
| `iconUrl` | String | Yes | URL to the app icon image. Use `.webp` format for best performance. Recommended size: 192x192px. |
| `storeUrl` | String | Yes | Google Play Store URL. UTM parameters are appended automatically for attribution tracking. |
| `weight` | Integer | No | Reserved for future use. Default: `1`. |

### Tagline Object

Each tagline object contains language codes as keys and the localized text as values:

```json
{
  "en": "English tagline",
  "es": "Spanish tagline",
  "ar": "Arabic tagline",
  "fr": "French tagline"
}
```

| Language Code | Language |
|---------------|----------|
| `en` | English (fallback) |
| `es` | Spanish |
| `ar` | Arabic |
| `fr` | French |
| `de` | German |
| `hi` | Hindi |
| `bn` | Bengali |
| `ru` | Russian |
| `zh` | Chinese |

**Fallback behavior:** If the user's device language is not available, falls back to `en`. If `en` is not available, uses the first available language.

## Banner Behavior

### App Selection
- **Banner (Home Page):** Always shows the **first app** in the list
- **More Apps (Settings):** Shows **all apps** in the list

To change which app appears in the banner, move it to the first position in the `apps` array.

### Tagline Cycling

The banner cycles through all taglines plus a "Download Now" message:

```
[Tagline 1] → 3 sec → [Tagline 2] → 3 sec → [Download Now] → 3 sec → repeat
```

### Visual Elements

The `titleColor` is applied to:
- App name text
- Card border/stroke
- Download icon tint

## Examples

### Single Tagline (Legacy Format)

Still supported for backwards compatibility:

```json
{
  "tagline": {
    "en": "Single tagline here"
  }
}
```

### Multiple Taglines (Recommended)

```json
{
  "tagline": [
    {
      "en": "Rewrite your text in any tone",
      "es": "Reescribe tu texto con cualquier tono"
    },
    {
      "en": "Sound professional in every email",
      "es": "Suena profesional en cada correo"
    },
    {
      "en": "AI-powered writing assistant",
      "es": "Asistente de escritura con IA"
    }
  ]
}
```

### Complete Example

```json
{
  "updatedAt": "2026-08-03",
  "apps": [
    {
      "packageName": "ai.keyboard.assistant.oki",
      "name": "Oki - AI Keyboard",
      "titleColor": "#FB7927",
      "tagline": [
        {
          "en": "Rewrite your text in any tone, anywhere",
          "es": "Reescribe tu texto con el tono que quieras"
        },
        {
          "en": "Sound professional in every email",
          "es": "Suena profesional en todos tus correos"
        }
      ],
      "iconUrl": "https://raw.githubusercontent.com/waqas-334/app-promo-manifest/main/assets/oki-icon.webp",
      "storeUrl": "https://play.google.com/store/apps/details?id=ai.keyboard.assistant.oki",
      "weight": 1
    },
    {
      "packageName": "com.example.secondapp",
      "name": "Second App",
      "titleColor": "#4CAF50",
      "tagline": [
        {
          "en": "Amazing features await"
        }
      ],
      "iconUrl": "https://raw.githubusercontent.com/waqas-334/app-promo-manifest/main/assets/second-icon.webp",
      "storeUrl": "https://play.google.com/store/apps/details?id=com.example.secondapp",
      "weight": 1
    }
  ]
}
```

## Caching

- Apps cache the manifest for **24 hours**
- After updating this file, users will see changes within 24 hours
- For immediate testing, clear the app's data or reinstall

## Icon Hosting

Store icons in the `assets/` folder of this repository:

```
app-promo-manifest/
├── promo-manifest.json
├── assets/
│   ├── oki-icon.webp
│   └── other-app-icon.webp
└── README.md
```

Then reference them using the raw GitHub URL:
```
https://raw.githubusercontent.com/waqas-334/app-promo-manifest/main/assets/oki-icon.webp
```

## UTM Tracking

When users click on a promo, UTM parameters are automatically appended:

| Parameter | Value | Description |
|-----------|-------|-------------|
| `utm_source` | App slug (e.g., `vault`) | Which app showed the promo |
| `utm_medium` | `cross_promo` | Fixed identifier |
| `utm_campaign` | `banner` or `more_apps` | Where the promo was clicked |

Example tracked URL:
```
https://play.google.com/store/apps/details?id=ai.keyboard.assistant.oki
  &referrer=utm_source%3Dvault%26utm_medium%3Dcross_promo%26utm_campaign%3Dbanner
```
