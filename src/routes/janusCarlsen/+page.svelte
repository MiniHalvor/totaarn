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
	let colorToMove = whitePieces;
	function changeTurns() {
		if (colorToMove[0] == whitePieces[0]) {
			colorToMove = blackPieces;
		} else {
			colorToMove = whitePieces;
		}
	}
	function sleep(ms) {
		return new Promise((resolve) => setTimeout(resolve, ms));
	}
	function containsKings() {
		// Flatten the board array and check for both kings
		const flatBoard = board.flat();
		const whiteKingExists = flatBoard.includes('K');
		const blackKingExists = flatBoard.includes('k');
		return whiteKingExists && blackKingExists;
	}
	async function startRandomGame() {
		while (containsKings()) {
			let rand1 = Math.floor(Math.random() * 8);
			let rand2 = Math.floor(Math.random() * 8);
			let rand3 = Math.floor(Math.random() * 8);
			let rand4 = Math.floor(Math.random() * 8);

			handleSquareClick(rand1, rand2);
			handleSquareClick(rand3, rand4);
			await sleep(0.0005);
		}
	}

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
		function movePiece() {
			if (selectedPiece === 'p' && rowIndex % 7 === 0) {
				board[rowIndex][colIndex] = 'q';
				board[selectedFrom.row][selectedFrom.col] = null;
				changeTurns();
			} else if (selectedPiece === 'P' && rowIndex % 7 === 0) {
				board[rowIndex][colIndex] = 'Q';
				board[selectedFrom.row][selectedFrom.col] = null;
				changeTurns();
			} else {
				board[rowIndex][colIndex] = selectedPiece;
				board[selectedFrom.row][selectedFrom.col] = null;
				changeTurns();
			}

			
			
		}
		// If a piece is already selected, move it to the new square
		if (colorToMove.includes(selectedPiece)) {
			// Check if the piece is being moved to a different square
			if (selectedFrom.row !== rowIndex || selectedFrom.col !== colIndex) {
				const targetPiece = board[rowIndex][colIndex];
				if (targetPiece && targetPiece.toLowerCase() === 'k') {
					console.log('cannot capture king');
				} else {
					// checks if the piece it moves to
					if (
						!(whitePieces.includes(selectedPiece)
							? whitePieces.includes(board[rowIndex][colIndex])
							: blackPieces.includes(board[rowIndex][colIndex]))
					) {
						if (selectedPiece.toLowerCase() === 'p') {
							//if pawn
							const pawnDirection = selectedPiece === 'p' ? 1 : -1;

							const isFirstMove = selectedFrom.row % 5 === 1;
							const pawnDistance = (rowIndex - selectedFrom.row) * pawnDirection;
							const pawnIsBlocked =
								board[selectedFrom.row + 1 * pawnDirection * pawnDistance][selectedFrom.col] !=
								null;
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
								movePiece();
							}
						} else if (selectedPiece.toLowerCase() === 'r') {
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
								movePiece();
							}
						} else if (selectedPiece.toLowerCase() === 'n') {
							let distanceX = Math.abs(colIndex - selectedFrom.col);
							let distanceY = Math.abs(rowIndex - selectedFrom.row);
							let distance = Math.max(distanceX, distanceY);

							if (distance === 2 && distanceX != distanceY && distanceX != 0 && distanceY != 0) {
								movePiece();
							}
						} else if (selectedPiece.toLowerCase() === 'b') {
							let maxDistance = 0;
							let bishopDirection =
								colIndex < selectedFrom.col
									? rowIndex < selectedFrom.row
										? 'leftUp'
										: 'leftDown'
									: rowIndex < selectedFrom.row
										? 'rightUp'
										: 'rightDown';

							let distanceX = Math.abs(colIndex - selectedFrom.col);
							let distanceY = Math.abs(rowIndex - selectedFrom.row);
							let distance = Math.max(distanceX, distanceY);
							switch (bishopDirection) {
								case 'leftUp':
									for (
										let indexRow = selectedFrom.row - 1, indexCol = selectedFrom.col - 1;
										indexRow > -1 && indexCol > -1;
										indexRow--, indexCol--
									) {
										const element = board[indexRow][indexCol];
										if (element === null) {
											maxDistance++;
										} else if (
											selectedPiece === 'b'
												? whitePieces.includes(element)
												: blackPieces.includes(element)
										) {
											maxDistance++;
											break;
										} else if (
											selectedPiece === 'b'
												? blackPieces.includes(element)
												: whitePieces.includes(element)
										) {
											break;
										}
										debug += element;
									}
									break;
								case 'rightUp':
									for (
										let indexRow = selectedFrom.row - 1, indexCol = selectedFrom.col + 1;
										indexRow > -1 && indexCol < 8;
										indexRow--, indexCol++
									) {
										const element = board[indexRow][indexCol];
										if (element === null) {
											maxDistance++;
										} else if (
											selectedPiece === 'b'
												? whitePieces.includes(element)
												: blackPieces.includes(element)
										) {
											maxDistance++;
											break;
										} else if (
											selectedPiece === 'b'
												? blackPieces.includes(element)
												: whitePieces.includes(element)
										) {
											break;
										}
										debug += element;
									}
									break;
								case 'leftDown':
									for (
										let indexRow = selectedFrom.row + 1, indexCol = selectedFrom.col - 1;
										indexRow < 8 && indexCol > -1;
										indexRow++, indexCol--
									) {
										const element = board[indexRow][indexCol];
										if (element === null) {
											maxDistance++;
										} else if (
											selectedPiece === 'b'
												? whitePieces.includes(element)
												: blackPieces.includes(element)
										) {
											maxDistance++;
											break;
										} else if (
											selectedPiece === 'b'
												? blackPieces.includes(element)
												: whitePieces.includes(element)
										) {
											break;
										}
										debug += element;
									}
									break;
								case 'rightDown':
									for (
										let indexRow = selectedFrom.row + 1, indexCol = selectedFrom.col + 1;
										indexRow < 8 && indexCol < 8;
										indexRow++, indexCol++
									) {
										const element = board[indexRow][indexCol];
										if (element === null) {
											maxDistance++;
										} else if (
											selectedPiece === 'b'
												? whitePieces.includes(element)
												: blackPieces.includes(element)
										) {
											maxDistance++;
											break;
										} else if (
											selectedPiece === 'b'
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
								' max distance: ' +
								maxDistance +
								' direction: ' +
								bishopDirection; //debugging

							if (distance <= maxDistance && distanceX === distanceY) {
								movePiece();
							}
						} else if (selectedPiece.toLowerCase() === 'q') {
							let distanceX = Math.abs(colIndex - selectedFrom.col);
							let distanceY = Math.abs(rowIndex - selectedFrom.row);
							let distance = Math.max(distanceX, distanceY);

							if (distanceX === distanceY || distanceX === 0 || distanceY === 0) {
								if (distanceX === distanceY) {
									let maxDistance = 0;
									let queenDirection =
										colIndex < selectedFrom.col
											? rowIndex < selectedFrom.row
												? 'leftUp'
												: 'leftDown'
											: rowIndex < selectedFrom.row
												? 'rightUp'
												: 'rightDown';

									switch (queenDirection) {
										case 'leftUp':
											for (
												let indexRow = selectedFrom.row - 1, indexCol = selectedFrom.col - 1;
												indexRow > -1 && indexCol > -1;
												indexRow--, indexCol--
											) {
												const element = board[indexRow][indexCol];
												if (element === null) {
													maxDistance++;
												} else if (
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												} else if (
													selectedPiece === 'q'
														? blackPieces.includes(element)
														: whitePieces.includes(element)
												) {
													break;
												}
												debug += element;
											}
											break;
										case 'rightUp':
											for (
												let indexRow = selectedFrom.row - 1, indexCol = selectedFrom.col + 1;
												indexRow > -1 && indexCol < 8;
												indexRow--, indexCol++
											) {
												const element = board[indexRow][indexCol];
												if (element === null) {
													maxDistance++;
												} else if (
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												} else if (
													selectedPiece === 'q'
														? blackPieces.includes(element)
														: whitePieces.includes(element)
												) {
													break;
												}
												debug += element;
											}
											break;
										case 'leftDown':
											for (
												let indexRow = selectedFrom.row + 1, indexCol = selectedFrom.col - 1;
												indexRow < 8 && indexCol > -1;
												indexRow++, indexCol--
											) {
												const element = board[indexRow][indexCol];
												if (element === null) {
													maxDistance++;
												} else if (
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												} else if (
													selectedPiece === 'q'
														? blackPieces.includes(element)
														: whitePieces.includes(element)
												) {
													break;
												}
												debug += element;
											}
											break;
										case 'rightDown':
											for (
												let indexRow = selectedFrom.row + 1, indexCol = selectedFrom.col + 1;
												indexRow < 8 && indexCol < 8;
												indexRow++, indexCol++
											) {
												const element = board[indexRow][indexCol];
												if (element === null) {
													maxDistance++;
												} else if (
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												} else if (
													selectedPiece === 'q'
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
										' max distance: ' +
										maxDistance +
										' direction: ' +
										queenDirection; //debugging

									if (distance <= maxDistance && distanceX === distanceY) {
										movePiece();
									}
								} else {
									let maxDistance = 0;
									let queenDirection =
										selectedFrom.row === rowIndex
											? colIndex < selectedFrom.col //if on the same row
												? 'left'
												: 'right'
											: rowIndex < selectedFrom.row //if on the same column
												? 'up'
												: 'down';

									switch (queenDirection) {
										case 'left':
											for (let index = selectedFrom.col - 1; index > -1; index--) {
												const element = board[selectedFrom.row][index];
												if (element === null) {
													maxDistance++;
												}
												if (
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												}
												if (
													selectedPiece === 'q'
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
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												}
												if (
													selectedPiece === 'q'
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
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												}
												if (
													selectedPiece === 'q'
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
													selectedPiece === 'q'
														? whitePieces.includes(element)
														: blackPieces.includes(element)
												) {
													maxDistance++;
													break;
												}
												if (
													selectedPiece === 'q'
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
										queenDirection; //debugging

									if (distance <= maxDistance) {
										movePiece();
									}
								}
							}
						} else if (selectedPiece.toLowerCase() === 'k') {
							let distanceX = Math.abs(colIndex - selectedFrom.col);
							let distanceY = Math.abs(rowIndex - selectedFrom.row);
							let distance = Math.max(distanceX, distanceY);

							if (distance < 2) {
								movePiece();
							}
						}
					}
				}
			}
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

<main class="flex md:flex-row flex-col items-center justify-center p-4 h-full">
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
		<button
			class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
			on:click={startRandomGame}>Se Arthur spille!</button
		>
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
