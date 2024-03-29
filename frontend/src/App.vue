<template>
	<div id="top"></div>
	<div class="access" v-if="!access.access">
		<p>
			Sorry! This page is still under development. <br />
			If you have access to the site, please use your given password:
		</p>
		<input
			ref="input"
			type="password"
			v-model="access.input"
			@input="accessSubmit"
		/>
	</div>

	<div class="background">
		<div class="first-layer"></div>
		<div class="second-layer"></div>
	</div>
	<div ref="wrapper" class="wrapper" v-if="access.access">
		<Navbar :solid="data.navbarSolid || route.name != 'Home'" />
		<Home v-show="route.name == 'Home'" />
		<About v-show="route.name == 'Home'" />
		<Tickets v-show="route.name == 'Home'" />
		<Newsletter v-show="route.name == 'Home'" v-if="false"/>
		<Sponsor v-show="route.name == 'Home'" v-if="false" />
		<Support v-show="route.name == 'Home'" />
		<router-view v-show="route.name != 'Home'"></router-view>
		<Footer />

	</div>
</template>

<script>
import "animate.css";
import Navbar from "@/components/Navbar.vue";
import { onMounted, reactive, ref } from "@vue/runtime-core";
import { useRoute, useRouter } from "vue-router";
import { debounce } from "debounce";
import Background from "./components/Background.vue";
import Home from "./components/content/Home.vue";
import About from "./components/content/About.vue";
import Tickets from "./components/content/Tickets.vue";
import Sponsor from "./components/content/Sponsor.vue";
import Newsletter from "./components/content/Newsletter.vue";
import Support from "./components/content/Support.vue"
import Footer from "./components/Footer.vue";

export default {
	components: {
		Navbar,
		Background,
		Home,
		About,
		Tickets,
		Sponsor,
		Footer,
		Newsletter,
		Support,
	},
	setup() {
		const access = reactive({
			access: true,
			input: null,
			password: "devball",
		});

		const data = reactive({
			shift: false,
			navbarSolid: false,
		});
		const router = useRouter();
		const route = useRoute();

		const input = ref(null);

		const wrapper = ref(null);

		onMounted(() => {
			if (!access.access) {
				input.value.focus();
			}

			wrapper.value.onscroll = function (e) {
				if (wrapper.value.scrollTop > 0) {
					data.navbarSolid = true;
				} else {
					data.navbarSolid = false;
				}
			};

			document.querySelectorAll('a[href^="#"]').forEach((anchor) => {
				anchor.addEventListener("click", function (e) {
					e.preventDefault();

					document
						.querySelector(this.getAttribute("href"))
						.scrollIntoView({
							behavior: "smooth",
						});
				});
			});
		});

		function processKeyDownEvent(event) {
			if (event.key === "Shift") {
				data.shift = true;
			}

			if (event.key == "ArrowUp") {
				pageUp();
			} else if (event.key == "ArrowDown") {
				pageDown();
			}
		}

		function processKeyUpEvent(event) {
			if (event.key === "Shift") {
				data.shift = false;
			}
		}

		function processWheelEvent(event) {
			if (!data.shift) {
				if (event.deltaY < 0) {
					pageUp();
				} else if (event.deltaY > 0) {
					pageDown();
				}
			}
		}

		function pageUp() {
			router.push({
				name: route.meta.prev,
			});
		}

		function pageDown() {
			router.push({
				name: route.meta.next,
			});
		}

		function accessSubmit() {
			access.access = access.input === access.password;

			if (access.access) {
				addListeners();
			}
		}

		function addListeners() {
			window.addEventListener(
				"keydown",
				debounce(processKeyDownEvent, 400, true)
			);
			window.addEventListener("keyup", processKeyUpEvent);
			window.addEventListener(
				"wheel",
				debounce(processWheelEvent, 400, true)
			);
		}

		return {
			data,
			route,
			input,
			access,
			accessSubmit,
			wrapper,
		};
	},
};
</script>

<style lang="scss">
body {
	margin: 0;
}

.access {
	display: inline-block;
	vertical-align: middle;
}

#app {
	font-family: "Source Sans Pro", sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: $black;
	height: 100vh;

	display: flex;
	flex-direction: column;

	.background {
		position: absolute;
		width: 100%;
		height: 100%;

		* {
			position: absolute;
			width: 100%;
			height: 100%;
		}

		.first-layer {
			background: url("~@/assets/background_2023.jpeg") center center no-repeat;
		}

		.second-layer {
			background: url("~@/assets/logo_2023.png") center/40% no-repeat;
			transform: translateY(-10%);

			@media screen and (max-width: 780px) {
				background-size: 70%;
			}

			filter: drop-shadow(3px 3px 0.6rem #ed7627);
		}
	}

	h1,
	h2 {
		font-weight: normal;
		margin: 0;
	}

	h3 {
		font-weight: bold;
		margin: 0;
	}

	.wrapper {
		flex-grow: 1;
		position: relative;
		overflow-x: auto;
		height: 100vh;

		box-sizing: border-box;

		.component {
			position: absolute;
			top: 0;
			padding: 5rem 0;
			height: 100%;
			width: 100%;

			box-sizing: inherit;
		}
	}
}

.slide-up-enter-active,
.slide-up-leave-active,
.slide-down-enter-active,
.slide-down-leave-active {
	transition: all 0.2s;
}

.slide-up-enter-from,
.slide-down-leave-to {
	top: 0 !important;
	height: 0 !important;
}
.slide-up-enter-to,
.slide-down-leave-from {
	top: 0 !important;
	height: 100% !important;
}
.slide-up-leave-to,
.slide-down-enter-from {
	bottom: 0 !important;
	height: 0 !important;
}
.slide-up-leave-from,
.slide-down-enter-to {
	bottom: 0 !important;
	height: 100% !important;
}
</style>