<script lang="ts">
	import { parse } from 'svelte/compiler';
	// import input from './data_sample.txt?raw';
	import input from './data.txt?raw';
	let answer: number = 0;
	let lines: number[][] = [];
    let lines_safe: number[][] = [];

    input.split('\n').forEach((line) => {
		// remove spaces and convert items to number
		let elements: number[] = line.split(' ').map(Number);
		lines.push(elements);
	});

	// make all lines increase (at least w/r/t the first and last element)
	lines.forEach((line) => {
		if (line[0] > line[line.length - 1]) {
			line.reverse();
		}
	});

    // given an increasing array (at least w/r/t the first and last element), determine if it is "safe"
	let isSafe = (line: number[]) => {
		let isSafe = true;

		//loop from second element to end of array
		for (let i = 1; i < line.length; i++) {
			let this_elem = line[i];
			let prev_elem = line[i - 1];
			let elem_diff = this_elem - prev_elem;
			if (elem_diff <= 0 || elem_diff > 3) {
				isSafe = false;
			}
		}
		return isSafe;
	};

	lines.forEach((line) => {
		// check for safety as-is
		if (isSafe(line)) {
			lines_safe.push(line);
		} else {
			// check for permutations via the dampener
			let isSafeWhenDampened = false;

            // one at a time, remove an element from line and check if the resulting sub-line is "safe"
			for (let i = 0; i < line.length; i++) {
				let dampenedLine = [...line];
				dampenedLine.splice(i, 1);
				if (isSafe(dampenedLine)) {
					isSafeWhenDampened = true;
				}
			}

			if (isSafeWhenDampened) {
				lines_safe.push(line);
			}
		}
	});

	answer = lines_safe.length;
</script>

<h1>answer</h1>
{answer}

<h1>input</h1>
<pre class="aoc_input">{input}</pre>

<h1>parsed</h1>
<div>
	num lines: {lines.length}<br />
	num lines safe: {lines_safe.length}<br />
</div>

<pre class="aoc_input">
    {JSON.stringify(lines, null, 3)}
</pre>
