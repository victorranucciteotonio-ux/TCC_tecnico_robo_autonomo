# 🤖 Robô Seguidor de Linha com Monitoramento por Câmera Remota

> **Trabalho de Conclusão de Curso (TCC)**  
> **Instituição:** Instituto Federal de Educação, Ciência e Tecnologia de São Paulo (IFSP) — Câmpus Hortolândia  
> **Curso:** Técnico em Automação Industrial (2024)  
> **Orientador:** Prof. Rafael Bachega  

---


## 📌 Sobre o Projeto

Este projeto consiste no desenvolvimento de um **robô autônomo seguidor de linha** integrado a um sistema de **vigilância e monitoramento de vídeo em tempo real**. 

O robô utiliza sensores infravermelhos (IR) para navegar de forma autônoma sobre trajetórias predefinidas no chão, enquanto transmite áudio e vídeo em tempo real via rede sem fio utilizando protocolo TCP/UDP e um servidor HTTP customizado desenvolvido em Python.

### 🎯 Objetivos
* **Navegação Autônoma:** Seguir trajetórias desenhadas em superfícies sem intervenção humana contínua.
* **Monitoramento Remoto:** Capturar e transmitir vídeo/áudio via Wi-Fi para auxílio em rondas de segurança e inspeção industrial.
* **Baixo Custo & Modulidade:** Utilizar componentes eletrônicos acessíveis e integrá-los de forma eficiente.

---

## 🛠️ Componentes e Hardware

| Componente | Quantidade | Função no Projeto |
| :--- | :---: | :--- |
| **Arduino Uno** (ATmega328P) | 1 | Microcontrolador principal responsável pela lógica de movimentação e leitura de sensores |
| **ESP32** (240MHz) / Módulo Câmera Wi-Fi | 1 | Interface de comunicação e transmissão do fluxo de vídeo em tempo real |
| **Ponte H L298N** | 1 | Driver de controle de direção e velocidade (PWM) dos motores DC |
| **Motores DC 5V com Redução** | 2 | Tração e movimentação diferencial do robô |
| **Sensores Infravermelhos (MH-B / IR)** | 3 | Leitura da faixa (detecção de contraste preto/branco no solo) |
| **Bateria 12V + Suporte** | 1 | Alimentação geral dos motores e placas de controle |
| **Chassi MDF + Papelão** | 1 | Estrutura física e carenagem protetora do veículo |
| **Roda Caster (Giratória)** | 1 | Apoio omnidirecional frontal |
| **Protoboard & Jumpers** | - | Conexões e barramentos de alimentação |

---

## 💻 Arquitetura de Software

O projeto é dividido em dois blocos principais de software:

```
┌────────────────────────────────┐         ┌────────────────────────────────┐
│         ARDUINO UNO            │         │     CÂMERA WI-FI / ESP32       │
│  - Leitura dos Sensores IR     │         │  - Captura MJPEG / Áudio G711  │
│  - Lógica de Controle Motores  │         │  - Transmissão TCP/UDP         │
└──────────────┬─────────────────┘         └──────────────┬─────────────────┘
               │                                          │
               ▼                                          ▼
     [Movimentação Física]                      [Servidor Python V720]
                                                - OpenCV (Processamento)
                                                - Fake Server HTTP (Stream)
```

### 1. Firmware do Carrinho (Arduino / C++)
O código do Arduino lê os 3 sensores infravermelhos (Esquerda, Meio e Direita) e toma decisões em tempo real via Ponte H:
* **Linha Reta:** Sensor do meio ativo ($\text{SensM} = \text{true}$), laterais inativos.
* **Ajuste à Esquerda:** Sensor direito detecta a linha $\rightarrow$ Motor esquerdo desacelera/inverte.
* **Ajuste à Direita:** Sensor esquerdo detecta a linha $\rightarrow$ Motor direito desacelera/inverte.
* **Parada de Segurança:** Perda total de sinal de linha.

### 2. Servidor de Vídeo e Comunicação (Python)
Desenvolvido em Python para fazer a engenharia reversa/intermédio do fluxo da câmera (protocolo V720 / A9):
* **`main.py`:** Interface CLI via `argparse` para listar arquivos no SD, stream ao vivo e configurações Wi-Fi.
* **`a9_live.py`:** Decodificação de frames MJPEG com OpenCV (`cv2`), cálculo de FPS e renderização de Timestamp.
* **`v720_sta.py` / `v720_http.py`:** Servidor HTTP Multithreading para distribuir o fluxo de vídeo para navegadores.

---

## ⚙️ Como Executar o Projeto

### Pré-requisitos
* **Arduino IDE** instalada para carregar o código do robô.
* **Python 3.8+** instalado no computador para rodar o servidor de vídeo.

### Dependências do Python
Instale as bibliotecas necessárias para o módulo de vídeo:
```bash
pip install opencv-python numpy pillow tqdm netifaces
```

### Executando o Servidor de Vídeo
1. Conecte-se à rede Wi-Fi da câmera do robô.
2. Execute o servidor em modo live:
```bash
python main.py --live --host 192.168.169.1:6123
```
3. Para abrir o servidor HTTP e visualizar a transmissão via navegador:
```bash
python main.py --server --proxy-port 8080
```

---

## 👥 Integrantes do Grupo

* **Lucas Vinícius Locateli da Silva**
* **Vinicius de Souza Fernandes**
* **Marcos Vinicius Soares Satler**
* **Miguel Evangelista Faustino Siqueira**
* **Victor Ranucci Teotonio**
* **Mateus Henrique de Souza**

---

## 📄 Licença e Agradecimentos

Trabalho desenvolvido como Requisito Avaliativo do Curso Técnico em Automação Industrial no **Instituto Federal de São Paulo (IFSP) — Câmpus Hortolândia**. Agradecimentos especiais ao orientador Prof. Rafael Bachega e aos demais docentes do curso.
