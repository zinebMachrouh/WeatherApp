# 🌤️ Weather App 

## Overview

Welcome to the Weather App! This is a sleek, single-page weather application built with Vue 3.5, Vanilla CSS, and TypeScript 5. The app seamlessly fetches real-time weather data from public APIs and presents it beautifully!

## 💻 Tech Stack

- **Vue 3.5** – The progressive JavaScript framework that powers the app’s smooth, reactive interface.
- **Vanilla CSS** – Lightweight and fast, delivering a simple yet stylish UI without relying on heavy frameworks.
- **TypeScript 5** – Enjoy a modern, type-safe development experience for a more reliable and maintainable codebase.

## 📈 Features

- **Real-time Weather Data** – Fetch and display current weather conditions.
- **Weather Forecast** – See future weather predictions for your location.
- **Responsive Design** – Clean and modern UI that looks great on all devices.
- **Global Coverage** – Get weather updates for locations around the world.

## 🚀 Getting Started

Ready to dive into the world of weather? Follow these simple steps to get the app running on your local machine!

### Prerequisites

Before you start, ensure you have the following installed:

- Node.js (preferably the latest LTS version)
- npm or yarn (package managers for handling dependencies)

### Installation

Clone the repository:

```sh
git clone https://github.com/zinebMachrouh/WeatherApp.git
```

Navigate to the project directory:

```sh
cd WeatherApp
```

Install dependencies:

```sh
npm install
```

## 🚀 Running the App

To launch the app and see the weather forecast in action, simply run the following command:

```sh
npm run dev
```

This will start the development server and open the app in your browser. Now you’re all set to check the weather!

## 🎥 Demo

Want to see the Weather App in action before setting it up? Check out our live demo:

[Live Demo](https://zinebmachrouh.github.io/WeatherApp/)

Explore the app's features and see how it provides real-time weather updates and forecasts with a sleek and responsive design.

## 🧹 Code Linting

We take code quality seriously! To ensure the app maintains clean and readable code, we use ESLint. To run the linter, execute:

```sh
npx eslint .
```

## 💡 API Integration

This app pulls in real-time weather data from two reliable sources:

- **OpenWeatherMap API 🌍**  
    Get the latest weather updates, forecasts, and historical data from OpenWeatherMap!  
    Sign up for an API key [here](https://openweathermap.org/api) to start fetching data.

- **WeatherAPI 🌤️**  
    Another trusted weather provider offering accurate forecasts and weather data.  
    Sign up [here](https://www.weatherapi.com/) for an API key.

Both APIs provide global coverage, so you can check the weather anywhere, anytime!

## 🧑‍💻 How It Works

1. The app takes your location geolocation.
2. It sends a request to the **OpenWeatherMap API** and **WeatherAPI** with the location.
3. The API responds with the current weather data, which is displayed on the screen.
4. The app also provides a 7-day weather forecast for your location.

## 💡 Troubleshooting

1. **Issue: App not loading after running `npm run dev`.**
     - **Solution:** Make sure your dependencies are up to date. Try running `npm install` again.

2. **Issue: API keys not working.**
     - **Solution:** Double-check that you’ve copied and pasted the API keys correctly and that they are active.

## 📚 Documentation

- [Vue 3.5 Documentation](https://v3.vuejs.org/)
- [OpenWeatherMap API Documentation](https://openweathermap.org/api)
- [WeatherAPI Documentation](https://www.weatherapi.com/docs/)

## 🌟 Contributing

We welcome contributions! If you have ideas for new features or improvements, feel free to fork the repository, create a branch, and submit a pull request. Let's make the weather even better together!
