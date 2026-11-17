
# Etapa 2 — Reconhecimento de serviços com Nmap

## Objetivo
Identificar portas abertas, serviços ativos e versões dos softwares em execução na máquina Metasploitable 2.

## Ferramenta utilizada
Nmap — Network Mapper.

## Identificação do alvo

Endereço IP: 192.168.56.102

## Primeiro reconhecimento

Comando executado:

```bash
sudo nmap -sV -sC -oN reconhecimento-inicial.txt 192.168.56.102
```

### Parâmetros utilizados

| Parâmetro | Função |
|---|---|
| sudo | Executa com privilégios administrativos |
| -sV | Identifica versões dos serviços |
| -sC | Executa scripts padrão de reconhecimento |
| -oN | Salva o resultado em formato normal |
| IP | Define o endereço do alvo |

## Varredura completa de portas TCP

Comando executado:

```bash
sudo nmap -p- -T3 -oN portas_abertas.txt 192.168.56.102
```

### Parâmetros utilizados

| Parâmetro | Função |
|---|---|
| -p- | Verifica todas as portas TCP |
| -T3 | Define velocidade moderada da varredura |
| -oN | Salva o resultado em formato normal |

## Serviços identificados

Durante o reconhecimento, foram identificados os seguintes serviços:

| Porta | Serviço | Situação |
|---|---|---|
| 21/TCP | FTP | Aberta |
| 80/TCP | HTTP | Aberta |
| 139/TCP | NetBIOS/SMB | Aberta |
| 445/TCP | SMB | Aberta |

A identificação posterior do serviço FTP apontou a versão vsftpd 2.3.4.

## Resultado

O reconhecimento permitiu identificar serviços expostos na máquina vulnerável e selecionar os alvos para as próximas etapas de auditoria.

## Observação

O reconhecimento inicial indicou ProFTPD 1.3.1, mas uma nova verificação específica da porta 21 identificou vsftpd 2.3.4. Foi adotado o resultado da verificação posterior.