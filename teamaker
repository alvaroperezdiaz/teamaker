////////////////////////////////////////////////////////////////////////
// Alvaro Perez, periprohus @ www.instructables.com
// Some parts of the code obtained from www.arduino.cc Example page and
// adapted to my purposes (music and servo templates).
//
// Visit http://www.instructables.com/id/DIY-Automated-Tea-Maker/ for 
// more info and license.
////////////////////////////////////////////////////////////////////////


// SERVO SETUP
#include <Servo.h>
Servo myservo;

// HARDWARE SETUP
const int button1 = 2;
const int button2 = 3;
const int buzzerPin = 12;
const int ledPin = 13;

/////
// THIS FOUR VALUES SPECIFY THE SERVO POSITIONS FOR EACH OF THE STATES, DEPENDING ON YOUR
// SERVO AND YOUR CASE YOU MAY HAVE TO ADAPT SOME OF THEM, JUST TRY!
const int brewingVal = 85; // WHEN THE STICK IS LOWERED TO SUBMERGE THE TEABAG
const int readyVal = 120; // WHEN THE STICK IS RAISED, WAITING FOR ACTION
const int movingVal = 95; // WHEN THE STICK STIRS THE ONBREWING TEA
const int offVal = 10; // FOLDED STATE FOR TRAVELLING AND OFF MODE
/////

/////
// HERE YOU CAN MODIFY THE ALARM SOUND, WHEN TEA IS READY
// "c", "c#", "d", "d#", "e", "f", "f#", "g", "g#", "a", "b",
// "C", "C#", "D", "D#", "E", "F", "F#", "G", "G#", "A", "A#", "B"
// JUST ADD OR REPLACE SOUNDS IN THE LIST BELOW
char* notes[] = {"c","d","e"}; // A SPACE " " REPRESENTS A REST
int beats[] = {1,1,1};
int tempo = 260;
////

// STATE 0 OFF, 1 READY, 2 BREWING!
int state = 1;
int old_state = 0;

void setup() {
  myservo.attach(10);
  pinMode(button1, INPUT_PULLUP);
  pinMode(button2, INPUT_PULLUP);
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledPin, OUTPUT);
  
  myservo.write(offVal);
  digitalWrite(ledPin, LOW);
}


void loop() {
  int buttonState1 = digitalRead(button1);
  int buttonState2 = digitalRead(button2);


  if (buttonState1 == LOW && buttonState2 == HIGH) {
    state = 0;
    if (state != old_state) {
      digitalWrite(ledPin, LOW);
      myservo.write(offVal);
      old_state = 0;
    }
  }
  if (buttonState1 == HIGH && buttonState2 == LOW) {
    state = 2;
    if (state != old_state) {
      digitalWrite(ledPin, HIGH);
      beep();
      startBrew();
      endBrew();
      beep();
      for (int i = 1; i< 15; i++) {
        digitalWrite(ledPin, LOW);
        delay(200);
        digitalWrite(ledPin, HIGH);
        delay(200);
      }
      old_state = 2;
    }
  }
  if (buttonState1 == HIGH && buttonState2 == HIGH) {
    state = 1;
    if (state != old_state) {
      digitalWrite(ledPin, HIGH);
      myservo.write(readyVal);
      old_state = 1;
    }
  }
}

void beep() {
  for (int i = 0; i < sizeof(notes); i++) {
    if (notes[i] == " ") {
      delay(beats[i] * tempo); // rest
    } 
    else {
      playNote(notes[i], beats[i] * tempo);
    }
    delay(tempo / 8);
  }
}

void startBrew() {
  int steps = 30;
  for (int i = 1; i <= steps; i++) {
    int setVal = (-readyVal + brewingVal)/steps*i + readyVal;
    myservo.write(setVal);
    delay(100);
  }
  myservo.write(brewingVal);
  /////
  // ADJUST YOUR DESIRED BREWING TIME IN SECONDS
  // NOW APPROX 5MIN, AS WE LOOP 5 TIMES, WAITING 50SEC STILL AND THEN STIRING
  // THE TEABAG, GIVING APPROX. 5X60SEC, THAT IS 5MIN.
  for (int i = 0; i < 5; i++) {
    // DELAYS 50SEC (10+10+10+10+10)
    delay(1000*10);
    delay(1000*10);
    delay(1000*10);
    delay(1000*10);
    delay(1000*10);
    int steps = 10;
    // STIRS THE TEA GOING A BIT UP AND DOWN
    for (int i = 1; i <= steps; i++) {
      int setVal = (movingVal - brewingVal)/steps*i + brewingVal;
      myservo.write(setVal);
      delay(100);
    }
    for (int i = 1; i <= steps; i++) {
      int setVal = (-movingVal + brewingVal)/steps*i + movingVal;
      myservo.write(setVal);
      delay(100);
    }
  }
}

void endBrew() {  
  // GRADUAL REMOVAL OF TEABAG
  int steps = 30;
  for (int i = 1; i <= steps; i++) {
    int setVal = (readyVal - brewingVal)/steps*i + brewingVal;
    myservo.write(setVal);
    delay(100);
  }
  myservo.write(readyVal);
}

void playTone(int tone, int duration) {
  for (long i = 0; i < duration * 1000L; i += tone * 2) {
    digitalWrite(buzzerPin, HIGH);
    delayMicroseconds(tone);
    digitalWrite(buzzerPin, LOW);
    delayMicroseconds(tone);
  }
}

void playNote(char* note, int duration) {
  char* names[] = { 
    "c", "c#", "d", "d#", "e", "f", "f#", "g", "g#", "a", "b", "C", "C#", "D", "D#", "E", "F", "F#", "G", "G#", "A", "A#", "B"     };
  int tones[] = { 
    1915, 1805, 1700, 1607, 1519, 1432, 1351, 1275, 1204, 1136, 1014, 956, 904, 852, 803, 758, 716, 676, 638, 602, 568, 536, 507    };

  // play the tone corresponding to the note name
  for (int i = 0; i < 24; i++) {
    if (names[i] == note) {
      playTone(tones[i], duration);
    }
  }
}
