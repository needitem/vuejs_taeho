import EventBus from "../EvenBus.js"
<!-- eslint-disable vue/no-deprecated-filter -->
eslint-disable vue/no-deprecated-filter
<template>
    <div style="overflow:auto">
        <ul class="forecasts">
            <li v-for="forecast in forecasts" :key="forecast.dt" class="item">
                <div class="weekday">
                    {{ convertToDate(forecast.dt) | weekday }}
                </div>
                <div class="icon">
                    <img :src="`http://openweathermap.org/img/w/${forecast.weather[0].icon}.png`">
                </div>
                <div class="temperature">
                    {{ (forecast.main.temp - 273.15).toFixed(0) }}℃
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
import EventBus from '../EvenBus';
import axios from 'axios';

export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: 'forecast',
    data() {
        return {
            forecasts: [],
            lat: 37.566535,
            lon: 126.977969
        }
    },
    filters: {
        weekday(date) {
            let weekdayName = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
            return weekdayName[date.getDay()];
        }
    },
    mounted() {
        axios.get(`http://api.openweathermap.org/data/2.5/forecast?lat=${this.lat}&lon=${this.lon}&appid=67c78818c6afe50f6425fa397c98a955`)
            .then((response) => {
                let data = response.data
                this.forecasts = data.list.slice(0,8)
            })
            .catch((error) => {
                console.log(error);
            })
    },
    created() {
        EventBus.$on('centerChanged', (center) => {
            this.lat = center.lat();
            this.lon = center.lng();
            this.updateForecast(this.lat, this.lon);
        })
    },
    methods: {
        convertToDate(timestamp) {
            return new Date(timestamp * 1000)
        },
        updateForecast(lat, lon) {
            axios.get(`http://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&appid=67c78818c6afe50f6425fa397c98a955`)
                .then((response) => {
                    let data = response.data
                    this.forecasts = data.list.slice(0, 8)
                })
                .catch((error) => {
                    console.log(error);
                })
        }

    }
}
</script>

<style scoped>
.forecasts {
    display: flex;
    width: 80%;
    padding: 0;
    margin: 0 auto;
    align-items: center;
    justify-content: center;
}

.forecasts>li.item {
    position: relative;
    flex: 1;
    color: white;
    text-align: center;
    list-style-type: none;
}

.forecasts>li.item:first-child:before {
    content: '';
    position: absolute;
    top: -10px;
    left: 50%;
    display: block;
    width: 6px;
    height: 6px;
    margin-left: -3px;
    border-radius: 50%;
    background-color: #fa4444;
}

.forecasts>li.item>.temperature {
    margin-top: -8px;
}</style>