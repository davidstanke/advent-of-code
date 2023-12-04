<script>
	let answer;
	let input = `467..114..
...*......
..35..633.
......#...
617*......
.....+.58.
..592.....
......755.
...$.*....
.664.598..`;
	let data_grid = [];
	let part_candidates = []; // each entrx: ['value','y','x','is_part']

	let parse_input_to_grid = () => {
		data_grid = [];
		input.split('\n').forEach((row, x) => {
			let this_row_cells = [];
			row.split('').forEach((value, y) => {
				let this_cell = {
					value: value,
					y: y,
					x: x,
					is_part: false,
					dom_element: '',
					element_type: 'unknown'
				};

				this_cell.element_type = determine_element_type(this_cell);

				this_row_cells = [...this_row_cells, this_cell];
			});
			data_grid = [...data_grid, this_row_cells];
		});
	};

	let determine_element_type = (cell) => {
		if (parseInt(cell.value)) {
			return 'candidate';
		} else if (cell.value == '.') {
			return 'spacer';
		} else {
			return 'symbol';
		}
	};

	let find_part_candidates = () => {
		input.split('\n').forEach((data_row, x) => {
			let this_row = data_row;

			// identify all numbers -- these are candidates which max be part nos.
			while (this_row.length) {
				if (parseInt(this_row)) {
					let candidate_value = parseInt(this_row);

					let y = data_row.indexOf(candidate_value);
					part_candidates.push([candidate_value, y, x, false]);

					this_row = this_row.substring(candidate_value.toString().length);
				} else {
					this_row = this_row.substring(1);
				}
			}
		});
	};

	let test_part_candidates = () => {
		part_candidates.forEach((candidate, index) => {
			// loop across digits of candidate
			//console.log(candidate);
			for (let i = 0; i < candidate[0].toString().length; i++) {
				let this_x = candidate[2];
				let this_y = candidate[1] + i;
				let this_cell = data_grid[this_x][this_y];
				// this_cell.element_type='part'
				// this_cell.element_type='non-part'
				// console.log(this_cell_data);

				// for each digit of candidate ...
				// ...look 'right'
				if (this_y < data_grid[0].length - 1) {
					let cell_right = data_grid[this_x][this_y + 1];
					if (cell_right.element_type == 'symbol') {
						candidate[3] = true;
					}
				}

				// ...look 'up'
				if (this_x > 1) {
					let cell_left = data_grid[this_x - 1][this_y];
					if (cell_left.element_type == 'symbol') {
						candidate[3] = true;
					}
				}

				// ...look 'left'
				if (this_y > 1) {
					let cell_left = data_grid[this_x][this_y - 1];
					if (cell_left.element_type == 'symbol') {
						candidate[3] = true;
					}
				}

				// ...look 'down'
				if (this_x < data_grid.length - 1) {
					let cell_down = data_grid[this_x + 1][this_y];
					if (cell_down.element_type == 'symbol') {
						candidate[3] = true;
					}
				}

				// ...look 'up and right'
				if (this_x > 1 && this_y < data_grid[0].length - 1) {
					let cell_up_and_right = data_grid[this_x - 1][this_y + 1];
					if (cell_up_and_right.element_type == 'symbol') {
						candidate[3] = true;
					}
				}

				// ...look 'up and left'
				if (this_x > 1 && this_y > 1) {
					let cell_up_and_left = data_grid[this_x - 1][this_y - 1];
					if (cell_up_and_left.element_type == 'symbol') {
						candidate[3] = true;
					}
				}

				// ...look 'down and left'
				if (this_x < data_grid.length - 1 && this_y > 1) {
					let target_cell = data_grid[this_x + 1][this_y - 1];
					if (target_cell.element_type == 'symbol') {
						candidate[3] = true;
					}
				}

				// ...look 'down and right'
				if (this_x < data_grid.length - 1 && this_y < data_grid[0].length - 1) {
					let target_cell = data_grid[this_x + 1][this_y + 1];
					if (target_cell.element_type == 'symbol') {
						candidate[3] = true;
					}
				}
			}
		});

		// console.log(part_candidates);
		identify_parts_on_grid();
	};

	let identify_parts_on_grid = () => {
		part_candidates.forEach((candidate) => {
			if (candidate[3]) {
				for (let i = 0; i < candidate[0].toString().length; i++) {
					let this_x = candidate[2];
					let this_y = candidate[1] + i;
					let this_cell = data_grid[this_x][this_y];
					this_cell.element_type = 'part';
				}
			}
		});
	};

	const parse = () => {
        answer = 0;
		parse_input_to_grid();
		find_part_candidates();
		// console.log(part_candidates)
		// console.log(data_grid)
		test_part_candidates();
    
		// loop across part_candidates and add values of all that are parts (is_part=true) to answer
		part_candidates.forEach((part) => {
			if (part[3]) {
				answer += parseInt(part[0]);
			}
		});
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
	{#each data_grid as row, index_y}
		{#each row as cell, index_x}
			<div bind:this={data_grid[index_x][index_y].dom_element} class={cell.element_type}>
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
        font-size:8px;
		div {
			display: inline-block;
			padding: 0.2rem 0.5rem;
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
