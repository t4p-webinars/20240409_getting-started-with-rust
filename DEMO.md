# Getting Started with Rust Demo

This demo will walk you through creating a simple Tic-Tac-Toe game in Rust. The game will be played in the terminal.

Learn More About Tic-tac-toe: [Wikipedia](https://en.wikipedia.org/wiki/Tic-tac-toe)

1. Let's define a player for the game.

    ```rust
    #[derive(Clone)]
    struct Player {
        name: String,
        mark: char,
    }

    impl Player {
        fn new(name: String, mark: char) -> Self {
            Self { name, mark }
        }
    }
    ```

2. Let's define a game board.

    ```rust
    struct Board {
        cells: [[char; 3]; 3],
    }

    impl Board {
        fn new() -> Self {
            Self {
                cells: [[' '; 3]; 3],
            }
        }
    }
    ```

3. Let's define a game.

    ```rust
    struct Game {
        player1: Player,
        player2: Player,
        board: Board,

    }

    impl Game {
        fn new() -> Self {
            Self {
                player1: Player::new("Player 1".to_string(), 'O'),
                player2: Player::new("Player 2".to_string(), 'X'),
                board: Board::new(),
            }
        }
    }
    ```

4. Let's update the `main` function to create a new game.

    ```rust
    fn main() {
        let game = Game::new();
    }
    ```

    Open a terminal window, and try to compile the code.
    
    ```bash
    cargo build
    ```

    It should fail with errors about unused variables and dead code. To turn this off for now, add the following attributes to the top of the file.

    ```rust
    #![allow(unused_variables)]
    #![allow(dead_code)]
    ```

    Now try to compile again. If should succeed. You can run the program although there is nothing to display yet.

    ```bash
    cargo run
    ```

5. Let's add a method to display the game board. Add the following code to the `Board` implementation.

    ```rust
    impl Game {

        // code omitted for brevity

        fn display_board(&self, player1_name: &str, player2_name: &str) {
            println!("\nO: {} vs X: {}", player1_name, player2_name);
            for row in self.cells.iter() {
                println!("+---+---+---+");
                print!("|");
                for &cell in row.iter() {
                    print!(" {} |", cell);
                }
                println!();
            }
            println!("+---+---+---+");
        }
    }
    ```

6. Let's update the `main` function to display the game board.

    ```rust
    fn main() {
        let game = Game::new();
        game.board.display_board(&game.player1.name, &game.player2.name);
    }
    ```

    Try compiling and running the program again with `cargo`.

7. Let's add the ability to set the player names. Add the following code to the `Game` implementation.

    ```rust
    impl Game {

        // code omitted for brevity

        fn get_player_name(&self, player_number: i32) -> String {
            let mut input = String::new();
            print!("Player {player_number}, enter your name: ");
            std::io::stdout().flush().unwrap();
            std::io::stdin().read_line(&mut input).unwrap();
            input.trim().to_string()
        }

        fn setup_player1_name(&mut self) {
            self.player1.name = self.get_player_name(1);
        }

        fn setup_player2_name(&mut self) {
            self.player2.name = self.get_player_name(2);
        }
    }
    ```

    Add the following `use` statement to the top of the file.

    ```rust
    use std::io::Write;
    ```

    Update the `main` function to set the player names.

    ```rust
    fn main() {
        let mut game = Game::new();

        game.setup_player1_name();
        game.setup_player2_name();

        game.board.display_board(&game.player1.name, &game.player2.name);
    }
    ```

8. Add the ability to for users to enter their move.


    ```rust
    impl Game {
        // code omitted for brevity

        fn get_player_input(&self, player_name: &str) -> (usize, usize) {
            let mut input = String::new();
            println!("Player {player_name}, enter row and column (0-2) separated by space: ");
            std::io::stdin().read_line(&mut input).unwrap();
            let mut parts = input.trim().split_whitespace();
            let row = parts.next().unwrap().parse().unwrap();
            let col = parts.next().unwrap().parse().unwrap();
            (row, col)
        }
    }
    ```

    Update the `main` function to get the player input.

    ```rust
    loop {
        let (row, col) = game.get_player_input(&game.player1.name);
        println!("Row: {}, Col: {}", row, col);
    }    
    ```


9. Build out the `Board` implementation.

    ```rust
    impl Board {

        // code omitted for brevity

        fn check_win(&self) -> Option<char> {

            let first_col_index = 0;
            let second_col_index = 1;
            let third_col_index = 2;

            let cells = self.cells;

            for row in cells.iter() {
                if row[first_col_index] != ' ' &&
                    row[first_col_index] == row[second_col_index] &&
                    row[second_col_index] == row[third_col_index] {
                    return Some(row[0]);
                }
            }
        
            let first_row = cells[0];
            let second_row = cells[1];
            let third_row = cells[2];

            for col_index in 0..3 {

                if first_row[col_index] != ' ' &&
                    first_row[col_index] == second_row[col_index] &&
                    second_row[col_index] == third_row[col_index] {
                    return Some(first_row[col_index]);
                }
            }
        
            if first_row[first_col_index] != ' ' &&
                first_row[first_col_index] == second_row[second_col_index] &&
                second_row[second_col_index] == third_row[third_col_index] {
                return Some(first_row[first_col_index]);
            }
        
            None
        }
        
        fn is_tie(&self) -> bool {
            for row in self.cells.iter() {
                for &cell in row.iter() {
                    if cell == ' ' {
                        return false;
                    }
                }
            }
        
            false
        }

        fn make_move(&mut self, row: usize, col: usize, mark: char) -> bool {
            if row >= 3 || col >= 3 || self.cells[row][col] != ' ' {
                return false;
            }
            self.cells[row][col] = mark;
            true
        }            
    }
    ```

10. Fully implement the methods for the `Game` struct.

    ```rust
    #[derive(PartialEq)]
    enum GameTurnPlayer {
        Player1,
        Player2,
    }

    enum GameStatus {
        InProgress,
        Tie,
        Win(Player),
    }

    struct Game {
        turn_player: GameTurnPlayer,

        // code omitted for brevity

    }

    impl Game {

        fn new() -> Self {
            Self {
                turn_player: GameTurnPlayer::Player1,
                player1: Player::new("Player 1".to_string(), 'O'),
                player2: Player::new("Player 2".to_string(), 'X'),
                board: Board::new(),
            }
        }      

        // code omitted for brevity

        fn process_turn(&mut self) -> GameStatus {
            self.board.display_board(&self.player1.name, &self.player2.name);
            let current_player = if self.turn_player == GameTurnPlayer::Player1 { &self.player1} else { &self.player2};
            let (row, col) = self.get_player_input(&current_player.name); 
            if self.board.make_move(row, col, current_player.mark) {
                if let Some(winner) = self.board.check_win() {
                    return GameStatus::Win(if self.player1.mark == winner
                        { self.player1.clone() } else { self.player2.clone() });
                }
                if self.board.is_tie() {
                    return GameStatus::Tie;
                }
                self.turn_player = if self.turn_player == GameTurnPlayer::Player1
                    { GameTurnPlayer::Player2 } else { GameTurnPlayer::Player1 };
                GameStatus::InProgress
            } else {
                println!("Invalid move. Try again.");
                GameStatus::InProgress
            }        
        }  
    }
    ```

11. Replace the `main` function to play the game.

    ```rust
    fn main() {

        let mut game = Game::new();
        game.setup_player1_name();
        game.setup_player2_name();

        loop {
            match game.process_turn() {
                GameStatus::InProgress => continue,
                GameStatus::Tie => {
                    println!("Tie!");
                    break;
                },
                GameStatus::Win(player) => {
                    println!("Congratulations, {}!", player.name);
                    break;
                }
            }
        }

    }
    ```