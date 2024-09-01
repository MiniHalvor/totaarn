<script>
	// Defining the initial state of the board
	const initialBoard = [
		['r', 'n', 'b', 'q', 'k', 'b', 'n', 'r'],
		['p', 'p', 'p', 'p', 'p', 'p', 'p', 'p'],
		[null, null, null, null, null, null, null, null],
		[null, null, null, null, null, null, null, null],
		[null, null, null, null, null, null, null, null],
		[null, null, null, null, null, null, null, null],
		['P', 'P', 'P', 'P', 'P', 'P', 'P', 'P'],
		['R', 'N', 'B', 'Q', 'K', 'B', 'N', 'R']
	];
	const blackPieces = ['p', 'q', 'k', 'b', 'n', 'r'];
	const whitePieces = ['P', 'Q', 'K', 'B', 'N', 'R'];

	// Reactive state, changes as the game progress
	let board = initialBoard;
	let debug = 'this is for debugging';

	// Mapping pieces to image file paths
	const pieceSymbols = {
		r: 'black-rook.png',
		n: 'black-knight.png',
		b: 'black-bishop.png',
		q: 'black-queen.png',
		k: 'black-king.png',
		p: 'black-pawn.png',
		R: 'white-rook.png',
		N: 'white-knight.png',
		B: 'white-bishop.png',
		Q: 'white-queen.png',
		K: 'white-king.png',
		P: 'white-pawn.png'
	};

	// Track the selected piece and its original position
	let selectedPiece = null;
	let selectedFrom = { row: null, col: null };

	// Function to handle square clicks
	const handleSquareClick = (rowIndex, colIndex) => {
		// If a piece is already selected, move it to the new square
		if (selectedPiece) {
			// Check if the piece is being moved to a different square
			if (selectedFrom.row !== rowIndex || selectedFrom.col !== colIndex) {
				const targetPiece = board[rowIndex][colIndex];
				if (targetPiece && targetPiece.toLowerCase() === 'k'){
					console.log("cannot capture king");
				} else {
				// checks if the piece it moves to
				if (
					!(whitePieces.includes(selectedPiece)
						? whitePieces.includes(board[rowIndex][colIndex])
						: blackPieces.includes(board[rowIndex][colIndex]))
				) {
					if (selectedPiece.toLowerCase() === 'p') {
						//hvis det er bonde
						const pawnDirection = selectedPiece === 'p' ? 1 : -1;

						const isFirstMove = selectedFrom.row % 5 === 1;
						const pawnDistance = (rowIndex - selectedFrom.row) * pawnDirection;
						const pawnIsBlocked =
							board[selectedFrom.row + 1 * pawnDirection * pawnDistance][selectedFrom.col] != null;
						const pawnCanStrikeRight =
							selectedPiece === 'p'
								? whitePieces.includes(
										board[selectedFrom.row + 1 * pawnDirection][selectedFrom.col + 1]
									)
								: blackPieces.includes(
										board[selectedFrom.row + 1 * pawnDirection][selectedFrom.col + 1]
									);
						const pawnCanStrikeLeft =
							selectedPiece === 'p'
								? whitePieces.includes(
										board[selectedFrom.row + 1 * pawnDirection][selectedFrom.col - 1]
									)
								: blackPieces.includes(
										board[selectedFrom.row + 1 * pawnDirection][selectedFrom.col - 1]
									);
						if (
							((pawnDistance === 2 && isFirstMove) || pawnDistance === 1) &&
							(colIndex === selectedFrom.col ||
								(pawnCanStrikeRight && pawnDistance === 1 && colIndex - 1 === selectedFrom.col) ||
								(pawnCanStrikeLeft && pawnDistance === 1 && colIndex + 1 === selectedFrom.col)) &&
							(!pawnIsBlocked ||
								(pawnCanStrikeRight && pawnDistance === 1 && colIndex - 1 === selectedFrom.col) ||
								(pawnCanStrikeLeft && pawnDistance === 1 && colIndex + 1 === selectedFrom.col))
						) {
							board[rowIndex][colIndex] = selectedPiece;
							board[selectedFrom.row][selectedFrom.col] = null;
						}
					} else if (selectedPiece.toLowerCase() === 'r') {
						//Logikken her er lagd på søvnmangel. Logger ut nå. 02:51.

						let maxDistance = 0;
						let rookDirection =
							selectedFrom.row === rowIndex
								? colIndex < selectedFrom.col //if on the same row
									? 'left'
									: 'right'
								: rowIndex < selectedFrom.row //if on the same column
									? 'up'
									: 'down';

						let distance = Math.abs(
							rowIndex === selectedFrom.row
								? colIndex - selectedFrom.col
								: rowIndex - selectedFrom.row
						);
						switch (rookDirection) {
							case 'left':
								for (let index = selectedFrom.col - 1; index > -1; index--) {
									const element = board[selectedFrom.row][index];
									if (element === null) {
										maxDistance++;
									}
									if (
										selectedPiece === 'r'
											? whitePieces.includes(element)
											: blackPieces.includes(element)
									) {
										maxDistance++;
										break;
									}
									if (
										selectedPiece === 'r'
											? blackPieces.includes(element)
											: whitePieces.includes(element)
									) {
										break;
									}
									debug += element;
								}
								break;
							case 'right':
								for (let index = selectedFrom.col + 1; index < 8; index++) {
									const element = board[selectedFrom.row][index];
									if (element === null) {
										maxDistance++;
									}
									if (
										selectedPiece === 'r'
											? whitePieces.includes(element)
											: blackPieces.includes(element)
									) {
										maxDistance++;
										break;
									}
									if (
										selectedPiece === 'r'
											? blackPieces.includes(element)
											: whitePieces.includes(element)
									) {
										break;
									}
									debug += element;
								}
								break;
							case 'down':
								for (let index = selectedFrom.row + 1; index < 8; index++) {
									const element = board[index][selectedFrom.col];
									if (element === null) {
										maxDistance++;
									}
									if (
										selectedPiece === 'r'
											? whitePieces.includes(element)
											: blackPieces.includes(element)
									) {
										maxDistance++;
										break;
									}
									if (
										selectedPiece === 'r'
											? blackPieces.includes(element)
											: whitePieces.includes(element)
									) {
										break;
									}
									debug += element;
								}
								break;
							case 'up':
								for (let index = selectedFrom.row - 1; index > -1; index--) {
									const element = board[index][selectedFrom.col];
									if (element === null) {
										maxDistance++;
									}
									if (
										selectedPiece === 'r'
											? whitePieces.includes(element)
											: blackPieces.includes(element)
									) {
										maxDistance++;
										break;
									}
									if (
										selectedPiece === 'r'
											? blackPieces.includes(element)
											: whitePieces.includes(element)
									) {
										break;
									}
									debug += element;
								}
								break;

							default:
								break;
						}
						debug +=
							' distance: ' +
							distance +
							' max dsitance: ' +
							maxDistance +
							' direction: ' +
							rookDirection; //debugging
						let sameColOrSameRow = rowIndex === selectedFrom.row || colIndex === selectedFrom.col;

						if (sameColOrSameRow && distance <= maxDistance) {
							board[rowIndex][colIndex] = selectedPiece;
							board[selectedFrom.row][selectedFrom.col] = null;
						}
					} else if (selectedPiece.toLowerCase() === 'n') {
						let distanceX = Math.abs(colIndex - selectedFrom.col);
						let distanceY = Math.abs(rowIndex - selectedFrom.row);
						let distance = Math.max(distanceX, distanceY);

						if (distance === 2 && distanceX != distanceY && distanceX != 0 && distanceY != 0) {
							board[rowIndex][colIndex] = selectedPiece;
							board[selectedFrom.row][selectedFrom.col] = null;
						}
					} else if (selectedPiece.toLowerCase() === 'b') {
						let maxDistance = 0;
						let bishopDirection =
							colIndex < selectedFrom.col
								? rowIndex < selectedFrom.row //if on the same row
									? 'leftUp'
									: 'leftDown'
								: rowIndex < selectedFrom.row //if on the same column
									? 'rightUp'
									: 'rightDown';
						let distance = Math.abs(colIndex - selectedFrom.col);
						switch (bishopDirection) {
							case 'leftUp':
								for (let indexRow = selectedFrom.row - 1; indexRow > -1; indexRow--) {
									let indexCol = selectedFrom.col - 1;
									const element = board[indexRow][indexCol];
									if (element === null) {
										maxDistance++;
									}
									if (
										selectedPiece === 'b'
											? whitePieces.includes(element)
											: blackPieces.includes(element)
									) {
										maxDistance++;
										break;
									}
									if (
										selectedPiece === 'b'
											? blackPieces.includes(element)
											: whitePieces.includes(element)
									) {
										break;
									}
									debug += element;
									if (indexCol > 0) {
										indexCol--;
									} else {
										break;
									}
								}
								break;
							// 	case 'rightUp':
							// 		for (let index = selectedFrom.col + 1; index < 8; index++) {
							// 			const element = board[index][index];
							// 			if (element === null) {
							// 				maxDistance++;
							// 			}
							// 			if (
							// 				selectedPiece === 'b'
							// 					? whitePieces.includes(element)
							// 					: blackPieces.includes(element)
							// 			) {
							// 				maxDistance++;
							// 				break;
							// 			}
							// 			if (
							// 				selectedPiece === 'b'
							// 					? blackPieces.includes(element)
							// 					: whitePieces.includes(element)
							// 			) {
							// 				break;
							// 			}
							// 			debug += element;
							// 		}
							// 		break;
							// 	case 'leftDown':
							// 		for (let index = selectedFrom.row + 1; index < 8; index++) {
							// 			const element = board[index][index];
							// 			if (element === null) {
							// 				maxDistance++;
							// 			}
							// 			if (
							// 				selectedPiece === 'b'
							// 					? whitePieces.includes(element)
							// 					: blackPieces.includes(element)
							// 			) {
							// 				maxDistance++;
							// 				break;
							// 			}
							// 			if (
							// 				selectedPiece === 'b'
							// 					? blackPieces.includes(element)
							// 					: whitePieces.includes(element)
							// 			) {
							// 				break;
							// 			}
							// 			debug += element;
							// 		}
							// 		break;
							// 	case 'leftDown':
							// 		for (let index = selectedFrom.row - 1; index > -1; index--) {
							// 			const element = board[index][index];
							// 			if (element === null) {
							// 				maxDistance++;
							// 			}
							// 			if (
							// 				selectedPiece === 'b'
							// 					? whitePieces.includes(element)
							// 					: blackPieces.includes(element)
							// 			) {
							// 				maxDistance++;
							// 				break;
							// 			}
							// 			if (
							// 				selectedPiece === 'b'
							// 					? blackPieces.includes(element)
							// 					: whitePieces.includes(element)
							// 			) {
							// 				break;
							// 			}
							// 			debug += element;
							// 		}
							// 		break;

							default:
								break;
						}
						debug +=
							' distance: ' +
							distance +
							' max dsitance: ' +
							maxDistance +
							' direction: ' +
							bishopDirection; //debugging

						if (distance <= maxDistance) {
							board[rowIndex][colIndex] = selectedPiece;
							board[selectedFrom.row][selectedFrom.col] = null;
						}
					} else if (selectedPiece.toLowerCase() === 'q') {
						board[rowIndex][colIndex] = selectedPiece;
						board[selectedFrom.row][selectedFrom.col] = null;
					} else if (selectedPiece.toLowerCase() === 'k') {
						board[rowIndex][colIndex] = selectedPiece;
						board[selectedFrom.row][selectedFrom.col] = null;
					}
				}
			}}
			// Reset selection
			selectedPiece = null;
			selectedFrom = { row: null, col: null };
		} else {
			// If no piece is selected, select the current piece
			selectedPiece = board[rowIndex][colIndex];
			selectedFrom = { row: rowIndex, col: colIndex };
		}
	};
</script>

<main class="flex md:flex-row sm:flex-col items-center justify-center p-4 h-full">
	<!-- Chessboard with squares and pieces -->
	<div
		class="grid grid-cols-8 grid-rows-8 sm:w-full md:w-1/2 aspect-square border-2 border-gray-800"
	>
		{#each board as row, rowIndex}
			{#each row as piece, colIndex}
				<!-- svelte-ignore a11y-no-static-element-interactions -->
				<!-- svelte-ignore a11y-click-events-have-key-events -->
				<div
					class={`flex justify-center items-center w-full h-full cursor-pointer ${rowIndex % 2 === colIndex % 2 ? 'bg-[#f0d9b5]' : 'bg-[#b58863]'} ${selectedFrom.row === rowIndex && selectedFrom.col === colIndex ? 'border-4 border-violet-400' : ''}`}
					on:click={() => handleSquareClick(rowIndex, colIndex)}
				>
					{#if piece}
						<img src={`chess-pieces/${pieceSymbols[piece]}`} alt="chess piece" class="piece" />
					{/if}
				</div>
			{/each}
		{/each}
	</div>
	<div class=" sm:w-full md:w-1/2 aspect-square border-2 border-gray-800">
		<!-- Additional content or controls can go here -->
		<p>{debug}</p>
	</div>
</main>

<style>
	/* Ensure the chess pieces fit well within the squares */
	.piece {
		width: 100%; /* Scale pieces to fit within squares */
		height: auto; /* Maintain aspect ratio */
	}
</style>
