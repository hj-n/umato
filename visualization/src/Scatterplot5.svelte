<!-- https://svelte.dev/examples#scatterplot -->
<!-- https://svelte.recipes/components/scatterplot/ -->
<!-- https://svelte.dev/repl/b4c485ee69484fd8a63b8dc07c3b20a2?version=3.4.1 -->

<script lang="ts">
    import * as d3 from 'd3';

    export let algoname: string;
    export let dname: string;
    export let x: number;
    export let y: number;

    function read_csv(path) {
        let request = new XMLHttpRequest();  
        request.open("GET", path, false);   
        request.send(null);

        let csvData = new Array();
        let jsonObject = request.responseText.split(/\r?\n|\r/);
        for (let i = 0; i < jsonObject.length; i++) {
            csvData.push(jsonObject[i].split(','));
        }
        csvData.splice(0, 1); // remove labels
        csvData.forEach(d => {
            d[0] = +d[0];
            d[1] = +d[1];
            d[2] = +d[2];
        })
        return csvData
    }

    let data = read_csv("results/" + dname + "/" + algoname + ".csv")

    let svgWidth = 450
    let svgHeight = 450
    const margin = { top: 25, right: 25, bottom: 25, left: 25 };

    let width = svgWidth - margin.left - margin.right
    let height = svgHeight - margin.top - margin.bottom

    let r = 3;
    
	// const [minX, maxX] = d3.extent(data,(d) => d[0]);
    // const [minY, maxY] = d3.extent(data,(d) => d[1]);

    const [minX, maxX] = [x, y];
    const [minY, maxY] = [x, y];

	$: xScale = d3.scaleLinear()
		.domain([minX, maxX])
        .range([0, width]);
        
	$: yScale = d3.scaleLinear()
		.domain([minY, maxY])
        .range([height, 0]);

    const colorDomain = d3.extent(data, d => d[2])
    const colorRange = ["#4e79a7","#f28e2c","#76b7b2","#59a14f","#edc949","#af7aa1","#ff9da7","#9c755f","#bab0ab", "#e15759","#5E4FA2"];
    // const colorRange = ["#3288BD", "#5E4FA2", "#66C2A5", "#ABDDA4", "#E6F598", "#FFFFBF", "#FEE08B", "#FDAE61", "#F46D43", "#D53E4F", "#9E0142"];
    let colorScale = d3.scaleOrdinal()
        .domain(d3.extent(colorDomain))
        .range(colorRange);

</script>

<div class="outer">
    <div class="inner" style='margin-right: 50px'>
        <svg {width} {height}>
            <!-- <text fill="currentColor" x="400px" y="400px">asdas</text> -->
            {#each data as d}
                <circle class="circle-line"
                    r={r}
                    cx='{xScale(d[0])}'
                    cy='{yScale(d[1])}'
                    fill='{colorScale(d[2])}'
                ></circle>
            {/each}
        </svg>
        <!-- <h2>{algoname.toUpperCase()}</h2> -->
        <!-- <h2>{algoname.replace(/_/g,' ')}</h2> -->
        <h2>{algoname.replace(/_/g,' ').toUpperCase() + ' epochs'}</h2>
        <!-- <h2>{dname.replace(/_/g,' ').toUpperCase()}</h2> -->
    </div>
</div>