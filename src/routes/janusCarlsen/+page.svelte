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
	let attackedByBlack = [
		[0, 1, 1, 1, 1, 1, 1, 0],
		[1, 1, 1, 1, 1, 1, 1, 1],
		[1, 1, 1, 1, 1, 1, 1, 1],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0]
	];
	let attackedByWhite = [
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0],
		[1, 1, 1, 1, 1, 1, 1, 1],
		[1, 1, 1, 1, 1, 1, 1, 1],
		[0, 1, 1, 1, 1, 1, 1, 0]
	];
	const blackPieces = ['p', 'q', 'k', 'b', 'n', 'r'];
	const whitePieces = ['P', 'Q', 'K', 'B', 'N', 'R'];
	let colorToMove = whitePieces;
	let board = initialBoard;
	let debug = 'Sjakken er ganske broken nå. Du kan ikke sette seg selv i sjakk, men du trenger ikke bry deg hvis du blir satt i sjakk heller. En passant og rokkade er ikke adda enda. De røde bokstavene indikerer hvilke felt som er angrepet av hvit -> W og sort -> B. <br><br> Det jeg mangler er altså:'+
	'<br>-->En passant <br>-->Rokkade <br>-->Arthur 1.1 <br>-->sjakk-matt ';
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

	let selectedPiece = null;
	let selectedFrom = { row: null, col: null };
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
		const flatBoard = board.flat();
		const whiteKingExists = flatBoard.includes('K');
		const blackKingExists = flatBoard.includes('k');
		return whiteKingExists && blackKingExists;
	}
	function isKingInCheck(tempBoard) {
		//send in tempboard to simulate moves that may end up in discovered check
		//the way it is made right now doesnt make sure that discovered checks are forbidden
		for (let i = 0; i < 8; i++) {
			for (let j = 0; j < 8; j++) {
				if (tempBoard[i][j] == 'k' && attackedByWhite[i][j] == 1) {
					return true;
				}
				if (tempBoard[i][j] == 'K' && attackedByBlack[i][j] == 1) {
					return true;
				}
			}
		}
	}

	async function startRandomGame() {
		while (containsKings()) {
			// Ensure game continues while both kings are on the board
			let pieceFound = false;
			let rand1 = 0,
				rand2 = 0;

			// Find a non-null piece on the board
			while (!pieceFound) {
				rand1 = Math.floor(Math.random() * 8);
				rand2 = Math.floor(Math.random() * 8);
				if (board[rand1][rand2] != null) {
					pieceFound = true; // Break the loop if a piece is found
				}
			}

			// Select a random destination
			let rand3 = Math.floor(Math.random() * 8);
			let rand4 = Math.floor(Math.random() * 8);

			// Execute moves
			handleSquareClick(rand1, rand2); // Select the piece
			handleSquareClick(rand3, rand4); // Try to move to the new location

			// page freezes without the sleep. sorry
			await sleep(1);
		}
	}

	function updateStrikeTable() {
		for (let i = 0; i < 8; i++) {
			for (let j = 0; j < 8; j++) {
				if (!board[i][j]) continue; // Skip empty squares

				//Iterate through alle the pieces on the board
				function updateStrikeTableForRooks() {
					// as a fucntion so that when i get to the queen i can call upon this function and the bishop function
					if (board[i][j] == board[i][j].toLowerCase()) {
						for (let k = j + 1; k < 8; k++) {
							if (board[i][k] != null) {
								attackedByBlack[i][k] = 1;
								break;
							} else {
								attackedByBlack[i][k] = 1;
							}
						}
						for (let k = j - 1; k > 0; k--) {
							if (board[i][k] != null) {
								attackedByBlack[i][k] = 1;
								break;
							} else {
								attackedByBlack[i][k] = 1;
							}
						}
						for (let k = i + 1; k < 8; k++) {
							if (board[k][j] != null) {
								attackedByBlack[k][j] = 1;
								break;
							} else {
								attackedByBlack[k][j] = 1;
							}
						}
						for (let k = i - 1; k > 0; k--) {
							if (board[k][j] != null) {
								attackedByBlack[k][j] = 1;
								break;
							} else {
								attackedByBlack[k][j] = 1;
							}
						}
					} else if (board[i][j] == board[i][j].toUpperCase()) {
						for (let k = j + 1; k < 8; k++) {
							if (board[i][k] != null) {
								attackedByWhite[i][k] = 1;
								break;
							} else {
								attackedByWhite[i][k] = 1;
							}
						}
						for (let k = j - 1; k > 0; k--) {
							if (board[i][k] != null) {
								attackedByWhite[i][k] = 1;
								break;
							} else {
								attackedByWhite[i][k] = 1;
							}
						}
						for (let k = i + 1; k < 8; k++) {
							if (board[k][j] != null) {
								attackedByWhite[k][j] = 1;
								break;
							} else {
								attackedByWhite[k][j] = 1;
							}
						}
						for (let k = i - 1; k > 0; k--) {
							if (board[k][j] != null) {
								attackedByWhite[k][j] = 1;
								break;
							} else {
								attackedByWhite[k][j] = 1;
							}
						}
					}
				}
				function updateStrikeTableForKnights() {
					//completed for knights
					if (board[i][j] == 'n') {
						const potPairs = [
							[i - 2, j - 1],
							[i - 1, j - 2],
							[i + 1, j - 2],
							[i + 2, j - 1],
							[i + 2, j + 1],
							[i + 1, j + 2],
							[i - 1, j + 2],
							[i - 2, j + 1]
						];

						for (let k = 0; k < 8; k++) {
							if (
								potPairs[k][0] > -1 &&
								potPairs[k][0] < 8 &&
								potPairs[k][1] > -1 &&
								potPairs[k][0] < 8
							) {
								attackedByBlack[potPairs[k][0]][potPairs[k][1]] = 1;
							}
						}
					} else if (board[i][j] == 'N') {
						const potPairs = [
							[i - 2, j - 1],
							[i - 1, j - 2],
							[i + 1, j - 2],
							[i + 2, j - 1],
							[i + 2, j + 1],
							[i + 1, j + 2],
							[i - 1, j + 2],
							[i - 2, j + 1]
						];

						for (let k = 0; k < 8; k++) {
							if (
								potPairs[k][0] > -1 &&
								potPairs[k][0] < 8 &&
								potPairs[k][1] > -1 &&
								potPairs[k][0] < 8
							) {
								attackedByWhite[potPairs[k][0]][potPairs[k][1]] = 1;
							}
						}
					}
				}
				function updateStrikeTableForBishops() {
					//completed
					if (board[i][j] == board[i][j].toLowerCase()) {
						for (let k = i + 1, p = j + 1; k < 8 && p < 8; k++, p++) {
							if (board[k][p] != null) {
								attackedByBlack[k][p] = 1;
								break;
							} else {
								attackedByBlack[k][p] = 1;
							}
						}
						for (let k = i + 1, p = j - 1; k < 8 && p > -1; k++, p--) {
							if (board[k][p] != null) {
								attackedByBlack[k][p] = 1;
								break;
							} else {
								attackedByBlack[k][p] = 1;
							}
						}
						for (let k = i - 1, p = j - 1; k > -1 && p > -1; k--, p--) {
							if (board[k][p] != null) {
								attackedByBlack[k][p] = 1;
								break;
							} else {
								attackedByBlack[k][p] = 1;
							}
						}
						for (let k = i - 1, p = j + 1; k > -1 && p < 8; k--, p++) {
							if (board[k][p] != null) {
								attackedByBlack[k][p] = 1;
								break;
							} else {
								attackedByBlack[k][p] = 1;
							}
						}
					} else {
						for (let k = i + 1, p = j + 1; k < 8 && p < 8; k++, p++) {
							if (board[k][p] != null) {
								attackedByWhite[k][p] = 1;
								break;
							} else {
								attackedByWhite[k][p] = 1;
							}
						}
						for (let k = i + 1, p = j - 1; k < 8 && p > -1; k++, p--) {
							if (board[k][p] != null) {
								attackedByWhite[k][p] = 1;
								break;
							} else {
								attackedByWhite[k][p] = 1;
							}
						}
						for (let k = i - 1, p = j - 1; k > -1 && p > -1; k--, p--) {
							if (board[k][p] != null) {
								attackedByWhite[k][p] = 1;
								break;
							} else {
								attackedByWhite[k][p] = 1;
							}
						}
						for (let k = i - 1, p = j + 1; k > -1 && p < 8; k--, p++) {
							if (board[k][p] != null) {
								attackedByWhite[k][p] = 1;
								break;
							} else {
								attackedByWhite[k][p] = 1;
							}
						}
					}
				}
				function updateStrikeTableForQueens() {
					//completed for queens
					updateStrikeTableForBishops();
					updateStrikeTableForRooks();
				}
				function updateStrikeTableForKings() {
					//completed
					//completed
					if (board[i][j] == board[i][j].toLowerCase()) {
						const potPairs = [
							[i + 1, j + 1],
							[i + 1, j],
							[i + 1, j - 1],
							[i, j - 1],
							[i - 1, j - 1],
							[i - 1, j],
							[i - 1, j + 1],
							[i, j + 1]
						];

						for (let k = 0; k < 8; k++) {
							if (
								potPairs[k][0] > -1 &&
								potPairs[k][0] < 8 &&
								potPairs[k][1] > -1 &&
								potPairs[k][0] < 8
							) {
								attackedByBlack[potPairs[k][0]][potPairs[k][1]] = 1;
							}
						}
					} else {
						const potPairs = [
							[i + 1, j + 1],
							[i + 1, j],
							[i + 1, j - 1],
							[i, j - 1],
							[i - 1, j - 1],
							[i - 1, j],
							[i - 1, j + 1],
							[i, j + 1]
						];

						for (let k = 0; k < 8; k++) {
							if (
								potPairs[k][0] > -1 &&
								potPairs[k][0] < 8 &&
								potPairs[k][1] > -1 &&
								potPairs[k][0] < 8
							) {
								attackedByWhite[potPairs[k][0]][potPairs[k][1]] = 1;
							}
						}
					}
				}
				function updateStrikeTableForPawns() {
					//completed

					if (board[i][j] == board[i][j].toLowerCase()) {
						const potPairs = [
							[i + 1, j + 1],
							[i + 1, j - 1]
						];

						for (let k = 0; k < 2; k++) {
							if (
								potPairs[k][0] > -1 &&
								potPairs[k][0] < 8 &&
								potPairs[k][1] > -1 &&
								potPairs[k][0] < 8
							) {
								attackedByBlack[potPairs[k][0]][potPairs[k][1]] = 1;
							}
						}
					} else {
						const potPairs = [
							[i - 1, j + 1],
							[i - 1, j - 1]
						];

						for (let k = 0; k < 2; k++) {
							if (
								potPairs[k][0] > -1 &&
								potPairs[k][0] < 8 &&
								potPairs[k][1] > -1 &&
								potPairs[k][0] < 8
							) {
								attackedByWhite[potPairs[k][0]][potPairs[k][1]] = 1;
							}
						}
					}
				}
				if (board[i][j].toLowerCase() == 'r') {
					updateStrikeTableForRooks();
				} else if (board[i][j].toLowerCase() == 'n') {
					updateStrikeTableForKnights();
				} else if (board[i][j].toLowerCase() == 'b') {
					updateStrikeTableForBishops();
				} else if (board[i][j].toLowerCase() == 'q') {
					updateStrikeTableForQueens();
				} else if (board[i][j].toLowerCase() == 'k') {
					updateStrikeTableForKings();
				} else if (board[i][j].toLowerCase() == 'p') {
					updateStrikeTableForPawns();
				}
			}
		}
	}

	// Function to handle square clicks
	const handleSquareClick = (rowIndex, colIndex) => {
		function movePiece() {
			//Promote to queen if pawn goes all the way
			let tempBoard = board;
			tempBoard[rowIndex][colIndex] = selectedPiece;
			tempBoard[selectedFrom.row][selectedFrom.col] = null;
			if (!isKingInCheck(board) && !isKingInCheck(tempBoard)) {
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
				updateStrikeTable();
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
										//debug += element;
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
										//debug += element;
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
										//debug += element;
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
										//debug += element;
									}
									break;

								default:
									break;
							}
							// debug +=
							// 	' distance: ' +
							// 	distance +
							// 	' max dsitance: ' +
							// 	maxDistance +
							// 	' direction: ' +
							// 	rookDirection; //debugging
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
										//debug += element;
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
										//debug += element;
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
										//debug += element;
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
										//debug += element;
									}
									break;

								default:
									break;
							}
							// debug +=
							// 	' distance: ' +
							// 	distance +
							// 	' max distance: ' +
							// 	maxDistance +
							// 	' direction: ' +
							// 	bishopDirection; //debugging

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
												//debug += element;
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
												//debug += element;
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
												//debug += element;
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
												//debug += element;
											}
											break;

										default:
											break;
									}
									// debug +=
									// 	' distance: ' +
									// 	distance +
									// 	' max distance: ' +
									// 	maxDistance +
									// 	' direction: ' +
									// 	queenDirection; //debugging

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
												//debug += element;
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
												//debug += element;
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
												//debug += element;
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
												// += element;
											}
											break;

										default:
											break;
									}
									// debug +=
									// 	' distance: ' +
									// 	distance +
									// 	' max dsitance: ' +
									// 	maxDistance +
									// 	' direction: ' +
									// 	queenDirection; //debugging

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
								if (selectedPiece === 'k' && attackedByWhite[rowIndex][colIndex] == 0) {
									movePiece();
								} else if (attackedByBlack[rowIndex][colIndex] == 0) {
									movePiece();
								}
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
					class={`relative flex justify-center items-center w-full h-full cursor-pointer ${rowIndex % 2 === colIndex % 2 ? 'bg-[#f0d9b5]' : 'bg-[#b58863]'} ${selectedFrom.row === rowIndex && selectedFrom.col === colIndex ? 'border-4 border-violet-400' : ''}`}
					on:click={() => handleSquareClick(rowIndex, colIndex)}
				> <!--diven har class relative for at barna under skal kunne ligge oppå-->
					{#if piece}
						<img src={`chess-pieces/${pieceSymbols[piece]}`} alt="chess piece" class="piece" />
					{/if}
					<!--For å fjerne bokstavene slett det HERFRA til -->
					<div class="absolute inset-0 flex items-center justify-center pointer-events-none">
						{#if attackedByBlack[rowIndex][colIndex] || attackedByWhite[rowIndex][colIndex]}
							<span class="text-red-600 text-xl font-bold">
								{attackedByBlack[rowIndex][colIndex] && attackedByWhite[rowIndex][colIndex]
									? 'B/W'
									: attackedByBlack[rowIndex][colIndex]
										? 'B'
										: attackedByWhite[rowIndex][colIndex]
											? 'W'
											: ''}
							</span>
						{/if}
					</div>
					<!--HIT-->
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
		<p>{@html debug}</p>
	</div>
</main>

<style>
	/* Ensure the chess pieces fit well within the squares */
	.piece {
		width: 100%; /* Scale pieces to fit within squares */
		height: auto; /* Maintain aspect ratio */
	}
</style>
