# 🏨 Hotel Finder

A beautiful and intuitive hotel search interface built with SvelteKit and TailwindCSS.

## ✨ Features

- **Smart City Search** – Find cities with an elegant searchable dropdown
- **Dynamic Hotel Filtering** – Hotels automatically populate based on selected city
- **Zone Information** – Hotels display with their zone for easy identification
- **Modern UI** – Sleek design with TailwindCSS and custom styling

## 🚀 Getting Started

### Prerequisites

Ensure your backend API is running at `http://localhost:8000` with the following endpoints:
- `GET /cities/?format=json`
- `GET /hotels/?format=json&city={cityId}`

### Installation

```bash
# Install dependencies
npm install

# Start development server
npm run dev
```

Visit `http://localhost:5173` to see the app in action.

## 🛠️ Built With

- [SvelteKit](https://kit.svelte.dev/) – Web framework
- [TailwindCSS](https://tailwindcss.com/) – Styling
- [svelte-select](https://github.com/rob-balfre/svelte-select) – Enhanced select components

## 📦 Building for Production

```bash
npm run build
npm run preview
```
