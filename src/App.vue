<script>
import { ref, onMounted } from 'vue'
import axios from "axios";

import Card from './components/Card.vue'
import Loading from './components/Loading.vue'

export default {
  components: {
    Card,
    Loading
  },
  setup() {
    const data = ref([])
    const countries = ref([])
    const isloading = ref(false)
    const search = ref('')
    const regions = ref([
      { name: 'Africa', value: 'africa' },
      { name: 'Americas', value: 'americas' },
      { name: 'Asia', value: 'asia' },
      { name: 'Europe', value: 'europe' },
      { name: 'Oceania', value: 'oceania' },
    ])
    const region = ref('')


    const getCountries = async () => {
      isloading.value = true
      await axios
      .get(`https://restcountries.com/v3.1/all`)
      .then((res) => {
        data.value = res.data
        countries.value = data.value
        isloading.value = false
      })
    }

    function handleSearch() {
      countries.value = 
        search.value !== '' ? data.value.filter( country => country.name.official.toLowerCase().includes(search.value.toLowerCase()) ) : data.value
      region.value = ''
    }

    function handleFilter() {
      countries.value = 
        region.value !== '' ? data.value.filter( country => country.region.toLowerCase().includes(region.value.toLowerCase()) ) : data.value
      search.value = ''
    }

    onMounted(()=> {
      getCountries()
    })

    return {
      countries,
      isloading,
      search,
      regions,
      region,
      handleFilter,
      handleSearch,
    };
  }
}

</script>

<template>
  <Loading v-if="isloading"/>
  <header class="header">
    <div class="wrapper">
      <div class="header-inner">
        <h1 class="header-title">Where in the World?</h1>
        <div class="header-content">
          <div class="header-search">
            <input type="text" v-model="search" @input="handleSearch" placeholder="search by country name, native name or partial name">
          </div>
          <div class="header-filter">
            <select v-model="region" @change="handleFilter">
              <option value="" disabled selected>Filter by Region</option>
              <option v-for="region in regions" :value="region.value">{{region.name}}</option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </header>

  <main class="main">
    <div class="wrapper">
      <div class="main-content">
        <TransitionGroup name="card" tag="div" class="main-cards">
          <Card v-for="country in countries" :key="country.name.common">
            <template #image>
              <img :src="country.flags.svg" alt="">
            </template>
            <template #country>{{country.name.official}}</template>
            <template #population>{{country.population}}</template>
            <template #region>{{country.region}}</template>
            <template #capital v-if="country.capital">{{country.capital[0]}}</template>
          </Card>
        </TransitionGroup>
      </div>
    </div>
  </main>
</template>

<style lang="scss">
@import './assets/base.css';

.wrapper {
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
}

.header {

  &-inner {
    padding: 30px 0;
    margin-bottom: 20px;
  }

  &-title {
    font-weight: 600;
    margin-bottom: 30px;
  }

  &-content {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  &-filter {
    flex-shrink: 0;
    width: 300px;

    select {
      width: 100%;
      height: 42px;
      padding: 0 10px;
    }
  }

  &-search {
    width: 500px;
    flex-shrink: 0;

    input {
      width: 100%;
      height: 42px;
      padding: 0 10px;
      border: 1px solid rgba(gray, 0.5);
      border-radius: 5px;
    }
  }
}

.main {

  &-cards {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 40px;
  }
}

.card-enter-active,
.card-leave-active {
  transition-property: opacity, transform;
  transition-timing-function: ease-in-out;
  transition-duration: 300ms;
}

.card-enter-active {
  transform: translateX(0);
}

.card-leave-active {
  transform: translateX(30px);
  opacity: 0;
}

.card-enter-from,
.card-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* ensure leaving items are taken out of layout flow so that moving
   animations can be calculated correctly. */
// .card-leave-active {
//   position: absolute;
//   opacity: 0;
// }
</style>
