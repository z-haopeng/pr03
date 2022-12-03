<template>
    <v-container fill-height>
        <h1 class="text-center">Where Is The ISS Now?</h1>
        <svg></svg>
        <div class="text-center">{{coordinates}}</div>
    </v-container>
</template>

<script>
import * as d3 from "d3";
import * as topojson from "topojson-client";
import axios from "axios";

const width = 800;
const height = 400;
const projection = d3.geoEqualEarth()
    .scale(135)
    .translate([width/2, height/2]);

export default {
    data() {
        return {
            map: null,
            landmasses: null,
            coordinates: "[0°, 0°]"
        };
    },
    created() {
        axios.get(`https://cdn.jsdelivr.net/npm/world-atlas@2/land-50m.json`)
            .then(response => {
                this.landmasses = response.data;
                axios.get(`http://api.open-notify.org/iss-now.json`)
                    .then(iss_response => {
                        this.renderMap(iss_response.data.iss_position);
                        setInterval(this.update, 5000);
                    });
            });
    },
    methods: {
        renderMap(iss) {
            const svg = d3.select("svg")
                .attr("viewBox", [0, 0, width, height]);

            if(this.map) {
                d3.selectAll("g").remove();
            }

            this.map = svg.append("g");

            
            const path = d3.geoPath().projection(projection);

            this.map.append("path")
                .attr("fill", "#6AF")
                .attr("stroke", "#000")
                .attr("class", "sphere")
                .attr("d", path({type: 'Sphere'}));

            this.map.append("g")
                .attr("fill", "#060")
                .selectAll("path")
                .data(topojson.feature(this.landmasses, this.landmasses.objects.land).features)
                .join("path")
                .attr("d", path);

            this.map.append("path")
                .attr("stroke", "#333")
                .attr("fill", "none")
                .attr("stroke-width", ".25")
                .attr("class", "graticule")
                .datum(d3.geoGraticule10())
                .attr("d", path);
            
            this.drawCircle(iss);
        },

        drawCircle(iss) {
            d3.selectAll("circle").remove();

            this.map.append("circle")
                .attr("cx", projection([iss.longitude, iss.latitude])[0])
                .attr("cy", projection([iss.longitude, iss.latitude])[1])
                .attr("r", 2)
                .attr("fill", "red")
                .transition()
                    .attr("r", 5).delay(250).duration(1000);

            this.coordinates = this.coordString(iss);
            console.log(this.coordinates);
        },

        coordString(pos) {
            const long = parseFloat(pos.longitude);
            const lat = parseFloat(pos.latitude);

            var north_south, east_west;
            if(lat > 0)
                north_south = " N";
            else if(lat < 0)
                north_south = " S";

            if(long > 0)
                east_west = " E";
            else if(long < 0)
                east_west = " W";

            return "["+Math.abs(lat.toFixed(2))+"°"+north_south+", "+Math.abs(long.toFixed(2))+"°"+east_west+"]";
        },

        update() {
            axios.get(`http://api.open-notify.org/iss-now.json`)
                .then(iss_response => {
                    this.drawCircle(iss_response.data.iss_position);
                });
        }
    }
}
</script>