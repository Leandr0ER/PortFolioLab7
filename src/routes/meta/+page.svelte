<script>
import * as d3 from "d3";
import { onMount } from "svelte";

let width = 1000, height = 600;

let data = [];
let commits = [];

onMount(async () => {
	data = await d3.csv("/loc.csv", row => ({
    ...row,
    line: Number(row.line), // or just +row.line
    depth: Number(row.depth),
    length: Number(row.length),
    date: new Date(row.date + "T00:00" + row.timezone),
    datetime: new Date(row.datetime)
	}));
	// console.log(data)  //Step 1.1

	//Step 1.2
	commits = d3.groups(data, d => d.commit).map(([commit, lines]) => {
    let first = lines[0];
    let {author, date, time, timezone, datetime} = first;
    let ret = {
        id: commit,
        url: "https://github.com/Leandr0ER/REPO/commit/" + commit,
        author, date, time, timezone, datetime,
        hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
        totalLines: lines.length
    };

    // Like ret.lines = lines
    // but non-enumerable so it doesn’t show up in JSON.stringify
    Object.defineProperty(ret, "lines", {
        value: lines,
        configurable: true,
        writable: true,
        enumerable: false,
    });

    return ret;
	});

	//console.log(commits)
});

$: minDate = d3.min(commits.map(d => d.date));
$: maxDate = d3.max(commits.map(d => d.date));
$: maxDatePlusOne = new Date(maxDate);
$: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

$: xScale = d3.scaleTime()
            .domain([minDate, maxDatePlusOne])
            .range([0, width])
            .nice();

$: yScale = d3.scaleLinear()
            .domain([24, 0])
            .range([height, 0]);

</script>
<svelte:head>
<title>Meta</title>
</svelte:head>
<h1>Meta</h1>
<p>This page includes stats about the code of this website</p>
<p>Total lines of code: {data.length}</p>


<!-- Step 1.3 -->
<section>
    <h2>Summary</h2>
    <dl class="stats">
    <dt>Total <abbr title="Lines of code">LOC</abbr></dt>
    <dd>{data.length}</dd>
    <dt>Files</dt>
    <dd>{d3.groups(data, d => d.file).length}</dd>
    <dt>Commits</dt>
    <dd>{d3.groups(data, d => d.commit).length}</dd>
    </dl>

    <svg viewBox="0 0 {width} {height}">
        <!-- scatterplot will go here -->
        <g class="dots">
            {#each commits as commit, index }
                <circle
                    cx={ xScale(commit.datetime) }
                    cy={ yScale(commit.hourFrac) }
                    r="5"
                    fill="steelblue"
                />
            {/each}
            </g>
    </svg>
</section>



<style>
    dl{
        display: grid;
        grid-template-columns: auto;
    }
    dt{
        grid-row: 1;
        font-family: inherit;
        font-weight: bold;
        color: var(--border-gray);
        text-transform: uppercase;
    }
    dd{
        font-family: inherit;
        font-weight: bold;
    }
    section{
        border-width:0.15em;
        border-style:solid;
        border-color:var(--border-gray);
        padding-left: 1em;
        padding-right: 1em;
    }
    svg {
        overflow: visible;
    }
</style>