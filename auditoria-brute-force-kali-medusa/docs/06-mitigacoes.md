
# Etapa 6 — Mitigações e Recomendações de Segurança

## 1. Objetivo

Esta etapa apresenta as recomendações de segurança relacionadas aos resultados das auditorias FTP, Web/DVWA e SMB realizadas no laboratório isolado.

O objetivo é propor medidas que reduzam os riscos identificados e fortaleçam os mecanismos de autenticação, controle de acesso e monitoramento.

## 2. Mitigações para FTP

### Achado

Durante a auditoria FTP, foi possível identificar uma combinação de usuário e senha válida no ambiente de laboratório utilizando o Medusa.

O teste demonstrou a exposição de serviços de autenticação a tentativas automatizadas de descoberta de credenciais.

### Recomendações

- Preferir SFTP ou FTPS em substituição ao FTP sem criptografia.
- Utilizar senhas fortes e exclusivas.
- Implementar limitação de tentativas de autenticação.
- Monitorar tentativas repetidas de login.
- Bloquear ou restringir endereços IP com comportamento suspeito.
- Desabilitar contas e serviços que não sejam necessários.

## 3. Mitigações para aplicações Web

### Achado

Na aplicação DVWA, configurada no nível de segurança Low, foi possível identificar uma credencial válida por meio da funcionalidade Brute Force.

O teste demonstrou a ausência de proteções suficientes contra tentativas automatizadas de autenticação nessa configuração.

### Recomendações

- Implementar limitação de tentativas de login.
- Aplicar atrasos progressivos após falhas consecutivas.
- Implementar mecanismos de proteção contra automação.
- Utilizar autenticação multifator (MFA).
- Registrar e monitorar tentativas de autenticação.
- Armazenar senhas utilizando algoritmos seguros de hash, como Argon2id.
- Evitar mensagens de erro que revelem informações desnecessárias.

## 4. Mitigações para SMB

### Achados

A auditoria SMB identificou:

- Portas TCP 139 e 445 abertas.
- 35 usuários enumerados pelo Enum4linux.
- Conexão anônima permitida.
- Acesso anônimo ao compartilhamento `tmp`, incluindo listagem de conteúdo.
- Acesso ao `IPC$` sem autorização para listar arquivos.
- Acesso negado aos compartilhamentos `opt`, `print$` e `ADMIN$`.
- Nenhuma credencial válida identificada no teste de password spraying realizado.

### Recomendações

- Desabilitar o acesso anônimo quando não for necessário.
- Revisar as permissões dos compartilhamentos.
- Aplicar o princípio do menor privilégio.
- Restringir o acesso SMB às redes autorizadas.
- Desabilitar SMBv1 quando não houver necessidade operacional.
- Utilizar senhas fortes e políticas de autenticação adequadas.
- Monitorar tentativas de autenticação e enumeração de usuários.
- Manter o serviço SMB atualizado.

## 5. Boas práticas gerais

As seguintes medidas devem ser consideradas em todos os serviços auditados:

1. Utilizar credenciais fortes e exclusivas.
2. Evitar contas compartilhadas.
3. Aplicar o princípio do menor privilégio.
4. Manter sistemas e serviços atualizados.
5. Monitorar logs de autenticação.
6. Implementar mecanismos de detecção de comportamento suspeito.
7. Realizar auditorias periódicas.
8. Documentar vulnerabilidades e acompanhar sua correção.

## 6. Conclusão

As auditorias demonstraram como serviços FTP, aplicações Web e compartilhamentos SMB podem apresentar riscos quando não possuem controles adequados de autenticação e autorização.

A aplicação das recomendações propostas contribui para reduzir a exposição de credenciais, restringir acessos indevidos e melhorar a capacidade de monitoramento dos ambientes.

O trabalho reforça a importância de combinar ferramentas de auditoria, análise técnica dos resultados e medidas de mitigação.