<script>
	let answer = 0;
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
	let part_candidates = []; // each entry: ['value','x','y','is_part']

	let parse_input_to_grid = () => {
		data_grid = [];
		input.split('\n').forEach((row, y) => {
			let this_row_cells = [];
			row.split('').forEach((value, x) => {
				let this_cell = {
					value: value,
					x: x,
					y: y,
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
		input.split('\n').forEach((data_row, y) => {
			let this_row = data_row;

			// identify all numbers -- these are candidates which may be part nos.
			while (this_row.length) {
				if (parseInt(this_row)) {
					let candidate_value = parseInt(this_row);

					let x = data_row.indexOf(candidate_value);
					part_candidates.push([candidate_value, x, y, false]);

					this_row = this_row.substring(candidate_value.toString().length);
				} else {
					this_row = this_row.substring(1);
				}
			}
		});
	};

	let test_part_candidates = () => {
		part_candidates.forEach((candidate, index) => {
			
            let this_is_part = false;

            // loop across digits of candidate
            console.log(candidate)
            for(let i=0;i < candidate[0].toString().length;i++){
                let this_cell_coordinates = [candidate[2], candidate[1]+i]
                let this_cell_data = data_grid[this_cell_coordinates[0]][this_cell_coordinates[1]]
                console.log(this_cell_data)
            }

			candidate[3] = true; // is_part
		});

		// console.log(part_candidates);
	};

	const parse = () => {
		parse_input_to_grid();
		find_part_candidates();
		// console.log(part_candidates)
		test_part_candidates();
	};

	// on page load, run program
	$: parse();
</script>

<h1>Day 03: Gear Ratios</h1>

<h3>Answer</h3>
{answer || 'not computed'}

<h3>Input</h3>
<textarea style="width:95vw;height:6em;font-family:monospace;" bind:value={input}></textarea>
<br />
<button on:click={() => parse()}>Parse!</button>

<h3>Parsed Data</h3>
<div id="data_grid">
	{#each data_grid as row, index_y}
		{#each row as cell, index_x}
			<div bind:this={data_grid[index_y][index_x].dom_element} class={cell.element_type}>
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
</div>

<style lang="scss">
	#data_grid {
		font-family: monospace;
		div {
			display: inline-block;
			padding: 0.2rem 0.5rem;
			color: white;

			&.unknown {
				background-color: darkgrey;
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
		}
	}
</style>
