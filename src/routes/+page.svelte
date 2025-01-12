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
	$: maxWidth = maxNumberOfLabelsForColumns +  cellsInRow;
	$: maxHeight = maxNumberOfLabelsForRows +  cellsInRow;
</script>

<div
	class="layout"
	style="grid-template: {maxNumberOfLabelsForRows}fr {cellsInRow}fr / {maxNumberOfLabelsForColumns}fr {cellsInRow}fr;
	width: min(100cqi, calc(100cqh*{maxWidth}/{maxHeight}));
	height: min(100cqh, calc(100cqi*{maxHeight}/{maxWidth}));"
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
		background-color: aqua;
		position: relative;
		bottom: 0;
		right: 0;
	}
	.columnsLabels {
		background-color: red;
	}
	.rowsLabels {
		background-color: blue;
	}
	.grid {
		display: grid;
	}
	:global(body) {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
		background-color: #030303;
		height: 100vh;
		display: flex;
		align-items: end;
		justify-content: end;
	}
</style>
