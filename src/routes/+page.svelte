<script lang="ts">
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

	async function forward() {
		n = (n + 1) % volumeList.length
	}

	async function backward() {
		n = (n - 1) % volumeList.length
		if (n < 0) n = volumeList.length - 1
	}

	// onMount(async () => {
	// 	const originalFetch = window.fetch

	// 	window.fetch = (url, options = {}) => {
	// 		options.cache = options.cache || 'force-cache' // Usa la cache se disponibile
	// 		return originalFetch(url, options)
	// 	}

	// })

	import Niivue from '$lib/components/Niivue.svelte'
</script>

<main>
	<Niivue canvasID="gl" src={volumeList[n][0].url}></Niivue>
	<button on:click={backward}> &larr; </button>
	<button>{n}</button>
	<button on:click={forward}> &rarr; </button>
</main>
