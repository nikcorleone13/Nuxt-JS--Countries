<template>
    <div class="wrapper">
      <div v-if="loading" class="loading-screen">
        <h1>Loading.....</h1>
      </div>
  
      <div v-else id="flag-section">
        <input type="text" id="input-box" v-model="searchText" placeholder="Search" class="search-box" />
        <div v-for="country in filteredCountries" :key="country.cca3" class="flag-div">
          <img :src="country.flags.png" alt="flag" class="flag-img" />
          <div class="flag-info">
            <h1>{{ country.name.common }}</h1>
            <p>
              Currency: {{ country.currencies ? Object.values(country.currencies)[0].name : "N/A" }}
            </p>
            <p>Current Date and Time: {{ currentDate }}</p>
            <div class="button-div">
              <button @click="showMap(country.maps.googleMaps)">Show Map</button>
              <button @click="showCountryDetails(country.cca3)">Show Country Details</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    data() {
      return {
        countries: [],
        searchText: "",
        loading: true,
        currentDate: new Date().toLocaleString(),
      };
    },
    computed: {
      filteredCountries() {
        return this.countries.filter((country) =>
          country.name.common.toLowerCase().includes(this.searchText.toLowerCase())
        );
      },
    },
    methods: {
      async fetchCountries() {
        try {
          const response = await axios.get("https://restcountries.com/v3.1/all");
          this.countries = response.data;
          localStorage.setItem("countries", JSON.stringify(response.data));
        } catch (error) {
          console.error("Error fetching countries data:", error);
        } finally {
          this.loading = false;
        }
      },
      showMap(mapUrl) {
        window.open(mapUrl, "_blank");
      },
      showCountryDetails(countryCode) {
        this.$router.push(`/${countryCode}`);
      },
    },
    mounted() {
      const savedCountries = localStorage.getItem("countries");
      if (savedCountries) {
        this.countries = JSON.parse(savedCountries);
        this.loading = false;
      } else {
        this.fetchCountries();
      }
    },
  };
  </script>
  
  <style scoped>
  .wrapper {
    padding: 2rem;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  #flag-section {
    padding: 1rem;
  }
  input {
    width: 100%;
    max-width: 600px;
    padding: 1rem;
    margin-bottom: 1rem;
    border: 2px solid gray;
    border-radius: 5px;
  }
  
  .loading-screen {
    position: fixed;
    top: 10%;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    font-size: 30px;
    z-index: 1000;
  }
  
  .loading-screen h1 {
    text-align: start;
  }
  
  .flag-div {
    width: 100%;
    max-width: 600px;
    display: flex;
    flex-direction: column;
    padding: 1rem;
    border: 2px solid gray;
    border-radius: 5px;
    margin-bottom: 1rem;
  }
  
  .flag-img {
    width: 100%;
    height: auto;
    border-bottom: 2px solid gray;
    margin-bottom: 1rem;
  }
  
  .flag-info {
    text-align: center;
  }
  
  .button-div {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }
  
  .button-div button {
    padding: 0.5rem;
    border: none;
    border-radius: 5px;
    color: rgb(8, 44, 206);
    cursor: pointer;
    border: 2px solid rgb(8, 44, 206);
  }
  
  .button-div button:hover {
    background-color: rgb(8, 44, 206);
    color: white;
  }
  
  @media (min-width: 600px) {
    .flag-div {
      flex-direction: row;
      align-items: center;
    }
  
    .flag-img {
      width: 50%;
      border-bottom: none;
      margin-bottom: 0;
      border-right: 2px solid gray;
      text-align: left;
    }
    .flag-info {
      margin-left: 1rem;
      text-align: left;
    }
  
    .button-div {
      width: 100%;
      flex-direction: row;
      justify-content: space-between;
    }
    .button-div button {
      background: white;
      text-wrap: none;
      min-width: 40%;
    }
  }
  </style>
  