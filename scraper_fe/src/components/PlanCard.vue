<template>
<div class="house-card" @click="planClicked">
    <div class="img-container">
        <img :src="planDetails.image_url">
    </div>

    <div class="specs">
      <div class="spec-stats">
        <div class="spec"><b>{{randSqft}}&nbsp;</b> Sqft</div>
        <div class="spec"><b>{{planDetails.beds}}&nbsp;</b>Bd</div>
        <div class="spec"><b>{{planDetails.baths}}&nbsp;</b>Ba</div>
        <div class="spec"><b>{{planDetails.beds}}&nbsp;</b>Stories</div>
        <div class="spec"><b>{{planDetails.beds}}&nbsp;</b>Width</div>
        <div class="spec"><b>{{planDetails.beds}}&nbsp;</b>Depth</div>
        <div class="spec"><b>{{planDetails.name}}&nbsp;</b>name</div>

      </div>
    </div>
</div>

</template>
  
  <script setup>
  import { ref } from 'vue';

  const emit = defineEmits(['plan-clicked'])

  const props = defineProps({
    planDetails: Object,
  });

  const max = 10000;
  const min = 2000;
  const randSqft = ref(Math.floor(Math.random() * (max - min + 1) + min))


  function planClicked() {
    emit('plan-clicked', props.planDetails)
  }
  </script>
  
  <style scoped>

.house-card {
  width: 300px;
  height: 250px;
  border-radius: 10px;
  box-shadow: rgba(0, 0, 0, 0.15) 0px 4px 15px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  will-change: transform;
}

.house-card:hover {
  transform: scale(1.05);
  box-shadow: rgba(0, 0, 0, 0.30) 0px 4px 15px;
  z-index: 10;
}

.img-container {
    width: 100%; 
    height: 200px;
    overflow: hidden; 
}

.img-container img {
    width: 100%; 
    height: 100%; 
    object-fit: cover; 
    border-radius: 10px;
}


.specs {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
}


.spec-stats {
  display: grid;
  grid-template-columns: repeat(3, 1fr); 
  grid-template-rows: repeat(1, auto); 
  width: 100%;
  box-sizing: border-box; /* Adjust as needed */
}

.spec {
  border-right: 1px solid #ddd;
  padding: 5px; /* Added padding to prevent text from touching edges */
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px; 
  word-break: break-word;
}

.spec:nth-child(4n) {
  border-right: none; 
}

.spec-description {
  border: none;
  padding: 8px;
  font-size: 12px; /* reduced font size */
  word-break: break-word; /* prevent words from overflowing */
  overflow: auto; /* enables scrolling if the description is too long */
  flex-grow: 1; /* allow the description to use the remaining space */
}

  </style>
  