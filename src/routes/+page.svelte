<script>
	import { preventDefault } from 'svelte/legacy';

	let cellsInRow = 5;

	$: solutionCells = Array.from({ length: cellsInRow * cellsInRow }, () => Math.random() < 0.5);
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
		).map((idx) => solutionCells[idx])
	).map((line) => generateLabelsForLine(line));
	$: labelsForRows = splitByGroupsLengthN(cellsInRow)(
		[...Array(cellsInRow ** 2).keys()].map((idx) => solutionCells[idx])
	).map((line) => generateLabelsForLine(line));

	$: maxNumberOfLabelsForRows = Math.max(...labelsForRows.map(({ length }) => length));
	$: maxNumberOfLabelsForColumns = Math.max(...labelsForColumns.map(({ length }) => length));
	$: maxWidth = maxNumberOfLabelsForRows + cellsInRow;
	$: maxHeight = maxNumberOfLabelsForColumns + cellsInRow;

	$: cells = Array(cellsInRow ** 2).fill('');

	let winningFlare = false;
	function gameOver() {
		if (cells.every((cell, i) => (solutionCells[i] ? cell === '█' : cell !== '█'))) {
			winningFlare = true;
			setTimeout(() => ((cellsInRow += 1), (winningFlare = false)), 1000);
		}
	}
	let indexStartedCell;
	let valueToDrawing;
	function handlePointerDown(e, i) {
		indexStartedCell = i;
		const eventType = e.type === 'touch' ? 'touch' : e.buttons == 1 ? 'leftMouse' : 'rightMouse';
		const transitions = {
			'': { leftMouse: '█', rightMouse: 'X', touch: '█' },
			'█': { leftMouse: '', rightMouse: 'X', touch: 'X' },
			X: { leftMouse: '', rightMouse: '', touch: '' }
		};
		valueToDrawing = transitions[cells[i]][eventType];
		cells[i] = valueToDrawing;
	}
	let freezeAxis;
	function handleDragged(e, i) {
		// if not pressed
		if (!(e.buttons == 0x1 || e.buttons == 0x2)) return;

		const index2d = (i) => [i % cellsInRow, ~~(i / cellsInRow)];
		if (!freezeAxis) {
			const [x, y] = index2d(i);
			const [sx, sy] = index2d(indexStartedCell);
			const { abs } = Math;
			freezeAxis = abs(x - sx) > abs(y - sy) ? 'y' : abs(x - sx) < abs(y - sy) ? 'x' : null;
			if (!freezeAxis) return;
		}
		const [x, y] = index2d(i);
		const [sx, sy] = index2d(indexStartedCell);
		const [tx, ty] = freezeAxis == 'y' ? [x, sy] : [sx, y];
		const targetCellIdx = ty * cellsInRow + tx;
		cells[targetCellIdx] = valueToDrawing;
	}
	function handlePointerUp() {
		freezeAxis = null;
	}
</script>

<svelte:body on:contextmenu|preventDefault />

<div
	class="layout"
	style="grid-template: {maxNumberOfLabelsForColumns}fr {cellsInRow}fr / {maxNumberOfLabelsForRows}fr {cellsInRow}fr;
	width: min(100cqi, calc(100cqh*{maxWidth}/{maxHeight}));
	aspect-ratio: {maxWidth} / {maxHeight};"
>
	<div></div>
	<div class="columnsLabels">
		{#each labelsForColumns as labelsGroup}
			<div class="columnBlockWithLabels">
				{#each labelsGroup as label}
					<div style="font-size: calc(100cqmin - 12px)">
						{label}
					</div>
				{/each}
			</div>
		{/each}
	</div>
	<div class="rowsLabels">
		{#each labelsForRows as labelsGroup}
			<div class="rowBlockWithLabels">
				{#each labelsGroup as label}
					<div style="font-size: calc(100cqmin / {maxNumberOfLabelsForRows} - 12px)">{label}</div>
				{/each}
			</div>
		{/each}
	</div>
	<div class="grid" style="grid-template: repeat({cellsInRow}, auto) / repeat({cellsInRow}, auto);">
		{#each cells as cell, i}
			<!-- svelte-ignore a11y_consider_explicit_label -->
			<button
				on:pointerdown={(e) => {
					handlePointerDown(e, i);
				}}
				on:pointerenter={(e) => {
					handleDragged(e, i);
				}}
				on:pointerup={handlePointerUp}
				on:contextmenu|preventDefault
				on:click={gameOver}
				class:filled={cell == '█'}
				class:crossMark={cell == 'X'}
			>
			</button>
		{/each}
	</div>
</div>
{#if winningFlare}
	<div class="winning-flare"></div>
{/if}

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
				container-type: inline-size;

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
				container-type: inline-size;

				& > * {
					width: 100%;
					height: 100%;
					display: flex;
					justify-content: center;
					align-items: center;
					user-select: none;
				}
			}
			& > .rowBlockWithLabels:nth-child(odd) {
				background-color: hsla(0, 0%, 43%, 0.445);
			}
		}
		& > .grid {
			display: grid;

			& > button {
				background-color: gray;
				cursor: pointer;
				&:focus {
					outline: none;
					box-shadow: none;
				}

				&.filled {
					background-color: black;
				}
				&.crossMark {
					background-color: darkgray;
				}
			}
		}
	}

	.winning-flare {
		position: absolute;
		inset: 0;
		background: radial-gradient(transparent 60%, green);
		animation: flare 1000ms;
		opacity: 0;
	}
	@keyframes flare {
		50% {
			opacity: 1;
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
		overflow: hidden;
	}
	/* * {
		outline: 2px solid red;
	} */
</style>
