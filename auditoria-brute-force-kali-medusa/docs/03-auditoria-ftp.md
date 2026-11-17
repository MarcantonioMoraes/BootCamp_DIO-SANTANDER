
# Etapa 3 — Auditoria de autenticação FTP

## Objetivo
Avaliar a segurança da autenticação do serviço FTP no Metasploitable 2 utilizando Nmap e Medusa.

## Ambiente
- Atacante: Kali Linux — 192.168.56.101
- Alvo: Metasploitable 2 — 192.168.56.102
- Serviço: FTP
- Porta: 21/TCP
- Versão identificada: vsftpd 2.3.4

## Reconhecimento

O Nmap identificou o serviço vsftpd 2.3.4 na porta 21.

O banner retornado pelo servidor foi:

220 vsFTPd2.3.4

## Teste de autenticação

Foi utilizada a ferramenta Medusa, com o módulo FTP e uma wordlist pequena criada exclusivamente para o laboratório.

Resultado observado:

ACCOUNT FOUND

O resultado indica que uma das combinações de usuário e senha testadas foi aceita pelo serviço FTP.

## Conclusão

O teste demonstrou que o serviço aceitou uma credencial presente na wordlist utilizada. Isso evidencia o risco de utilização de senhas previsíveis ou conhecidas.

O resultado não representa uma avaliação completa da resistência do serviço a ataques de força bruta.

## Recomendações de segurança

- Utilizar senhas fortes, únicas e não previsíveis.
- Desabilitar acesso anônimo, caso não seja necessário.
- Preferir SFTP ou FTP protegido por TLS.
- Implementar limitação de tentativas de autenticação.
- Monitorar logs de autenticação.
- Atualizar o serviço para uma versão suportada e corrigida.

## Observação

A atividade foi realizada exclusivamente em ambiente virtual isolado e autorizado.