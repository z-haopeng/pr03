<template>
    <v-container>
        <h1 class="text-center">Currently On Board:</h1>
        <div class="text-center">
            <p v-for="astronaut in astronauts" :key="astronaut.name">{{ astronaut.name }}</p>
        </div>
    </v-container>
</template>

<script>
import axios from "axios";

export default {
    data() {
        return {
            astronauts: null
        };  
    },
    created() {
        axios.get(`http://api.open-notify.org/astros.json`)
            .then(response => {
                this.astronauts = response.data.people.filter(a => a.craft == "ISS");
            });
    }

}
</script>