<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
            <RouterLink :to="{name: 'home'}">
                <div class="flex items-center gap-3">
                    <img src="/logosh.png" alt="Logo's Photo" class="h-9 w-9">
                    <p class="text-3xl font-bold">The Local Weather</p>
                </div>
            </RouterLink>

            <div class="flex gap-3 flex-1 justify-end">
                <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="toggleModal"></i>
                <i class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity" v-if="route.query.preview"></i>
            </div>

            <BaseModal :modal-active="modalActive" @close-modal="toggleModal">
                <div class="text-black text-lg">
                    <h1 class="text-2xl mb-1 font-bold">About:</h1>
                    <p class="mb-4">The Local Weather application allows you to monitor the current and future weather of cities of your choice.</p>
                    <h2 class="text-2xl mb-1 font-bold">How it operates:</h2>
                    <ol class="list-decimal list-inside mb-4">
                        <li>
                            Search for your city by entering the name into the search bar.
                        </li>
                        <li>
                            Choose a city from the search results; this will take you to the current weather for your selection.
                        </li>
                        <li>
                            Monitor the city by clicking on the "<strong>+</strong>" icon in the top right. This action will save the city for later viewing on the home page.
                        </li>
                    </ol>
                    <h2 class="text-2xl mb-1 font-bold">Removing a city:</h2>
                    <p>If you no longer wish to monitor a city, simply select the city on the home page. At the bottom of the page, there will be an option to delete the city.</p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from 'vue-router';
import BaseModal from "./BaseModal.vue"
import {ref} from "vue";
import {uid} from 'uid'

const savedCities = ref([])
const route = useRoute()
const router = useRouter()
const addCity = () => {
    if(localStorage.getItem('savedCities')){
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
    }

    const locationObj = {
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords: {
            lat: route.query.lat,
            lng: route.query.lng,
        },
    };

    savedCities.value.push(locationObj);
    localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

    let query = Object.assign({}, route.query);
    delete query.preview;
    query.id = locationObj.id;
    router.replace({query})
}

const modalActive = ref(null);
const toggleModal = () => {
    modalActive.value = !modalActive.value;
};
</script>