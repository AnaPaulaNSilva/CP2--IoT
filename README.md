# OBJETIVO 

O objetivo do nosso projeto é identificar quantas bolsas de cada tipo sanguíneo tem no estoque dos hospitais e também identificar qual é o tipo de sangue que esse dispositivo está se aproximando, e um site para a população e agentes da saúde saberem quais são os tipos que estão em falta, podendo ter um maior controle de estoque. 

# CODIGO COMPLETO

#include <SPI.h>

#include <MFRC522.h>

#include <LiquidCrystal_I2C.h>

// Definição dos pinos para o leitor RFID

#define SS_PIN 10

#define RST_PIN 9

MFRC522 mfrc522(SS_PIN, RST_PIN);   // Cria uma instância do MFRC522

// Configurações do LCD I2C

#define endereco  0x27 // Endereço I2C comum para o LCD

#define colunas   16    // Número de colunas do LCD

#define linhas    2     // Número de linhas do LCD

LiquidCrystal_I2C lcd(endereco, colunas, linhas); // Instancia o objeto do LCD

// Pinos para LEDs

#define verde 2

#define vermelho 3

// Variável para armazenar a mensagem

char st[20];

void setup() {

  Serial.begin(9600);   // Inicia a comunicação serial a 9600 bps
  
  SPI.begin();          // Inicia a comunicação SPI
  
  mfrc522.PCD_Init();   // Inicializa o leitor MFRC522
  
  Serial.println("Aproxime o seu cartao do leitor..."); // Mensagem inicial
  
  Serial.println();
  
  // Inicializa o LCD
  lcd.init();  
  
  lcd.backlight();    // Liga a iluminação do display
  
  mensageminicial();   // Exibe a mensagem inicial no LCD

  // Configura os pinos como saídas
  
  pinMode(7, OUTPUT);
  
  pinMode(6, OUTPUT);
  
  pinMode(4, OUTPUT);
  
}

void loop() {

  // Ativa todos os LEDs
  
  digitalWrite(4, HIGH);
  
  digitalWrite(7, HIGH);
  
  digitalWrite(6, HIGH);
  
  // Verifica se um novo cartão está presente
  
  if (!mfrc522.PICC_IsNewCardPresent()) {
    return; 
    // Se não houver novo cartão, sai da função
    
  }

  // Seleciona o cartão se estiver presente
  
  if (!mfrc522.PICC_ReadCardSerial()) {
    return; 
    // Se não puder ler o cartão, sai da função
    
  }

  // Exibe o UID do cartão no monitor serial
  
  Serial.print("UID da tag :");
  
  String conteudo = "";
  
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "); // Formata o UID
    Serial.print(mfrc522.uid.uidByte[i], HEX); // Exibe o UID em hexadecimal
    conteudo.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
    conteudo.concat(String(mfrc522.uid.uidByte[i], HEX)); // Armazena o UID em uma string
  }
  
  Serial.println();

  // Converte a string para maiúsculas para comparação
  
  conteudo.toUpperCase();

  // Verifica o UID para Sangue A+
  
  if (conteudo.substring(1) == "47 1D DE 2B") { // UID 1 - Chaveiro
    Serial.println("Sangue A+");
    Serial.println("Nicho Azul!");
    digitalWrite(4, HIGH);
    digitalWrite(6, HIGH);
    digitalWrite(7, LOW); // Acende LED correspondente
    lcd.clear(); // Limpa o LCD
    lcd.setCursor(0, 0);
    lcd.print("Sangue A+"); // Exibe no LCD
    lcd.setCursor(0, 1);
    lcd.print("Nicho Azul!");
    delay(3000); // Atraso para leitura da mensagem
    mensageminicial(); // Retorna à mensagem inicial
    
  }

  // Verifica o UID para Sangue O-
  
  if (conteudo.substring(1) == "84 A5 AE 29") { // UID 2 - Cartão
    Serial.println("Sangue O-");
    Serial.println("Nicho Amarelo");
    digitalWrite(4, LOW); // Desliga LED correspondente
    digitalWrite(7, HIGH);
    digitalWrite(6, HIGH);
    lcd.clear(); // Limpa o LCD
    lcd.setCursor(0, 0);
    lcd.print("Sangue O-"); // Exibe no LCD
    lcd.setCursor(0, 1);
    lcd.print("Nicho Amarelo");
    delay(3000); // Atraso para leitura da mensagem
    mensageminicial(); // Retorna à mensagem inicial
  }
}

void mensageminicial() {

  // Exibe mensagem inicial no LCD
  lcd.clear();
  lcd.print("Aproxime a bolsa");  
  lcd.setCursor(0, 1);
  lcd.print("Tipo Sanguíneo");  
  
}


# CONFIGURAÇÕES DO SERVIDOR 
Será necessario baixar essas bibliotecas para o funcionamento do código

<SPI.h>

<MFRC522.h>

<LiquidCrystal_I2C.h>

# DESENHO DA ARQUITETURA 
![image](https://github.com/user-attachments/assets/5766a21e-1f06-47c3-bfb5-8b76883c6c3c)


