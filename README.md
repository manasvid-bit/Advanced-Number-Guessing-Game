/*
 * ================================================
 *         ADVANCED NUMBER GUESSING GAME
 *         Developed by: MANASVI DHARNAMONI
 *         Upgraded: Beginner -> Intermediate
 * ================================================
 */

#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>
#include <math.h>

#ifdef _WIN32
  #include <windows.h>
  #define CLEAR "cls"
  #define BEEP_WIN 1
#else
  #define CLEAR "clear"
  #define BEEP_WIN 0
#endif

/* â”€â”€ ANSI Colors â”€â”€ */
#define RESET   "\033[0m"
#define BOLD    "\033[1m"
#define RED     "\033[1;31m"
#define GREEN   "\033[1;32m"
#define YELLOW  "\033[1;33m"
#define CYAN    "\033[1;36m"
#define MAGENTA "\033[1;35m"
#define WHITE   "\033[1;37m"
#define BLUE    "\033[1;34m"

/* â”€â”€ Difficulty settings â”€â”€ */
#define MAX_NAME   50
#define MAX_SCORES 10

typedef struct {
    char   name[MAX_NAME];
    int    score;
    int    attempts;
    long   timeTaken;
    char   difficulty[10];
} Player;

/* â”€â”€ Globals â”€â”€ */
Player leaderboard[MAX_SCORES];
int    leaderboardCount = 0;
int    totalGames  = 0;
int    totalWins   = 0;
int    totalLosses = 0;
long   totalAttempts = 0;
int    bestScore   = 0;

/* â”€â”€ Function Prototypes â”€â”€ */
void   intro();
void   printBanner();
void   printSeparator(char c, int len);
int    chooseDifficulty(int *limit, int *maxAttempts);
int    generateRandom(int limit);
void   showHint(int random, int guess, int limit);
int    calculateScore(int difficulty, int attemptsUsed, int maxAttempts, long seconds);
void   playGame(char *playerName);
void   aiOpponentMode(char *playerName);
void   twoPlayerMode();
void   saveHighScore(Player p);
void   showLeaderboard();
void   showStats();
void   sortLeaderboard();
int    validateInput(int *value, int low, int high);
void   flushInput();
void   menu(char *playerName);
void   loadHighScores();
void   beepSound(int freq, int dur);
void   printProgress(int current, int max);

/* ================================================================
   MAIN
   ================================================================ */
int main() {
    char playerName[MAX_NAME];
    srand((unsigned int)time(NULL));
    loadHighScores();
    intro();

    printf(CYAN "\n  Enter your name: " RESET);
    fgets(playerName, MAX_NAME, stdin);
    playerName[strcspn(playerName, "\n")] = '\0';
    if (strlen(playerName) == 0) strcpy(playerName, "Player");

    menu(playerName);
    return 0;
}

/* ================================================================
   INTRO / BANNER
   ================================================================ */
void intro() {
    system(CLEAR);
    printf(GREEN);
    printSeparator('=', 52);
    printf("\n");
    printf("   ###  ##  ## ##  ##  ## ###  #### ###  ###  \n");
    printf("  ## ## ##  ## ### ## ##  ## # ##   ##  ##  # \n");
    printf("  ##  # ##  ## ## ### ##  ###  ###  ##  ##    \n");
    printf("  ## ## ##  ## ##  # ##  ## # ##    ##  ##  # \n");
    printf("   ###   ####  ##  ##  ## ##  #### ###  ###   \n");
    printf("\n");
    printSeparator('=', 52);
    printf("\n");
    printf(YELLOW "        A D V A N C E D   E D I T I O N\n" RESET);
    printf(GREEN);
    printSeparator('=', 52);
    printf(RESET "\n");
    printf(WHITE "         Developed by: MANASVI DHARNAMONI\n" RESET);
    printSeparator('-', 52);
    printf(RESET "\n");
}

void printBanner() {
    printf(GREEN);
    printSeparator('=', 52);
    printf("\n   NUMBER GUESSING GAME â€” ADVANCED EDITION\n");
    printSeparator('=', 52);
    printf(RESET "\n");
}

void printSeparator(char c, int len) {
	int i;
    for (i = 0; i < len; i++) 
	printf("%c", c);
    printf("\n");
}

/* ================================================================
   MENU
   ================================================================ */
void menu(char *playerName) {
    int choice;
    char again = 'y';

    while (again == 'y' || again == 'Y') {
        system(CLEAR);
        printBanner();
        printf(CYAN "  Welcome, %s!\n\n" RESET, playerName);
        printf(WHITE "  [1]" RESET " Play â€” Single Player\n");
        printf(WHITE "  [2]" RESET " Play â€” Two Player Mode\n");
        printf(WHITE "  [3]" RESET " Play â€” vs AI Opponent\n");
        printf(WHITE "  [4]" RESET " Leaderboard\n");
        printf(WHITE "  [5]" RESET " Statistics\n");
        printf(WHITE "  [6]" RESET " Exit\n\n");
        printSeparator('-', 52);
        printf(CYAN "  Choose an option: " RESET);

        if (!validateInput(&choice, 1, 6)) { flushInput(); continue; }

        switch (choice) {
            case 1: playGame(playerName);           break;
            case 2: twoPlayerMode();                break;
            case 3: aiOpponentMode(playerName);     break;
            case 4: showLeaderboard();              break;
            case 5: showStats();                    break;
            case 6:
                printf(GREEN "\n  Thanks for playing, %s! Goodbye!\n" RESET, playerName);
                printf(WHITE "  Developed by: MANASVI DHARNAMONI\n\n" RESET);
                exit(0);
        }

        printf(YELLOW "\n  Return to main menu? (y/n): " RESET);
        scanf(" %c", &again);
        flushInput();
    }
}

/* ================================================================
   CHOOSE DIFFICULTY
   ================================================================ */
int chooseDifficulty(int *limit, int *maxAttempts) {
    int choice;
    printf(CYAN "\n  -- SELECT DIFFICULTY --\n\n" RESET);
    printf(GREEN  "  [1] Easy     " RESET "â€” 1 to 50,   15 attempts\n");
    printf(YELLOW "  [2] Medium   " RESET "â€” 1 to 100,  10 attempts\n");
    printf(MAGENTA"  [3] Hard     " RESET "â€” 1 to 500,   8 attempts\n");
    printf(RED    "  [4] Extreme  " RESET "â€” 1 to 1000,  6 attempts\n\n");
    printSeparator('-', 52);
    printf(CYAN "  Your choice: " RESET);

    if (!validateInput(&choice, 1, 4)) return 0;

    switch (choice) {
        case 1: *limit = 50;   *maxAttempts = 15; return 1;
        case 2: *limit = 100;  *maxAttempts = 10; return 2;
        case 3: *limit = 500;  *maxAttempts = 8;  return 3;
        case 4: *limit = 1000; *maxAttempts = 6;  return 4;
    }
    return 1;
}

/* ================================================================
   GENERATE RANDOM NUMBER
   ================================================================ */
int generateRandom(int limit) {
    return rand() % limit + 1;
}

/* ================================================================
   HINT SYSTEM
   ================================================================ */
void showHint(int random, int guess, int limit) {
    int diff = abs(random - guess);
    int pct  = (diff * 100) / limit;

    printf(YELLOW "\n  HINTS:\n" RESET);

    if      (pct <= 2)  printf(MAGENTA "  â˜… SCORCHING HOT! Extremely close!\n" RESET);
    else if (pct <= 5)  printf(RED     "  â˜… Very close!\n" RESET);
    else if (pct <= 15) printf(YELLOW  "  â˜… Getting warmer...\n" RESET);
    else if (pct <= 30) printf(CYAN    "  â˜… Lukewarm zone.\n" RESET);
    else                printf(BLUE    "  â˜… Ice cold â€” too far away!\n" RESET);

    if (diff % 5 == 0)  printf(WHITE   "  â˜… The difference is divisible by 5.\n" RESET);
    if (diff % 10 == 0) printf(WHITE   "  â˜… The difference is divisible by 10.\n" RESET);
    if (random % 2 == 0) printf(WHITE  "  â˜… The secret number is EVEN.\n" RESET);
    else                 printf(WHITE  "  â˜… The secret number is ODD.\n" RESET);

    int band = (limit / 5);
    int low  = ((random - 1) / band) * band + 1;
    int high = low + band - 1;
    if (high > limit) high = limit;
    printf(WHITE "  â˜… Number lies between %d and %d.\n" RESET, low, high);
}

/* ================================================================
   SCORE CALCULATION
   ================================================================ */
int calculateScore(int difficulty, int attemptsUsed, int maxAttempts, long seconds) {
    int base = 100 * difficulty;
    int penaltyAttempts = (attemptsUsed - 1) * (10 * difficulty);
    int penaltyTime = (int)(seconds / 5) * 2;
    int score = base - penaltyAttempts - penaltyTime;
    if (score < 0) score = 0;
    return score;
}

/* ================================================================
   PROGRESS BAR
   ================================================================ */
void printProgress(int current, int max) {
    int filled = (current * 20) / max;
    printf("  Attempts [");
    int i;
    for (i = 0; i < 20; i++) {
        if (i < filled) printf(RED "#" RESET);
        else            printf(GREEN "-" RESET);
    }
    printf("] %d/%d\n", current, max);
}

/* ================================================================
   SINGLE PLAYER GAME
   ================================================================ */
void playGame(char *playerName) {
    int limit, maxAttempts, difficulty;
    int random, guess, attemptsUsed = 0;
    int score;
    time_t start, end;
    char diffName[10];

    system(CLEAR);
    printBanner();
    difficulty = chooseDifficulty(&limit, &maxAttempts);
    if (difficulty == 0) return;

    switch (difficulty) {
        case 1: strcpy(diffName, "Easy");    break;
        case 2: strcpy(diffName, "Medium");  break;
        case 3: strcpy(diffName, "Hard");    break;
        case 4: strcpy(diffName, "Extreme"); break;
        default: strcpy(diffName, "Medium");
    }

    random = generateRandom(limit);
    start  = time(NULL);

    printf(GREEN "\n  Game started! Guess a number between 1 and %d.\n" RESET, limit);
    printf(WHITE "  You have %d attempts. Good luck!\n\n" RESET, maxAttempts);

    while (attemptsUsed < maxAttempts) {
        int remaining = maxAttempts - attemptsUsed;
        printProgress(attemptsUsed, maxAttempts);

        if (remaining <= 2)
            printf(RED   "  âš   Only %d attempt(s) left!\n" RESET, remaining);

        printf(CYAN "\n  [%d/%d] Enter guess (1-%d): " RESET,
               attemptsUsed + 1, maxAttempts, limit);

        if (!validateInput(&guess, 1, limit)) {
            printf(RED "  Invalid input. Try again.\n" RESET);
            flushInput();
            continue;
        }

        attemptsUsed++;

        /* Cheat detection: repeated identical guess */
        static int lastGuess = -1;
        static int repeatCount = 0;
        if (guess == lastGuess) {
            repeatCount++;
            if (repeatCount >= 2)
                printf(MAGENTA "  âš   Cheat alert: stop repeating the same guess!\n" RESET);
        } else {
            repeatCount = 0;
        }
        lastGuess = guess;

        if (guess == random) {
            end = time(NULL);
            long seconds = (long)(end - start);
            score = calculateScore(difficulty, attemptsUsed, maxAttempts, seconds);

            beepSound(1000, 500);
            printf(GREEN "\n");
            printSeparator('*', 52);
            printf("  CONGRATULATIONS %s!\n", playerName);
            printf("  You guessed %d correctly!\n", random);
            printSeparator('*', 52);
            printf(RESET);
            printf(WHITE "  Attempts used : %d / %d\n" RESET, attemptsUsed, maxAttempts);
            printf(WHITE "  Time taken    : %ld seconds\n" RESET, seconds);
            printf(WHITE "  Score         : " GREEN "%d points\n" RESET, score);
            printf(WHITE "  Difficulty    : %s\n\n" RESET, diffName);

            totalWins++;
            totalGames++;
            totalAttempts += attemptsUsed;
            if (score > bestScore) bestScore = score;

            Player p;
            strncpy(p.name, playerName, MAX_NAME);
            p.score     = score;
            p.attempts  = attemptsUsed;
            p.timeTaken = seconds;
            strncpy(p.difficulty, diffName, 10);
            saveHighScore(p);
            return;

        } else {
            if (guess < random)
                printf(RED "\n  â–² Too low! Guess HIGHER.\n" RESET);
            else
                printf(BLUE "\n  â–¼ Too high! Guess LOWER.\n" RESET);

            showHint(random, guess, limit);
        }
    }

    /* Game over */
    end = time(NULL);
    beepSound(300, 800);
    totalLosses++;
    totalGames++;
    totalAttempts += attemptsUsed;

    printf(RED "\n");
    printSeparator('!', 52);
    printf("  GAME OVER! You ran out of attempts.\n");
    printf("  The secret number was: %d\n", random);
    printSeparator('!', 52);
    printf(RESET "\n");
}

/* ================================================================
   TWO PLAYER MODE
   ================================================================ */
void twoPlayerMode() {
    char p1[MAX_NAME], p2[MAX_NAME];
    int secret, guess, limit = 100, maxAttempts = 10, attempts = 0;

    system(CLEAR);
    printBanner();
    printf(CYAN "\n  TWO PLAYER MODE\n\n" RESET);
    printf("  Player 1, enter your name: ");
    fgets(p1, MAX_NAME, stdin);
    p1[strcspn(p1, "\n")] = '\0';

    printf("  Player 2, enter your name: ");
    fgets(p2, MAX_NAME, stdin);
    p2[strcspn(p2, "\n")] = '\0';

    printf(YELLOW "\n  %s â€” enter a secret number (1-%d): " RESET, p1, limit);
    if (!validateInput(&secret, 1, limit)) { flushInput(); return; }

    /* Clear screen so P2 can't see */
    system(CLEAR);
    printBanner();
    printf(CYAN "\n  %s â€” guess the number (1-%d)!\n" RESET, p2, limit);
    printf(WHITE "  You have %d attempts.\n\n" RESET, maxAttempts);

    time_t start = time(NULL);
    while (attempts < maxAttempts) {
        printProgress(attempts, maxAttempts);
        printf(CYAN "\n  [%d/%d] Your guess: " RESET, attempts + 1, maxAttempts);
        if (!validateInput(&guess, 1, limit)) { flushInput(); continue; }
        attempts++;

        if (guess == secret) {
            time_t end = time(NULL);
            int score = calculateScore(2, attempts, maxAttempts, (long)(end - start));
            printf(GREEN "\n  WELL DONE %s! You guessed it in %d attempts!\n" RESET, p2, attempts);
            printf(WHITE "  Score: %d\n\n" RESET, score);

            Player p;
            strncpy(p.name, p2, MAX_NAME);
            p.score = score; p.attempts = attempts;
            p.timeTaken = (long)(end - start);
            strcpy(p.difficulty, "2P");
            saveHighScore(p);
            totalWins++; totalGames++;
            return;
        } else {
            if (guess < secret) printf(RED   "  â–² Too low!\n"  RESET);
            else                printf(BLUE  "  â–¼ Too high!\n" RESET);
            showHint(secret, guess, limit);
        }
    }
    printf(RED "\n  %s ran out of attempts! The number was %d.\n" RESET, p2, secret);
    totalLosses++; totalGames++;
}

/* ================================================================
   AI OPPONENT MODE  (binary search strategy)
   ================================================================ */
void aiOpponentMode(char *playerName) {
    int limit, maxAttempts, difficulty;
    int random, humanGuess, aiLow, aiHigh, aiGuess;
    int humanAttempts = 0, aiAttempts = 0;

    system(CLEAR);
    printBanner();
    printf(CYAN "\n  AI OPPONENT MODE\n  You vs the Machine!\n\n" RESET);

    difficulty = chooseDifficulty(&limit, &maxAttempts);
    if (difficulty == 0) return;

    random = generateRandom(limit);
    aiLow  = 1;
    aiHigh = limit;

    printf(GREEN "\n  Guess the number between 1 and %d.\n" RESET, limit);
    printf(WHITE "  You have %d attempts. AI has unlimited (benchmark).\n\n" RESET, maxAttempts);

    time_t start = time(NULL);

    while (humanAttempts < maxAttempts) {
        printProgress(humanAttempts, maxAttempts);
        printf(CYAN "\n  [%d/%d] Your guess (1-%d): " RESET, humanAttempts + 1, maxAttempts, limit);
        if (!validateInput(&humanGuess, 1, limit)) { flushInput(); continue; }
        humanAttempts++;

        /* AI binary search guess */
        aiAttempts++;
        aiGuess = (aiLow + aiHigh) / 2;
        printf(MAGENTA "  AI guesses: %d\n" RESET, aiGuess);

        if (aiGuess == random) {
            printf(RED "\n  AI found it in %d guesses!\n" RESET, aiAttempts);
        } else if (aiGuess < random) aiLow  = aiGuess + 1;
        else                         aiHigh = aiGuess - 1;

        if (humanGuess == random) {
            time_t end = time(NULL);
            int score = calculateScore(difficulty, humanAttempts, maxAttempts, (long)(end - start));
            printf(GREEN "\n  YOU WIN! Found it in %d attempts (AI took %d).\n" RESET,
                   humanAttempts, aiAttempts);
            printf(WHITE "  Score: %d\n\n" RESET, score);

            Player p;
            strncpy(p.name, playerName, MAX_NAME);
            p.score = score; p.attempts = humanAttempts;
            p.timeTaken = (long)(end - start);
            strcpy(p.difficulty, "AI");
            saveHighScore(p);
            totalWins++; totalGames++;
            return;
        } else {
            if (humanGuess < random) printf(RED  "  â–² Too low!\n"  RESET);
            else                     printf(BLUE "  â–¼ Too high!\n" RESET);
            showHint(random, humanGuess, limit);
        }
    }
    printf(RED "\n  Out of attempts! The number was %d. AI found it in %d.\n" RESET,
           random, aiAttempts);
    totalLosses++; totalGames++;
}

/* ================================================================
   SAVE HIGH SCORE (File Handling)
   ================================================================ */
void saveHighScore(Player p) {
    if (leaderboardCount < MAX_SCORES) {
        leaderboard[leaderboardCount++] = p;
    } else {
        /* Replace lowest score */
        int min = 0;
        int i;
        for (i = 1; i < MAX_SCORES; i++)
            if (leaderboard[i].score < leaderboard[min].score) min = i;
        if (p.score > leaderboard[min].score)
            leaderboard[min] = p;
    }
    sortLeaderboard();

    FILE *fp = fopen("scores.dat", "w");
    if (!fp) { printf(RED "  Could not save score file.\n" RESET); return; }
    fprintf(fp, "%d\n", leaderboardCount);
    int i;
    for (i = 0; i < leaderboardCount; i++)
        fprintf(fp, "%s|%d|%d|%ld|%s\n",
            leaderboard[i].name,
            leaderboard[i].score,
            leaderboard[i].attempts,
            leaderboard[i].timeTaken,
            leaderboard[i].difficulty);
    fclose(fp);
}

void loadHighScores() {
    FILE *fp = fopen("scores.dat", "r");
    if (!fp) return;
    fscanf(fp, "%d\n", &leaderboardCount);
    if (leaderboardCount > MAX_SCORES) leaderboardCount = MAX_SCORES;
    int i;
	for (i = 0; i < leaderboardCount; i++)
        fscanf(fp, "%49[^|]|%d|%d|%ld|%9s\n",
            leaderboard[i].name,
            &leaderboard[i].score,
            &leaderboard[i].attempts,
            &leaderboard[i].timeTaken,
            leaderboard[i].difficulty);
    fclose(fp);
}

/* ================================================================
   SORT LEADERBOARD (bubble sort by score)
   ================================================================ */
void sortLeaderboard() {
	int i, j;
    for (i = 0; i < leaderboardCount - 1; i++)
        for (j = 0; j < leaderboardCount - i - 1; j++)
            if (leaderboard[j].score < leaderboard[j + 1].score) {
                Player tmp = leaderboard[j];
                leaderboard[j] = leaderboard[j + 1];
                leaderboard[j + 1] = tmp;
            }
}

/* ================================================================
   SHOW LEADERBOARD
   ================================================================ */
void showLeaderboard() {
    system(CLEAR);
    printBanner();
    printf(YELLOW "\n  TOP SCORES â€” LEADERBOARD\n\n" RESET);
    printSeparator('-', 52);
    printf(WHITE "  %-3s %-15s %-8s %-8s %-6s %-6s\n" RESET,
           "#", "Name", "Score", "Attempts", "Time", "Mode");
    printSeparator('-', 52);

    if (leaderboardCount == 0) {
        printf(CYAN "  No scores recorded yet. Play a game!\n" RESET);
    }
    int i;
    for (i = 0; i < leaderboardCount; i++) {
        char *color = (i == 0) ? YELLOW : (i == 1) ? WHITE : (i == 2) ? MAGENTA : RESET;
        printf("%s  %-3d %-15s %-8d %-8d %-6lds %-6s\n" RESET,
               color, i + 1,
               leaderboard[i].name,
               leaderboard[i].score,
               leaderboard[i].attempts,
               leaderboard[i].timeTaken,
               leaderboard[i].difficulty);
    }
    printSeparator('-', 52);
    printf(CYAN "\n  Press Enter to return...\n" RESET);
    flushInput();
    getchar();
}

/* ================================================================
   STATISTICS DASHBOARD
   ================================================================ */
void showStats() {
    system(CLEAR);
    printBanner();
    printf(YELLOW "\n  STATISTICS DASHBOARD\n\n" RESET);
    printSeparator('-', 52);
    printf(WHITE "  Total games played  : " GREEN "%d\n"  RESET, totalGames);
    printf(WHITE "  Wins                : " GREEN "%d\n"  RESET, totalWins);
    printf(WHITE "  Losses              : " RED   "%d\n"  RESET, totalLosses);
    printf(WHITE "  Win rate            : " CYAN  "%.1f%%\n" RESET,
           totalGames > 0 ? (totalWins * 100.0 / totalGames) : 0.0);
    printf(WHITE "  Average attempts    : " CYAN  "%.1f\n" RESET,
           totalGames > 0 ? (totalAttempts * 1.0 / totalGames) : 0.0);
    printf(WHITE "  Best score ever     : " YELLOW "%d\n" RESET, bestScore);
    printSeparator('-', 52);
    printf(CYAN "\n  Press Enter to return...\n" RESET);
    flushInput();
    getchar();
}

/* ================================================================
   INPUT VALIDATION
   ================================================================ */
int validateInput(int *value, int low, int high) {
    char buf[64];
    if (!fgets(buf, sizeof(buf), stdin)) return 0;
    buf[strcspn(buf, "\n")] = '\0';

    /* Check all chars are digits (allow leading '-' for negatives) */
    int start = 0;
    if (buf[0] == '-') start = 1;
    if (strlen(buf) == 0 || strlen(buf) == (size_t)start) return 0;
    int i;
	for (i = start; buf[i]; i++)
        if (buf[i] < '0' || buf[i] > '9') return 0;

    *value = atoi(buf);
    if (*value < low || *value > high) {
        printf(RED "  Out of range! Enter between %d and %d.\n" RESET, low, high);
        return 0;
    }
    return 1;
}

void flushInput() {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}

/* ================================================================
   BEEP / SOUND
   ================================================================ */
void beepSound(int freq, int dur) {
#ifdef _WIN32
    Beep(freq, dur);
#else
    /* ANSI bell on Linux/Mac */
    printf("\a");
    fflush(stdout);
#endif
    (void)freq; (void)dur;
}
