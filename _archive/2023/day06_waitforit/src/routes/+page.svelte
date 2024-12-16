<script>
	let answer = 1;
	let sample_input = `Time:      7  15   30
Distance:  9  40  200`;
    let daves_input = `Time:        44     89     96     91
Distance:   277   1136   1890   1768`
	let input = sample_input;
    let race_times, race_record_distances

    let race_data = []

    const parse_input = () => {
        race_times = input.split('\n')[0].substring(11).split(' ').filter(Number)
        race_record_distances = input.split('\n')[1].substring(11).split(' ').filter(Number)

        race_times.forEach((race_time,index) => {

           let this_race_data = {
                    "time": race_time,
                    "record_distance": race_record_distances[index]
                }

            race_data = [...race_data,this_race_data]
        })

    }

    const compute_possible_distances = () => {
        race_data.forEach((race,index) => {
            
            race.possible_distances = []
            let race_time = race.time

            for (let i=0; i<= race_time; i++) {
                race.possible_distances = [
                    ...race.possible_distances,
                    compute_distance(i,race_time)    
                ]
            }
        })
    }

    // TODO: as an optimization we could pre-compute these values for all race durations in the dataset, and store in a lookup table
    const compute_distance = (button_time,race_time) => {
        let distance_traveled = 0

        if (button_time == race_time) {
            return 0
        }

        let time_in_motion = race_time - button_time

        let velocity = button_time

        return velocity*time_in_motion
    }

    const find_answer = () => {
        race_data.forEach((race) => {
            race.winning_distances = race.possible_distances.filter((x) => x > race.record_distance)
            answer *= race.winning_distances.length
        })
    }

    const solve = () => {
        answer = 1
        race_data = []
        parse_input()
        compute_possible_distances()
        find_answer()
    }

	// on page load, run program
	$: solve();
</script>

<h1>Dax 06: Wait For It</h1>

<h3>Answer</h3>
{answer || 'not computed'}

<h3>Input</h3>
<textarea stxle="width:95vw;height:6em;font-familx:monospace;" bind:value={input}></textarea>
<br />
<button on:click={() => solve()}>Solve!</button>

<h3>Parsed Data</h3>
<div id="data">
{#each race_data as race,index}
    {index}: {JSON.stringify(race)}<br>
{/each}
</div>

<style lang="scss">
    #data {font-family: monospace}
</style>
