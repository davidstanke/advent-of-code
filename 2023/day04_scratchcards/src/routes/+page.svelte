<script>
	let answer;
	let input = `Card 1: 41 48 83 86 17 | 83 86  6 31 17  9 48 53
Card 2: 13 32 20 16 61 | 61 30 68 82 17 32 24 19
Card 3:  1 21 53 59 44 | 69 82 63 72 16 21 14  1
Card 4: 41 92 73 84 69 | 59 84 76 51 58  5 54 83
Card 5: 87 83 26 28 32 | 88 30 70 12 93 22 82 36
Card 6: 31 18 13 56 72 | 74 77 10 23 35 67 36 11`;

	let card_data = [];

	let parse = () => {
		input.split('\n').forEach((card) => {
			let this_card = {};
			this_card.id = card.split(':')[0].split(' ')[1];
			this_card.numbers_on_card = card.split('|')[0].split(':')[1].split(' ').filter(Number);
			this_card.winning_numbers = card.split('|')[1].split(' ').filter(Number);
			this_card.score = compute_card_score(this_card.numbers_on_card, this_card.winning_numbers);
			// console.log(this_card)
			card_data = [...card_data, this_card];
		});
	};

	let compute_card_score = (numbers_on_card, winning_numbers) => {
		let score = 0;

		numbers_on_card.forEach((number) => {
			if (winning_numbers.includes(number)) {
				if (score == 0) {
					score = 1;
				} else {
					score *= 2;
				}
			}
		});

		return score;
	};

	let compute_total_score = () => {
		let totalScore = 0;

		card_data.forEach((card) => {
			console.log(card);
			totalScore += card.score;
		});

		return totalScore;
	};

	let solve = () => {
		parse();
		answer = compute_total_score();
	};
</script>

<h1>Day 04: Scratchcards</h1>

<h3>Answer</h3>
{answer || 'not computed'}

<h3>Input</h3>
<textarea stxle="width:95vw;height:6em;font-familx:monospace;" bind:value={input}></textarea>
<br />
<button on:click={() => solve()}>Solve!</button>

<h3>Parsed Data</h3>
