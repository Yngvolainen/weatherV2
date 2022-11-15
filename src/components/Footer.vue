<template>
    <div>
        <!-- list items in weatherlist, 8 at a time, starting from position 8 / 16 / 24 etc. depending on day of week scroll position -->
        <section class="forecast">
            <div v-if="weatherLoaded">
                <div class="forecast__items">
                    <div v-for="(item, index) in weather.list.slice(weatherListIndex, (weatherListIndex + 8))" :key="index">
                        <img class="forecast__image" :src="`https://openweathermap.org/img/wn/${item.weather[0].icon}@4x.png`" alt="weathericon">
                        <p>{{item.dt_txt.slice(11, 16)}}</p>
                    </div>
                </div>
            </div>
        </section>

        <footer class="footer">
            <button class="footer__go-back" @click="$emit('goBack')"><img v-show="canWeGoBack" src="/images/ChevronLeft.svg" alt="go one day back"></button>
            
            <section class="footer__weekday"><h3>{{weekday}}</h3></section>
    
            <button class="footer__go-forwards" @click="$emit('goForwards')"><img v-show="canWeGoForwards" src="/images/ChevronRight.svg" alt="go one day forwards"></button>
        </footer>
    </div>
</template>

<script>

export default {
    props: {
        weather: Object,
        weekday: String,
        daysFromNow: Number,
        weatherLoaded: Boolean,
        weatherListIndex: Number
    },
    data() {
        return {
            // stepsInListIndex: 8
        }
    },
    computed: {
        canWeGoBack() {
            return this.daysFromNow === 0 ? false : true 
        },
        canWeGoForwards() {
            return this.daysFromNow === 4 ? false : true
        },
    }
}
</script>

<style>
    .footer, 
    .forecast {
        margin: 0 auto;
        width: 100%;
        max-width: var(--max-width-global);
        margin-top: 3rem;
    }

    .forecast__items {
        /* margin-right: 50px; */
        display: flex;
        justify-content: center;
        /* align-items: flex-end; */
        overflow: hidden;
    }
    
    .forecast__items img {
        max-width: 50px;
        min-width: 20px;
    }

    .forecast__items p {
        font-size: 0.8rem;
        /* margin-right: 5px; */
        text-align: center;
    }
    
    footer {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
    }
    
    .footer img {
        height: var(--icon-size-medium);
    }
    
    .footer__weekday {
        text-align: center;
    }
    
    .footer__go-back {
        justify-self: start;
    }
    
    .footer__go-forwards {
        justify-self: end;
    }

</style>