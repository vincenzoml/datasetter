<script lang="ts">
	import { Niivue } from '@niivue/niivue'
	import { onMount } from 'svelte'

	const volumeList = [
		[
			{
				url: 'test_images/sub-stroke0001_ses-01_ncct-s1.nii.gz',
				colormap: 'gray',
				visible: true,
				opacity: 1
			},
			{
				url: 'test_images/sub-stroke0001_ses-01_ncct-atlaswarp.nii.gz',
				colormap: 'red',
				visible: true,
				opacity: 1
			}
		],
		[
			{
				url: 'test_images/sub-stroke0002_ses-01_ncct-s1.nii.gz',
				colormap: 'gray',
				visible: true,
				opacity: 1
			},
			{
				url: 'test_images/sub-stroke0002_ses-01_ncct-atlaswarp.nii.gz',
				colormap: 'red',
				visible: true,
				opacity: 1
			}
		]
	]

	let n = 0

	let nv: Niivue

	async function updateVolumes() {
		await nv.loadVolumes(volumeList[n])
	}

	async function forward() {
		n = (n + 1) % volumeList.length
		await updateVolumes()
	}

	async function backward() {
		n = (n - 1) % volumeList.length
		if (n < 0) n = volumeList.length - 1
		await updateVolumes()
	}

	onMount(async () => {
		const originalFetch = window.fetch
		
		window.fetch = (url, options = {}) => {
			options.cache = options.cache || 'force-cache' // Usa la cache se disponibile
			return originalFetch(url, options)
		}

		nv = new Niivue()
		await nv.attachTo('gl')

		updateVolumes()
	})
</script>

<main>
	<canvas id="gl" height="400px"></canvas>
	<button on:click={backward}> &larr; </button>
	<button on:click={updateVolumes}>{n}</button>
	<button on:click={forward}> &rarr; </button>
</main>
