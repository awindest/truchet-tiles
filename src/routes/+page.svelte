<script lang="ts">
	import TruchetTile from '$lib/TruchetTile.svelte'

	const elementScale = 64
	let nrows = $state(0) as number

	let elementsPerRow = $state(0) as number
	let rotated = $state(true)

	let intervals: Array<number> = []

	function updateDimensions() {
		elementsPerRow = Math.ceil(window.innerWidth / elementScale)
		nrows = Math.ceil(window.innerHeight / elementScale)
		console.log(window.innerWidth, elementsPerRow, window.innerHeight, nrows)
	}

	function getRanInt(min: number, max: number) {
		return Math.floor(Math.random() * (max - min + 1)) + min
	}

	function rotateElement(element: HTMLElement, random = true) {
		const direction = random ? (Math.random() < 0.5 ? 1 : -1) : 1
		const currentRotation = parseInt(element.getAttribute('data-rotation') || '0')
		const newRotation = currentRotation + 90 * direction
		element.style.transform = `rotate(${newRotation}deg)`
		element.setAttribute('data-rotation', String(newRotation))
	}

	function startIntervals() {
		document.querySelectorAll('.circle').forEach((circle) => {
			const intervalId = setInterval(() => rotateElement(circle), getRanInt(1000, 42000))
			intervals.push(intervalId)
		})
	}

	function clearIntervals() {
		intervals.forEach(clearInterval)
		intervals = []
	}

	$effect(() => {
		updateDimensions()
		startIntervals()
	})

	function randomRotate() {
		let alt = 0
		document.body.addEventListener('click', () => {
			if (alt == 0) {
				document.querySelectorAll('.circle').forEach((cir, index) => {
					const newRotation = index % 4 === 0 ? 90 : 0
					cir.style.transform = `rotate(${newRotation}deg)`
					cir.setAttribute('data-rotation', newRotation)
				})
				alt++
			} else if (alt == 1) {
				document.querySelectorAll('.circle').forEach((cir, index) => {
					const newRotation = index % 2 === 0 ? 90 : 0
					cir.style.transform = `rotate(${newRotation}deg)`
					cir.setAttribute('data-rotation', String(newRotation))
				})
				alt++
			} else if (alt == 2) {
				document.querySelectorAll('.circle').forEach((cir, index) => {
					const newRotation = index % 3 === 0 ? 90 : 0
					cir.style.transform = `rotate(${newRotation}deg)`
					cir.setAttribute('data-rotation', String(newRotation))
				})
				alt++
			} else {
				document.querySelectorAll('.circle').forEach((cir) => {
					cir.style.transform = 'rotate(0deg)'
					cir.setAttribute('data-rotation', 0)
				})
				alt = 0
			}
		})

		// document.body.addEventListener('mouseenter', clearIntervals)
		// document.body.addEventListener('mouseleave', startIntervals)

		// window.addEventListener('resize', () => {
		// 	clearIntervals()
		// 	createGrid()
		// 	startIntervals()
		// })
	}
	// for div: onmouseover={() => {
	// 	rotateElement(this)
	// }}
	function rotateMe(event: MouseEvent) {
		rotated = !rotated
		console.log('got pointer event', rotated)
	}
	function init() {
		clearIntervals()
		updateDimensions()
		startIntervals()
	}
	function onresize() {
		location.reload()
		init()
	}
</script>

<svelte:window {onresize} />
<svelte:body onmouseenter={init} />
<!-- <svelte:body onmouseenter={() => clearIntervals} onmouseleave={() => startIntervals} /> -->

<div class="circleContainer">
	{#each { length: nrows } as _, r}
		<div class="rows">
			{#each { length: elementsPerRow } as _, c}
				<!-- svelte-ignore a11y_click_events_have_key_events, a11y_no_static_element_interactions (because no need here) -->
				<div
					class="circle"
					style={`transform: rotate(${(c % 2 == 0 && r % 2 == 0) || (c % 2 == 1 && r % 2 == 1) ? '90deg' : '0deg'})`}
					class:{rotated}
					onclick={rotateMe}
				>
					<TruchetTile />
				</div>
			{/each}
			<!-- </div> -->
		</div>
	{/each}
</div>

<style>
	div {
		cursor: pointer;
	}
	.rotated {
		transform: rotate(180);
	}
</style>
