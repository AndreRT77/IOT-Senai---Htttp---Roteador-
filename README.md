# IOT-Senai---Htttp---Roteador-

# Projeto com Esp32, utilizando requisição via http, e roteamento

## Vídeo
https://youtube.com/shorts/PgKiCesgYIE?feature=share

## Código
#include <WiFi.h>
#include <HTTPClient.h>
#include <WiFiClientSecure.h>

// Credenciais da rede industrial
const char* ssid = "Katatonia";
const char* password = "Skyvoidofstars";

// URL de teste (JSONPlaceholder é um serviço comum para testes de API)
const char* serverName = "https://jsonplaceholder.typicode.com/todos/1";

void setup() {
  Serial.begin(115200);

  WiFi.begin(ssid, password);
  Serial.print("Conectando ao Wi-Fi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nConectado com sucesso!");
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {

    // Criamos o objeto cliente seguro
    WiFiClientSecure *client = new WiFiClientSecure;

    if (client) {
      // Em um cenário real, você usaria client->setCACert(rootCACertificate);
      // Para este exemplo simples, vamos ignorar a validação do certificado:
      client->setInsecure();

      HTTPClient https;

      Serial.print("[HTTPS] Iniciando requisição...\n");
      if (https.begin(*client, serverName)) {

        // Realiza o GET
        int httpCode = https.GET();

        // httpCode será positivo se o servidor responder
        if (httpCode > 0) {
          Serial.printf("[HTTPS] Código de resposta: %d\n", httpCode);

          if (httpCode == HTTP_CODE_OK) {
            String payload = https.getString();
            Serial.println("Conteúdo recebido:");
            Serial.println(payload);
          }

        } else {
          Serial.printf("[HTTPS] Falha na requisição, erro: %s\n", https.errorToString(httpCode).c_str());
        }

        https.end();
      } else {
        Serial.printf("[HTTPS] Não foi possível conectar ao servidor\n");
      }

      delete client; // Libera memória
    }
  }

  // Aguarda 10 segundos para a próxima leitura (evita sobrecarga no servidor)
  delay(10000);
}

## 2º Código

// --------------------------------------------
// Conexão WiFi básica no ESP32
// --------------------------------------------

#include <WiFi.h>

// Substitua pelas suas credenciais de rede
const char* ssid     = "Katatonia";
const char* password = "Skyvoidofstars";

void setup() {
  Serial.begin(115200);
  Serial.print("Conectando a: ");
  Serial.println(ssid);

  // Inicia conexão como Station
  WiFi.begin(ssid, password);

  // Aguarda até conectar (loop bloqueante com timeout)
  int tentativas = 0;
  while (WiFi.status() != WL_CONNECTED && tentativas < 20) {
    delay(500);
    Serial.print(".");
    tentativas++;
  }

  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("\n✓ Conectado!");
    Serial.print("IP: ");
    Serial.println(WiFi.localIP());   // Exibe o IP do ESP32
    Serial.print("RSSI (sinal): ");
    Serial.print(WiFi.RSSI());
    Serial.println(" dBm");
  } else {
    Serial.println("\n✗ Falha na conexão!");
  }
}

void loop() {
  // Verifica conexão a cada 10 segundos
  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("Reconectando...");
    WiFi.reconnect();
  }

  delay(10000);
}
