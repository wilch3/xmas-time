<template>
	<div class="main">
		<div class="snowflakes" aria-hidden="true">
			<div class="snowflake">
			❅
			</div>
			<div class="snowflake">
			❅
			</div>
			<div class="snowflake">
			❆
			</div>
			<div class="snowflake">
			❄
			</div>
			<div class="snowflake">
			❅
			</div>
			<div class="snowflake">
			❆
			</div>
			<div class="snowflake">
			❄
			</div>
			<div class="snowflake">
			❅
			</div>
			<div class="snowflake">
			❆
			</div>
			<div class="snowflake">
			❄
			</div>
		</div>
		<div class="joululaulu">
			<div class="song">
				<audio ref="joululaulu" src="./joululaulu.wav" loop>
				</audio>
				<button class="controls" v-if="!audioPlay" @click="play()">&#9654;</button>
				<button class="controls" v-else @click="pause()">&#9208;</button>
				<p style="font-size: 10px;">"pelleiläkin on joulua" laulu by Aino, Lumi & Otso</p>
			</div>
		</div>
		<div class="center">
			<h1 class="funky">MuTe Christmas Plugin Calendar</h1>
			<p style="margin: 40px 0px;">Discover a daily selected (free!) plugin of the MuTe student community to play around with during the holidays!</p>
		</div>
		<div class="calendar">
			<div v-for="[k, v] of Object.entries(plugins)" class="calendar-item" :class="{ 'today': getDay(k) === Number(dd), 'past': getDay(k) <= Number(dd) }" @click="showPluginOrNot(getDay(k), v)">
				<div class="day-wrap" :class="{ 'today-circle': getDay(k) === Number(dd) }" :style="generateRandom(k)">
					<span class="day funky">{{ getDay(k) }}</span>
				</div>
			</div>
		</div>
		<div class="plugin" ref="plugin" v-if="pluginOpen">
			<div v-if="pluginData && pluginData.id === pluginOfTheDay.id">
				<div v-if="!revealed">
					<p>Click on the box for the grand reveal...</p>
					<p class="bock" @click="revealBocks()">&#x1f381;</p>
				</div>
				<div v-else>
					<p>And today's plugin is... &#129345; &#x1F941; &#x1F941;</p>
					<div v-if="fakeLoad">
						<span class="spinner rotate">&#x1f381;</span>
					</div>
					<div v-else>
						<h2 class="funky">{{ pluginOfTheDay["manufacturer"] }}: {{ pluginOfTheDay["plugin_name"] }}</h2>
						<p> &#9924; Go enjoy it <a :href="pluginOfTheDay['link']" target="_blank">here!!!</a>&#9924;</p>
					</div>
				</div>
			</div>
			<div v-else-if="pluginData">
				<div>
					<p>&#129345; &#x1F941; &#x1F941;</p>
					<div>
						<h2 class="funky">{{ pluginData["manufacturer"] }}: {{ pluginData["plugin_name"] }}</h2>
						<p> &#9924; Go enjoy it <a :href="pluginData['link']" target="_blank">here!!!</a>&#9924;</p>
					</div>
				</div>
			</div>

		</div>
		<footer class="credits"> With love, <br/> MuTe 3rd year bachelors; Christmas calendar team <br/> (Armi, Elias N., Jekaterina, Niko, Roosa)<br/> <br/> Implementation: Jekaterina</footer>
	</div>
</template>

<script setup lang="ts">
import { onClickOutside } from '@vueuse/core'

const today = new Date()
const dd = String(today.getDate()).padStart(2, '0')
const mm = String(today.getMonth() + 1).padStart(2, '0')
const yyyy = today.getFullYear()
const showPreviousDays = ref(false)
const audioPlay = ref(false)
const revealed = ref(false)
const fakeLoad = ref(true)

const target = useTemplateRef('plugin')
onClickOutside(target, event => { if (pluginOpen.value) {pluginOpen.value = false} })

const pluginOpen = ref(false)
const pluginData = ref(null)

const getDay = (date: String) => {
	return Number(date.split('-')[2])
}

// const generateRandom = () => {
// 	return { top: Math.random() * 40 + 'px', left: Math.random() * 40 + 'px' }
// }

const noChristmas = ref(false)

useHead({
	title: 'MuTe Xmas Daily Plugins :)',
})

const formatted = yyyy + '-' + mm + '-' + dd

const getPlugins = () => import('~/public/plugins.json').then(m => m.default || m)
const getPluginOfToday = (data: any) => {
	if (!data[formatted]) {
		return null
	}
	return data[formatted][0]
}

const plugins = await getPlugins()
const pluginOfTheDay = ref()
pluginOfTheDay.value = getPluginOfToday(plugins)

const randomCache: any = {}

const generateRandom = (key: string) => {
	if (!randomCache[key]) {
		randomCache[key] = {
		top: Math.random() * 40 + 'px',
		left: Math.random() * 40 + 'px'
		}
	}
	return randomCache[key]
}

const joululaulu = ref(null)
onMounted(() => {
	console.log(joululaulu.value)
})

const showPluginOrNot = (day: number, data: any) => {
	console.log('hello', day, Number(dd), data[0])
	console.log(today)
	if (day <= Number(dd)) {
		pluginOpen.value = !pluginOpen.value
		pluginData.value = data[0]
		console.log(pluginData)
	}
}

const revealBocks = () => {
	revealed.value = true
	setTimeout(() => {
		fakeLoad.value = false
	}, 3000)
}

const play = () => {
	audioPlay.value = true
	joululaulu.value?.play()
}

const pause = () => {
	audioPlay.value = false
	joululaulu.value?.pause()
}

const previousDays: any = ref({})

for (const [k, v] of Object.entries(plugins)) {
	if (parseInt(k.split('-')[2]) < parseInt(formatted.split('-')[2])) {
		previousDays.value[k] = v[0]
	}
}

</script>

<style lang="css">
@import url('https://fonts.googleapis.com/css2?family=Parkinsans:wght@300..800&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Rubik:ital,wght@0,300..900;1,300..900&family=Shizuru&display=swap');
@import url('https://fonts.googleapis.com/css2?family=VT323&display=swap');

.funky {
	font-family: "Shizuru", system-ui;
}

p {
	font-size: 18px;
}

html {
	/* font-family: Arial, Helvetica, sans-serif; */
	font-family: "VT323", monospace;
	background-color: #1f2766;
	color: white;

}
.center {
	margin: auto;
	width: 60%;
	max-width: 500px;
	padding: 10px;
	text-align: center;
	/* margin-bottom: 70px; */
}
@media (max-width: 500px) {
	.center {
		width: 100%;
	}
}
.green-bocks {
	padding: 20px;
	border: 2px solid black;
	background-color: #B6D7A8;
	color: black;
}
.green-bocks a {
	color: black;
}
.prev-days {
	cursor: pointer;
}

.prev-days:hover {
	color: green;
}

.controls {
    border: none;
    color: white;
    background: none;
    font-size: 30px;
}
.joululaulu {
	display: none;
	/* display: flex; */
    justify-content: end;
}
.song {
	display: flex;
    flex-direction: column;
    align-items: end;
}
.credits {
	position: fixed;
    bottom: 12px;
    font-size: 10px;
}
.bock {
	size: 30px;
	padding: 20px;
	cursor: pointer;
}

.rotate{
  animation: spinner 1.5s linear infinite; 
}
@keyframes spinner {
  to { transform: rotate(360deg); }
}

.spinner {
  display: inline-block;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  /* box-shadow: inset -2px 0 0 2px #0bf; */
}

/* customizable snowflake styling */
.snowflake {
  color: #fff;
  font-size: 1em;
  font-family: Arial;
  text-shadow: 0 0 1px #000;
}
.prev {
	font-size: 12px;
}

h2 {
	font-size: 48px;
}
.calendar {
	display: grid;
	grid-template-columns: repeat(6, 1fr);
	column-gap: 20px;
	row-gap: 20px;
	width: 100%;
	max-width: 750px;
    margin: auto;
	/* padding-top: 50px; */
    /* grid-template-rows: minmax(10px, 1fr) 3fr; */
}

@media (max-width: 790px) {
	.calendar {
		grid-template-columns: repeat(4, 1fr);
	}
}

@media (max-width: 400px) {
	.calendar {
		grid-template-columns: repeat(3, 1fr);
	}
}

.calendar-item {
	height: 45px;
	display: flex;
	justify-content: center;
	align-items: center;
	border: 0.5px dashed grey;
	padding: 24px 16px;
	position: relative;
}

.day {
	/* position: absolute; */
	font-size: 32px;
	display: flex;
	justify-content: center;
}

.today-circle {
	background-image: url(/today.svg);
    background-repeat: no-repeat;
    background-size: contain;
}

.day-wrap {
	width: 60px;
	height: 60px;
	position: absolute;
}

.today:hover {
	transform: scale(1.2);
}

.calendar-item:not(.past):not(.today) {
	color: grey;
}

.past {
	cursor: pointer;
	transition: 0.2s;
}

.past:hover {
	transform: scale(1.1);
}

.plugin {
	padding: 24px 38px;
    position: fixed;
    margin: auto;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    backdrop-filter: blur(40px);
	border: 6px solid;
	display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
	min-width: 400px;
    min-height: 200px;
}

a {
	color: red;
}

@-webkit-keyframes snowflakes-fall{0%{top:-10%}100%{top:100%}}@-webkit-keyframes snowflakes-shake{0%{-webkit-transform:translateX(0px);transform:translateX(0px)}50%{-webkit-transform:translateX(80px);transform:translateX(80px)}100%{-webkit-transform:translateX(0px);transform:translateX(0px)}}@keyframes snowflakes-fall{0%{top:-10%}100%{top:100%}}@keyframes snowflakes-shake{0%{transform:translateX(0px)}50%{transform:translateX(80px)}100%{transform:translateX(0px)}}.snowflake{position:fixed;top:-10%;z-index:9999;-webkit-user-select:none;-moz-user-select:none;-ms-user-select:none;user-select:none;cursor:default;-webkit-animation-name:snowflakes-fall,snowflakes-shake;-webkit-animation-duration:10s,3s;-webkit-animation-timing-function:linear,ease-in-out;-webkit-animation-iteration-count:infinite,infinite;-webkit-animation-play-state:running,running;animation-name:snowflakes-fall,snowflakes-shake;animation-duration:10s,3s;animation-timing-function:linear,ease-in-out;animation-iteration-count:infinite,infinite;animation-play-state:running,running}.snowflake:nth-of-type(0){left:1%;-webkit-animation-delay:0s,0s;animation-delay:0s,0s}.snowflake:nth-of-type(1){left:10%;-webkit-animation-delay:1s,1s;animation-delay:1s,1s}.snowflake:nth-of-type(2){left:20%;-webkit-animation-delay:6s,.5s;animation-delay:6s,.5s}.snowflake:nth-of-type(3){left:30%;-webkit-animation-delay:4s,2s;animation-delay:4s,2s}.snowflake:nth-of-type(4){left:40%;-webkit-animation-delay:2s,2s;animation-delay:2s,2s}.snowflake:nth-of-type(5){left:50%;-webkit-animation-delay:8s,3s;animation-delay:8s,3s}.snowflake:nth-of-type(6){left:60%;-webkit-animation-delay:6s,2s;animation-delay:6s,2s}.snowflake:nth-of-type(7){left:70%;-webkit-animation-delay:2.5s,1s;animation-delay:2.5s,1s}.snowflake:nth-of-type(8){left:80%;-webkit-animation-delay:1s,0s;animation-delay:1s,0s}.snowflake:nth-of-type(9){left:90%;-webkit-animation-delay:3s,1.5s;animation-delay:3s,1.5s}
</style>