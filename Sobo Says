/*
 * SoboSays.c
 *
 * Created: 11/15/2020 3:47:32 PM
 * Author: ryans
 */ 

#include <avr/io.h>
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <avr/interrupt.h>
#include <stdbool.h>
#define F_CPU 16000000UL
#include <util/delay.h>
int numOfLEDs = 4;
int pattern[10];
int userInput[10];
int level = 1;
volatile uint8_t portbhistory = 0xFF;
bool inputted = false;
int count = 1;

//flashes all LEDs on & off rapidly
void flashAllLEDs(){
	for(int i = 0; i < 4; i++){
		PORTD |= 0b01111100;
		_delay_ms(200);
		PORTD &= 0b00000000;
		_delay_ms(200);
	}
}
//toggles the End level LED to signify to the player that they are starting a new level
void endLevel(){
	PORTD |= 0b01000000;
	_delay_ms(500);
	PORTD &= 0b00000000;
	_delay_ms(300);
}
//this function exits the code and signals to the player the game is over
void endGame(){
	flashAllLEDs();
	exit(0);
}
//this function plays the game
void play(){
	while(level <= 10){
		//adds the newly generated level to the pattern array
		pattern[level] = rand() % numOfLEDs + 2;
		//this for loop loops through every level in the pattern and outputs them to PORTD
		for(int i = 1; i <= level; i++){
			PORTD |= (1<<pattern[i]);
			_delay_ms(1000);
			PORTD &= 0b00000000;
			_delay_ms(500);
		}
		sei();
		while(!inputted);
		endLevel();
		level++;
		inputted = false;
	}
	endGame();
}
//sets up pin change interrupt and port I/O
void main(){
	//define variables
	DDRB = 0x00;
	DDRD = 0xFF;
	PCMSK0 = 0x0F;
	PCICR = 0b00000001;
	//game loop
	flashAllLEDs();
	srand(time(0));
	play();
}
//Interrupt function
ISR (PCINT0_vect){
	//inputted = true;
	uint8_t changedbits;
	changedbits = PINB ^ portbhistory;
	portbhistory = PINB;
	//Green - PCINT0
	if(changedbits & (1 << PINB0)){
		userInput[count] = 2;
		PORTD |= 0b00000100;
		_delay_ms(500);
		PORTD &= 0b00000000;
		_delay_ms(500);
	}
	//Yellow - PCINT1
	 else if(changedbits & (1 << PINB1)){
		userInput[count] = 3;
		PORTD |= 0b00001000;
		_delay_ms(500);
		PORTD &= 0b00000000;
		_delay_ms(500);
	}
	//Blue - PCINT2
	else if(changedbits & (1 << PINB2)){
		userInput[count] = 4;
		PORTD |= 0b00010000;
		_delay_ms(500);
		PORTD &= 0b00000000;
		_delay_ms(500);
	}
	//Red - PCINT3
	else if(changedbits & (1 << PINB3)){
		userInput[count] = 5;
		PORTD |= 0b00100000;
		_delay_ms(500);
		PORTD &= 0b00000000;
		_delay_ms(500);
	}
	else{
		userInput[count] = 0;
	}
	if(pattern[count] != userInput[count]){
		endGame();
	}
	for(int i = 0; i <= level; i++){
		userInput[i] = 0;
	}
	count++;
}
