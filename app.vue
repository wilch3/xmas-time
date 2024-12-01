<template>
	<div class="main">
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
			<h1>MuTe Student’s Christmas Plugin Calendar</h1>
			<p style="margin: 40px 0px;">Discover a daily selected (free!) plugin of the MuTe student community to play around with during the holidays!</p>
			<div class="green-bocks">
				<p>And today's plugin is... &#129345; &#x1F941; &#x1F941;</p>
				<h2>{{ pluginOfTheDay["manufacturer"] }}: {{ pluginOfTheDay["plugin_name"] }}</h2>
				<p> &#9924; Go enjoy it <a :href="pluginOfTheDay['link']" target="_blank">here!!!</a>&#9924;</p>
			</div>
			<h4 style="margin: 40px 0px;" class="prev-days" @click="showPreviousDays=!showPreviousDays">⇩ Click here to show previous days ⇩</h4>
			<div v-if="showPreviousDays" class="list-prev-days">
				Nothing to see here.. yet!
			</div>
		</div>
		<footer class="credits"> Idea: MuTe 2nd year bachelors <br/> Code: Jekaterina <br/> Design: Elias N.<br/></footer>
	</div>
</template>

<script setup lang="ts">
const today = new Date()
const dd = String(today.getDate()).padStart(2, '0')
const mm = String(today.getMonth() + 1).padStart(2, '0')
const yyyy = today.getFullYear()
const showPreviousDays = ref(false)
const audioPlay = ref(false)

useHead({
	title: 'MuTe Colossal Xmas Calendar',
})

const formatted = yyyy + '-' + mm + '-' + dd

const getPlugins = () => import('~/public/plugins.json').then(m => m.default || m)
const getPluginOfToday = (data: any) => {
	return data[formatted]
}

const plugins = await getPlugins()
const pluginOfTheDay = ref()
pluginOfTheDay.value = getPluginOfToday(plugins)[0]

const joululaulu = ref(null)
onMounted(() => {
	console.log(joululaulu.value)
})

const play = () => {
	audioPlay.value = true
	joululaulu.value?.play()
}

const pause = () => {
	audioPlay.value = false
	joululaulu.value?.pause()
}
</script>

<style lang="css">
@import url('https://fonts.googleapis.com/css2?family=Parkinsans:wght@300..800&display=swap');
.parkinsans-300 {
  font-family: "Parkinsans", sans-serif;
  font-optical-sizing: auto;
  font-weight: 300;
  font-style: normal;
}
html {
	font-family: "Parkinsans", sans-serif;
	background-color: #CC0000;
	color: white;

}
.center {
	margin: auto;
	width: 60%;
	max-width: 500px;
	padding: 10px;
	text-align: center;
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
	display: flex;
    justify-content: end;
}
.song {
	display: flex;
    flex-direction: column;
    align-items: end;
}
.credits {
	position: absolute;
    bottom: 12px;
    font-size: 10px;
}
</style>