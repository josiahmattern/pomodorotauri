<script>
	import { writable } from 'svelte/store';
	import { onDestroy, onMount } from 'svelte';
	import Settings from '../components/Settings.svelte';

	// Create writable stores for settings
	let pomoMin = writable(localStorage.getItem('pomoMin') || '25');
	let shortMin = writable(localStorage.getItem('shortMin') || '5');
	let longMin = writable(localStorage.getItem('longMin') || '15');

	// Timer duration calculations are reactive
	$: pomoTime = parseInt($pomoMin) * 60;
	$: shortTime = parseInt($shortMin) * 60;
	$: longTime = parseInt($longMin) * 60;
	$: time = pomoTime; // Default timer

	let shortBreakCount = 0;

	/**
	 * @type {number | undefined}
	 */
	let interval;
	let playing = false;

	$: label = 'pomodoro';
	$: currentValue = Math.ceil((time / getTimeBlock()) * 100);

	// Subscribe to store changes and update localStorage
	pomoMin.subscribe((value) => localStorage.setItem('pomoMin', value));
	shortMin.subscribe((value) => localStorage.setItem('shortMin', value));
	longMin.subscribe((value) => localStorage.setItem('longMin', value));

	let bellSound = new Audio('/bell.wav');

	function toggleTimer() {
		playing = !playing;
		if (playing) {
			interval = setInterval(() => {
				if (time > 0) {
					time -= 1;
				} else {
					bellSound.play();
					playing = false;
					changeLabel();
					resetTimer();
				}
			}, 1000);
		} else {
			clearInterval(interval);
		}
	}

	function skipTimer() {
		bellSound.play();
		changeLabel();
		resetTimer();
	}

	function getTimeBlock() {
		if (label == 'pomodoro') {
			return pomoTime;
		} else if (label == 'short break') {
			return shortTime;
		} else {
			return longTime;
		}
	}

	function resetTimer() {
		clearInterval(interval);
		if (label == 'pomodoro') {
			time = pomoTime;
		} else if (label == 'short break') {
			time = shortTime;
		} else {
			time = longTime;
		}
	}

	function changeLabel() {
		label = label == 'pomodoro' ? (shortBreakCount < 3 ? 'short break' : 'long break') : 'pomodoro';
		shortBreakCount = label == 'long break' ? 0 : shortBreakCount + 1;
	}

	onDestroy(() => {
		clearInterval(interval);
	});
</script>

<main class="h-screen min-w-2xl overflow-hidden transition-all duration-300">
	<Settings {pomoMin} {shortMin} {longMin} />
	<section class="h-fit w-screen flex flex-col items-center justify-center">
		<h1 class="pb-10 font-bold text-xl">
			{#if label == 'short break'}
				{label + ' #' + shortBreakCount}
			{:else}
				{label}
			{/if}
		</h1>
		<div
			class="radial-progress text-primary mb-8"
			style="--value:{currentValue}; --size:12rem;"
			role="progressbar"
		>
			<div></div>
			{Math.floor(time / 60)}:{('0' + (time % 60)).slice(-2)}
		</div>
		<div class="flex items-center">
			{#if playing}
				<button on:click={toggleTimer}>
					<i class="ph ph-pause text-2xl text-content ml-10"></i>
				</button>
			{:else}
				<button on:click={toggleTimer}>
					<i class="ph ph-play text-2xl text-content ml-10"></i>
				</button>
			{/if}
			<button on:click={skipTimer} class="ml-4">
				<i class="ph ph-skip-forward text-2xl text-content"></i>
			</button>
		</div>
	</section>
</main>
