# Vue TypeScript App - Country Holidays Viewer

This project is a web application developed with **Vue.js 3** and **TypeScript** that allows users to search for countries and display their upcoming holidays. It also features functionality to randomly select countries and display their upcoming holidays.

## Features

- **Country Search**: Filter a list of countries by name.
- **Holiday Display**: Show the upcoming holidays for the selected country.
- **Random Country Selection**: Randomly selects 3 countries and shows their upcoming holidays.
- **API Integration**: Uses the [Nager.Date](https://date.nager.at) API to fetch public holidays for countries.

## Requirements

- **Node.js** >= 18.x.x
- **npm** >= 6.x.x

## Installation

Follow these steps to set up the project locally.

1. **Clone the repository**:

   ```bash
   git clone https://github.com/your-username/your-repo.git

2. **Navigate to the project directory**:
    ```bash
   cd vue-ts-app

3. **Install dependencies**:
    ```bash
   npm install
    
## Usage
1. **Run**:
  ```bash
   npm run serve
```
## Project Structure
 ```bash
vue-ts-app/
├── node_modules/       # Contains project dependencies
├── public/             # Static assets (e.g., index.html)
├── src/                # Source code
│   ├── assets/         # Image and other asset files
│   ├── components/     # Vue components
│   ├── views/          # View components for different pages
│   ├── router/         # Router configuration
│   ├── store/          # Vuex store (for state management)
│   ├── main.ts         # Main entry point of the app
│   └── App.vue         # Root Vue component
├── .eslintrc.js        # ESLint configuration
├── .prettierrc         # Prettier configuration
├── package.json        # Project metadata and dependencies
├── tsconfig.json       # TypeScript configuration
└── README.md           # Project documentation
```
## Technologies Used
- Vue.js: A progressive JavaScript framework for building user interfaces.
- TypeScript: A typed superset of JavaScript that compiles to plain JavaScript.
- Vue Router: The official router for Vue.js to navigate between pages.
- Nager.Date API: A public holiday API to fetch country and holiday data.
- ESLint: A tool for identifying and fixing code quality issues.
- Prettier: A code formatter that ensures consistent code style.

