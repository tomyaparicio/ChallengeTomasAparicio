<template>
  <div class="home-page">
    <!-- Input de búsqueda y lista de países -->
    <div class="country-list">
      <!-- Caja de búsqueda donde se puede filtrar la lista de países -->
      <input
        v-model="query"
        placeholder="Search for a country"
        class="search-input"
      />
      <ul>
        <!-- Lista de países filtrados -->
        <li
          v-for="country in filteredCountries"
          :key="country.countryCode"
          class="country-item"
        >
          <!-- Botón que redirige al país correspondiente -->
          <button
            @click="goToCountry(country.countryCode)"
            class="country-button"
          >
            {{ country.name }}
          </button>
        </li>
      </ul>
    </div>

    <!-- Widget que muestra 3 países aleatorios y sus próximos feriados -->
    <div class="random-countries-widget">
      <h3>Random Countries Widget</h3>
      <div
        v-for="country in randomCountries"
        :key="country.name"
        class="random-country-card"
      >
        <h4>{{ country.name }}</h4>
        <p>Next Holiday: {{ country.holidayName }}</p>
        <p>Date: {{ country.date }}</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface Country {
  countryCode: string;
  name: string;
}

export default defineComponent({
  data() {
    return {
      query: '', // Almacena el texto de búsqueda que el usuario ingresa
      countries: [] as Country[], // Aquí guardamos la lista de países
      randomCountries: [] as Array<{
        name: string;
        holidayName: string;
        date: string;
      }>, // Aquí guardamos 3 países aleatorios con sus próximos feriados
    };
  },

  computed: {
    // Filtra los países en función de lo que el usuario está buscando
    filteredCountries(): Country[] {
      return this.countries.filter((country) =>
        country.name.toLowerCase().includes(this.query.toLowerCase()),
      );
    },
  },

  methods: {
    // Redirigir al país cuando se haga clic en el botón
    goToCountry(countryCode: string) {
      this.$router.push(`/country/${countryCode}`);
    },

    // Obtiene la lista de países de la API
    async fetchCountries() {
      const res = await fetch(
        'https://date.nager.at/api/v3/AvailableCountries',
      );
      this.countries = await res.json(); // Guarda la lista de países en `countries`
    },

    // Selecciona 3 países aleatorios y obtiene el próximo feriado de cada uno
    async fetchRandomCountries() {
      // Primero obtenemos la lista de países
      const resCountries = await fetch(
        'https://date.nager.at/api/v3/AvailableCountries',
      );
      const countries: Country[] = await resCountries.json();

      // Luego seleccionamos 3 países aleatorios
      const selectedCountries = this.getRandomItems(countries, 3);

      // Para cada país, hacemos una nueva solicitud a la API para obtener sus feriados
      const randomCountriesWithHolidays = await Promise.all(
        selectedCountries.map(async (country) => {
          const resHolidays = await fetch(
            `https://date.nager.at/api/v3/NextPublicHolidays/${country.countryCode}`,
          );
          const holidays = await resHolidays.json();
          const nextHoliday = holidays[0]; // Tomamos el primer feriado (el más próximo)

          return {
            name: country.name,
            holidayName: nextHoliday ? nextHoliday.name : 'No upcoming holiday', // Si no hay feriados, mostramos un mensaje
            date: nextHoliday ? nextHoliday.date : 'N/A',
          };
        }),
      );

      // Guardamos la lista de países aleatorios con sus feriados
      this.randomCountries = randomCountriesWithHolidays;
    },

    // Función que selecciona 3 elementos aleatorios de un array (la lista de países)
    getRandomItems(array: any[], count: number) {
      return array.sort(() => 0.5 - Math.random()).slice(0, count); // Ordena aleatoriamente y toma los primeros 3
    },
  },

  mounted() {
    // Al cargar la página, obtenemos la lista de países y los países aleatorios con sus feriados
    this.fetchCountries();
    this.fetchRandomCountries();
  },
});
</script>

<style scoped>
/* Página principal */
.home-page {
  display: flex;
  justify-content: space-between;
  padding: 20px;
  background-color: #f0f4f8; /* Fondo claro con un toque de azul */
  color: #333;
  font-family: 'Roboto', Arial, sans-serif;
}

/* Estilo de la lista de países */
.country-list {
  width: 40%;
}

/* Caja de búsqueda */
.search-input {
  width: 100%;
  padding: 12px;
  margin-bottom: 20px;
  border: 2px solid #d0d5db;
  border-radius: 8px;
  background-color: #fff;
  font-size: 16px;
  transition: border-color 0.3s ease;
}

.search-input:focus {
  border-color: #007bff; /* Azul brillante cuando se enfoca */
  outline: none;
}

/* Estilo para cada país en la lista */
.country-item {
  padding: 10px;
  margin-bottom: 10px;
}

/* Botones que representan los países */
.country-button {
  width: 100%;
  padding: 12px;
  background-color: #007bff; /* Azul brillante */
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  transition:
    background-color 0.3s ease,
    transform 0.2s ease;
}

.country-button:hover {
  background-color: #0056b3; /* Un azul más oscuro al pasar el ratón */
  transform: scale(1.05); /* Aumenta ligeramente el tamaño al pasar el ratón */
}

/* Widget de países aleatorios */
.random-countries-widget {
  width: 55%;
  background-color: #ffffff;
  border: 2px solid #d0d5db;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Sombra más pronunciada */
}

/* Títulos del widget */
.random-countries-widget h3 {
  margin-bottom: 20px;
  font-size: 20px;
  font-weight: bold;
  color: #007bff;
  text-align: center;
}

/* Tarjetas de los países aleatorios */
.random-country-card {
  padding: 15px;
  margin-bottom: 20px;
  background-color: #fafafa; /* Fondo claro */
  border: 2px solid #e0e0e0;
  border-radius: 10px;
  text-align: center;
  transition:
    background-color 0.3s ease,
    border-color 0.3s ease;
}

.random-country-card:hover {
  background-color: #ff5722; /* Fondo naranja al pasar el ratón */
  color: white;
  border-color: #ff5722;
}

/* Estilo de los textos */
.random-country-card h4 {
  font-size: 18px;
  margin-bottom: 10px;
  color: #333;
}

.random-country-card p {
  font-size: 14px;
  margin: 5px 0;
  color: #555;
}
</style>
