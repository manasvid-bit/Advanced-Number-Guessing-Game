
Advanced console-based number guessing game in C featuring AI opponent mode, multiplayer gameplay, persistent leaderboards, statistics tracking, file handling, and interactive terminal UI.
---
Run Locally

```bash
gcc main.c -o game
./game
```
Features

Core Gameplay
- Random number generation
- Multiple difficulty levels
- Limited attempts system
- Smart hint system
- Dynamic score calculation
- Time tracking system
- Progress bar visualization
- Replay functionality

---

Difficulty Modes

| Difficulty | Range | Attempts |
|---|---|---|
| Easy | 1 – 50 | 15 |
| Medium | 1 – 100 | 10 |
| Hard | 1 – 500 | 8 |
| Extreme | 1 – 1000 | 6 |

---

Smart Hint System

The game provides intelligent hints such as:
- 🔥 Scorching hot / very close
- ❄️ Ice cold / far away
- Even/Odd hints
- Divisibility hints
- Number range segmentation
- Warmness detection based on distance

---

Score System

Score is dynamically calculated using:
- Difficulty level
- Number of attempts used
- Time taken to complete the game

---

AI Opponent Mode

Includes an AI opponent that uses:
- Binary Search Algorithm

The AI intelligently guesses the secret number using optimized logic.

---

Two Player Mode

Features:
- Player vs Player gameplay
- Hidden secret number system
- Competitive guessing mechanics

---

Statistics Dashboard

Tracks:
- Total games played
- Wins
- Losses
- Win rate
- Average attempts
- Best score

---

Leaderboard System

Features:
- Persistent high scores
- File handling using `scores.dat`
- Top player rankings
- Score sorting using Bubble Sort

---

Input Validation

The game safely handles:
- Invalid inputs
- Non-numeric inputs
- Out-of-range values
- Empty input handling

---

Cheat Detection

Detects:
- Repeated guesses
- Suspicious gameplay patterns

---

UI / UX Features

- ANSI color-based terminal interface
- ASCII art banners
- Dynamic progress bars
- Sound effects
- Clean menu system

---

Cross Platform Support

Supports:
- Windows
- Linux
- macOS

Implemented using conditional compilation:

```c
#ifdef _WIN32
```

---

Technologies & Libraries Used

## Standard Libraries

### `stdio.h`
Used for:
- Input/Output operations
- File handling

### `stdlib.h`
Used for:
- Random number generation
- Utility functions
- System commands

### `time.h`
Used for:
- Random seed generation
- Timer functionality

### `string.h`
Used for:
- String manipulation
- String handling functions

### `math.h`
Used for:
- Mathematical calculations

### `windows.h`
Used for:
- Windows beep sound effects

---

Programming Concepts Used

## Beginner Concepts
- Variables
- Loops
- Conditional statements
- Functions

## Intermediate Concepts
- Arrays
- Strings
- Structures
- File handling
- Modular programming
- Input validation

## Advanced Logic
- Binary Search Algorithm
- Bubble Sort
- Dynamic score calculation
- Statistics tracking

---

File Structure

```text
Advanced-Number-Guessing-Game
│
├── main.c
├── scores.dat
└── README.md
```

---

How to Compile & Run

## Using GCC

### Compile
```bash
gcc main.c -o game
```

### Run
```bash
./game
```

---

Preview

(Add screenshots here)

Example:

```md
![Game Screenshot](screenshot.png)
```

---

Future Improvements

Possible future upgrades:
- GUI version using SDL2 or Raylib
- Online multiplayer support
- Database integration
- Advanced AI learning system
- Configurable themes
- Achievements system
- Player profiles
- Save/Load game states

---

Project Level

This project demonstrates:
- Intermediate-level C programming
- Console application development
- Game logic design
- File persistence
- Data structures
- Algorithm implementation
- Cross-platform programming

---

Developed By

## Manasvi Dharnamoni

---

License

This project is open-source and free to use for learning purposes.
