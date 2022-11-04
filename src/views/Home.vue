<template>
	<Header
	v-model="city"
	@changeCity="getWeatherByCity(city)" 
	/>

	<!-- <keep-alive> -->
		<!-- note, keep-alive not actually implemented -->
		<main class="weather">
			
			<!-- display silly gif while loading weather -->
			<div class="weather__loading"
				v-if="!weatherLoaded && !error">
	
				<img src="/images/loading.gif" 
				alt="loading weather">
				
				<p>Looking up your city</p>
			</div>
		
			<!-- else actually display weather -->
			<div class="weather__items" 
				v-else-if="weatherLoaded">
				
				<div class="weather__icon">
					<img :src="`https://openweathermap.org/img/wn/${weather.list[weatherListIndex].weather[0].icon}@4x.png`" alt="weathericon">
				</div>
				
				<div class="weather__temperature">
					<h2>
						{{Math.round(weather.list[weatherListIndex].main.temp)}} ºc
					</h2>
				</div>
				
				<div class="weather__subitems">
					<div class="weather__description">
						<p>
							{{weather.list[weatherListIndex].weather[0].description}}
						</p>
					</div>	
					
					<div class="weather__wind">
						<p>
							{{Math.round(weather.list[weatherListIndex].wind.speed)}} m/s
						</p>
					</div>
				</div>
			</div>
			
			<div class="weather__error" 
				v-if="!weatherLoaded && error">
				<p>{{error}}</p>
			</div>
		</main>
		
	<!-- </keep-alive> -->

		<Footer 
		:weather="weather"
		:weatherLoaded="weatherLoaded"
		:weekday="weekday" 
		:daysFromNow="daysFromNow" 
		:weatherListIndex="weatherListIndex"
		@goBack="goBack" 
		@goForwards="goForwards"
		/>
</template>

<script>
import Header from '../components/Header.vue'
import Footer from '../components/Footer.vue'

export default {
	components: {
		Header,
		Footer
	},
	data() {
		return {
			city: 'finding location...',
			weather: null,
			weatherLoaded: false,
			weatherListIndex: 0,
			weekday: '',
			daysFromNow: 0,
			// 8 steps equals 24hrs
			stepsInListIndex: 8,
			lat: null,
			lon: null,
			error: null
			// lat: 59.9464927,
			// lon: 10.6730612,
      // placeholder for weatherinfo for testing purposes
			// weather: {
			// 	list: [{
			// 		main: {
			// 			temp: 0
			// 		},
			// 		weather: [{
			// 			description: 'weather description',
			// 			icon: '04d'
			// 		}],
			// 		wind: {
			// 			speed: 0,
			// 			deg: 0
			// 		}
			// 	}]
			// }
		}
	},
	// get date and weather through coords at component create
	created() {
		this.getDate()
		this.getCoordinates()
	},
	// default: get weather with geo coords
	methods: {
		async getCoordinates() {
			try {
				await navigator.geolocation.getCurrentPosition(position => {
				const { latitude, longitude } = position.coords;
				this.lat = latitude,
				this.lon= longitude,
				console.log('lat and lon set')
				this.getWeatherByLocation()
				})
			} catch (error) {
				this.error = error
			}
		},
		// this runs after search is used
		async getWeatherByCity(city) {
			this.error = null
			this.weatherLoaded = false
			try {
			const APP_ID = import.meta.env.VITE_CLIENT_ID;
			const url = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${APP_ID}`
			const response = await fetch(url)
			const weatherInfo = await response.json()
			this.setWeather(weatherInfo)
			console.log(weatherInfo)
			} catch (error) {
				console.log(error)
				this.error = 'city not found'
			}
		},
		async getWeatherByLocation() {
			this.error = null
			this.weatherLoaded = false
			try {
				const APP_ID = import.meta.env.VITE_CLIENT_ID;
				const url = `https://api.openweathermap.org/data/2.5/forecast?lat=${this.lat}&lon=${this.lon}&units=metric&appid=${APP_ID}`
				const response = await fetch(url)
				const weatherInfo = await response.json()	
				this.setWeather(weatherInfo)
			} catch (error) {
				this.error = error
			}
		},
		setWeather(weatherInfo) {
			this.weather = weatherInfo
			this.city = weatherInfo.city.name
			this.weatherLoaded = true
			console.log(weatherInfo)
		},
		getDate() {
			const weekday = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday","Monday","Tuesday","Wednesday"]
			const d = new Date()
			let day = weekday[d.getDay()+this.daysFromNow]
			this.weekday = day
		},
		goForwards() {
			if(this.daysFromNow >= 0 && this.daysFromNow < 4) {
				this.weatherListIndex += this.stepsInListIndex
				this.daysFromNow += 1
				this.getDate()
			} else {
				return
			}
		},
		goBack() {
			if(this.daysFromNow < 5 && this.daysFromNow > 0) {
				this.weatherListIndex -= this.stepsInListIndex
				this.daysFromNow -= 1
				this.getDate()
			} else {
				return
			}
		}
	}
}
</script>

<style>
	.weather__items,
	.weather__loading
	{
		/* height: min-content; */
		display: flex;
		flex-flow: column nowrap;
		justify-content: space-between;
		align-items: center;
	}

	.weather img {
		width: 250px;
	}

	.weather__temperature {
		margin-bottom: 2rem;
	}

	.weather__temperature h2 {
		font-size: 3rem;
	}

	.weather__subitems {
		display: flex;
		flex-flow: row nowrap;
		justify-content: space-between;
	}

	.weather__description {
		padding-right: 25px;
	}

	.weather__wind {
		padding-left: 25px;
	}

	.weather__error {
		text-align: center;
	}
</style>