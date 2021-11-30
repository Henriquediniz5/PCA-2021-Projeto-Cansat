// Inclui a biblioteca
#include <LiquidCrystal.h>
//#include <DHT.h>
// Inicia a biblioteca com os números dos pinos de interface      
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

//Simbolo grau
byte grau[8] ={ B00001100,
                B00010010,
                B00010010,
                B00001100,
                B00000000,
                B00000000,
                B00000000,
                B00000000,};

//Incerteza de temperatura
byte maismenos[8] ={ B00100,
                     B00100,
                     B11111,
                     B00100,
                     B00100,
                     B00000,
                     B11111,
                     B00000,};

float umid;

void setup() {
  Serial.begin(9600); 
  analogReference(INTERNAL);
  
  //LCD
  lcd.begin(16, 2);
  
  // Mostra a mensagem inicial no LCD
  lcd.setCursor(0,0);
  lcd.print("Projeto CanSat");
  delay(3000);
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print ("Henrique Diniz");
  lcd.setCursor(0, 1);
  lcd.print("Matricula:5405840");
  delay(3000);
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("");
  lcd.setCursor(0, 1);
  lcd.print("");
  delay(3000);
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("");
  lcd.setCursor(0,1);
  lcd.print("");
  delay(3000);
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Projeto nomeado");
  lcd.setCursor(0,1);
  lcd.print("MetSat :)");
  delay(3000);
  lcd.clear();
  
  //Mensagem inicial no monitor
  Serial.print("Ola, tudo bem?");
  delay(200);
  Serial.print("\n Esse e um modelo de Estação Meteorológica");
  delay(200);
   
  //caractere customizado com o simbolo do grau
  lcd.createChar(0, grau);
  lcd.createChar(1, maismenos);
 
}

void loop() 
{ 
  //Sensor de temperatura
  float temperatura = ler_temperatura();
  //Mostrando no LCD
  lcd.setCursor(0,0);
  lcd.print("Temperatura(");
  
  lcd.write((byte)0);
  lcd.print("C):");
  lcd.setCursor(0,1);
  lcd.print(temperatura, 1);
  lcd.write((byte)1);
  lcd.print("1.0");
  //Mostrando no Monitor Serial
  Serial.print("\nTemperatura calibrada(*C) = ");
  Serial.print(temperatura, 1);
  //Valor da incerteza
  Serial.print("\nIncerteza da temperatura(*C) = 1.0");
  Serial.println("\n****************");
  //Aguarda um segundo
  delay(3000);
  lcd.clear();
  
  //Sensor de umidade
  umid = dht_readHumidity();    
  umid = 1.084*umid - 1.491;      
  //Mostrando no LCD
  lcd.setCursor(0,0);
  lcd.print("Umidade(%):");
  lcd.setCursor(0,1);
  lcd.print(umid,1);
  lcd.write((byte)1);
  lcd.print("2.2"); //Valor da incerteza
  Serial.print("\nUmidade calibrada(%) = ");
  Serial.print(umid,1); // Exibe umidade
  //Valor da incerteza
  Serial.print("\nIncerteza da umidade(%) = 2.2");
  Serial.println("\n****************");
  
  //Aguarda um segundo
  delay(3000);
  lcd.clear();
  
  
  //Sensor de Gás
  int bin;
  double tensao, res, lum;
  bin = analogRead(A1);
  tensao = bin * 1.1/1023;
  res = (50000 * tensao)/(3.3-tensao);
  lum = pow(10,5-log10(res));
  //Mostrando no LCD
  lcd.setCursor(0,0);
  lcd.print("Nenhum gas detectado");
  lcd.setCursor(0,1);
  lcd.print(lum,1);  
  
  //Aguarda um segundo
  delay(3000);
  lcd.clear();
  
  
  //Aguarda um segundo
  delay(3000);
  lcd.clear();
 

}
//------------------------------------------------------- 
//--------------------------------------------------------
//Função do DHT22
//Leitura de umidade (simulada)
float dht_readHumidity(){
	float umidade = map(analogRead(A2), 0, 1023, 0, 999);
  	umidade -= 20 + random(41);		//Incerteza
  	return umidade/10.0;   
}


//Faz a leitura de tensão do TMP36
float ler_voltagem_tmp36(){
  //Lê tensão Analógica na porta A0
  int adc = analogRead(A0);
  //Converte para um valor em volts
  float v = adc * (1.1/1023.0);
  //retorna valor de tensão
  return v;  
}

//Converte a voltagem em temperatura
float ler_temperatura(){
  //Voltagem -> Temperatura
  float v = ler_voltagem_tmp36();
  float t = 102.025 * v - 51.927; //Calibração
  return t;
}
 

