<script>
	// Defining the initial state of the board with images
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

	// Reactive store to hold the current board state
	let board = initialBoard;

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
				// Move the piece to the new location
				if (
					!(whitePieces.includes(selectedPiece)
						? whitePieces.includes(board[rowIndex][colIndex])
						: blackPieces.includes(board[rowIndex][colIndex]))
				) {
					if (selectedPiece.toLowerCase() === 'p') {
						//hvis det er bonde
						const pawnDirection = selectedPiece === 'p' ? 1 : -1; // Direction of pawn movement

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

						// let rookSpaceUp = 0;
						// const rookSpaceDown = 0;
						// const rookSpaceLeft = 0;
						// const rookSpaceRight = 0;
						// for (let index = selectedFrom.row; index < board[selectedFrom.col].length; index++) {
						//     const element = board[index][selectedFrom.col];
						//     if (selectedPiece==='r'){
						//         if (element===null){
						//             rookSpaceUp++;
						//         } else if (whitePieces.includes(element)){
						//             rookSpaceUp++;
						//             break;
						//         }
						//     } else {

						//     }

						// }
						board[rowIndex][colIndex] = selectedPiece;
						board[selectedFrom.row][selectedFrom.col] = null;
					} else if (selectedPiece.toLowerCase() === 'n') {
						board[rowIndex][colIndex] = selectedPiece;
						board[selectedFrom.row][selectedFrom.col] = null;
					} else if (selectedPiece.toLowerCase() === 'b') {
						board[rowIndex][colIndex] = selectedPiece;
						board[selectedFrom.row][selectedFrom.col] = null;
					} else if (selectedPiece.toLowerCase() === 'q') {
						board[rowIndex][colIndex] = selectedPiece;
						board[selectedFrom.row][selectedFrom.col] = null;
					} else if (selectedPiece.toLowerCase() === 'k') {
						board[rowIndex][colIndex] = selectedPiece;
						board[selectedFrom.row][selectedFrom.col] = null;
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

<main class="flex flex-row items-center justify-center p-4 h-full">
	<!-- Chessboard with squares and pieces -->
	<div class="grid grid-cols-8 grid-rows-8 w-1/2 aspect-square border-2 border-gray-800">
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
	<div>
		<!-- Additional content or controls can go here -->
	</div>
</main>

<style>
	/* Ensure the chess pieces fit well within the squares */
	.piece {
		width: 100%; /* Scale pieces to fit within squares */
		height: auto; /* Maintain aspect ratio */
	}
</style>
