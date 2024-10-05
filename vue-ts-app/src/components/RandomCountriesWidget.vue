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
    // Función para obtener países aleatorios y su próximo feriado
    async fetchRandomCountries() {
      // Obtener la lista de países disponibles
      const resCountries = await fetch(
        'https://date.nager.at/api/v3/AvailableCountries',
      );
      const countries = await resCountries.json();

      // Seleccionar 3 países aleatorios
      const selectedCountries = this.getRandomItems(countries, 3);

      // Obtener el próximo feriado para cada país
      this.randomCountries = await Promise.all(
        selectedCountries.map(async (country) => {
          // Solicitar los feriados del país específico
          const resHolidays = await fetch(
            `https://date.nager.at/api/v3/NextPublicHolidays/${country.countryCode}`,
          );
          const holidays = await resHolidays.json();

          // Filtrar feriados para obtener el más próximo (después de la fecha actual)
          const nextHoliday = holidays.length > 0 ? holidays[0] : null;

          // Devolver el país y su próximo feriado
          return {
            name: country.name,
            holidayName: nextHoliday ? nextHoliday.name : 'No upcoming holiday',
            date: nextHoliday ? nextHoliday.date : 'N/A',
          };
        }),
      );
    },

    // Función para seleccionar una cantidad específica de elementos de un array
    getRandomItems(array: any[], count: number) {
      return array.sort(() => 0.5 - Math.random()).slice(0, count); // Selecciona países aleatorios
    },
  },
  mounted() {
    // Llamar a la función para obtener los países y feriados cuando el componente esté montado
    this.fetchRandomCountries();
  },
});
</script>
