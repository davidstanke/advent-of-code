<script>
	let constraints = {
		red: 12,
		green: 13,
		blue: 14
	};
	let input = `Game 1: 3 blue, 4 red; 1 red, 2 green, 6 blue; 2 green
Game 2: 1 blue, 2 green; 3 green, 4 blue, 1 red; 1 green, 1 blue
Game 3: 8 green, 6 blue, 20 red; 5 blue, 4 red, 13 green; 5 green, 1 red
Game 4: 1 green, 3 red, 6 blue; 3 green, 6 red; 3 green, 15 blue, 14 red
Game 5: 6 red, 1 blue, 3 green; 2 blue, 1 red, 2 green`;
	let parsed_input = {};
	let game_evaluation = {};
	let answer;

	const parse = () => {
		input.split('\n').forEach((line, index) => {
			let this_game = index + 1;
			let game_data = line.substring(line.search(': ') + 1);

			// console.log(game_data)

			parsed_input[this_game] = {};
			game_data.split('; ').forEach((draw, draw_index) => {
				draw = draw.trimStart();

				// console.log(draw)
				parsed_input[this_game][draw_index] = {
					red: 0,
					green: 0,
					blue: 0
				};

				draw.split(', ').forEach((color_pull) => {
					color_pull = color_pull.trimStart();
					// console.log(color_pull)
					let [number, color] = color_pull.split(' ');
					// console.log(color)
					parsed_input[this_game][draw_index][color] = parseInt(number);
				});
			});
		});
		// console.log(parsed_input)
	};

	const evaluate_games = () => {
		parse();

		for (const [key, game_data] of Object.entries(parsed_input)) {
			// console.log(key)
			// console.log(game_data)

			let game_index = key;
			let is_possible = true;
			Object.entries(game_data).forEach(([_, draw]) => {
				// console.log(data)
				if (draw.red > constraints.red) {
					is_possible = false;
				}
				if (draw.green > constraints.green) {
					is_possible = false;
				}
				if (draw.blue > constraints.blue) {
					is_possible = false;
				}
			});

			game_evaluation[key] = is_possible;
		}
	};

	const solve = () => {
        answer = 0
		evaluate_games();
		for (const [game_index, is_possible] of Object.entries(game_evaluation)) {
			if (is_possible) {
				answer += parseInt(game_index);
			}
		}
	};
</script>

<h1>Day 02: Cube Conundrum</h1>

<h3>Constraints</h3>
{#each Object.entries(constraints) as [color, quantity]}
	<span style="background-color:{color};color:white;padding:2px 6px;margin:0 4px;border-radius:25%;"
		>{quantity}</span
	>
{/each}

<h3>Answer</h3>
{answer||"not computed"}

<h3>Input</h3>
<textarea style="width:95vw;height:6em;font-family:monospace;" bind:value={input}></textarea>
<br />
<button on:click={() => solve()}>Compute</button>

<h3>Parsed Input</h3>
<div style="font-family:monospace">
	{#each Object.entries(parsed_input) as [index, game_data]}
		{index}:
		{#each Object.entries(game_data) as [index, draw_data]}
			{index}:
			<span style="color:red">{draw_data['red']}</span>
			<span style="color:green">{draw_data['green']}</span>
			<span style="color:blue">{draw_data['blue']}</span>
			|&nbsp;
		{/each}
		<br />
	{/each}
</div>

<h3>Possible Games</h3>
<div>
	{#each Object.entries(game_evaluation) as [index, is_possible]}
		{index}: {is_possible}<br />
	{/each}
</div>
