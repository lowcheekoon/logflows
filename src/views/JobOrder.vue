<script setup lang="ts">
import { ref, onBeforeMount } from 'vue';
import MapLocation from './MapLocation.vue';

const props = defineProps({
    data: {
        type: Object,
        required: true,
    },
});

const pickUpAdress = ref('');
const dropOffAdress = ref('');

onBeforeMount(() => {
    pickUpAdress.value = props.data.pickup.address;
    dropOffAdress.value = props.data.dropoff.address;
});

</script>

<template>
    <div class="main">
        <h3>Job Order</h3>
        <div class="form">
            <div class="left">
                <div>
                    <label>Pickup Address:</label>
                    <input v-model="pickUpAdress">
                </div>
            </div>
            <div class="right">
                <div>
                    <label>Drop-off Address:</label>
                    <input v-model="dropOffAdress">
                </div>
            </div>
        </div>
        <map-location :pickup="data.pickup" :dropoff="data.dropoff"/>
    </div>
</template>

<style scoped>
.main {
    margin-top: 10px;
    padding: 20px 50px;
    background-color: white;
    border-radius: 10px;
    border: 1px solid #cfcece;
    box-shadow: 2px 2px 5px 5px #cfcece;
}
h3 {
    font-weight: 700;
}
.form {
    display: grid;
    grid-template-columns: 1fr 1fr;
}
.left {
    padding-right: 5px;
}
.right {
    padding-left: 5px;
}
label {
    width: 100px;
    display: inline-block;
    font-size: 12px;
}
input:focus {
    border: 1px solid #a1a1a1;
}
input {
    width: calc(100% - 100px);
    margin: 8px 0;
    display: inline-block;
    border: 1px solid #ccc;
    box-shadow: inset 0 1px 3px #ddd;
    border-radius: 4px;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
    padding-left: 20px;
    padding-right: 20px;
    padding-top: 12px;
    padding-bottom: 12px;
    outline: none;
}

</style>