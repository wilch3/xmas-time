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
			<h1>MuTe Student’s Christmas Plugin Calendar</h1>
			<p style="margin: 40px 0px;">Discover a daily selected (free!) plugin of the MuTe student community to play around with during the holidays!</p>
			<div v-if="noChristmas">
				<div class="green-bocks">
					<h2>New plugins next year!</h2>
				</div>
				<div class="list-prev-days">
					<div class="prev" v-for="[k, v] of Object.entries(plugins)">
						<p>{{ k }}: <a :href="plugins[k][0]['link']" style="color: white;" target="_blank"> {{ plugins[k][0]['plugin_name'] }} </a> by {{ plugins[k][0]['manufacturer'] }}</p>

					<!-- <div class="prev" v-for="[k, v] of Object.entries(plugins)"> -->
						<!-- <p>{{ k }}: <a :href="plugins[k]['link']" style="color: white;" target="_blank"> {{ plugins[k]['plugin_name'] }} </a> by {{ plugins[k]['manufacturer'] }}</p> -->
					</div>
				</div>
			</div>
			<div v-else>
				<div class="green-bocks">
					<div v-if="!revealed">
						<p>Click on the bocks to reveal...</p>
						<p class="bock" @click="revealBocks()">&#x1f381;</p>
					</div>
					<div v-else>
						<p>And today's plugin is... &#129345; &#x1F941; &#x1F941;</p>
						<div v-if="fakeLoad">
							<span class="spinner rotate">&#x1f381;</span>
						</div>
						<div v-else>
							<h2>{{ pluginOfTheDay["manufacturer"] }}: {{ pluginOfTheDay["plugin_name"] }}</h2>
							<p> &#9924; Go enjoy it <a :href="pluginOfTheDay['link']" target="_blank">here!!!</a>&#9924;</p>
						</div>
					</div>
				</div>
				<h4 style="margin: 40px 0px;" class="prev-days" @click="showPreviousDays=!showPreviousDays">⇩ Click here to show previous days ⇩</h4>
				<div v-if="showPreviousDays" class="list-prev-days">
					<div class="prev" v-for="[k, v] of Object.entries(previousDays)">
						<p>{{ k }}: <a :href="previousDays[k]['link']" style="color: white;" target="_blank"> {{ previousDays[k]['plugin_name'] }} </a> by {{ previousDays[k]['manufacturer'] }}</p>
					</div>
				</div>
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
const revealed = ref(false)
const fakeLoad = ref(true)

const noChristmas = ref(true)

useHead({
	title: 'MuTe Colossal Xmas Calendar',
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

const joululaulu = ref(null)
onMounted(() => {
	console.log(joululaulu.value)
})

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
	margin-bottom: 70px;
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
	display: flex;
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

@-webkit-keyframes snowflakes-fall{0%{top:-10%}100%{top:100%}}@-webkit-keyframes snowflakes-shake{0%{-webkit-transform:translateX(0px);transform:translateX(0px)}50%{-webkit-transform:translateX(80px);transform:translateX(80px)}100%{-webkit-transform:translateX(0px);transform:translateX(0px)}}@keyframes snowflakes-fall{0%{top:-10%}100%{top:100%}}@keyframes snowflakes-shake{0%{transform:translateX(0px)}50%{transform:translateX(80px)}100%{transform:translateX(0px)}}.snowflake{position:fixed;top:-10%;z-index:9999;-webkit-user-select:none;-moz-user-select:none;-ms-user-select:none;user-select:none;cursor:default;-webkit-animation-name:snowflakes-fall,snowflakes-shake;-webkit-animation-duration:10s,3s;-webkit-animation-timing-function:linear,ease-in-out;-webkit-animation-iteration-count:infinite,infinite;-webkit-animation-play-state:running,running;animation-name:snowflakes-fall,snowflakes-shake;animation-duration:10s,3s;animation-timing-function:linear,ease-in-out;animation-iteration-count:infinite,infinite;animation-play-state:running,running}.snowflake:nth-of-type(0){left:1%;-webkit-animation-delay:0s,0s;animation-delay:0s,0s}.snowflake:nth-of-type(1){left:10%;-webkit-animation-delay:1s,1s;animation-delay:1s,1s}.snowflake:nth-of-type(2){left:20%;-webkit-animation-delay:6s,.5s;animation-delay:6s,.5s}.snowflake:nth-of-type(3){left:30%;-webkit-animation-delay:4s,2s;animation-delay:4s,2s}.snowflake:nth-of-type(4){left:40%;-webkit-animation-delay:2s,2s;animation-delay:2s,2s}.snowflake:nth-of-type(5){left:50%;-webkit-animation-delay:8s,3s;animation-delay:8s,3s}.snowflake:nth-of-type(6){left:60%;-webkit-animation-delay:6s,2s;animation-delay:6s,2s}.snowflake:nth-of-type(7){left:70%;-webkit-animation-delay:2.5s,1s;animation-delay:2.5s,1s}.snowflake:nth-of-type(8){left:80%;-webkit-animation-delay:1s,0s;animation-delay:1s,0s}.snowflake:nth-of-type(9){left:90%;-webkit-animation-delay:3s,1.5s;animation-delay:3s,1.5s}
</style>