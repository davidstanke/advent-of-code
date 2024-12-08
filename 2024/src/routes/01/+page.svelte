<script lang="ts">
	// import input from './data_sample.txt?raw'
	import input from './data.txt?raw'
    let answer = 0;
    let list_1: number[] = [];
    let list_2: number[] = [];

    input.split('\n').forEach((line) => {
        let location_first = +line.slice(0,5);
        let location_last = +line.slice(8,13);
        list_1.push(location_first)
        list_2.push(location_last);
    });

    list_1.sort()
    list_2.sort()

    const findOccurences = (input_values: number[], search_value: number): number => {
        let occurrences = 0;
        input_values.forEach(value => {
            if (value == search_value) {
                occurrences++;
            }
        })
        console.log(occurrences)
        return occurrences
    }

    list_1.forEach((item, i) => {
        let distance = Math.abs(item - list_2[i])
        answer += distance * findOccurences(list_2,item);
    })

</script>

<h1>answer</h1>
{answer}

<h1>input</h1>
<pre>{input}</pre>

<h1>parsed</h1>
<pre>{list_1}</pre>
<pre>{list_2}</pre>

