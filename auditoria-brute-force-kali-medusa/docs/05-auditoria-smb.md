
# Etapa 5 — Auditoria SMB com Enum4linux, Medusa e SMBClient

## 1. Objetivo

Realizar uma auditoria controlada do serviço SMB no Metasploitable 2, contemplando:

- Identificação dos serviços SMB.
- Enumeração de usuários.
- Teste controlado de password spraying com Medusa.
- Verificação de acesso anônimo aos compartilhamentos.
- Análise dos resultados e recomendações de segurança.

## 2. Ambiente utilizado

| Item | Informação |
|---|---|
| Sistema de auditoria | Kali Linux |
| IP do auditor | 192.168.56.101 |
| Sistema alvo | Metasploitable 2 |
| IP do alvo | 192.168.56.102 |
| Serviço | SMB |
| Ferramentas | Nmap, Enum4linux, Medusa e SMBClient |

## 3. Reconhecimento SMB

Foi realizada a identificação das portas utilizadas pelo serviço SMB:

```bash
nmap -p 139,445 -sV 192.168.56.102
```

### Resultado

As portas TCP 139 e 445 foram identificadas como abertas no alvo.

O endereço MAC retornado pelo Nmap pertence à interface de rede virtual do ambiente VirtualBox.

## 4. Enumeração de usuários

Foi utilizada a ferramenta Enum4linux para identificar usuários disponíveis no serviço SMB:

```bash
enum4linux -U 192.168.56.102
```

A saída apresentou diversas informações sobre o serviço, incluindo uma lista de usuários.

Foram identificados e registrados 35 usuários no arquivo:

`wordlists/usuarios-smb.txt`

## 5. Teste de password spraying com Medusa

Foi verificada a disponibilidade do módulo SMB:

```bash
medusa -d | grep -i smb
```

O módulo identificado foi:

`smbnt.mod` — Brute force module for SMB (SMBv1-3), versão 3.0.

Foi realizado um teste controlado utilizando uma única senha (`password`) contra a lista de usuários identificados.

### Resultado informado

| Parâmetro | Resultado |
|---|---|
| Hosts | 1 |
| Usuários | 35 |
| Senhas | 1 |
| Senha testada | password |
| ACCOUNT FOUND | Não identificado |

O teste não identificou credenciais válidas.

**Observação:** a ausência de `ACCOUNT FOUND` não comprova que todas as contas possuem senhas incorretas. Indica apenas que nenhuma autenticação válida foi identificada nessa execução.

## 6. Verificação de acesso anônimo

Foi utilizado o SMBClient para verificar a possibilidade de conexão sem credenciais:

```bash
smbclient -L //192.168.56.102 -N
```

### Resultado

O servidor retornou:

`Anonymous login successful`

Foram identificados cinco compartilhamentos:

- print$
- tmp
- opt
- IPC$
- ADMIN$

## 7. Testes individuais dos compartilhamentos

Foram realizadas tentativas de conexão anônima aos compartilhamentos identificados.

| Compartilhamento | Resultado |
|---|---|
| tmp | Conexão anônima permitida e listagem com `ls` permitida |
| opt | `NT_STATUS_ACCESS_DENIED` |
| print$ | Acesso negado |
| ADMIN$ | Acesso negado |
| IPC$ | Conexão permitida, mas listagem com `ls` não autorizada |

### Evidência principal

O compartilhamento `tmp` permitiu estabelecer uma sessão anônima e executar o comando `ls`, demonstrando exposição de conteúdo sem autenticação.

O compartilhamento `IPC$` aceitou a conexão, mas não autorizou a listagem de arquivos.

Os compartilhamentos `opt`, `print$` e `ADMIN$` não permitiram o acesso anônimo.

## 8. Análise de segurança

O principal achado foi a permissão de acesso anônimo ao compartilhamento `tmp`, incluindo a possibilidade de listar seu conteúdo.

Essa configuração pode permitir que usuários não autenticados obtenham informações armazenadas no compartilhamento.

A conexão anônima ao `IPC$`, por si só, não representa comprovação de exposição de arquivos, pois esse compartilhamento possui finalidade específica de comunicação entre processos.

O password spraying realizado não identificou credenciais válidas.

## 9. Recomendações de mitigação

- Desabilitar o acesso anônimo aos compartilhamentos SMB quando não houver necessidade operacional.
- Revisar as permissões do compartilhamento `tmp`.
- Aplicar o princípio do menor privilégio.
- Restringir o acesso SMB a redes e equipamentos autorizados.
- Utilizar senhas fortes e políticas contra tentativas repetidas de autenticação.
- Monitorar tentativas de autenticação e acessos anônimos.
- Desabilitar SMBv1 quando não for necessário.
- Manter o serviço SMB atualizado.

## 10. Conclusão

A auditoria identificou usuários por meio do Enum4linux e realizou um teste controlado de password spraying com o Medusa, sem identificar credenciais válidas.

Também foi confirmada a possibilidade de conexão anônima ao serviço SMB e identificada exposição de conteúdo no compartilhamento `tmp`.

Os resultados demonstram a importância de revisar permissões de compartilhamento, restringir acessos anônimos e monitorar os serviços de rede.