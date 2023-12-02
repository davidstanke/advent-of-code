<h1>Advent of Code 2023 Day 1: Trebuchet</h1>
<script>
    let calibration_donkument = `1abc2
pqr3stu8vwx
a1b2c3d4e5f
treb7uchet`
        ,result = ''

    const process_donkument = () => {
        console.log(calibration_donkument);

        let total_sum = 0

        let donkument_lines = calibration_donkument.split('\n')
        for (const line of donkument_lines) {
            let line_chars = line.split('')
            let first_integer = line_chars.find((x) => parseInt(x))
            let line_chars_reverse = line_chars.toReversed()
            let last_integer = line_chars_reverse.find((x) => parseInt(x))
            total_sum += parseInt(first_integer + last_integer)
        }

        result = total_sum


    }

</script>

<h3>Input:</h3>
<textarea bind:value={calibration_donkument}/>
<br>
<button on:click={()=>process_donkument()}>submit</button>

<h3>Result:</h3>
{result}