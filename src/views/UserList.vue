<script setup lang="ts">
import { ref, onBeforeMount, computed } from 'vue';
import Axios from 'Axios';
import BasicInformation from './BasicInformation.vue'

const users = ref([]);
const userId = ref('');
const action = ref('list');

const add = function() {
    userId.value = '';
    action.value = 'add';
}

const viewDetail = function( id: any ) {
    userId.value = id;
    action.value = 'view';
}

const receiveEmitBackToList = async function() {
    userId.value = '';
    action.value = 'list';
    await getUserList();
}

const getUserList = async function() {
    await Axios.get('https://61f2aba52219930017f5080b.mockapi.io/users')
        .then(response => {
            users.value = response.data;
        })
        .catch(error => {

        })
        .finally();
}

onBeforeMount(async () => {
    await getUserList();
});

</script>

<template>
    <div v-if="action == 'list'" class="main">
        <h3>Users <button class="addBtn" @click="add">+</button></h3>
        <div class="table">
            <div class="tr">
                <div class="header">Name</div>
                <div class="header">Avatar</div>
                <div class="header">Email</div>
                <div class="header">Action</div>
            </div>
            <div class="tr" v-if="users.length" v-for="user in users">
                <div class="td">{{ user['name'] }}</div>
                <div class="td"><img :src="user['image']" width="50" height="50" /></div>
                <div class="td">{{ user['email'] }}</div>
                <div class="td"><button @click="viewDetail( user[`id`] )">View Detail</button></div>
            </div>
        </div>
    </div>
    <BasicInformation v-if="action == 'view' || action == 'add'" :userId="userId" :action="action" @back-to-list="receiveEmitBackToList"></BasicInformation>
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
    margin-bottom: 20px;
}
.table {
    width: 800px;
}
.tr {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    border-bottom: 1px solid #a1a1a1;
}
.header {
    font-size: 16px;
    font-weight: 700;
    height: 40px;
}
.td {
    margin: auto 0;
    font-size:12px;
    font-weight: 400;
    padding: 10px 0px;
}
button {
    background-color: blue;
    padding: 10px;
    border: 1px solid blue;
    border-radius: 5px;
    color: white;
}
button:hover {
    background-color: rgb(98, 0, 255);
}
.addBtn {
    margin-left: 20px;
    padding: 5px 10px;
}
</style>