#include <Adafruit_CircuitPlayground.h>
[Sheet for Final.pdf](https://github.com/user-attachments/files/15785758/Sheet.for.Final.pdf)
[Statemachineforfinal.drawio.pdf](https://github.com/user-attachments/files/15785751/Statemachineforfinal.drawio.pdf)

const int easyTimeLimit = 3000; // 3 seconds in milliseconds
const int hardTimeLimit = 1000; // 1 second in milliseconds
const int easyLives = 3;
const int hardLives = 1;
const int ledPin = 13; // Using the onboard LED for feedback

int lives;
int timeLimit;
int highScore = 0;
int score = 0;

void setup() {
  Serial.begin(9600);
  CircuitPlayground.begin();
  pinMode(ledPin, OUTPUT);

  resetGame();
}

void loop() {
  bool isHardMode = CircuitPlayground.slideSwitch();

  if (isHardMode) {
    timeLimit = hardTimeLimit;
    lives = hardLives;
  } else {
    timeLimit = easyTimeLimit;
    lives = easyLives;
  }

  while (lives > 0) {
    int ledColor = random(0, 2); // 0 for red, 1 for green
    unsigned long startTime = millis();
    bool correctButtonPressed = false;

    if (ledColor == 0) {
      CircuitPlayground.setPixelColor(0, 255, 0, 0); // Red
    } else {
      CircuitPlayground.setPixelColor(0, 0, 255, 0); // Green
    }

    while (millis() - startTime < timeLimit) {
      if (CircuitPlayground.leftButton() && ledColor == 0) {
        correctButtonPressed = true;
        break;
      } else if (CircuitPlayground.rightButton() && ledColor == 1) {
        correctButtonPressed = true;
        break;
      }
    }

    CircuitPlayground.clearPixels();

    if (correctButtonPressed) {
      score++;
      Serial.print("Score: ");
      Serial.println(score);
      if (score > highScore) {
        highScore = score;
        playHighScoreTone();
      }
    } else {
      lives--;
      if (lives == 0) {
        playGameOverTone();
      } else {
        Serial.print("Lives remaining: ");
        Serial.println(lives);
      }
    }
  }

  Serial.print("Game over. Your final score is: ");
  Serial.println(score);
  resetGame();
}

void playHighScoreTone() {
  for (int i = 1000; i <= 2000; i += 250) {
    CircuitPlayground.playTone(i, 100);
  }
}

void playGameOverTone() {
  for (int i = 2000; i >= 1000; i -= 250) {
    CircuitPlayground.playTone(i, 100);
  }
}

void resetGame() {
  score = 0;
  Serial.println("Press the reset button to start a new game.");
}
