int diodaS1 = 16;
int diodaS2 = 14;
int diodaS3 = 12;
int diodaS4 = 13;
int diodaS5 = 10;
int Psilnik = 5;  //HIGH - prosto, LOW - tył
int Lsilnik = 4;  //HIGH - prosto, LOW - tył
int PsilnikPWM = 0;
int LsilnikPWM = 2;
int LDsensor = 0;
int LBsensor = 0;
int Ssensor = 0;
int PDsensor = 0;
int PBsensor = 0;
int przejazdNumer = 0;
char tablicaKierunkow[30] = {};
char tablicaKierunkowSkrocona[10] = {'P', 'S', 'L', 'P', 'K'};
int dlugoscDrogi = 0;
int skracanieDD = 0;
int i = 0;
int dlugoscDrogiSkrocona = 0;
//To jest na stronie
// Low - czyli jak nie widać lini 
// HIGH - jak widać linie 

//U mnie - na odwrót
// 0 - to detekcja
// 1 - nie ma lini


void setup() {
  pinMode(PsilnikPWM, OUTPUT);
  pinMode(LsilnikPWM, OUTPUT);
  pinMode(Psilnik, OUTPUT);
  pinMode(Lsilnik, OUTPUT);
  pinMode(diodaS1, INPUT);
  pinMode(diodaS2, INPUT);
  pinMode(diodaS3, INPUT);
  pinMode(diodaS4, INPUT);
  pinMode(diodaS5, INPUT);
  // digitalWrite(PsilnikPWM, 300);
  // digitalWrite(LsilnikPWM, 300);
}

void odczyt() {
  LDsensor = digitalRead(diodaS1);
  LBsensor = digitalRead(diodaS2);
  Ssensor = digitalRead(diodaS3);
  PBsensor = digitalRead(diodaS4);
  PDsensor = digitalRead(diodaS5);
}

void prosto() {
  if(digitalRead(diodaS2) == 1) {
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, LOW); //LOW
    delay(1);
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, HIGH);  //HIGH
    delay(1);
    return;
  }
  
  if(digitalRead(diodaS4) == 1) {
    digitalWrite(Lsilnik, LOW);  //LOW
    digitalWrite(Psilnik, HIGH);
    delay(1);
    digitalWrite(Lsilnik, HIGH);  //HIGH
    digitalWrite(Psilnik, HIGH);
    delay(1);
    return;
  }

  digitalWrite(Lsilnik, HIGH);
  digitalWrite(Psilnik, HIGH);
  delay(1);
  digitalWrite(Lsilnik, LOW);
  digitalWrite(Psilnik, LOW);
  delay(1);
}

void prawo() {
  while(digitalRead(diodaS2) == 0) {
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, LOW);
    delay(1);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(1);
  }

  while(digitalRead(diodaS2) == 1) {
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, LOW);
    delay(1);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(1);
  }

  //dodaj tutaj dodawanie do tablicy
  if(przejazdNumer == 0) {
    tablicaKierunkow[dlugoscDrogi] = 'P';
    dlugoscDrogi++;    
  }
}

void lewo() {
  while(digitalRead(diodaS2) == 1) {
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, HIGH);
    delay(1);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(1);
  }

  while(digitalRead(diodaS2) == 0) {
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, HIGH);
    delay(1);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(1);
  }

    //dodaj tutaj dodawanie do tablicy
    if(przejazdNumer == 0) {
      tablicaKierunkow[dlugoscDrogi] = 'L';
      dlugoscDrogi++;    
  }
}

void zawrot() {
  while(digitalRead(diodaS2) == 1 || digitalRead(diodaS3) == 1) {
    digitalWrite(LsilnikPWM, HIGH);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, HIGH);
    delay(1);
    digitalWrite(LsilnikPWM, LOW);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(1);  
  }

  digitalWrite(Lsilnik, HIGH);
  digitalWrite(Psilnik, HIGH);
  delay(100);  
  
  while(digitalRead(diodaS2) == 0) {
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, LOW);
    delay(1);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(1);
  }

  while(digitalRead(diodaS2) == 1) {
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, LOW);
    delay(1);
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(1);
  }
  
  prosto();

  if(przejazdNumer == 0) {
    tablicaKierunkow[dlugoscDrogi] = 'Z';
    dlugoscDrogi++;
    // tablicaKierunkow[dlugoscDrogi] = 'P';
    // dlugoscDrogi++;     
  }
}

void drguiPrzejazd() {
  if(digitalRead(diodaS1) == 1 && digitalRead(diodaS5) == 1) { // zmiana warunku 
    prosto();
  }
  else {
    if(tablicaKierunkowSkrocona[i] == 'P') {
      prawo();
    }
    if(tablicaKierunkowSkrocona[i] == 'L') {
      lewo();
    }
    if(tablicaKierunkowSkrocona[i] == 'S') {
      digitalWrite(Lsilnik, HIGH);
      digitalWrite(Psilnik, HIGH);
      delay(250);
      digitalWrite(Lsilnik, LOW);
      digitalWrite(Psilnik, LOW);
      delay(50);
      prosto();
    }
    if(tablicaKierunkowSkrocona[i] == 'K') {
    digitalWrite(Lsilnik, LOW);
    digitalWrite(Psilnik, LOW);
    delay(5000);
    return;
    }
    i++; 
  }
  drguiPrzejazd();
}

void koniec() {
  digitalWrite(Lsilnik, LOW);
  digitalWrite(Psilnik, LOW);
  skracanieDrogi();  
  przejazdNumer++;
  tablicaKierunkowSkrocona[dlugoscDrogiSkrocona + 1] = 'K';
  delay(10000);
  drguiPrzejazd();   
}

void logika() {
  if(digitalRead(diodaS1) == 0 && digitalRead(diodaS5) == 0) {
    lewo();
    return; 
  }

  if(digitalRead(diodaS1) == 0) {  // && digitalRead(diodaS2) == 0 && digitalRead(diodaS3) == 0)
    lewo();
    return;
  }

  if(digitalRead(diodaS5) == 0 && digitalRead(diodaS4) == 0) { //&& digitalRead(diodaS3) == 0 && digitalRead(diodaS2) == 1 && digitalRead(diodaS1) == 1
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, HIGH);
    delay(150);
    if(digitalRead(diodaS5) == 1 && digitalRead(diodaS4) == 1 && digitalRead(diodaS3) == 1 && digitalRead(diodaS2) == 1 && digitalRead(diodaS1) == 1) {
      prawo();
    }
    else if(digitalRead(diodaS3) == 0 && digitalRead(diodaS2) == 0) {
      prosto();
      tablicaKierunkow[dlugoscDrogi] = 'S';
      dlugoscDrogi++;           
    }
    return;
  }

  if(digitalRead(diodaS1) == 1 && digitalRead(diodaS2) == 1 && digitalRead(diodaS3) == 1 && digitalRead(diodaS4) == 1 && digitalRead(diodaS5) == 1) {
    digitalWrite(Lsilnik, HIGH);
    digitalWrite(Psilnik, HIGH);
    delay(200);
    if(digitalRead(diodaS1) == 1 && digitalRead(diodaS2) == 1 && digitalRead(diodaS3) == 1 && digitalRead(diodaS4) == 1 && digitalRead(diodaS5) == 1) { 
      zawrot(); 
      return;     
    }
    else {
      koniec()         
    }
  }  
}

// void skracanieDrogi() {
//   if(tablicaKierunkow[skracanieDD - 1] == 'L' && tablicaKierunkow[skracanieDD + 1] == 'L') {
//     //tablicaKierunkowSkrocona[dlugoscDrogiSkrocona] = 'S';
//     tablicaKierunkow[skracanieDD - 1] = ' ';
//     tablicaKierunkow[skracanieDD] = ' ';
//     tablicaKierunkow[skracanieDD + 1] = 'S';
//     dlugoscDrogiSkrocona++;
//   }
//   else if(tablicaKierunkow[skracanieDD - 1] == 'S' && tablicaKierunkow[skracanieDD + 1] == 'L') {
//     //tablicaKierunkowSkrocona[dlugoscDrogiSkrocona] = 'S';
//     tablicaKierunkow[skracanieDD - 1] = ' ';
//     tablicaKierunkow[skracanieDD] = ' ';
//     tablicaKierunkow[skracanieDD + 1] = 'P';
//     dlugoscDrogiSkrocona++;
//   }
//   else if(tablicaKierunkow[skracanieDD - 1] == 'L' && tablicaKierunkow[skracanieDD + 1] == 'S') {
//     //tablicaKierunkowSkrocona[dlugoscDrogiSkrocona] = 'S';
//     tablicaKierunkow[skracanieDD - 1] = ' ';
//     tablicaKierunkow[skracanieDD] = ' ';
//     tablicaKierunkow[skracanieDD + 1] = 'P';
//     dlugoscDrogiSkrocona++;
//   }
// }

void loop() {
  odczyt();  
  if(LDsensor == 1 && PDsensor == 1 && (Ssensor == 0 || LBsensor == 0 || PBsensor == 0)) {
    prosto();    
  }
  else {
    logika();
  }
}