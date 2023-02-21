<script>
import ProgressBar from "progressbar.js";
import alert from "../assets/alert.mp3";

export default {
	name: "Pomodoro",
	data() {
		const pomodoroDuration = 25 * 60;
		return {
			pomodoroDuration,
			restDuration: 5 * 60,
			currentTimeInSeconds: pomodoroDuration * 60,
			topRight: null,
			bottomRight: null,
			bottomLeft: null,
			topLeft: null,
			btn: "Go",
			currentSegment: 1,
			interval: null,
			alert: new Audio(alert),
			pathOptions: {
				easing: "linear",
				duration: (pomodoroDuration + 1) * 1000,
				strokeWidth: 15,
				color: "#F85959",
				trailColor: "#E9D2B1",
				trailWidth: 15,
			}

		}
	},
	mounted() {
		this.topRight = new ProgressBar.Path("#top-right", this.pathOptions);
		this.topRight.set(1);

		this.bottomRight = new ProgressBar.Path("#bottom-right", this.pathOptions);
		this.bottomRight.set(1);

		this.bottomLeft = new ProgressBar.Path("#bottom-left", this.pathOptions);
		this.bottomLeft.set(1);

		this.topLeft = new ProgressBar.Path("#top-left", this.pathOptions);
		this.topLeft.set(1);
	},
	methods: {

		animatedBar() {
			this.interval = setInterval(() => {
				if (this.currentTimeInSeconds > 0) {
					this.currentTimeInSeconds--;
				} else {
					this.currentTimeInSeconds = 0;
					this.btn = "Go";
					clearInterval(this.interval);
				}
			}, 1000);
			switch (this.currentSegment) {
				case 1:
					this.topRight.animate(0);
					break;
				case 2:
					this.bottomRight.animate(0);
					break;
				case 3:
					this.bottomLeft.animate(0);
					break;
				case 4:
					this.topLeft.animate(0);
					break;
			}
		},
		startTimer() {
			if (this.btn === "Go" || this.btn === "Resume") {
				this.animatedBar();
				this.btn = "Stop";
			} else if (this.btn === "Stop") {
				this.pauseBar();
				this.btn = "Resume";
			}
		},

		onFinish() {
			clearInterval(this.interval);
			if (this.currentSegment < 4) {
				this.currentSegment++;
			} else {
				this.currentSegment = 1;
			}

			this.alert.play();
			setTimeout(() => {
				this.btn = "Go";
				this.currentTimeInSeconds = this.restDuration;
				this.alert.pause();

			}, 4500);
		}
	},
	computed: {
		timeDisplay() {
			const minutes = String(parseInt(this.currentTimeInSeconds / 60));
			const seconds = String(this.currentTimeInSeconds % 60);
			const paddedMinutes = ("0" + minutes).slice(-2);
			const paddedSeconds = ("0" + seconds).slice(-2);

			return `${paddedMinutes}:${paddedSeconds}`;
		},
	},

};
</script>

<template>
	<div class="container">
		<div id="title">
			<h1>Pomodoro &#x1F552</h1>
		</div>
		<div class="timer">

			<svg width="163" height="165" viewBox="-8 -8 184 188" fill="none" id="first-segment">
				<path stroke="#E9D2B1" stroke-width="15" stroke-linecap="round" d="M165.16,163.38A172,172,0,0,0,0,0" />
				<path id="top-right" stroke="#F85959" stroke-width="15" stroke-linecap="round"
					d="M165.16,163.38A172,172,0,0,0,0,0" />
			</svg>
			<svg width="163" height="165" viewBox="-8 -8 184 188" fill="none" id="second-segment">
				<path stroke="#E9D2B1" stroke-width="15" stroke-linecap="round" d="M0,163.34A172,172,0,0,0,164.44,0" />
				<path id="bottom-right" stroke="#F85959" stroke-width="15" stroke-linecap="round"
					d="M0,163.34A172,172,0,0,0,164.44,0" />
			</svg>
			<svg width="163" height="165" viewBox="-8 -8 184 188" fill="none" id="third-segment">
				<path stroke="#E9D2B1" stroke-width="15" stroke-linecap="round" d="M0,0A172,172,0,0,0,165.16,162.61" />
				<path id="bottom-left" stroke="#F85959" stroke-width="15" stroke-linecap="round"
					d="M0,0A172,172,0,0,0,165.16,162.61" />
			</svg>
			<svg width="163" height="165" viewBox="-8 -8 184 188" fill="none" id="fourth-segment">
				<path stroke="#E9D2B1" stroke-width="15" stroke-linecap="round" d="M160.17,0A172,172,0,0,0,0,161.51" />
				<path id="top-left" stroke="#F85959" stroke-width="15" stroke-linecap="round"
					d="M160.17,0A172,172,0,0,0,0,161.51" />
			</svg>
			<h2>{{ timeDisplay }}</h2>
		</div>
		<button @click="startTimer">{{ btn }}</button>
	</div>
</template>

<style scoped>
.container {
	height: 100vh;
	display: flex;
	flex-direction: column;
	align-items: center;
}

#title {
	display: flex;
	flex-direction: row;
	margin-top: 6rem;
	align-items: center;
}

h1 {
	font-size: 5rem;
	margin-right: 2rem;
}

.timer {
	position: relative;
	margin-top: 8rem;
	width: 20rem;
	height: 20rem;
}

button {
	margin-top: 3rem;
	width: 5rem;
	height: 3rem;
	background-color: #f85959;
	border: none;
	border-radius: 10px;
	cursor: pointer;
	font-size: 1.5rem;
	line-height: 3rem;
	text-align: center;
	color: #fff;
}

button:focus {
	outline: none;
}

button:hover {
	background-color: #333232;
	color: #f1f1f1;
	transition: .5s;
}

#first-segment {
	position: absolute;
	top: 0px;
	right: 0px;
}

#second-segment {
	position: absolute;
	bottom: 0px;
	right: 0px;
}

#third-segment {
	position: absolute;
	bottom: 0px;
	left: 0px;
}

#fourth-segment {
	position: absolute;
	top: 0px;
	left: 0px;
}

h2 {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	font-size: 3rem;
	color: #f85959;
}
</style>
