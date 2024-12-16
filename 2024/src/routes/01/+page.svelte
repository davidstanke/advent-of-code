<script lang="ts">
	// import input from './data_sample.txt?raw'
	import input from './data.txt?raw';
	let answer = 0;
	let list_1: number[] = [];
	let list_2: number[] = [];

	input.split('\n').forEach((line) => {
		line = line.replace('   ', ',');
		let location_first = parseInt(line.substring(0, line.indexOf(',')));
		let location_last = parseInt(line.substring(line.indexOf(',') + 1));

		list_1.push(location_first);
		list_2.push(location_last);
	});

	list_1.sort();
	list_2.sort();

	const findOccurences = (input_values: number[], search_value: number): number => {
		let occurrences = 0;
		input_values.forEach((value) => {
			if (value == search_value) {
				occurrences++;
			}
		});
		return occurrences;
	};

	list_1.forEach((item, i) => {
		answer += item * findOccurences(list_2, item);
	});
</script>

<h1>answer</h1>
{answer}

<h1>input</h1>
<pre>{input}</pre>

<h1>parsed</h1>
<pre>{list_1}</pre>
<pre>{list_2}</pre>
