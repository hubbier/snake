<script lang="ts">
	interface Props {
		width: number;
	}

	const { width } = $props<Props>();

	interface Cell {
		hasSnakeSegment: boolean;
		hasFood?: boolean;
	}

	interface Coordinate {
		x: number;
		y: number;
	}

	interface SnakeSegment extends Coordinate {
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

	let snake = $state(new Map<string, boolean>());

	let length = $state(1);

	function generateFood() {
		let j = Math.floor(Math.random() * width * width);
		let i = 0;
		let x = 0;
		let y = 0;
		while (i < j) {
			if (!snake.get(`${y}_${x}`)) {
				i++;
			}
			if (x + 1 === width) {
				y += 1;
			}
			x = (x + 1) % width;
		}
		console.log(`placing food at row ${y} column ${x}`);

		return { x, y };
	}

	let food: Coordinate = $state(generateFood());

	let grid: Cell[][] = $derived.by(() => {
		let newGrid: Cell[][] = Array(width);
		for (let i = 0; i < newGrid.length; i++) {
			newGrid[i] = Array(width);
			for (let j = 0; j < newGrid[i].length; j++) {
				newGrid[i][j] = { hasSnakeSegment: false };
			}
		}
		// let newGrid = Array<Cell[]>(width).fill(Array<Cell>(width).fill({ on: false }));
		// paint the snake
		let current: SnakeSegment | undefined = head;
		while (current) {
			if (!newGrid[current.y]) {
				let pause = 'pause';
			}
			newGrid[current.y][current.x].hasSnakeSegment = true;
			current = current.next;
		}
		// do {
		// 	if (!newGrid[current.y]) {
		// 		let pause = 'pause';
		// 	}
		// 	newGrid[current.y][current.x].hasSnakeSegment = true;
		// 	current = current.next;
		// } while (current);

		newGrid[food.y][food.x].hasFood = true;

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
		snake.set(`${head.y}_${head.x}`, true);

		if (head.x == food.x && head.y == food.y) {
			food = generateFood();
			length++;
		} else {
			let current = head;
			while (current?.next?.next) {
				current = current.next;
			}
			snake.set(`${current?.next?.y}_${current?.next?.x}`, false);
			current.next = undefined;
		}

		console.log('nextFrame');
	}

	$effect(() => {
		const nextFrameInterval = setInterval(nextFrame, 200);
		return () => clearInterval(nextFrameInterval);
	});

	function getCellBackgroundColor(cell: Cell) {
		if (cell.hasFood) return '#0F0';
		if (cell.hasSnakeSegment) return '#000';
		return '#FFF';
	}
</script>

<h1>Snake</h1>

{#each grid as row}
	<div class="row">
		{#each row as cell}
			<div class="cell" style="--bg-color: {getCellBackgroundColor(cell)}" />
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
