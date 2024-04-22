<script>
	import { writable } from 'svelte/store';

	// Create writable stores and initialize with localStorage or default values
	export let pomoMin = writable(localStorage.getItem('pomoMin') || '25');
	export let shortMin = writable(localStorage.getItem('shortMin') || '5');
	export let longMin = writable(localStorage.getItem('longMin') || '15');

	// Function to show settings dialog
	function showSettings() {
		let modal = document.getElementById('my_modal_2');
		// @ts-ignore
		modal.showModal();
	}

	// Function to save settings to localStorage
	function save() {
		localStorage.setItem('pomoMin', $pomoMin);
		localStorage.setItem('shortMin', $shortMin);
		localStorage.setItem('longMin', $longMin);

		let modal = document.getElementById('my_modal_2');
		// @ts-ignore
		modal.close();
	}
</script>

<nav class="w-screen flex justify-end pr-5 py-4 pb-2">
	<button class="ph ph-gear text-2xl text-content" on:click={showSettings}></button>
</nav>

<dialog id="my_modal_2" class="modal">
	<div class="modal-box">
		<h3 class="font-bold text-lg pb-2">Settings</h3>
		<div class="flex justify-between items-center my-1">
			<p class="text-lg">pomodoro minutes:</p>
			<input
				id="pomoMin"
				type="text"
				bind:value={$pomoMin}
				min="1"
				max="60"
				placeholder="ex: 25"
				class="input w-32 max-w-xs bg-base-100"
			/>
		</div>
		<div class="flex justify-between items-center my-1">
			<p class="text-lg">short break minutes:</p>
			<input
				id="shortMin"
				type="text"
				bind:value={$shortMin}
				placeholder="ex: 5"
				class="input w-32 max-w-xs bg-base-100"
			/>
		</div>

		<div class="flex justify-between items-center my-1">
			<p class="text-lg">long break minutes:</p>
			<input
				id="longMin"
				type="text"
				bind:value={$longMin}
				placeholder="ex: 15"
				class="input w-32 max-w-xs bg-base-100"
			/>
		</div>
		<div class="flex w-full justify-end mt-4">
			<button on:click={save} class="btn btn-primary px-6">Save</button>
		</div>
	</div>
</dialog>
