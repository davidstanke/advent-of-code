<script>
	import { parse } from 'svelte/compiler';

	let answer;
	let sample_input = `O....#....
O.OO#....#
.....##...
OO.#O....O
.O.....O#.
O.#..O.#.#
..O..#O..O
.......O..
#....###..
#OO..#....`;
	let input = sample_input;
	let data_grid = [];
	let row_loads = [];
	let max_x, max_y; // convenience variables to define the size of the data_grid
	const parseInputToGrid = () => {
		data_grid = [];
		let cellObject = {};
		input.split('\n').forEach((row, y) => {
			data_grid = [...data_grid, []];
			row.trim().split('').forEach((cell, x) => {
				cellObject = {};
				cellObject.y = y;
				cellObject.x = x;
				switch (cell) {
					case 'O':
						cellObject.type = 'round';
						break;
					case '#':
						cellObject.type = 'cubic';
						break;
					case '.':
						cellObject.type = 'empty';
						break;
					default:
						cellObject.type = 'error';
				}

				data_grid[y] = [...data_grid[y], cellObject];
			});
		});

		max_y = data_grid.length - 1;
		max_x = data_grid[0].length - 1;
	};

	// attempt a tiltNorth action; returns true if something changed
	const tiltNorth = () => {
		let something_changed = false;
		let data_grid_before = JSON.stringify(data_grid);
		data_grid.forEach((row, y) => {
			row.forEach((cell, x) => {
				tiltCellNorth(y, x);
			});
		});

		something_changed = JSON.stringify(data_grid) != data_grid_before;
		// console.log(something_changed);
		return something_changed;
	};

	const tiltCellNorth = (y, x) => {
		let this_cell = data_grid[y][x];
		if (this_cell.type == 'round' && y > 0) {
			let cell_above = data_grid[y - 1][x];
			if (cell_above.type == 'empty') {
				cell_above.type = 'round';
				this_cell.type = 'empty';
			}
		}
	};

	const computeRowLoads = () => {
		data_grid.forEach((row, y) => {
			let load_per_rock = max_y - y + 1;

			let rocks_in_row = data_grid[y].filter((cell) => {
				return cell.type == 'round';
			});

			row_loads[y] = load_per_rock * rocks_in_row.length;
		});
	};

	const solve = () => {
		parseInputToGrid();

		// tilt north until everything has gone as far as it can
		while (tiltNorth());
		computeRowLoads();
	};

	$: solve();
</script>

<h1>Day 14: Parabolic Reflector Dish</h1>

<h3>Input</h3>
<textarea style="width:20em;height:8em;font-family:monospace;" bind:value={input}></textarea>
<br />
<button on:click={() => solve()}>Solve!</button>

<h3>Total weight: {row_loads.reduce((a, b) => a + b)}</h3>

<h3>Parsed Data</h3>
<div id="data_grid">
	{#each data_grid as row, y}
		{#each row as cell, x}
			<div bind:this={data_grid[y][x].dom_element} class={cell.type}>
				•
				<!-- {cell.y},{cell.x} -->
			</div>
			{#if x == max_x}
				<div class="row_load">{row_loads[y]}</div>
			{/if}
		{/each}
		<br />
	{/each}
	<br /><br />
	<div class="round">round</div>
	<div class="cubic">cubic</div>
	<div class="empty">empty</div>
	<div class="highlight">highlight</div>
	<div class="error">ERROR</div>
</div>

<style lang="scss">
	#data_grid {
		div {
			display: inline-block;
			font-family: monospace;
			margin: 0.1rem 0.2rem;
			padding: 0.2rem;
			min-height: 1rem;
			min-width: 1rem;
			text-align: center;
		}

		div.round {
			background-color: dodgerblue;
			color: white;
			border-radius: 50%;
		}

		div.cubic {
			background-color: chocolate;
			color: white;
		}

		div.empty {
			color: grey;
		}

		div.highlight {
			background-color: yellow;
		}

		div.error {
			color: red;
		}

		div.row_load {
			background-color: lightgray;
			color: darkgray;
			border-radius: 0.25rem;
		}
	}
</style>
