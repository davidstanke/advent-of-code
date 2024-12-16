<script lang="ts">
	import { parse } from 'svelte/compiler';
	// import input from './data_sample.txt?raw';
	import input from './data.txt?raw';
	let answer: number = 0;

	const regex = /mul\([0-9]*,[0-9]*\)/g;
	const muls = input.match(regex);

	muls.forEach(mul => {
		let mul_sanitized = mul.replace("mul(","").replace(")","")
		let mul_arr: Number[] = mul_sanitized.split(',')
		answer += parseInt(mul_arr[0]) * parseInt(mul_arr[1])
	})


</script>

<h1>answer</h1>
{answer}

<h1>input</h1>
<pre class="aoc_input">{input}</pre>

<h1>parsed</h1>
<div>
</div>

<pre class="aoc_input">
    
</pre>
