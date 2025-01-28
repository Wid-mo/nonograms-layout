<script>
	let cellsInRow = 15;

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
	const splitByGroupsLengthN = (n) => (arr) =>
		Array.from({ length: arr.length / n }, (_, i) => arr.slice(i * n, (i + 1) * n));
	$: labelsForColumns = splitByGroupsLengthN(cellsInRow)(
		Array.from(
			{ length: cellsInRow ** 2 },
			(_, i) => ~~(i / cellsInRow) + ((i * cellsInRow) % cellsInRow ** 2)
		).map((idx) => cells[idx])
	).map((line) => generateLabelsForLine(line));
	$: labelsForRows = splitByGroupsLengthN(cellsInRow)(
		[...Array(cellsInRow ** 2).keys()].map((idx) => cells[idx])
	).map((line) => generateLabelsForLine(line));

	$: maxNumberOfLabelsForRows = Math.max(...labelsForColumns.map(({ length }) => length));
	$: maxNumberOfLabelsForColumns = Math.max(...labelsForRows.map(({ length }) => length));
	$: maxWidth = maxNumberOfLabelsForColumns + cellsInRow;
	$: maxHeight = maxNumberOfLabelsForRows + cellsInRow;

	$: checkboxes = Array(cellsInRow ** 2).fill(false);
	$: gameOver = checkboxes.every((v, i) => v === cells[i]);
	$: if (gameOver) {
		alert("OK")
	}
</script>

<div
	class="layout"
	style="grid-template: {maxNumberOfLabelsForRows}fr {cellsInRow}fr / {maxNumberOfLabelsForColumns}fr {cellsInRow}fr;
	width: min(100cqi, calc(100cqh*{maxWidth}/{maxHeight}));
	aspect-ratio: {maxWidth} / {maxHeight};"
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
		{#each checkboxes as checked}
			<input type="checkbox" bind:checked />
		{/each}
	</div>
</div>

<style>
	.layout {
		display: grid;

		& > .columnsLabels {
			display: flex;
			justify-content: space-around;
			align-items: flex-end;

			& > .columnBlockWithLabels {
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
					/* font-size: 13cqmin; */
					user-select: none;
				}
			}
			& > .columnBlockWithLabels:nth-child(odd) {
				background-color: hsla(0, 0%, 43%, 0.445);
			}
		}
		& > .rowsLabels {
			display: flex;
			justify-content: space-around;
			/* align-items: flex-end; */
			flex-direction: column;

			& > .rowBlockWithLabels {
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
					/* font-size: 13cqmin; */
					user-select: none;
				}
			}
			& > .rowBlockWithLabels:nth-child(odd) {
				background-color: hsla(0, 0%, 43%, 0.445);
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
		color: gray;
	}
	* {
		/* outline: 2px solid red; */
	}
</style>
