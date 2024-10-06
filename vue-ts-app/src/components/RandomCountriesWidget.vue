<template>
  <div>
    <!-- Muestra los países con su próximo feriado -->
    <div v-for="country in randomCountries" :key="country.name">
      <h3>{{ country.name }}</h3>
      <p>Next holiday: {{ country.holidayName }} on {{ country.date }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  data() {
    return {
      randomCountries: [] as Array<{
        name: string;
        holidayName: string;
        date: string;
      }>,
    };
  },
  methods: {
    async fetchRandomCountries() {
      const resCountries = await fetch(
        `${process.env.VUE_APP_API}/AvailableCountries`,
      );
      const countries = await resCountries.json();

      // Seleccionar 3 países aleatorios
      const selectedCountries = this.getRandomItems(countries, 3);

      // Obtener el próximo feriado para cada país
      this.randomCountries = await Promise.all(
        selectedCountries.map(async (country) => {
          const resHolidays = await fetch(
            `${process.env.VUE_APP_API}/NextPublicHolidays/${country.countryCode}`,
          );
          const holidays = await resHolidays.json();

          const nextHoliday = holidays.length > 0 ? holidays[0] : null;

          return {
            name: country.name,
            holidayName: nextHoliday ? nextHoliday.name : 'No upcoming holiday',
            date: nextHoliday ? nextHoliday.date : 'N/A',
          };
        }),
      );
    },

    getRandomItems(array: any[], count: number) {
      return array.sort(() => 0.5 - Math.random()).slice(0, count);
    },
  },
  mounted() {
    this.fetchRandomCountries();
  },
});
</script>
