# Game-Tetris
//Tetris C++
#include <iostream>
#include <cstdlib>
#include <ctime>
#include <conio.h>
#include<bits/stdc++.h>

using namespace std;

const int width = 10;
const int height = 20;
bool gameOver;
int score;
int speed;
int sleepDuration;

enum Direction { STOP = 0, LEFT, RIGHT, DOWN };

class Tetris {
public:
    Tetris();
    void Draw();
    void Input();
    void Logic();

private:
    int x, y;
    int prevX, prevY;
    int shapeIndex;
    Direction dir;
    bool* shape;
    bool* prevShape;

    bool CheckCollision();
    void MergeShape();
    void Rotate();
};

bool board[width * height];

Tetris::Tetris() {
    x = width / 2 - 1;
    y = 0;
    shapeIndex = rand() % 7;
    shape = new bool[16];
    prevShape = new bool[16];
    dir = DOWN;

    for (int i = 0; i < 16; ++i) {
        shape[i] = false;
        prevShape[i] = false;
    }

    switch (shapeIndex) {
    case 0:  // I-shape
        shape[1] = shape[5] = shape[9] = shape[13] = true;
        break;
    case 1:  // J-shape
        shape[1] = shape[5] = shape[9] = shape[8] = true;
        break;
    case 2:  // L-shape
        shape[1] = shape[5] = shape[9] = shape[10] = true;
        break;
    case 3:  // O-shape
        shape[1] = shape[2] = shape[5] = shape[6] = true;
        break;
    case 4:  // S-shape
        shape[2] = shape[5] = shape[9] = shape[8] = true;
        break;
    case 5:  // T-shape
        shape[1] = shape[4] = shape[5] = shape[6] = true;
        break;
    case 6:  // Z-shape
        shape[1] = shape[4] = shape[5] = shape[8] = true;
        break;
    }
}

void Tetris::Draw() {
    system("cls");

    for (int i = 0; i < width; ++i)
        cout << "#";
    cout << endl;

    for (int i = 0; i < height; ++i) {
        for (int j = 0; j < width; ++j) {
            if (j == 0 || j == width - 1)
                cout << "#";
            else
                cout << (board[i * width + j] ? "X" : " ");
        }
        cout << endl;
    }

    for (int i = 0; i < width; ++i)
        cout << "#";
    cout << endl;

    cout << "Score: " << score << endl;
}

void Tetris::Input() {
    if (_kbhit()) {
        switch (_getch()) {
        case 'a':
            dir = LEFT;
            break;
        case 'd':
            dir = RIGHT;
            break;
        case 's':
            dir = DOWN;
            break;
        case 'w':
            Rotate();
            break;
        case 'x':
            gameOver = true;
            break;
        }
    }
}

bool Tetris::CheckCollision() {
    for (int i = 0; i < 16; ++i) {
        int newX = (i % 4) + x;
        int newY = (i / 4) + y;

        if (shape[i] && (newX <= 0 || newX >= width - 1 || newY >= height || board[newY * width + newX])) {
            return true;
        }
    }

    return false;
}

void Tetris::MergeShape() {
    for (int i = 0; i < 16; ++i) {
        int newX = (i % 4) + x;
        int newY = (i / 4) + y;

        if (newX >= 0 && newX < width && newY >= 0 && newY < height && shape[i]) {
            board[newY * width + newX] = true;
        }
    }
}

void Tetris::Rotate() {
    for (int i = 0; i < 16; ++i) {
        prevShape[i] = shape[i];
        shape[i] = false;
    }

    for (int i = 0; i < 16; ++i) {
        int newX = -(i / 4) + 3;
        int newY = (i % 4);

        shape[newY * 4 + newX] = prevShape[i];
    }

    if (CheckCollision()) {
        for (int i = 0; i < 16; ++i) {
            shape[i] = prevShape[i];
        }
    }
}

void Tetris::Logic() {
    prevX = x;
    prevY = y;

    switch (dir) {
    case LEFT:
        x--;
        break;
    case RIGHT:
        x++;
        break;
    case DOWN:
        y++;
        break;
    }

    if (CheckCollision()) {
        x = prevX;
        y = prevY;
        MergeShape();

        for (int i = 0; i < width; ++i) {
            if (board[i] == true) {
                gameOver = true;
                break;
            }
        }

        delete[] shape;
        delete[] prevShape;

        Tetris();
        score += 10;
    }

    dir = STOP;
}

int main() {
    srand(static_cast<unsigned>(time(NULL)));

    Tetris tetris;

    while (!gameOver) {
        tetris.Draw();
        tetris.Input();
        tetris.Logic();
    }

    cout << "Game Over! Your score: " << score << endl;

    return 0;
}
