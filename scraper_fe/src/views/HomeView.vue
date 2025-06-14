<template>

  <SearchBar @search="search"></SearchBar>
  <div class="map-plan-container">
    <Map :geojson="geojson"></Map>
    <Transition name="slide-fade">
      <PlanGrid :plans="plans" v-if="plans.length > 0"></PlanGrid>
    </Transition>
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
  try {
    plans.value = [];
    geojson.value = {};
    const geoResponse = await fetch(`http://127.0.0.1:8001/api/compute_poly/?address=${address}`);
    
    if (!geoResponse.ok) {
      throw new Error(`HTTP error! status: ${geoResponse.status}`);
    }

    geojson.value = await geoResponse.json();


// Step 2: Post to get fitting blueprints
    const blueprintResponse = await fetch('http://127.0.0.1:8001/fit-blueprints', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ features: geojson.value })
    });

    if (!blueprintResponse.ok) {
      throw new Error(`Blueprint fetch failed! status: ${blueprintResponse.status}`);
    }
    
    const blueprints = await blueprintResponse.json();
    plans.value = blueprints.houses;


  } catch (error) {
    console.error('Error fetching GeoJSON:', error);
  }
}


  onMounted(() => {
    // searchPlans("a")
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

  /* --- Transition CSS for Slide-In --- */

/* Base styles for the transition */
.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.8s ease-out; /* Adjust duration and easing as desired */
}

/* Entering transition state (from) */
.slide-fade-enter-from {
  transform: translateX(100%); /* Start off-screen to the right */
  opacity: 0; /* Start invisible */
}

/* Leaving transition state (to) */
.slide-fade-leave-to {
  transform: translateX(100%); /* End off-screen to the right */
  opacity: 0; /* End invisible */
}

/* When the component is active (visible), it will transition from enter-from to its final state */
.slide-fade-enter-to,
.slide-fade-leave-from {
  transform: translateX(0); /* Final position */
  opacity: 1; /* Final visibility */
}

</style>