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
	$: labelsForColumns = [[2], [2, 1], [2], [2, 1]];
	// [].map((line) => generateLabelsForLine(line));
	$: labelsForRows = [[2], [2, 1], [2], [2, 1]];
	$: maxNumberOfLabelsForRows = Math.max(...labelsForColumns.map(({ length }) => length));
	$: maxNumberOfLabelsForColumns = Math.max(...labelsForRows.map(({ length }) => length));
	$: maxWidth = maxNumberOfLabelsForColumns + cellsInRow;
	$: maxHeight = maxNumberOfLabelsForRows + cellsInRow;
</script>

<div
	class="layout"
	style="grid-template: {maxNumberOfLabelsForRows}fr {cellsInRow}fr / {maxNumberOfLabelsForColumns}fr {cellsInRow}fr;
	width: min(100cqi, calc(100cqh*{maxWidth}/{maxHeight}));
	height: min(100cqh, calc(100cqi*{maxHeight}/{maxWidth}));"
>
	<div></div>
	<div class="columnsLabels">
		{#each labelsForColumns as labelsGroup}
			<div class="columnBlockWithLabels">
				{#each labelsGroup as label}
					<div>{label}</div>
				{/each}
			</div>
		{/each}
	</div>
	<div class="rowsLabels">
		{#each labelsForRows as labelsGroup}
			<div class="rowBlockWithLabels">
				{#each labelsGroup as label}
					<div>{label}</div>
				{/each}
			</div>
		{/each}
	</div>
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

		& > .columnsLabels {
			background-color: red;
			display: flex;
			justify-content: space-around;
			align-items: flex-end;

			& > .columnBlockWithLabels {
				background-color: yellow;
				height: 100%;
				width: 100%;
				border-radius: 2cqmin;
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;

				& > * {
					width: 100%;
					height: 100%;
					display: flex;
					justify-content: center;
					align-items: center;
					font-size: 13cqmin;
					user-select: none;
				}
			}
			& > .columnBlockWithLabels:nth-child(odd) {
				background-color: lightblue;
			}
		}
		& > .rowsLabels {
			background-color: red;
			display: flex;
			justify-content: space-around;
			/* align-items: flex-end; */
			flex-direction: column;


			& > .rowBlockWithLabels {
				background-color: yellow;
				height: 50%;
				width: 100%;
				border-radius: 2cqmin;
				display: flex;
				align-items: center;
				justify-content: center;

				& > * {
					width: 100%;
					height: 100%;
					display: flex;
					justify-content: center;
					align-items: center;
					font-size: 13cqmin;
					user-select: none;
				}
			}
			& > .rowBlockWithLabels:nth-child(odd) {
				background-color: lightblue;
			}
		}
		& > .grid {
			display: grid;
		}
	}

	:global(body) {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
		background-color: #030303;
		height: 100vh;
		display: flex;
		align-items: center;
		justify-content: center;
		font-family: Arial, sans-serif;
		color: white;
	}
</style>
