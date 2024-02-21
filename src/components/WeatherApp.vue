<template>
	<div class="wrapper">
		<div class="top">
			<h1>Twoja Pogoda</h1>
			<div class="main-info">
				<div>
					<h3 class="city-name"></h3>
					<input v-model="input" @keyup.enter="getWeather" type="text" placeholder="Wpisz nazwę miasta..." /><button
						@click="getWeather">
						Wyślij
					</button>
					<p class="warning"></p>
				</div>
				<img :src="returnImg" alt="Obrazek przedstawiający pogodę" class="photo" />
			</div>
		</div>
		<div class="bottom">
			<div class="headings">
				<p>Pogoda:</p>
				<p>Temperatura:</p>
				<p>Wilgotność:</p>
			</div>
			<div class="weather-info">
				<p v-if="weatherName">{{ weatherName }}</p>
				<p v-if="temp">{{ temp }}°C</p>
				<p v-if="humidity">{{ humidity }}%</p>
			</div>
		</div>
	</div>
</template>

<script setup>
import { ref, computed } from 'vue'

const API_LINK = ref('https://api.openweathermap.org/data/2.5/weather?q=')
const API_KEY = ref('&appid=e5fedbf772660b79e5aea3e41126ff8c')
const API_UNITS = ref('&units=metric')

const input = ref('')
const temp = ref('')
const humidity = ref('')

const weatherImg = ref('/img/unknown.png')
const weatherName = ref('')

const getWeather = () => {
	if (input.value === '') {
		input.value = 'Warsaw'
	}

	const URL = API_LINK.value + input.value + API_KEY.value + API_UNITS.value

	axios
		.get(URL)
		.then(res => {
			temp.value = res.data.main.temp
			humidity.value = res.data.main.humidity

			const weatherId = res.data.weather[0].id

			if (weatherId >= 200 && weatherId <= 300) {
				weatherImg.value = 'img/thunderstorm.png'
				weatherName.value = 'Burza'
			} else if (weatherId >= 300 && weatherId <= 500) {
				weatherImg.value = 'img/drizzle.png'
				weatherName.value = 'Mżawka'
			} else if (weatherId >= 500 && weatherId <= 600) {
				weatherImg.value = 'img/rain.png'
				weatherName.value = 'Opady deszczu'
			} else if (weatherId >= 600 && weatherId <= 701) {
				weatherImg.value = 'img/ice.png'
				weatherName.value = 'Opady śniegu'
			} else if (weatherId >= 701 && weatherId <= 800) {
				weatherImg.value = 'img/fog.png'
				weatherName.value = 'Za mgłą'
			} else if (weatherId === 800 && weatherId <= 801) {
				weatherImg.value = 'img/sun.png'
				weatherName.value = 'Słonecznie'
			} else if (weatherId > 801) {
				weatherImg.value = 'img/cloud.png'
				weatherName.value = 'Pochmurno'
			} else {
				weatherImg.value = 'img/unknown.png'
				weatherName.value = 'Błąd'
			}
		})
		.catch(err => console.error(err))
}

const returnImg = computed(() => {
	return weatherImg.value
})
</script>
