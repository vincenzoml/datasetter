<script lang="ts">
	import { Niivue } from '@niivue/niivue'
	import { onMount } from 'svelte'

	const volumeList = [
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
	]

	onMount(async () => {
		const nv = new Niivue()
		
		await nv.attachTo('gl')
		await nv.loadVolumes(volumeList)
	})
</script>

<main id="container">
	<canvas id="gl" height="400px"></canvas>
</main>
