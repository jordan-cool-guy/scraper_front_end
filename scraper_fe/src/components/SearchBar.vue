<template>
    <div class='search-bar-container'>
      <input v-model="searchBarVal" type="text" placeholder="Search..." class="search-bar"/>
      <button @click="search" class="search-button"><i class="fa fa-arrow-up"></i></button>
    </div>
</template>


<script setup>
    import { ref } from 'vue'
    
    const searchBarVal = ref('')
    const emit = defineEmits(['search'])

    function search(){
        emit('search', encodeURIComponent(searchBarVal.value))
    }
</script>

<style scoped>
  .search-bar-container {
    width: 100%;
    display: flex;
    height: 4rem;
  }

    .search-bar {
    flex: 1;
    max-width: 100%;
    border-radius: 10px;
    border-style: none;
    font-size: 24px;
    box-shadow: 2px 6px 12px rgba(163, 152, 152, 0.8);  /* Subtle shadow */
    box-sizing: border-box;

  }

    .search-button {
    margin-left: -120px;
    border: none;
    background-color: rgb(170, 179, 255);
    border-radius: 25px;
    margin-top: 5px;    /* Set top margin */
    margin-bottom: 5px;
    padding-left: 45px;
    padding-right: 45px;
    font-size: 24px;
  }
  
  </style>