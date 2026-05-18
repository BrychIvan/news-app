## Getting Started

### Prerequisites

- Node.js 20.19.0 or higher
- npm or yarn package manager

### Installation

1. Clone or navigate to the project directory
2. Install dependencies:
```bash
npm install
```

### Development

Start the development server:
```bash
npm run dev
```

The application will be available at `http://localhost:5173`

### Production Build

Build for production:
```bash
npm run build
```

Preview production build:
```bash
npm run preview
```

## Configuration

### NewsAPI Setup

To use real news data:

1. Visit [NewsAPI.org](https://newsapi.org/) and sign up for a free account
2. Copy your API key
3. Open `src/App.vue` and replace:
```javascript
const API_KEY = 'demo'
```
with:
```javascript
const API_KEY = 'your_api_key_here'
```

## Project Structure

```
src/
├── App.vue                 # Main application component
├── main.js                 # Application entry point
└── components/
    ├── NewsCard.vue       # Individual news article card
    ├── NewsFilters.vue    # Search and filter controls
    └── NewsLoader.vue     # Loading indicator
```

## Available Languages

- English
- Ukrainian

## Default Demo Data

When API key is not configured, the app displays sample news articles for demonstration purposes.

## Author

Created for educational purposes
