# Getting Started

Quick guide to get up and running with React Hook Form AI.

## Installation

```bash
npm install react-hook-form-ai
# or
pnpm add react-hook-form-ai
# or
yarn add react-hook-form-ai
```

## Basic Usage

React Hook Form AI is a drop-in replacement for React Hook Form with added AI capabilities.

### Simple Form with AI Autofill

```tsx
import { useForm } from 'react-hook-form-ai';

interface FormData {
  firstName: string;
  lastName: string;
  email: string;
  age: number;
}

function App() {
  const {
    register,
    handleSubmit,
    aiAutofill,
    aiLoading,
    aiAvailability,
    formState: { errors },
  } = useForm<FormData>();

  const handleAutofill = async () => {
    try {
      await aiAutofill();
    } catch (error) {
      console.error('Autofill failed:', error);
    }
  };

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <input {...register('firstName')} placeholder="First Name" />
      
      <input {...register('lastName', { required: true })} placeholder="Last Name" />
      {errors.lastName && <p>Last name is required.</p>}
      
      <input {...register('email')} placeholder="Email" type="email" />
      
      <input {...register('age', { pattern: /\d+/ })} placeholder="Age" />
      {errors.age && <p>Please enter number for age.</p>}
      
      <button 
        type="button" 
        onClick={handleAutofill}
        disabled={aiLoading || !aiAvailability?.available}
      >
        {aiLoading ? 'Filling...' : 'AI Autofill'}
      </button>
      
      {aiAvailability && !aiAvailability.available && (
        <p>AI is not available. Status: {aiAvailability.status}</p>
      )}
      
      <input type="submit" />
    </form>
  );
}
```

## Global Configuration with AIFormProvider

For better organization and to avoid repeating configuration, wrap your app with `AIFormProvider`:

```tsx
import { AIFormProvider } from 'react-hook-form-ai';
import App from './App';

function Root() {
  return (
    <AIFormProvider
      providers={[
        { type: 'chrome', priority: 10 },
        { 
          type: 'openai', 
          apiKey: process.env.REACT_APP_OPENAI_KEY || '',
          model: 'gpt-3.5-turbo',
          priority: 5 
        }
      ]}
      fallbackOnError={true}
    >
      <App />
    </AIFormProvider>
  );
}
```

Now all forms in your app will use these AI providers automatically.

## Key Concepts

### AI Providers

React Hook Form AI supports multiple AI providers:

- **Chrome Built-in AI**: Free, privacy-friendly, on-device AI (requires Chrome 127+)
- **OpenAI**: Cloud-based AI using GPT models (requires API key)
- **Custom Server**: Your own AI backend
- **Browser AI**: Browser-based AI services

### Provider Priority and Fallback

Providers are tried in order based on:
1. `executionOrder` array (if specified)
2. `priority` values (highest first)

When `fallbackOnError` is `true`, the next provider is automatically tried if one fails.

### AI Availability

Chrome Built-in AI may require downloading a model (~1-2GB) on first use. Use `aiAvailability` to check status:

```tsx
const { aiAvailability, aiDownloadProgress } = useForm();

if (aiAvailability?.needsDownload) {
  // Show download button
}

if (aiAvailability?.status === 'downloading') {
  // Show progress: aiDownloadProgress (0-100)
}

if (aiAvailability?.available) {
  // AI is ready to use
}
```

## Next Steps

- [Provider Configuration](./PROVIDERS.md) - Detailed provider setup
- [API Reference](./API_REFERENCE.md) - Complete API documentation
- [Examples](./EXAMPLES.md) - More usage examples
- [Browser Compatibility](./BROWSER_COMPATIBILITY.md) - Browser requirements

## Learn More

This library builds on [React Hook Form](https://react-hook-form.com/). All React Hook Form features work exactly the same - we just add the AI part.
