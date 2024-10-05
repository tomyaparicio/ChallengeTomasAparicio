<template>
  <div>
    <input
      v-model="query"
      placeholder="Search for a country"
      @input="fetchCountries"
    />
    <ul>
      <li v-for="country in filteredCountries" :key="country.countryCode">
        <router-link :to="`/country/${country.countryCode}`">{{
          country.name
        }}</router-link>
      </li>
    </ul>
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
    async fetchCountries() {
      const res = await fetch(
        'https://date.nager.at/api/v3/AvailableCountries',
      );
      this.countries = await res.json();
    },
  },
  created() {
    this.fetchCountries();
  },
});
</script>
