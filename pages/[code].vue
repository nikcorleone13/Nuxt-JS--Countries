<template>
    <div class="wrapper">
      <div v-if="!country" class="loading-screen">
        <h1>Loading.....</h1>
      </div>
  
      <div v-else class="country-detail">
        <div class="country-info">
          <div class="flag-image-picture">
            <img :src="country.flags.png" alt="flag" class="flag-img" />
          </div>
          <div id="information">
            <h2>{{ country.name.nativeName ? Object.values(country.name.nativeName)[0].common : 'N/A' }}</h2>
            <p>Capital: {{ country.capital ? country.capital.join(', ') : 'N/A' }}</p>
            <p>Population: {{ country.population.toLocaleString() }}</p>
            <p>Region: {{ country.region }}</p>
            <p>Subregion: {{ country.subregion }}</p>
            <p>Area: {{ country.area.toLocaleString() }} km²</p>
            <p>Country Code: {{ country.idd.root }}{{ country.idd.suffixes.length > 1 ? " " : country.idd.suffixes[0] }}</p>
            <p>Languages: {{ Object.values(country.languages).join(', ') }}</p>
            <p>Timezones: {{ country.timezones.join(', ') }}</p>
          </div>
        </div>
        <div class="neighbours">
          <h2>Neighbours</h2>
          <div class="neighbour-flags">
            <div v-for="neighbour in neighbours" :key="neighbour.cca3"          class="neighbour">
              <img :src="neighbour.flags.png" :alt="flag" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        country: null,
        neighbours: [],
        currentDate: new Date().toLocaleString()
      };
    },
    computed: {
      countries() {
        const savedCountries = localStorage.getItem("countries");
        return savedCountries ? JSON.parse(savedCountries) : [];
      }
    },
    methods: {
      getCountryByCode(code) {
        return this.countries.find(country => country.cca3 === code);
      },
      getNeighbourCountries(borders) {
        return this.countries.filter(country => borders.includes(country.cca3));
      }
    },
    mounted() {
      const countryCode = this.$route.params.code;
      this.country = this.getCountryByCode(countryCode);
  
      if (this.country && this.country.borders) {
        this.neighbours = this.getNeighbourCountries(this.country.borders);
      }
    }
  };
  </script>
  
  <style scoped>
  .wrapper {
    padding: 2rem;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .loading-screen {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    width: 100%;
    background: green;
    color: white;
    font-size: 2rem;
  }
  
  .country-detail {
    width: 100%;
    max-width: 1000px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .country-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    border: 2px solid gray;
    border-radius: 5px;
    padding: 1rem;
    margin-bottom: 2rem;
    width: 100%;
  }
  
  .flag-img {
    width: 100%;
    max-width: 600px;
    height: auto;
    margin-bottom: 1rem;
  }
  
  #information {
    text-align: center;
  }
  
  .neighbours {
    width: 100%;
  }
  
  .neighbour-flags {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  
  .neighbour {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 1rem;
  }
  
  .neighbour img {
    width: 150px;
    height: auto;
    border: 1px solid gray;
    border-radius: 5px;
    margin-bottom: 0.5rem;
  }
  
  @media (min-width: 600px) {
    .country-info {
      flex-direction: row;
      justify-content: space-evenly;

    }
    .flag-image-picture{
        width: 50%;
        min-height: 100%;
    }
    .flag-img {
      width: 90%;
      height: auto;
      object-fit: cover;
      margin-bottom: 0;
    }
  
    #information {
      width: 50%;
      text-align: left;
      padding-left: 1rem;
    }
  
    .neighbours{
        border: 2px solid gray;
        padding: 1rem;


    }

    .neighbour-flags{
        display: flex;
        justify-content: start;
        gap: 2rem;
    }
    .neighbour{
        height: 100%;
    }
    .neighbour img{
        width: 200px;
    }
  }
  </style>
  