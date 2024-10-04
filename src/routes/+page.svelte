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
				colormap: 'random',
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
				colormap: 'random',
				visible: true,
				opacity: 1
			}
		]
	]

	let n = 0

	$: src = volumeList[n][0].url
	$: overlays = volumeList[n].slice(1).map((x) => x.url)
	$: overlayColors = Object.fromEntries(
		volumeList[n].slice(1).map((x) => [x.url, x.colormap])

	) as Record<string, string>

	async function forward() {
		n = (n + 1) % volumeList.length
	}

	async function backward() {
		n = (n - 1) % volumeList.length
		if (n < 0) n = volumeList.length - 1
	}

	import Niivue from '$lib/components/Niivue.svelte'
</script>

<main class="h-screen w-screen">
	<div class="flex h-full flex-col p-8">
		<div class="flex h-full w-full">
			<Niivue {src} {overlays} {overlayColors}></Niivue>
		</div>
		<div class="flex">
			<button type="button" class="variant-filled btn rounded-none" on:click={backward}>
				&larr;
			</button>
			<button type="button" class="variant-filled btn rounded-none">{n}</button>
			<button type="button" class="variant-filled btn rounded-none" on:click={forward}>
				&rarr;
			</button>
		</div>
	</div>
</main>
