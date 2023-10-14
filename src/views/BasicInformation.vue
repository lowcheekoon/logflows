<script setup lang="ts">
import { ref, onBeforeMount, computed } from 'vue';
import Axios from 'Axios';
import JobOrder from './JobOrder.vue'

const props = defineProps({
    userId: String,
    action: String,
});

const emit = defineEmits(['backToList']);

interface Location {
    address: String;
    latitude: String;
    longitude: String;
}

interface JO {
    pickup: Location;
    dropoff: Location;
}

const companyName = ref('');
const fullName = ref('');
const email = ref('');
const remarks = ref('');
const image = ref('');
const jobOrders = ref<JO[]>([]);

const location = ( address: String, latitude: String, longitude: String) => {
    let location: Location = { address: address, latitude: latitude, longitude: longitude }
    return location;
}

const addRow = () => {
    let jobOrder: JO = { pickup: location( '', '', '' ), dropoff: location( '', '', '' ) };
    jobOrders.value.push( jobOrder );
}

const removeRow = ( index: number ) => jobOrders.value = jobOrders.value.filter((item, i) => { return i != index } );

const add = function() {
    //jobOrderCollection();
    const params = {
        "name": fullName.value,
        "email": email.value,
        "companyName": companyName.value,
        "remarks": remarks.value,
        "image": image.value,
        "jobOrders": jobOrders.value,
    }
    
    Axios.post('https://61f2aba52219930017f5080b.mockapi.io/users/', params )
        .then(response => {
            if( (response.statusText ?? '') == 'Created' ) {
                emit('backToList');
            }
        })
        .catch(error => {})
        .finally();
}

const update = function() {
    const params = {
        "name": fullName.value,
        "email": email.value,
        "companyName": companyName.value,
        "remarks": remarks.value,
    }
    Axios.put('https://61f2aba52219930017f5080b.mockapi.io/users/' + props.userId, params )
        .then(response => {
            if( (response.statusText ?? '') == 'OK' ) {
                emit('backToList');
            }
        })
        .catch(error => {})
        .finally();
};
//https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/800.jpg
onBeforeMount(() => {
    if( props.action == 'view' ) {
        Axios.get('https://61f2aba52219930017f5080b.mockapi.io/users/' + props.userId)
        .then(response => {;
            companyName.value = response.data.companyName;
            fullName.value = response.data.name;
            email.value = response.data.email;
            remarks.value = response.data.remarks;
            
            let jos = response.data.jobOrders;
            jos.forEach(( item: any ) => {
                let jobOrder: JO = { 
                    pickup: location( item.pickup.address, item.pickup.latitude, item.pickup.longitude ), 
                    dropoff: location( item.dropoff.address, item.dropoff.latitude, item.dropoff.longitude ) 
                };
                jobOrders.value.push( jobOrder );
            });
        })
        .catch(error => {})
        .finally();
    } else if( props.action == 'add' ) {
        addRow();
    }
});

</script>

<template>
    <div class="main">
        <h3>Basic Information</h3>
        <div class="form">
            <div class="left">
                <div>
                    <label class="label-1">Company Name :</label>
                    <input class="input-1" type="text" v-model="companyName">
                </div>
                <div>
                    <label class="label-1">Full Name :</label>
                    <input class="input-1" type="text" v-model="fullName">
                </div>
                <div>
                    <label class="label-1">Email Address :</label>
                    <input class="input-1" type="text" v-model="email">
                </div>
            </div>
            <div class="right">
                <div>
                    <label class="textarea label-1">Remark :</label>
                    <textarea rows="9" v-model="remarks"></textarea>
                </div>
            </div>
        </div>
        <div v-if="action==`add`">
            <label class="label-1">Avatar Link :</label>
            <input class="input-1" type="text" v-model="image">
        </div>
        <div v-if="action==`add`" class="my-1">
            <h3>Job Orders</h3>
            <div>
                <button @click="addRow">Add More</button>
            </div>
            <div v-for="(jobOrder, index) in jobOrders" :key="index" class="job-order-row my-1">
                <div v-if="jobOrders.length > 1" class="text-right">
                    <a hef="javascript:void()" @click="removeRow( index )">Remove</a>
                </div>
                <div class="job-order-field">
                    <div class="left">
                        <div><label class="label-2">Pick Up Address :</label></div>
                        <div><input class="input-2" type="text" v-model="jobOrder.pickup.address"></div>
                    </div>
                    <div class="middle">
                        <div><label class="label-2">Pick Up Latitude :</label></div>
                        <div><input class="input-2" type="text" v-model="jobOrder.pickup.latitude"></div>
                    </div>
                    <div class="right">
                        <div><label class="label-2">Pick Up Longitude :</label></div>
                        <div><input class="input-2" type="text" v-model="jobOrder.pickup.longitude"></div>
                    </div>
                </div>
                <div class="job-order-field">
                    <div class="left">
                        <div><label class="label-2">Drop Off Address :</label></div>
                        <div><input class="input-2" type="text" v-model="jobOrder.dropoff.address"></div>
                    </div>
                    <div class="middle">
                        <div><label class="label-2">Drop Off Latitude :</label></div>
                        <div><input class="input-2" type="text" v-model="jobOrder.dropoff.latitude"></div>
                    </div>
                    <div class="right">
                        <div><label class="label-2">Drop Off Longitude :</label></div>
                        <div><input class="input-2" type="text" v-model="jobOrder.dropoff.longitude"></div>
                    </div>
                </div>
            </div>
        </div>
        <div class="text-center">
            <button @click="emit('backToList')">Back</button>
            <button v-if="action == `view`" @click.once="update" >Update</button>
            <button v-if="action == `add`" @click.once="add">Add</button>
        </div>
    </div>
    <job-order v-if="action==`view`" v-for="jobOrder in jobOrders" :data="{ ...jobOrder }" />
</template>

<style scoped>
.my-1 {
    margin-top: 1rem;
    margin-bottom: 1rem;
}
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
.job-order-row {
    border-bottom: 1px solid #ccc;
}
.job-order-field {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
}
.left, .middle {
    padding-right: 5px;
}
.right, .middle {
    padding-left: 5px;
}
.label-1 {
    width: 100px;
    display: inline-block;
    font-size: 12px;
}
.label-2 {
    width: 100%;
    display: inline-block;
    font-size: 12px;
}
label.textarea {
    text-align: right;
    vertical-align: top;
    padding-top: 20px;
    padding-right: 10px;
}
input:focus, textarea:focus {
    border: 1px solid #a1a1a1;
}
.input-1, textarea {
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
.input-2 {
    width: 100;
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
.text-center {
    text-align: center;
}
.text-right {
    text-align: right;  
}
button {
    background-color: black;
    padding: 10px;
    border: 1px solid black;
    border-radius: 5px;
    color: white;
    margin: 5px;
}
button:hover {
    background-color: rgb(61, 61, 61);
}
a {
    cursor: pointer;
    color: black;
    padding: 5px;
}
</style>