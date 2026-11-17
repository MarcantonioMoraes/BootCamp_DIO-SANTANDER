
# Auditoria de Autenticação e Segurança com Kali Linux e Medusa

![Kali Linux](https://img.shields.io/badge/Kali_Linux-Pentest-blue?logo=kalilinux)
![Medusa](https://img.shields.io/badge/Medusa-2.3-orange)
![Nmap](https://img.shields.io/badge/Nmap-Reconhecimento-green)
![Status](https://img.shields.io/badge/Projeto-Concluído-success)
![License](https://img.shields.io/badge/Licença-MIT-lightgrey)

## Sobre o projeto

Este projeto foi desenvolvido como atividade prática de estudos em Cibersegurança, com o objetivo de compreender e executar auditorias controladas de autenticação em serviços FTP, aplicações Web e compartilhamentos SMB.

Utilizando Kali Linux, Medusa, Nmap, Enum4linux e SMBClient, foi construído um laboratório virtual isolado para simular cenários de descoberta de credenciais, enumeração de usuários e análise de permissões de acesso.

Além da execução dos testes, foram documentados os resultados, as evidências e as recomendações de mitigação.

## Objetivos

- Compreender o funcionamento de ataques de força bruta e password spraying.
- Identificar serviços e portas expostos em uma máquina de laboratório.
- Realizar testes controlados de autenticação FTP.
- Auditar formulários de autenticação Web utilizando DVWA.
- Enumerar usuários e compartilhamentos SMB.
- Identificar permissões inadequadas de acesso anônimo.
- Documentar vulnerabilidades e propor medidas de mitigação.

## Ambiente de laboratório

Os testes foram realizados em máquinas virtuais utilizando VirtualBox, com rede Host-only para garantir o isolamento do ambiente.

| Máquina | Sistema | Endereço IP |
|---|---|---|
| Máquina de auditoria | Kali Linux | 192.168.56.101 |
| Máquina alvo | Metasploitable 2 | 192.168.56.102 |

**Rede utilizada:** 192.168.56.0/24

**Topologia:** rede virtual isolada (Host-only).

## Ferramentas utilizadas

| Ferramenta | Finalidade |
|---|---|
| Kali Linux | Sistema operacional de auditoria |
| Medusa | Testes controlados de autenticação |
| Nmap | Reconhecimento de portas e serviços |
| Enum4linux | Enumeração de informações SMB |
| SMBClient | Verificação de acesso aos compartilhamentos |
| Netcat | Consulta de banner de serviços |
| DVWA | Aplicação Web vulnerável para laboratório |
| VirtualBox | Virtualização do ambiente |

## Estrutura do projeto

```text
auditoria-brute-force-kali-medusa/
│
├── README.md
│
├── docs/
│   ├── 01-configuracao-laboratorio.md
│   ├── 02-reconhecimento-nmap.md
│   ├── 03-auditoria-ftp.md
│   ├── 04-auditoria-dvwa.md
│   ├── 05-auditoria-smb.md
│   └── 06-mitigacoes.md
│
├── wordlists/
│   ├── usuarios-ftp.txt
│   ├── usuarios-dvwa.txt
│   └── usuarios-smb.txt
│
├── commands/
│   └── comandos-utilizados.md
│
└── images/
```

As listas de senhas utilizadas no laboratório são mantidas fora da publicação para evitar a exposição de credenciais de teste.

## Etapas desenvolvidas

### Etapa 1 — Configuração do laboratório

Preparação das máquinas virtuais Kali Linux e Metasploitable 2, configuração da rede Host-only e verificação da conectividade.

**Resultado:** comunicação entre as máquinas confirmada.

Documentação: `docs/01-configuracao-laboratorio.md`

### Etapa 2 — Reconhecimento com Nmap

Identificação de portas abertas e serviços disponíveis na máquina alvo.

Serviços relevantes identificados:

| Porta | Serviço |
|---|---|
| 21 | FTP |
| 80 | HTTP |
| 139 | SMB |
| 445 | SMB |

Documentação: `docs/02-reconhecimento-nmap.md`

### Etapa 3 — Auditoria FTP

Utilização do Medusa para realizar um teste controlado de autenticação FTP no Metasploitable 2.

**Resultado:** o Medusa apresentou `ACCOUNT FOUND`, e a combinação encontrada foi confirmada manualmente.

Documentação: `docs/03-auditoria-ftp.md`

### Etapa 4 — Auditoria Web com DVWA

Utilização do módulo `web-form` do Medusa na funcionalidade:

`DVWA → Vulnerabilities → Brute Force`

O teste foi realizado com a aplicação configurada no nível de segurança Low.

**Resultado:** o Medusa apresentou `ACCOUNT FOUND`, e o acesso foi confirmado manualmente na própria funcionalidade Brute Force do DVWA.

Documentação: `docs/04-auditoria-dvwa.md`

### Etapa 5 — Auditoria SMB

Utilização do Nmap, Enum4linux, Medusa e SMBClient para analisar o serviço SMB.

**Resultados:**

- Portas 139 e 445 abertas.
- 35 usuários identificados pelo Enum4linux.
- Password spraying com uma senha e 35 usuários, sem identificação de `ACCOUNT FOUND`.
- Login anônimo confirmado.
- Cinco compartilhamentos identificados.
- Compartilhamento `tmp` acessível anonimamente, permitindo listagem.
- Compartilhamento `IPC$` acessível, mas sem autorização para listar conteúdo.
- Compartilhamentos `opt`, `print$` e `ADMIN$` com acesso anônimo negado.

**Principal achado:** exposição de conteúdo no compartilhamento `tmp` sem autenticação.

Documentação: `docs/05-auditoria-smb.md`

### Etapa 6 — Mitigações

Consolidação das recomendações para reduzir os riscos encontrados nas auditorias.

Foram abordados:

- Proteção contra tentativas automatizadas de autenticação.
- Uso de senhas fortes.
- Autenticação multifator.
- Restrição de acessos anônimos.
- Revisão das permissões de compartilhamentos.
- Monitoramento de logs.
- Atualização e proteção dos serviços.

Documentação: `docs/06-mitigacoes.md`

## Principais resultados

| Auditoria | Resultado |
|---|---|
| FTP | Credencial de laboratório identificada e validada |
| DVWA | Credencial identificada e validada na funcionalidade Brute Force |
| SMB | 35 usuários enumerados |
| SMB | Nenhuma credencial válida identificada no password spraying |
| SMB | Acesso anônimo ao compartilhamento `tmp` confirmado |

## Aprendizados

Durante o desenvolvimento deste projeto, foram praticados conceitos importantes:

- Reconhecimento de rede e identificação de serviços.
- Análise de banners e versões.
- Funcionamento de ferramentas de auditoria de autenticação.
- Diferenças entre brute force e password spraying.
- Enumeração de usuários e compartilhamentos SMB.
- Análise de autenticação e autorização.
- Interpretação de resultados e falsos positivos.
- Documentação técnica de testes de segurança.
- Elaboração de recomendações de mitigação.

## Segurança e autorização

Este projeto foi executado exclusivamente em ambiente virtual próprio, isolado por meio da rede Host-only do VirtualBox.

O Metasploitable 2 e o DVWA foram utilizados intencionalmente como alvos vulneráveis para fins educacionais.

Nenhum teste foi direcionado a sistemas de terceiros ou ambientes de produção.

As técnicas demonstradas devem ser utilizadas somente em ambientes próprios ou mediante autorização expressa.

## Autor

Projeto desenvolvido como parte da jornada de estudos em Cibersegurança, com foco em segurança ofensiva, auditoria de autenticação e análise de vulnerabilidades.

**Tecnologias e ferramentas:** Kali Linux, Medusa, Nmap, Enum4linux, SMBClient, DVWA e VirtualBox.

---

**Projeto concluído — Auditoria de autenticação com Kali Linux e Medusa.**