<template>

  <SearchBar @search="search"></SearchBar>
  <div class="map-plan-container">
    <Map :geojson="geojson"></Map>
    <PlanGrid :plans="plans"></PlanGrid>
  </div>



</template>


<script setup>
  import SearchBar from '@/components/SearchBar.vue'
  import Map from '@/components/Map.vue'
  import PlanGrid from '@/components/PlanGrid.vue'

  import {ref, onMounted} from 'vue'
  import axios from 'axios';

  const plans = ref([])
  const filteredPlans = ref([])

  const loading = ref(false);
  const error = ref(null);
  const geojson =ref({})

  const searchPlans = async (input) => {
    loading.value = true;
    error.value = null;
    plans.value = [];
    filteredPlans.value = [];

    try {
      let response;

      if (typeof input === 'string') {
        response = await axios.get(`http://127.0.0.1:8000/api/houses?query=${encodeURIComponent(input)}`);
      } else if (typeof input === 'object' && input !== null) {
        response = await axios.post(`http://127.0.0.1:8000/api/houses/filter`, input);
      } else {
        throw new Error('Invalid input');
      }

      plans.value = response.data.houses;
      filteredPlans.value = response.data.houses
    } catch (err) {
      error.value = 'Failed to fetch results';
      console.log(error)
    } finally {
      loading.value = false;
    }
  };


async function search(address) {
  console.log(address)
  try {
    const response = await fetch(`http://127.0.0.1:8001/api/compute_poly/?address=${address}`);
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    geojson.value = await response.json();

  } catch (error) {
    console.error('Error fetching GeoJSON:', error);
  }
}


  onMounted(() => {
    searchPlans("a")
  })
</script>


<style scoped>
  .map-plan-container {
    display: flex;
    height: 100%; /* Optional: ensure full height if needed */
  }

  .map-plan-container > *:first-child {
    flex: 2;
  }

  .map-plan-container > *:last-child {
    flex: 1;
  }


</style>