/*  RickRollCode

    AUTHOR: Rowan Packard
    rowanpackard@gmail.com

    DISCLAIMER: The song "Never Gonna Give You Up" by Rick Astley
    is not  the creative property of the author. This code simply
    plays a Piezo buzzer  rendition of the song.
*/

#define  a3f    208     // 208 Hz
#define  b3f    233     // 233 Hz
#define  b3     247     // 247 Hz
#define  c4     261     // 261 Hz MIDDLE C
#define  c4s    277     // 277 Hz
#define  e4f    311     // 311 Hz    
#define  f4     349     // 349 Hz 
#define  a4f    415     // 415 Hz  
#define  b4f    466     // 466 Hz 
#define  b4     493     //  493 Hz 
#define  c5     523     // 523 Hz 
#define  c5s    554     // 554  Hz
#define  e5f    622     // 622 Hz  
#define  f5     698     // 698 Hz 
#define  f5s    740     // 740 Hz
#define  a5f    831     // 831 Hz 

#define  rest    -1

int piezo = 10; // Connect your piezo buzzer to this pin or change  it to match your circuit!
int led = LED_BUILTIN; 

volatile int beatlength  = 100; // determines tempo
float beatseparationconstant = 0.3;

int threshold;

int  a; // part index
int b; // song index
int c; // lyric index

boolean  flag;

// Parts 1 and 2 (Intro)

int song1_intro_melody[] =
{c5s,  e5f, e5f, f5, a5f, f5s, f5, e5f, c5s, e5f, rest, a4f, a4f};

int song1_intro_rhythmn[]  =
{6, 10, 6, 6, 1, 1, 1, 1, 6, 10, 4, 2, 10};

// Parts 3 or 5 (Verse 1)

int  song1_verse1_melody[] =
{ rest, c4s, c4s, c4s, c4s, e4f, rest, c4, b3f, a3f,
  rest, b3f, b3f, c4, c4s, a3f, a4f, a4f, e4f,
  rest, b3f, b3f, c4, c4s, b3f,  c4s, e4f, rest, c4, b3f, b3f, a3f,
  rest, b3f, b3f, c4, c4s, a3f, a3f, e4f,  e4f, e4f, f4, e4f,
  c4s, e4f, f4, c4s, e4f, e4f, e4f, f4, e4f, a3f,
  rest,  b3f, c4, c4s, a3f, rest, e4f, f4, e4f
};

int song1_verse1_rhythmn[] =
{  2, 1, 1, 1, 1, 2, 1, 1, 1, 5,
  1, 1, 1, 1, 3, 1, 2, 1, 5,
  1, 1, 1, 1, 1,  1, 1, 2, 1, 1, 1, 1, 3,
  1, 1, 1, 1, 2, 1, 1, 1, 1, 1, 1, 4,
  5, 1, 1, 1,  1, 1, 1, 1, 2, 2,
  2, 1, 1, 1, 3, 1, 1, 1, 3
};

const char* lyrics_verse1[]  =
{ "We're ", "no ", "strangers ", "", "to ", "love ", "", "\
\
",
  "You ", "know ", "the ", "rules ", "and ", "so ", "do ", "I\
\
",
  "A ", "full ", "commitment's ", "", "", "what ", "I'm ", "thinking  ", "", "of", "\
\
",
  "You ", "wouldn't ", "", "get ", "this  ", "from ", "any ", "", "other ", "", "guy\
\
",
  "I ", "just  ", "wanna ", "", "tell ", "you ", "how ", "I'm ", "feeling", "\
\
",
  "Gotta ", "", "make ", "you ", "understand", "", "\
\
"
};

//  Parts 4 or 6 (Chorus)

int song1_chorus_melody[] =
{ b4f, b4f, a4f, a4f,
  f5, f5, e5f, b4f, b4f, a4f, a4f, e5f, e5f, c5s, c5, b4f,
  c5s, c5s, c5s, c5s,
  c5s, e5f, c5, b4f, a4f, a4f, a4f, e5f, c5s,
  b4f, b4f, a4f, a4f,
  f5,  f5, e5f, b4f, b4f, a4f, a4f, a5f, c5, c5s, c5, b4f,
  c5s, c5s, c5s, c5s,
  c5s, e5f, c5, b4f, a4f, rest, a4f, e5f, c5s, rest
};

int song1_chorus_rhythmn[]  =
{ 1, 1, 1, 1,
  3, 3, 6, 1, 1, 1, 1, 3, 3, 3, 1, 2,
  1, 1, 1, 1,
  3, 3, 3, 1, 2, 2, 2, 4, 8,
  1, 1, 1, 1,
  3, 3, 6, 1, 1, 1, 1, 3, 3, 3,  1, 2,
  1, 1, 1, 1,
  3, 3, 3, 1, 2, 2, 2, 4, 8, 4
};

const char*  lyrics_chorus[] =
{ "Never ", "", "gonna ", "", "give ", "you ",  "up\
\
",
  "Never ", "", "gonna ", "", "let ", "you ", "down",  "", "\
\
",
  "Never ", "", "gonna ", "", "run ", "around ",  "", "", "", "and ", "desert ", "", "you\
\
",
  "Never ", "",  "gonna ", "", "make ", "you ", "cry\
\
",
  "Never ", "", "gonna  ", "", "say ", "goodbye ", "", "", "\
\
",
  "Never ", "",  "gonna ", "", "tell ", "a ", "lie ", "", "", "and ", "hurt ",  "you\
\
"
};

void setup_1()
{
  pinMode(piezo, OUTPUT);
  pinMode(led,  OUTPUT);

  digitalWrite(led, LOW);
  Serial.begin(9600);
  flag = false;
  a = 4;
  b = 0;
  c = 0;
}

void loop_1()
{
  flag = 1;
  while(flag) {
    while (Serial.available()>0) {
      char f = Serial.read();
      if (f=='v') {
        a = 4;
        b = 0;
        c = 0;
        flag = 0;
        
      }
    }
    play();
  }
  noTone(10);
}

void  play() {
  int notelength;
  if (a == 1 || a == 2) {
    // intro
    notelength  = beatlength * song1_intro_rhythmn[b];
    if (song1_intro_melody[b] > 0) {
      digitalWrite(led, HIGH);
      tone(piezo, song1_intro_melody[b], notelength);
    }
    b++;
    if (b >= sizeof(song1_intro_melody) / sizeof(int)) {
      a++;
      b = 0;
      c = 0;
    }
  }
  else if (a == 3  || a == 5) {
    // verse
    notelength = beatlength * 2 * song1_verse1_rhythmn[b];
    if (song1_verse1_melody[b] > 0) {
      digitalWrite(led, HIGH);
      Serial.print(lyrics_verse1[c]);
      tone(piezo, song1_verse1_melody[b], notelength);
      c++;
    }
    b++;
    if (b >= sizeof(song1_verse1_melody) / sizeof(int)) {
      a++;
      b = 0;
      c = 0;
    }
  }
  else if (a == 4 || a == 6) {
    // chorus
    notelength = beatlength * song1_chorus_rhythmn[b];
    if  (song1_chorus_melody[b] > 0) {
      digitalWrite(led, HIGH);
      Serial.print(lyrics_chorus[c]);
      tone(piezo, song1_chorus_melody[b], notelength);
      c++;
    }
    b++;
    if (b >= sizeof(song1_chorus_melody) / sizeof(int)) {
      Serial.println("");
      a++;
      b = 0;
      c = 0;
    }
  }
  delay(notelength);
  noTone(piezo);
  digitalWrite(led, LOW);
  delay(notelength * beatseparationconstant);
  //if (a == 7) { // loop back around to beginning of song
    //a = 1;
  //}
}












const int PWM_1 = 5;
const int PWM_2 = 6;
const int DIR_1 = 4;
const int DIR_2 = 7;
const int DIR_1a = 9;
const int DIR_2a = 8;
const int MUZ = 10;

void setup() {
  pinMode(PWM_1, OUTPUT);
  pinMode(PWM_2, OUTPUT);
  pinMode(DIR_1, OUTPUT);
  pinMode(DIR_2, OUTPUT);
  pinMode(DIR_1a, OUTPUT);
  pinMode(DIR_2a, OUTPUT);
  pinMode(MUZ, OUTPUT);
  Serial.begin(9600);
  motors(0,0);
 /*for (int i = 0; i<100; i++) {
  tone (10,i*10);
  delay(100);
 }
  noTone(10);*/

  setup_1();
}

int m = 255;

void loop() {
  
  if (Serial.available()>0) {
    char f = Serial.read();
    if (f=='F') motors(m,m);
    if (f=='B') motors(-m,-m);
    if (f=='L') motors(-m,m);
    if (f=='R') motors(m,-m);
    if (f=='G') motors(0.5*m,m);
    if (f=='I') motors(m,0.5*m);
    if (f=='S') motors(0,0);
    if (f=='0') m = 0;
    if (f=='1') m = 25;
    if (f=='2') m = 50;
    if (f=='3') m = 75;
    if (f=='4') m = 100;
    if (f=='5') m = 125;
    if (f=='6') m = 150;
    if (f=='7') m = 175;
    if (f=='8') m = 200;
    if (f=='9') m = 225;
    if (f=='q') m = 255;

    if (f=='W') muz();
    if (f=='V') loop_1();
  }
}

void motors(int speed_1, int speed_2) {
  speed_2 = speed_2 * 1.05;
  if (speed_1>0) {
    digitalWrite(DIR_1,1);
    digitalWrite(DIR_1a,0);
    analogWrite(PWM_1,speed_1);
  }
  else {
    digitalWrite(DIR_1,0);
    digitalWrite(DIR_1a,1);
    analogWrite(PWM_1,-speed_1);
  }
  if (speed_2>0) {
    digitalWrite(DIR_2,1);
    digitalWrite(DIR_2a,0);
    analogWrite(PWM_2,speed_2);
  }
  else {
    digitalWrite(DIR_2,0);
    digitalWrite(DIR_2a,1);
    analogWrite(PWM_2,-speed_2);
  }
}

void muz() {
  char f = Serial.read();
  for (int i = 0; i<100 and f!='w'; i++) {
    while (Serial.available()>0) {
      f = Serial.read();
      if (f == 'w') break;
    }
    tone (10,i*10);
    delay(100);
  }
  noTone(10);
}

