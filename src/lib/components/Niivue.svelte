<script lang="ts">
	// https://niivue.github.io/niivue/devdocs/ (library documentation)

	import { Niivue, NVImage } from '@niivue/niivue'
	import { onMount } from 'svelte'
	import { images } from './ImageCache'

	export let src: string
	export let overlays: string[] = []
	export let overlayColors: Record<string, RgbaColor | string> = {}	
	
	$: {
		console.log('SRC', src, 'OVERLAYS', overlays)
	}

	import type { RgbaColor } from 'svelte-awesome-color-picker'
	const colormapColors: Record<string, RgbaColor | string> = {}

	function delay(ms: number) {
		return new Promise((resolve) => setTimeout(resolve, ms))
	}

	// const cmap = {
	// 	R: [3, 64, 0, 0, 255, 255, 255], // THE NUMBER OF ELEMENTS OF THE ARRAY ARE THE "SEGMENTS" OF THE COLORMAP!!
	// 	G: [0, 0, 0, 255, 255, 192, 3],
	// 	B: [0, 32, 48, 56, 64, 96, 128],
	// 	A: [0, 8, 16, 24, 32, 52, 80], // NO IDEA WHAT A AND I DO; see 	// https://github.com/niivue/niivue/issues/83 (colormaps)
	// 	I: [0, 32, 64, 96, 160, 192, 255]
	// }

	function mkCmap(rgb: RgbaColor) {
		const result = {
			R: [0, 1, rgb.r],
			G: [0, 1, rgb.g],
			B: [0, 1, rgb.b],
			// A: [0,1, Math.floor(255*rgb.a)],
			A: [0, 255, 255],
			I: [0, 1, 255]
		}

		return result
	}

	function prefetch(overlay: string) {
		if (!images[overlay]) {
			images[overlay] = (async () => {
				// await delay(10) TODO: maybe this is still needed
				return NVImage.loadFromUrl({
					url: overlay,
					colormap: overlay
				})
			})()
		}
	}

	let nv: Niivue
	let canvas: HTMLCanvasElement

	onMount(async () => {
		nv = new Niivue({ isResizeCanvas: true })
		nv.attachToCanvas(canvas)
		nv.setSliceType(nv.sliceTypeMultiplanar)
	})

	async function updateOverlayColors(
		nv: Niivue,
		overlayColors: Record<string, RgbaColor | string>
	) {
		if (nv) {
			for (const [index, overlay] of overlays.entries()) {
				if (overlayColors[overlay]) {
					const color = overlayColors[overlay]

					if (colormapColors[overlay] != color) {
						colormapColors[overlay] = color

						if (typeof color === 'string') {
							console.log(
								nv.volumes,
								nv.volumes.map((x) => x.id)
							)
							nv.setColorMap(nv.volumes[index + 1].id, color)
							nv.setOpacity(index+1, 0.5)
						} else {
							nv.addColormap(overlay, mkCmap({ ...color }))
							// nv.setColorMap(overlay, overlay) // TODO: The colourmap has the same name as the overlay. Period. Could also be called like the rgb name and not recomputed each time? Does this work?
							nv.setOpacity(index + 1, color.a)
						}
						nv.updateGLVolume()
					}
				}
			}
		}
	}

	async function setOverlay(overlay: string, i: number) {
		prefetch(overlay)
		// await delay(0) // TODO: maybe this is still needed

		let img = await images[overlay]

		if (img && ((0 == i && src == overlay) || overlays.includes(overlay))) {
			// FIX for when the image takes a long time to load and the user has deselected the overlay in the meantime
			if (nv.volumes[i] && nv.volumes[i].url != overlay) {
				await delay(0)
				nv.setVolume(nv.volumes[i], -1)
			}
			if (!nv.volumes[i] || nv.volumes[i].url != overlay) {
				const id = nv.getVolumeIndexByID(img.id)
				if (id < 0) {
					nv.addVolume(img)
				}
				await delay(0)
				if (nv.volumes[i] != img) nv.setVolume(img, i)
			}
		}
	}

	async function updateOverlays() {
		const overlayVolumes = nv.volumes.slice(1, nv.volumes.length)

		let close: any[] = []
		overlayVolumes.forEach((vol: any, i: number) => {
			if (!overlays.includes(vol.url)) close.push({ cl: vol.url, img: vol })
		})

		for (const { cl, img } of close) {
			const id = nv.getVolumeIndexByID(img.id)
			if (id >= 0 && !overlays.includes(cl))
				// FIX for when the image takes a long time to load and the user has re-selected the overlay in the meantime
				nv.setVolume(img, -1)
		}

		let i = 1
		for (const ovl of overlays) {
			await setOverlay(ovl, i++)
		}
	}

	async function setBaseOverlay(src: string) {
		nv.addColormap(src, mkCmap({ r: 255, g: 255, b: 255, a: 1 }))
		await setOverlay(src, 0)
	}

	$: {
		;(async () => {
			if (src && NVImage && nv) {
				await setBaseOverlay(src)
			}

			if (nv && overlays !== undefined) {
				try {
					await updateOverlays()
					updateOverlayColors(nv, overlayColors)
				} catch (e) {
					console.warn('ERROR in update overlays', e)
				}
			}
		})()
	}
</script>

<div class="size-full">
	<canvas bind:this={canvas} />
</div>
