
# Etapa 1 — Configuração do laboratório

## Objetivo
Preparar um ambiente virtual isolado para realizar testes controlados de segurança utilizando Kali Linux e Metasploitable 2.

## Ferramentas utilizadas
- Oracle VirtualBox
- Kali Linux
- Metasploitable 2

## Configuração das máquinas virtuais

| Máquina | Função | Endereço IP |
|---|---|---|
| Kali Linux | Máquina atacante | 192.168.56.101 |
| Metasploitable 2 | Máquina vulnerável | 192.168.56.102 |

## Configuração de rede

Foi utilizada uma rede Host-only no VirtualBox.

Essa configuração permite a comunicação entre as máquinas virtuais e o computador hospedeiro, sem expor diretamente o ambiente vulnerável à rede externa.

## Teste de conectividade

No Kali Linux, foi executado:

```bash
ping 192.168.56.102
```

O teste confirmou a comunicação entre as máquinas virtuais.

## Resultado

O laboratório foi configurado com sucesso, permitindo a realização de testes de segurança em ambiente controlado.

## Medidas de segurança

- Utilização de rede Host-only.
- Ambiente isolado da internet.
- Testes limitados às máquinas virtuais próprias.
- Utilização de credenciais fictícias para as atividades.
