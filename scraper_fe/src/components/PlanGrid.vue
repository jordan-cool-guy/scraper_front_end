<template>
    <div class="grid-holder">
        <div class="grid-container" v-if="plans.length > 0">
            <PlanCard v-for="(item, index) in plans" :key="index" :plan-details="item" @plan-clicked="clickPlan"/>
        </div>
    </div>
</template>

<script setup>
    import PlanCard from './PlanCard.vue';
    import { useRouter } from 'vue-router';

    const props = defineProps({
        plans: Object,
    });

    function clickPlan(item){
    }


</script>

<style scoped>

    .grid-holder {
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: center;
        align-content: center;
    }

    .grid-container {
        display: grid;
        grid-template-columns: repeat(3, 1fr); /* 3 equal columns */
        gap: 20px; /* Space between grid   items */
    }

    .grid-container > * {
        border: 1px solid #ccc; /* Border around each ObjectView component */
        background-color: #f9f9f9; /* Optional background */
    } 
</style>