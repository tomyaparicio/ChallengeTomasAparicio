<template>
  <div class="home-page">
    <div class="country-list">
      <input
        v-model="query"
        placeholder="Search for a country"
        class="search-input"
      />
      <ul>
        <li
          v-for="country in filteredCountries"
          :key="country.countryCode"
          class="country-item"
        >
          <button
            @click="goToCountry(country.countryCode)"
            class="country-button"
          >
            {{ country.name }}
          </button>
        </li>
      </ul>
    </div>
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
      query: '',
      countries: [] as Country[],
      randomCountries: [] as Array<{
        name: string;
        holidayName: string;
        date: string;
      }>,
    };
  },

  computed: {
    filteredCountries(): Country[] {
      return this.countries.filter((country) =>
        country.name.toLowerCase().includes(this.query.toLowerCase()),
      );
    },
  },

  methods: {
    goToCountry(countryCode: string) {
      this.$router.push(`/country/${countryCode}`);
    },
    async fetchCountries() {
      const res = await fetch(`${process.env.VUE_APP_API}/AvailableCountries`);
      this.countries = await res.json();
    },

    async fetchRandomCountries() {
      const resCountries = await fetch(
        `${process.env.VUE_APP_API}/AvailableCountries`,
      );
      const countries: Country[] = await resCountries.json();
      const selectedCountries = this.getRandomItems(countries, 3);
      const randomCountriesWithHolidays = await Promise.all(
        selectedCountries.map(async (country) => {
          const resHolidays = await fetch(
            `${process.env.VUE_APP_API}/NextPublicHolidays/${country.countryCode}`,
          );
          const holidays = await resHolidays.json();
          const nextHoliday = holidays[0];

          return {
            name: country.name,
            holidayName: nextHoliday ? nextHoliday.name : 'No upcoming holiday',
            date: nextHoliday ? nextHoliday.date : 'N/A',
          };
        }),
      );
      this.randomCountries = randomCountriesWithHolidays;
    },

    getRandomItems(array: any[], count: number) {
      return array.sort(() => 0.5 - Math.random()).slice(0, count);
    },
  },

  mounted() {
    this.fetchCountries();
    this.fetchRandomCountries();
  },
});
</script>

<style scoped>
.home-page {
  display: flex;
  justify-content: space-between;
  padding: 20px;
  background-color: #f0f4f8;
  color: #333;
  font-family: 'Roboto', Arial, sans-serif;
}

.country-list {
  width: 40%;
}

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
  border-color: #007bff;
  outline: none;
}

.country-item {
  padding: 10px;
  margin-bottom: 10px;
}

.country-button {
  width: 100%;
  padding: 12px;
  background-color: #007bff;
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
  background-color: #0056b3;
  transform: scale(1.05);
}

.random-countries-widget {
  width: 55%;
  background-color: #ffffff;
  border: 2px solid #d0d5db;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.random-countries-widget h3 {
  margin-bottom: 20px;
  font-size: 20px;
  font-weight: bold;
  color: #007bff;
  text-align: center;
}

.random-country-card {
  padding: 15px;
  margin-bottom: 20px;
  background-color: #fafafa;
  border: 2px solid #e0e0e0;
  border-radius: 10px;
  text-align: center;
  transition:
    background-color 0.3s ease,
    border-color 0.3s ease;
}

.random-country-card:hover {
  background-color: #ff5722;
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
