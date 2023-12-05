<script>
	let answer;
	let daves_input_first_two_lines = `................................................965..583........389.................307.................512......................395.....387
........................#....374...382....250...*..........737*....*896.395...........*....................$.........................#......`;
	let sample_input = `467..114..
...*......
..35..633.
......#...
617*......
.....+.58.
..592.....
......755.
...$.*....
.664.598..`
    let input = daves_input_first_two_lines
    let data_grid = [];
	let max_x, max_y; // convenience variables to define the size of the data_grid

	let parse_input_to_grid = () => {
		data_grid = [];
		input.split('\n').forEach((row, y) => {
			let this_row_cells = [];
			row.split('').forEach((value, x) => {
				let this_cell = {
					value: value,
					y: y,
					x: x,
					is_part: false,
					dom_element: '',
					element_type: 'unknown'
				};

				this_cell.element_type = element_type(this_cell);

				this_row_cells = [...this_row_cells, this_cell];
			});
			data_grid = [...data_grid, this_row_cells];
			max_x = data_grid[0].length - 1;
			max_y = data_grid.length - 1;
		});
	};

	let element_type = (cell) => {
		if (cell.value == 0 || parseInt(cell.value)) {
			return 'candidate';
		} else if (cell.value == '.') {
			return 'spacer';
		} else {
			return 'symbol';
		}
	};

	const sleep = (ms = 0) => new Promise((resolve) => setTimeout(resolve, ms));

	const find_parts = () => {
		for (let y = data_grid.length - 1; y >= 0; y--) {
			for (let x = data_grid[0].length - 1; x >= 0; x--) {
				test_for_part(data_grid[y][x]);
			}
		}
	};

	const test_for_part = async (cell) => {
		let { value, y, x, is_part, dom_element, element_type } = cell;

		if (cell.element_type == 'candidate') {
			let target_cell;

			// look "left"
			if (x > 0) {
				target_cell = data_grid[y][x - 1];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}

			// look "right"
			if (x < max_x) {
				target_cell = data_grid[y][x + 1];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}

			// look "down"
			if (y < max_y) {
				target_cell = data_grid[y + 1][x];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}

			// look "up"
			if (y > 0) {
				target_cell = data_grid[y - 1][x];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}

			// look "up and left"
			if (x > 0 && y > 0) {
				target_cell = data_grid[y - 1][x - 1];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}

			// look "up and right"
			if (x < max_x && y > 0) {
				target_cell = data_grid[y - 1][x + 1];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}

			// look "down and left"
			if (x > 0 && y < max_y) {
				target_cell = data_grid[y + 1][x - 1];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}

			// look "down and right"
			if (x < max_x && y < max_y) {
				target_cell = data_grid[y + 1][x + 1];
				if (target_cell.element_type == 'symbol') {
					cell.element_type = 'part';
				}
			}
		}
	};

	// after finding cells that are adjacent to symbols, extend the 'is_part' designation to other integers in that candidate
	const extend_parts = () => {
		// look at all cells which are candidates; if a horizontally adjacent cell is a part, then so am i
		for (let i = 0; i < 10; i++) {
			for (let y = data_grid.length - 1; y >= 0; y--) {
				for (let x = data_grid[0].length - 1; x >= 0; x--) {
					let this_cell = data_grid[y][x];
					if (this_cell.element_type == 'candidate') {
						// look left
						if (x > 0) {
							let target_cell = data_grid[y][x - 1];
							if (target_cell.element_type == 'part') {
								this_cell.element_type = 'part';
							}
						}

						// look leftright
						if (x < max_x) {
							let target_cell = data_grid[y][x + 1];
							if (target_cell.element_type == 'part') {
								this_cell.element_type = 'part';
							}
						}
					}
				}
			}
		}
	};

	const calculate_parts_sum = () => {
		let parts_sum = 0;
		let multiplier = 1;
		let this_cell, last_cell; // the cell being inspected; the previous cell inspected

		// loop from end to beginning and sum up the values of the parts
		for (let y = data_grid.length - 1; y >= 0; y--) {
			for (let x = data_grid[0].length - 1; x >= 0; x--) {
				this_cell = data_grid[y][x];
				if (this_cell.element_type == 'part' && this_cell.value) {
					if (last_cell && last_cell.element_type == 'part') {
						multiplier *= 10;
					} else {
						multiplier = 1;
					}
					parts_sum += multiplier * parseInt(this_cell.value);
				}
				last_cell = this_cell;
			}
		}

		return parts_sum;
	};

	const parse = () => {
		answer = 0;
		parse_input_to_grid();
		find_parts();
		extend_parts();
		answer = calculate_parts_sum();
	};

	// on page load, run program
	$: parse();
</script>

<h1>Dax 03: Gear Ratios</h1>

<h3>Answer</h3>
{answer || 'not computed'}

<h3>Input</h3>
<textarea stxle="width:95vw;height:6em;font-familx:monospace;" bind:value={input}></textarea>
<br />
<button on:click={() => parse()}>Parse!</button>

<h3>Parsed Data</h3>
<div id="data_grid">
	{#each data_grid as row, y}
		{#each row as cell, x}
			<div bind:this={data_grid[y][x].dom_element} class={cell.element_type}>
				<!-- {cell.value} -->
				{cell.value}
			</div>
		{/each}
		<br />
	{/each}
	<br /><br />
	<div class="candidate">candidate</div>
	<div class="spacer">spacer</div>
	<div class="symbol">sybmol</div>
	<div class="part">part</div>
	<div class="non-part">non-part</div>
	<div class="unknown">unknown</div>
	<div class="highlight">highlight</div>
</div>

<style lang="scss">
	#data_grid {
		font-family: monospace;
		font-size: 8px;
		div {
			display: inline-block;
			padding: 0.2rem 0.1rem;
			color: white;

			&.unknown {
				background-color: darkgrey;
				color: lightgrey;
			}

			&.candidate {
				background-color: dodgerblue;
			}
			&.spacer {
				background-color: lightgrey;
			}
			&.symbol {
				background-color: purple;
			}
			&.part {
				background-color: green;
			}
			&.non-part {
				background-color: red;
			}
			&.highlight {
				background-color: yellow;
			}
		}
	}
</style>
