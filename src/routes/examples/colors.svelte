<script>
	import { onMount } from 'svelte';
	import FadingH1 from '../../components/FadingH1.svelte';

	const changeSettings = {
		minChange: 2,
		maxChange: 5,
		speedMilliseconds: Math.round(1000 / 30),
	};

	let playing = false;
	let intervalId = null;

	onMount(() => {
		togglePlaying();
	})

	const togglePlaying = () => {
		playing = !playing;

		if (playing) {
			intervalId = setInterval(() => {
				cycleColors()
			}, changeSettings.speedMilliseconds);
		} else if (intervalId) {
			clearInterval(intervalId);
		}
	}

	const colors = {
		red: {
			rgb: { r: 255, g: 0, b: 0 },
			changeDir: {
				r: -1,
				g: 1,
				b: 1,
			},
		},
		green: {
			rgb: { r: 0, g: 255, b: 0 },
			changeDir: {
				r: 1,
				g: -1,
				b: 1,
			},
		},
		blue: {
			rgb: { r: 0, g: 0, b: 255 },
			changeDir: {
				r: 1,
				g: 1,
				b: -1,
			},
		},
	};

	$: styles = {
		nwColor: rgbToHex(colors.red.rgb),
		neColor: rgbToHex(colors.green.rgb),
		sColor: rgbToHex(colors.blue.rgb),
	};

	$: cssVarStyles = Object.entries(styles)
		.map(([key, value]) => `--${key}:${value}`)
		.join(";");

	const componentToHex = (c) => {
		var hex = c.toString(16);
		return hex.length == 1 ? "0" + hex : hex;
	};

	const rgbToHex = ({ r, g, b }) => {
		return "#" + componentToHex(r) + componentToHex(g) + componentToHex(b);
	};

	const calculateRandomIncrease = (color) => {
		for (const property in color.rgb) {
			const change =
				color.changeDir[property] *
				Math.floor(
					Math.random() * changeSettings.maxChange + changeSettings.minChange
				);
			const newValue = color.rgb[property] + change;

			if (newValue <= 255 && newValue >= 0) {
				color.rgb[property] = newValue;
			} else {
				color.changeDir[property] = -1 * color.changeDir[property];
			}
		}

		return color;
	};

	const cycleColors = () => {
		colors.NW = calculateRandomIncrease(colors.red);
		colors.NE = calculateRandomIncrease(colors.green);
		colors.S = calculateRandomIncrease(colors.blue);
	};

</script>

<main style={cssVarStyles} on:click={togglePlaying}>
	{#if !playing}
	<span class="pausedNotice">Paused (click to continue)</span>
	{/if}
	<FadingH1 visible={!playing}>Colors</FadingH1>

	<div class="swatch NW" />
	<div class="swatch NE" />
	<div class="swatch S" />
</main>

<style>
	main {
		position: fixed;
		text-align: center;
		top: 0;
		left: 0;
		height: 100%;
		width: 100%;
		margin: 0;
		padding: 0;
	}

	.pausedNotice {
		position: absolute;
		left: 1em;
		top: 1em;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

	.swatch {
		position: absolute;
		z-index: -1;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
	}

	.NW {
		background: radial-gradient(
			farthest-corner at 0px 0px,
			var(--nwColor) 10%,
			transparent 80%
		);
	}

	.NE {
		background: radial-gradient(
			farthest-corner at 100% 0,
			var(--neColor) 10%,
			transparent 80%
		);
	}

	.S {
		background: radial-gradient(
			farthest-corner at 50% 100%,
			var(--sColor) 10%,
			transparent 80%
		);
	}
</style>
