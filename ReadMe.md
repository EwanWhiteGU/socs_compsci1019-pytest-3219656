# Introduction

`pytest` is a testing framework for Python that makes it easy to write simple and scalable automated tests, including numerical tolerance comparisons, exception verification, parameterized data-driven tests, isolated temporary file testing, and standard output capturing. For this task, you are required to implement several unit tests for a Card Game application using the `pytest` library.

The game tested is Twenty One [https://en.wikipedia.org/wiki/Blackjack](https://en.wikipedia.org/wiki/Blackjack) and [Example Twenty One](https://www.youtube.com/watch?v=zuZqw12MrVI).

# Instructions

- Fork this project and make sure your repository is public. Then import the project into your IDE.
- You cannot change the signature of the provided functions or rename them.
- You cannot interact with your peers: work individually and do your best.
- The unit tests to implement are described through a set of sub-tasks.
- Implement the unit tests one at a time, strictly following the order given by the sub-tasks. Do not read ahead to the following sub-tasks.
- A sub-task can be considered complete once you are confident that the unit test has been implemented correctly.
- Every time you complete a sub-task, commit and push your code.
- At the end of the assignment, make sure you have pushed the entire project to the public repository.

# Run Tests

python -m pytest test/ -v

# Sub-tasks

Remember to read and implement the sub-tasks one at a time, following the given order. Do not move on to the next sub-task until the current one has been completed. Implement the required unit tests by working in the `test/test_tasks.py` file.

In the Card Game application, cards are represented as two- or three-character strings where the first character is the suit (`"C"`, `"D"`, `"H"`, `"S"`) and the remaining characters represent the rank (`"2"`–`"10"`, `"J"`, `"Q"`, `"K"`, `"A"`), for example `"HA"` (Ace of Hearts) or `"D10"` (10 of Diamonds).

## Sub-task 1 – Test Bust Probability Calculation with Numerical Tolerance

To test the calculation of bust probability in Twenty One when drawing an additional card, verifying floating-point precision with tolerance.

### Requirement

- Implement unit test `test_calculate_bust_probability` in `test/test_tasks.py` to verify bust probability calculation.
- We want to test `calculate_bust_probability`, located in `card_game/card/game/TwentyOne/TwentyOne.py`. This function calculates the probability (as a float between 0.0 and 1.0) that drawing another card from the deck will cause the given hand to bust (exceed 21).
- Define a hand with cards `["H10", "D6"]` (total score of 16: face cards (Jack, Queen, King) count as 10; Aces count as 11, but are automatically adjusted to 1 if the total would otherwise exceed 21; all other cards count at their face value, from 2 to 10).
- Define a remaining deck of 12 cards: `["H2", "H3", "H4", "H5", "H6", "H7", "H8", "H9", "H10", "HJ", "HQ", "HK"]`. Out of these 12 cards, drawing 2, 3, 4, or 5 keeps the hand safe (scores 18, 19, 20, 21), while drawing 6, 7, 8, 9, 10, J, Q, or K causes the hand to bust (8 cards out of 12, resulting in a theoretical bust probability of 8 / 12 = 0.6666...).
- Call `calculate_bust_probability(hand, deck)` and assert that the returned probability matches `0.667` within an absolute tolerance of `0.01` using the specific `pytest` helper for approximate floating-point comparisons.
- Push sub-task to GitHub, [Go To GitHub Commit](#github-commit)

## Sub-task 2 – Verify Exception on Non-Existent Configuration File

To verify that attempting to open a non-existent configuration file properly raises an IOError exception.

### Requirement

- Implement unit test `test_create_csv_reader_invalid_path` in `test/test_tasks.py` to verify exception handling when reading a missing file.
- We want to test `create_csv_reader`, located in `card_game/LoadCSV.py`, which takes a configuration file path, attempts to open the file with `open(config_path, "r")`, and re-raises an `IOError` exception if the file cannot be found.
- Call `create_csv_reader` with a non-existent file path, such as `"non_existent_config.csv"`.
- Use the specific `pytest` exception-checking context manager to assert that calling `create_csv_reader("non_existent_config.csv")` raises an `IOError` exception.
- Push sub-task to GitHub, [Go To GitHub Commit](#github-commit)

## Sub-task 3 – Parameterized Testing of Hand Scoring Combinations

To verify multiple card combinations and edge cases in Twenty One hand scoring using a single parameterized unit test.

### Requirement

- Implement unit test `test_score_hand_combinations(hand, expected_score)` in `test/test_tasks.py` to test multiple hand scoring scenarios.
- We want to test `score_hand`, located in `card_game/card/game/TwentyOne/TwentyOne.py`, which computes the score of a hand of cards according to Blackjack rules (remember that face cards (Jack, Queen, King) count as 10; Aces count as 11, but are automatically adjusted to 1 if the total would otherwise exceed 21; all other cards count at their face value, from 2 to 10; and any hand totaling more than 21 is a bust and scores 0).
- Use the specific `pytest` parameterization decorator on `test_score_hand_combinations(hand, expected_score)` to supply the following 5 distinct hand combinations and their expected scores:
  1. Standard numeric hand: `["H7", "D8"]` with expected score `15`.
  2. Face card hand: `["HJ", "SQ"]` with expected score `20`.
  3. Soft Ace hand (Ace counted as 11): `["HA", "D9"]` with expected score `20`.
  4. Hard Ace adjustment (Ace reduced from 11 to 1 to prevent busting): `["HA", "HJ", "D5"]` with expected score `16`.
  5. Bust hand (total over 21 resets to 0): `["H10", "D8", "S5"]` with expected score `0`.
- Inside the unit test, call `score_hand(hand)` and assert that the returned score equals `expected_score` for each of the above-mentioned parameterized case.
- Push sub-task to GitHub, [Go To GitHub Commit](#github-commit)

## Sub-task 4 – Verify Player Display via Standard Output Capture

To verify that displaying a player prints the expected text to the console, by capturing standard output during the test.

### Requirement

- Implement unit test `test_display_player_console_output(capsys)` in `test/test_tasks.py` to verify console output without relying on manual terminal inspection.
- We want to test `show_players`, located in `card_game/Game.py`, which iterate over the global `players` list, printing each player's name and score to the console.
- Reset the global player state by calling `clear_players()` (from `card_game/Game.py`).
- Create a computer player named `"Bob"` using `create_player` (from `card_game/Player.py`) with hand `player[HAND] = ["H10", "S7"]` and score `player[SCORE] = 17`.
- Register the created player using `add_player` (from `card_game/Game.py`)
- Call `show_players()` to print the player to standard output.
- Use the specific built-in `pytest` fixture for capturing standard output (`capsys`) to read the captured console text.
- Assert that `"Bob with score: 17"` is present in the captured stdout.
- Push sub-task to GitHub, [Go To GitHub Commit](#github-commit)

## Sub-task 5 – Verify CSV Reader Creation with Isolated Temporary Files

To verify that `create_csv_reader` correctly opens a valid configuration file, using an isolated temporary file created during the test so no real project files are touched.

### Requirement

- Implement unit test `test_create_csv_reader_valid_data(tmp_path)` in `test/test_tasks.py` to verify reading valid configuration data from disk without modifying real project files.
- We want to test `create_csv_reader`, located in `card_game/LoadCSV.py`, which opens a target CSV file path for reading and returns an open file stream.
- Use pytest's built-in `tmp_path` fixture to dynamically create an isolated temporary file named `card_player.csv` containing these 3 lines: 

  ```text
  DEALER,Star Dealer
  COMPUTER,King Computer
  COMPUTER,Queen Computer
  ```

- Call `create_csv_reader` with the string path of the temporary file just created.
- Read and strip all lines from the returned file reader (closing the reader after reading).
- Assert that the total number of lines read is 3, and that the first line equals `"DEALER,Star Dealer"`.
- Push sub-task to GitHub, [Go To GitHub Commit](#github-commit)

# GitHub Commit
* Open VS Code Terminal
* git add *
* git commit -m "sub task x complete"
* git push
