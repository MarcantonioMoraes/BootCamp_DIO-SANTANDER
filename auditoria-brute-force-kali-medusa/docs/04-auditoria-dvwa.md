

# Etapa 4 — Auditoria Web com DVWA

## Objetivo

Avaliar a segurança da autenticação Web utilizando a ferramenta Medusa e a aplicação DVWA (Damn Vulnerable Web Application), simulando tentativas automatizadas de descoberta de senhas.

## Ambiente utilizado

- Atacante: Kali Linux — 192.168.56.101
- Alvo: Metasploitable 2 — 192.168.56.102
- Aplicação: DVWA
- Nível de segurança: Low
- Ferramenta: Medusa 2.3
- Módulo: web-form

## Preparação da aplicação

O DVWA foi acessado pelo navegador do Kali Linux.

Para a realização do teste, foi utilizada a funcionalidade:

**Vulnerabilities → Brute Force (Força Bruta).**

O nível de segurança da aplicação foi configurado como Low.

O teste foi realizado diretamente na página Brute Force do DVWA, e não no formulário principal de login da aplicação.

## Wordlists utilizadas

Foi criado um arquivo específico para a auditoria.


### Arquivo de senhas

`wordlists/senhas-dvwa.txt`

Senhas utilizadas:

password
123456
admin


A lista foi criada exclusivamente para os testes no ambiente virtual.

## Inspeção do formulário

Durante a análise da funcionalidade Brute Force, foram identificadas as seguintes informações:

| Parâmetro | Informação |
|---|---|
| Página | /dvwa/vulnerabilities/brute/ |
| Método HTTP | GET |
| Campo de usuário | username |
| Campo de senha | password |
| Campo de envio | Login |
| Mensagem de falha | username and/or password incorrect |

Também foi necessário utilizar o cookie de sessão PHPSESSID para permitir o acesso à funcionalidade protegida.

Por segurança, o valor do cookie não foi incluído na documentação.

## Execução com Medusa

Foi utilizado o módulo `web-form` do Medusa para realizar as tentativas de autenticação.

Configurações utilizadas:

| Parâmetro | Configuração |
|---|---|
| Alvo | 192.168.56.102 |
| Usuário | admin |
| Wordlist | senhas-dvwa.txt |
| Módulo | web-form |
| Formulário | /dvwa/vulnerabilities/brute/ |
| Método | GET |
| Nível DVWA | Low |

## Resultado obtido

Durante a execução, o Medusa apresentou a mensagem:

ACCOUNT FOUND

O resultado indicou uma combinação de usuário e senha aceita pela aplicação.

Posteriormente, foi realizado um teste manual utilizando a combinação identificada pelo Medusa.

**Resultado da validação manual: SUCESSO.**

O acesso foi confirmado na aplicação DVWA.

## Análise dos resultados

O teste demonstrou que a configuração de segurança Low permite a realização de tentativas automatizadas de autenticação utilizando uma lista de senhas.

A utilização de uma wordlist pequena permitiu demonstrar o funcionamento de um ataque de força bruta em ambiente controlado.

A confirmação manual da credencial encontrada foi importante para validar o resultado apresentado pela ferramenta.

## Recomendações de segurança

- Utilizar senhas fortes e exclusivas.
- Implementar limitação de tentativas de autenticação.
- Adotar atrasos progressivos após falhas consecutivas.
- Monitorar tentativas suspeitas de acesso.
- Implementar autenticação multifator.
- Utilizar mecanismos de proteção contra automação abusiva.
- Manter aplicações e componentes atualizados.

## Conclusão

A auditoria foi concluída com sucesso.

Foi possível realizar um teste automatizado de autenticação utilizando o Medusa na funcionalidade Brute Force do DVWA, identificar uma combinação válida de usuário e senha e confirmar manualmente o acesso à aplicação.

A atividade demonstrou os riscos associados à utilização de senhas previsíveis e à ausência de mecanismos adequados de proteção contra tentativas automatizadas de autenticação.

Todos os testes foram realizados em ambiente virtual isolado e autorizado, exclusivamente para fins educacionais.