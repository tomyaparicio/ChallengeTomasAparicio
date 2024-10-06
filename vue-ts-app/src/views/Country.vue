<template>
  <div class="country-page">
    <button class="back-button" @click="goBack">Back</button>
    <h1>Holidays ({{ selectedYear }})</h1>
    <div class="year-buttons">
      <button
        v-for="year in years"
        :key="year"
        @click="fetchHolidays(year)"
        :class="{ 'active-year': selectedYear === year }"
        class="year-button"
      >
        {{ year }}
      </button>
    </div>
    <div v-if="holidays.length > 0" class="holidays-list">
      <div v-for="holiday in holidays" :key="holiday.date" class="holiday-item">
        <p>
          <strong>{{ holiday.name }}</strong> - {{ holiday.date }}
          {{ holiday.types }}
        </p>
      </div>
    </div>
    <div v-else>
      <p>No holidays found for this year.</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface Holiday {
  name: string;
  date: string;
  types: string;
}

export default defineComponent({
  data() {
    return {
      countryName: '',
      holidays: [] as Holiday[],
      selectedYear: new Date().getFullYear(),
      years: Array.from({ length: 11 }, (_, i) => 2020 + i),
    };
  },
  methods: {
    async fetchHolidays(year: number) {
      this.selectedYear = year;
      const countryCode = this.$route.params.countryCode as string;
      if (!countryCode) {
        console.error('Country code not found');
        return;
      }
      const res = await fetch(
        `${process.env.VUE_APP_API}/PublicHolidays/${year}/${countryCode}`,
      );
      this.holidays = await res.json();
    },
    goBack() {
      this.$router.back();
    },
  },
  mounted() {
    this.fetchHolidays(this.selectedYear);
  },
});
</script>

<style scoped>
/* Diseño general */
.country-page {
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f9f9f9;
  color: #333;
  max-width: 800px;
  margin: 0 auto;
  text-align: center;
  box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
  position: relative;
  animation: fadeIn 0.5s ease-in-out;
}

/* Animación suave de entrada */
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

/* Botón "Back" */
.back-button {
  position: absolute;
  top: 20px;
  left: 20px;
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition:
    background-color 0.3s ease,
    transform 0.2s ease;
  font-size: 14px;
}

.back-button:hover {
  background-color: #0056b3;
  transform: translateY(-2px);
}

/* Botones de años */
.year-buttons {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.year-button {
  padding: 10px 15px;
  margin: 0 5px;
  background-color: #e0e0e0;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition:
    background-color 0.3s ease,
    transform 0.2s ease;
}

.year-button:hover {
  background-color: #007bff;
  color: white;
  transform: translateY(-2px);
}

.year-button.active-year {
  background-color: #007bff;
  color: white;
  border-color: #007bff;
}

/* Lista de feriados */
.holidays-list {
  margin-top: 20px;
}

.holiday-item {
  background-color: #fff;
  padding: 15px;
  margin: 10px 0;
  border-radius: 8px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.05);
  transition:
    transform 0.3s ease,
    box-shadow 0.3s ease;
}

.holiday-item:hover {
  transform: translateY(-3px);
  box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.1);
}

.holiday-item p {
  margin: 0;
  font-size: 16px;
  color: #333;
}
</style>
