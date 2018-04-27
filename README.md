# 2048game
the 2048 game written in C
*updated: there's somthing wrong with the layout so you'll have to press the "raw" button to see the code before I figure it out.

#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#define SIZE 4

//print the 4X4 board

void printBoard(int board[SIZE][SIZE]){
    for(int j = 0; j < 4; j++){
        printf("*****************************\n");
        printf("*      *      *      *      *\n");
        printf("*");
        
        for(int i = 0; i < 4; i++){
            if(board[i][j]== 0){
                printf("      *");
            }
            else if(board[i][j]<10){
                printf("%4d  *", board[i][j]);
            }
            else if(board[i][j]<100){
                printf("  %d  *", board[i][j]);
            }
            else if(board[i][j]<1000){
                printf("  %d *", board[i][j]);
            }
            else if(board[i][j]<10000){
                printf(" %d *", board[i][j]);
            }
        }printf("\n*      *      *      *      *\n");
    }printf("*****************************\n" );
}

//Copys the previous board
void CopyBoard(int dst[4][4], int src[4][4]) {
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            dst[i][j] = src[i][j];
        }
    }
}

//randomly pops up 2 or 4 in a emtpy tile (the probabilty of 2 is 0.9 and 0.1 for 4)
int popup(int board[SIZE][SIZE]){
    srand((unsigned)time(NULL));
    int x,y;
    x = rand()%4;
    y = rand()%4;
    int isSuccessful = 0;
    
    if(board[x][y] == 0){
        double pos = rand()/(RAND_MAX + 1.0);
        if (pos > 0.9)
            board[x][y] = 4;
        else
            board[x][y] = 2;
        isSuccessful = 1;
    }
    return isSuccessful;
}

//makes the beginning board with 2 numbers

void initBoard(int board[SIZE][SIZE]){
    int count = 0;
    while (count < 2)
        count += popup(board);
    printBoard(board);
}

//turn the board so each move can use the function "fall" and "merge" , then turn it back
//up(1) => flip, right(2) = twist, left(3) = twist then flip

void swap(int board[SIZE][SIZE], int i, int j, int i2, int j2){
    int tem = board[i][j];
    board[i][j] = board[i2][j2];
    board[i2][j2] = tem;
}

void twist(int board[SIZE][SIZE])
{
    int i,j;
    int temBoard[SIZE][SIZE] = {};
    for (i = 0; i < SIZE; i++)
        for (j = 0; j < SIZE; j++){
            temBoard[i][j] = board[j][i];
        }
    for (i = 0; i < SIZE; i++)
        for (j = 0; j < SIZE; j++){
            board[i][j] = temBoard[i][j];
        }
}

void flip(int board[SIZE][SIZE]){
    int i,j;
    for (i = 0; i < SIZE; i++){
        for (j = 0; j < SIZE -2; j++){
            swap(board, i, j, i, SIZE-j-1);
        }
    }
}

//fill up the zeros with the numbers behind in the same column 
void fillup(int board[SIZE][SIZE]){
    int i,j;
    for (i = 0; i < SIZE; i++){
        for (j = 3; j > 0; j--) {
            if (board[i][j] == 0){
                int k = j-1;
                while (board[i][k] == 0 && k > 0)
                    k--;
                if(k>=0)
                    swap(board,i , j ,i, k);
            }
        }
    }
}

int merge(int board[SIZE][SIZE]){
    int canBeMerged = 0;
    fillup(board);
    for (int i = 0; i < SIZE; i++){
        for(int j = 3; j > 0; j--){
            if(board[i][j] == board[i][j-1]){
                board[i][j] = board[i][j]*2;
                board[i][j-1] = 0;
                canBeMerged = 1;
                fillup(board);
            }
        }
    }
    return canBeMerged;
}

int readMove(char c){
    
    char keys[4] = {'s','w','d','a'};
    int move = -1;
    
    for (int i = 0; i < 4; ++i){
        if (c == keys[i]){
            move = i;
        }
    }
    return move;
}

int moveDirection(int board[SIZE][SIZE], int way){
    if (way / 2)
        twist(board);
    if (way % 2)
        flip(board);
    int canBeMerged = merge(board);
 
    int count = 0;
    while (count < 1)
        count += popup(board);
    
    if (way % 2)
        flip(board);
    if (way / 2)
        twist(board);
    return canBeMerged;
}



int main(int argc, const char * argv[]) {
    
    int board[4][4] = {};
    int oldBoard[4][4]={};
    initBoard(board);
    printf("\n\n Press W/A/S/D to move up/left/down/right: ");
    
    for (;;) {
        char c = getchar();getchar();
        if((c = readMove(c)) != -1){
            CopyBoard(oldBoard, board);
            int canBeMerged = moveDirection(board, c); //moved
            
            if(canBeMerged == 0){
                printf("\n\n !!GAME OVER!! \n\n");
            }else{
               // printBoard(oldBoard);
                
                if (c == 0)
                    printf("\n\nDOWN\n");
                if (c == 1)
                    printf("\n\nUP\n");
                if (c == 2)
                    printf("\n\nRIGHT\n");
                if (c == 3)
                    printf("\n\nLEFT\n");
                
                printBoard(board);
            }
        }else{
            printf("\n\n PRESS W/A/S/D TO MOVE U BITCH \n");
        }
    }
    return 0;
}
