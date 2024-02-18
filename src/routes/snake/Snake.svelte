<script lang="ts">
	interface Props {
		width: number;
	}

	const { width } = $props<Props>();

	interface Cell {
		on: boolean;
	}

	interface SnakeSegment {
		x: number;
		y: number;
		next?: SnakeSegment;
	}

	let direction = $state({
		x: 0,
		y: 1
	});

	let head: SnakeSegment = $state({
		x: 0,
		y: 0
	});

	let grid: Cell[][] = $derived.by(() => {
		let newGrid: Cell[][] = Array(width);
		for (let i = 0; i < newGrid.length; i++) {
			newGrid[i] = Array(width);
			for (let j = 0; j < newGrid[i].length; j++) {
				newGrid[i][j] = { on: false };
			}
		}
		// let newGrid = Array<Cell[]>(width).fill(Array<Cell>(width).fill({ on: false }));
		// paint the snake
		let current: SnakeSegment | undefined = head;
		do {
			newGrid[current.y][current.x].on = true;
			current = current.next;
		} while (current);
		return newGrid;
	});

	$effect(() => {
		console.log('$effect');
		window.addEventListener(
			'keyup',
			(e) => {
				console.log('pressed ' + e.key);

				switch (e.key) {
					case 'ArrowLeft':
						direction = { x: -1, y: 0 };
						break;
					case 'ArrowRight':
						direction = { x: 1, y: 0 };
						break;
					case 'ArrowUp':
						direction = { x: 0, y: -1 };
						break;
					case 'ArrowDown':
						direction = { x: 0, y: 1 };
						break;
					default:
						break;
				}
			},
			true
		);
	});

	function nextFrame() {
		const newHead: SnakeSegment = {
			x: (head.x + direction.x + width) % width,
			y: (head.y + direction.y + width) % width,
			next: head
		};
		head = newHead;

		let current = head;
		while (current?.next?.next) {
			current = current.next;
		}
		current.next = undefined;
		console.log('nextFrame');
	}
	// clearInterval(clear)
	$effect(() => {
		console.log('$effect');
		let nextFrameInterval = setInterval(nextFrame, 500);
		return () => clearTimeout(nextFrameInterval);
	});
	// $inspect(grid);
</script>

<h1>Snake</h1>

{#each grid as row}
	<div class="row">
		{#each row as cell}
			<div class="cell" style="--bg-color: {cell.on ? '#000' : '#FFF'}" />
		{/each}
	</div>
{/each}

<style>
	.row {
		display: flex;
	}
	.cell {
		display: flex;
		min-width: 1rem;
		min-height: 1rem;
		background-color: var(--bg-color);
		border: 1px solid #f0f0f0;
	}
</style>
