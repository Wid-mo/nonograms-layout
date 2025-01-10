<script>
	const cellsInRow = 4;

	$: cells = Array.from({ length: cellsInRow * cellsInRow }, () => Math.random() < 0.5);
	const last = (arr) => arr[arr.length - 1];
	const generateLabelsForLine = (arr) => {
		const labels = arr.reduce((groupsLengths, field, i, arr) => {
			if (field === false) return groupsLengths;
			if (!arr[i - 1]) {
				return [...groupsLengths, 1];
			}
			return [...groupsLengths.slice(0, -1), last(groupsLengths) + 1];
		}, []);
		if (!labels.length) return [0];
		return labels;
	};
	// $: labels =
	$: maxNumberOfLabelsForRows = 6;
	$: maxNumberOfLabelsForColumns = 2;
	// $: cellSize = '100cqi';
	// $: cellSize = `min(100cqh/${cellsInRow+maxNumberOfLabelsForRows}, 100cqi/${cellsInRow+maxNumberOfLabelsForColumns})`
	// $: gridSize = `min(100cqh, 100cqi)`;
	// $: gridSize = `100cqmin`;
	// $: gridSize = `calc(100cqi*${cellsInRow / (cellsInRow + maxNumberOfLabelsForColumns)})`;
</script>

<div
	class="layout"
	style="grid-template: {maxNumberOfLabelsForRows}fr {cellsInRow}fr / {maxNumberOfLabelsForColumns}fr {cellsInRow}fr;"
>
	<div></div>
	<div class="columnsLabels"></div>
	<div class="rowsLabels"></div>
	<div class="grid" style="grid-template: repeat({cellsInRow}, auto) / repeat({cellsInRow}, auto);">
		{#each cells as cell}
			<input type="checkbox" checked={cell} />
		{/each}
	</div>
</div>

<style>
	.layout {
		display: grid;
		width: 100cqmin;
		height: 100cqmin;
		background-color: aqua;
		position: absolute;
		bottom: 0;
		right: 0;
	}
	.columnsLabels {
		/* position: absolute; */
		/* right: 0;

		top: 0; */
		/* width: 60cqi;
		height: 100px; */
		background-color: red;
	}
	.rowsLabels {
		/* position: absolute; */
		/* bottom: 0;

		left: 0;
		width: 100px;
		height: 80cqh; */
		background-color: blue;
	}
	.grid {
		/* position: absolute; */
		/* right: 0;
		bottom: 0; */
		display: grid;

		/* width: 80cqi;
		height: 80cqh; */
	}
	:global(body) {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
		background-color: #030303;
	}
</style>
