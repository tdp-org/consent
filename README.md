# Silktide Consent Manager v2.0

A free, lightweight and customizable consent manager for websites, designed to help you comply with GDPR and other privacy regulations.

[Learn more](https://silktide.com/consent-manager/) or [Configure it with our wizard](https://silktide.com/consent-manager/install/)

## Features

- **Customizable Design**: Easily customize the appearance to match your website's design
- **Multiple Positioning Options**: Choose from different positions for the prompt and icon
- **Granular Consent Control**: Allow users to accept or reject different types of consent (essential, analytics, marketing, etc.)
- **Automatic Script Injection**: Automatically load third-party scripts when consent is granted
- **Analytics Integration**: Built-in support for Google Tag Manager and Silktide Analytics
- **Event Callbacks**: Trigger custom JavaScript functions when users accept or reject consent
- **Accessibility**: Fully accessible with keyboard navigation, focus traps, and ARIA labels

## Installation

To use the Silktide Consent Manager, include the following files in your project:

1. **JavaScript File**: `silktide-consent-manager.js`
2. **CSS File**: `silktide-consent-manager.css`

You can either download these files and host them yourself, or use a CDN.

### Basic Example

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Website</title>
  <link rel="stylesheet" href="path/to/silktide-consent-manager.css">
</head>
<body>
  <script src="path/to/silktide-consent-manager.js"></script>
  <script>
    // Initialize the consent manager
    window.silktideConsentManager.init({
      consentTypes: [
        {
          id: 'essential',
          label: 'Essential',
          description: 'These are necessary for the website to function and cannot be switched off.',
          required: true,
        },
        {
          id: 'analytics',
          label: 'Analytics',
          description: 'These help us understand how visitors interact with the website.',
          defaultValue: true,
          gtag: 'analytics_storage', // Automatic Google Tag Manager / Silktide Analytics integration
        },
        {
          id: 'marketing',
          label: 'Marketing',
          description: 'These are used to deliver personalized advertisements.',
          defaultValue: false,
          gtag: ['ad_storage', 'ad_user_data', 'ad_personalization'],
          onAccept: function() {
            console.log('Marketing accepted');
          },
          onReject: function() {
            console.log('Marketing rejected');
          },
        },
      ],
    });
  </script>
</body>
</html>
```

## Configuration Options

### Core Configuration

#### `consentTypes` (required)

An array of consent type objects. Each object may include:

- `id` (string, required): Unique identifier for the consent type
- `label` (string, required): Display name shown to users
- `description` (string, required): Description shown in the preferences modal
- `required` (boolean): Whether this consent is essential and cannot be rejected (default: false)
- `defaultValue` (boolean): Default state when user hasn't made a choice (default: false)
- `gtag` (string or array): Google Tag Manager consent parameter(s) to update automatically
- `scripts` (array): Scripts to inject when consent is granted (see Script Injection below)
- `onAccept` (function): Callback triggered when consent is granted
- `onReject` (function): Callback triggered when consent is rejected

#### `text`

Customize all text displayed to users:

```javascript
text: {
  prompt: {
    description: '<p>We use cookies to enhance your experience.</p>',
    acceptAllButtonText: 'Accept all',
    acceptAllButtonAccessibleLabel: 'Accept all cookies',
    rejectNonEssentialButtonText: 'Reject non-essential',
    rejectNonEssentialButtonAccessibleLabel: 'Reject all non-essential cookies',
    preferencesButtonText: 'Preferences',
    preferencesButtonAccessibleLabel: 'Manage cookie preferences',
  },
  preferences: {
    title: 'Customize your preferences',
    description: '<p>Choose which cookies you want to accept.</p>',
    creditLinkText: 'Get this consent manager for free',
    creditLinkAccessibleLabel: 'Visit Silktide Consent Manager',
  },
}
```

#### `prompt`

Configure the initial consent prompt:

```javascript
prompt: {
  position: 'bottomRight' // Options: 'center', 'bottomLeft', 'bottomCenter', 'bottomRight'
}
```

#### `icon`

Configure the cookie icon that appears after initial consent:

```javascript
icon: {
  position: 'bottomLeft' // Options: 'bottomLeft', 'bottomRight'
}
```

#### `backdrop`

Configure the backdrop shown behind the prompt/modal:

```javascript
backdrop: {
  show: true // Show a backdrop behind the consent prompt (default: false)
}
```

#### Other Options

- `autoShow` (boolean): Whether to automatically show the prompt on first visit (default: true)
- `namespace` (string): Namespace for localStorage keys to support multiple consent managers on one domain
- `onAcceptAll` (function): Callback when user accepts all consent types
- `onRejectAll` (function): Callback when user rejects all non-essential consent types
- `onPromptOpen` (function): Callback when consent prompt is shown
- `onPromptClose` (function): Callback when consent prompt is closed
- `onPreferencesOpen` (function): Callback when preferences modal is opened
- `onPreferencesClose` (function): Callback when preferences modal is closed
- `onBackdropOpen` (function): Callback when backdrop is shown
- `onBackdropClose` (function): Callback when backdrop is hidden

## Script Injection

Automatically load third-party scripts when consent is granted:

```javascript
{
  id: 'analytics',
  label: 'Analytics',
  description: 'Google Analytics tracking',
  scripts: [
    {
      url: 'https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID',
      load: 'async', // Options: 'async', 'defer', or omit for default
      type: 'text/javascript',
      crossorigin: 'anonymous',
      integrity: 'sha384-...' // Optional SRI hash
    }
  ],
  onAccept: function() {
    // Initialize analytics
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    gtag('config', 'GA_MEASUREMENT_ID');
  }
}
```

Scripts will only be injected once when consent is granted. If consent is later revoked, the page will automatically reload to remove the scripts.

## Google Tag Manager Integration

The consent manager automatically integrates with Google Tag Manager consent mode:

```javascript
{
  id: 'analytics',
  label: 'Analytics',
  description: 'Analytics tracking',
  gtag: 'analytics_storage', // Single parameter
},
{
  id: 'marketing',
  label: 'Marketing',
  description: 'Advertising and marketing',
  gtag: ['ad_storage', 'ad_user_data', 'ad_personalization'], // Multiple parameters
}
```

When consent changes, the manager automatically calls `gtag('consent', 'update', {...})`.

## API Methods

### `init(config)`

Initialize the consent manager with a configuration object:

```javascript
window.silktideConsentManager.init({
  consentTypes: [/* ... */],
  // ... other options
});
```

### `update(partialConfig)`

Update the configuration by merging with existing config:

```javascript
window.silktideConsentManager.update({
  text: {
    prompt: {
      description: '<p>New description</p>'
    }
  }
});
```

### `resetConsent()`

Clear all consent choices and show the prompt again:

```javascript
window.silktideConsentManager.resetConsent();
```

### `getInstance()`

Get the current consent manager instance for advanced usage:

```javascript
const manager = window.silktideConsentManager.getInstance();

// Access consent choices
const analyticsConsent = manager.getConsentChoice('analytics'); // true, false, or null

// Get all accepted consents
const accepted = manager.getAcceptedConsents(); // { essential: true, analytics: true, ... }
```

## Styling

The consent manager uses CSS variables for easy customization. Override these in your own stylesheet:

```css
#stcm-wrapper {
  --fontFamily: 'Your Font', sans-serif;
  --primaryColor: #533BE2;
  --backgroundColor: #FFFFFF;
  --textColor: #253B48;
  --backdropBackgroundColor: #00000077;
  --backdropBackgroundBlur: 5px;
  --iconColor: #533BE2;
  --iconBackgroundColor: #FFFFFF;
}
```

All CSS classes and IDs use the `stcm-` prefix to avoid conflicts with your site's styles.

## License

This project is licensed under the [MIT License](./LICENSE).

## Contributing

Contributions are welcome! If you have suggestions, bug reports, or feature requests, please open an issue or submit a pull request.

Created with love by Silktide.
