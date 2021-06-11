<script>
	import { tweened } from 'svelte/motion';
	import { onMount } from 'svelte';
	/* Using custom interpolation function because d3-interpolation would 
	   fail when navigating to page directly used as described on 
		 https://svelte.dev/docs#tweened */
	// TODO: Investigate d3-interpolation direct navigation problem
	// import { interpolateLab } from 'd3-interpolate';
	import FadingH1 from '$components/FadingH1.svelte';

	let playing = false;

	let settings = {
		speed: 2500,
	}

	onMount(() => {
		togglePlaying();
	});

	const togglePlaying = () => {
		playing = !playing;

		if (playing) {
			randomizeColor(colorOne);
			randomizeColor(colorTwo);
			randomizeColor(colorThree);
		}
		// TODO: Stop in progress tweens?
	};

	function hexToRgb(hex) {
		var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
		return result
			? {
					r: parseInt(result[1], 16),
					g: parseInt(result[2], 16),
					b: parseInt(result[3], 16)
			  }
			: null;
	}

	const componentToHex = (c) => {
		var hex = Math.round(c).toString(16);
		return hex.length == 1 ? '0' + hex : hex;
	};

	const rgbToHex = ({ r, g, b }) => {
		return '#' + componentToHex(r) + componentToHex(g) + componentToHex(b);
	};

	const componentChange = (start, end) => {
		const change = end - start;
		if (change) {
			return (t) => {
				return start + Math.round(t * change);
			};
		} else {
			return (t) => {
				return isNaN(start) ? end : start;
			};
		}
	};

	const interpolateColor = (start, end) => {
		const startRgb = hexToRgb(start);
		const endRgb = hexToRgb(end);
		var r = componentChange(startRgb.r, endRgb.r),
			g = componentChange(startRgb.g, endRgb.g),
			b = componentChange(startRgb.b, endRgb.b);

		return function (t) {
			const d = { r: r(t), g: g(t), b: b(t) };
			// return `rgb(${d.r}, ${d.g}, ${d.b})`;
			return rgbToHex(d);
		};
	};

	const colorOne = tweened('#ff0000', {
		duration: settings.speed,
		interpolate: interpolateColor
		//interpolate: d3-interpolateLab
	});

	const colorTwo = tweened('#00ff00', {
		duration: settings.speed,
		interpolate: interpolateColor
		//interpolate: d3-interpolateLab
	});

	const colorThree = tweened('#0000ff', {
		duration: settings.speed,
		interpolate: interpolateColor
		//interpolate: d3-interpolateLab
	});

	const randomizeColor = (tweenedColor) => {
		tweenedColor
			.set(
				rgbToHex({
					r: Math.floor(Math.random() * 256),
					g: Math.floor(Math.random() * 256),
					b: Math.floor(Math.random() * 256)
				})
			)
			.then(() => {
				if (playing) randomizeColor(tweenedColor);
			});
	};
</script>

<main
	on:click={() => {
		togglePlaying();
	}}
>
	{#if !playing}
		<span class="pausedNotice">Paused (click to continue)</span>
	{/if}
	<FadingH1>Colors - tweened</FadingH1>
	<div
		class="swatch one"
		style="background: radial-gradient(farthest-corner at 0 0, {$colorOne} 10%, transparent 80%);"
	/>

	<div
		class="swatch two"
		style="background: radial-gradient(farthest-corner at 100% 0, {$colorTwo} 10%, transparent 80%);"
	/>

	<div
		class="swatch three"
		style="background: radial-gradient(farthest-corner at 50% 100%, {$colorThree} 10%, transparent 60%);"
	/>
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
	.swatch {
		position: absolute;
		z-index: -1;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
	}
</style>
