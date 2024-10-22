#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>

using namespace std;

// Constants for the game
const int NUM_PLAYERS = 4; // Number of players
const int NUM_TOKENS = 4;  // Tokens per player
const int WIN_POSITION = 30; // Position to win

// Function to simulate a dice roll
int rollDice() {
    return rand() % 6 + 1; // Returns a number between 1 and 6
}

// Function to check if a token can be moved to a new position
bool isValidMove(int currentPosition, int move) {
    return (currentPosition + move <= WIN_POSITION);
}

// Function to capture opponent's token
void captureToken(vector<int>& playerPositions, int player, int opponent) {
    for (int i = 0; i < NUM_TOKENS; ++i) {
        if (playerPositions[opponent * NUM_TOKENS + i] != -1 && playerPositions[opponent * NUM_TOKENS + i] == playerPositions[player * NUM_TOKENS + 0]) {
            cout << "Player " << player + 1 << " captured Player " << opponent + 1 << "'s token!" << endl;
            playerPositions[opponent * NUM_TOKENS + i] = -1; // Send opponent's token back to start
        }
    }
}

int main() {
    srand(static_cast<unsigned int>(time(0))); // Seed for random number generation

    vector<int> playerPositions(NUM_PLAYERS * NUM_TOKENS, -1); // Store positions for each token, -1 means token is at home
    vector<int> playerScores(NUM_PLAYERS, 0); // Store scores for each player
    bool gameWon = false;
    int currentPlayer = 0;

    while (!gameWon) {
        cout << "Player " << currentPlayer + 1 << "'s turn." << endl;
        int diceRoll = rollDice();
        cout << "Rolled a " << diceRoll << endl;

        // Moving one of the tokens (you can enhance this to choose which token to move)
        for (int i = 0; i < NUM_TOKENS; ++i) {
            // If token is at home (-1), check if a 6 is rolled to bring it onto the board
            if (playerPositions[currentPlayer * NUM_TOKENS + i] == -1 && diceRoll == 6) {
                playerPositions[currentPlayer * NUM_TOKENS + i] = 0; // Move to start position
                cout << "Player " << currentPlayer + 1 << " brings token " << i + 1 << " onto the board." << endl;
                break;
            }
            // Move token if it is on the board
            else if (playerPositions[currentPlayer * NUM_TOKENS + i] >= 0) {
                if (isValidMove(playerPositions[currentPlayer * NUM_TOKENS + i], diceRoll)) {
                    int oldPosition = playerPositions[currentPlayer * NUM_TOKENS + i];
                    playerPositions[currentPlayer * NUM_TOKENS + i] += diceRoll;
                    cout << "Player " << currentPlayer + 1 << " moves token " << i + 1 << " from " << oldPosition << " to " << playerPositions[currentPlayer * NUM_TOKENS + i] << endl;
                    
                    // Check for captures
                    for (int opponent = 0; opponent < NUM_PLAYERS; ++opponent) {
                        if (opponent != currentPlayer) {
                            captureToken(playerPositions, currentPlayer, opponent);
                        }
                    }

                    // Check if the player has won
                    if (playerPositions[currentPlayer * NUM_TOKENS + i] == WIN_POSITION) {
                        cout << "Player " << currentPlayer + 1 << " wins!" << endl;
                        gameWon = true;
                    }
                } else {
                    cout << "Invalid move for token " << i + 1 << "!" << endl;
                }
            }
        }

        // Move to the next player
        currentPlayer = (currentPlayer + 1) % NUM_PLAYERS;
    }

    return 0;
}