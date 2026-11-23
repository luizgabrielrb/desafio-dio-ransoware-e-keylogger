# 🔐 Simulação Educacional de Malwares com Python — Ransomware e Keylogger

> Projeto desenvolvido como parte do desafio de código da Digital Innovation One (DIO), com o objetivo de **compreender, documentar e refletir** sobre o funcionamento de Ransomware e Keylogger, em ambiente 100% controlado e com finalidade exclusivamente educacional.

⚠️ **Aviso importante:** Este repositório não contém malware funcional, código de captura furtiva de teclado, nem automação de exfiltração de dados. Todo o conteúdo aqui é voltado para **entendimento conceitual, defesa e conscientização em segurança da informação**. Nenhum script deste projeto deve ser usado fora de um ambiente de testes controlado e de propriedade do próprio executor.

---

## 📚 Sumário

- [Objetivos de Aprendizagem](#-objetivos-de-aprendizagem)
- [Parte 1 — Ransomware Simulado](#-parte-1--ransomware-simulado)
- [Parte 2 — Keylogger Simulado (documentação conceitual)](#-parte-2--keylogger-simulado-documentação-conceitual)
- [Parte 3 — Reflexão sobre Defesa e Prevenção](#-parte-3--reflexão-sobre-defesa-e-prevenção)
- [Estrutura do Repositório](#-estrutura-do-repositório)
- [Como Executar a Demonstração](#-como-executar-a-demonstração)
- [Aprendizados](#-aprendizados)
- [Referências](#-referências)

---

## 🎯 Objetivos de Aprendizagem

- Compreender o funcionamento prático de Ransomware e Keylogger.
- Identificar como esses malwares exploram vulnerabilidades técnicas e falhas humanas.
- Entender os princípios criptográficos e de captura de dados envolvidos.
- Refletir sobre estratégias de defesa e prevenção contra malwares.
- Documentar o processo de estudo e utilizar o GitHub como portfólio técnico.

---

## 🦠 Parte 1 — Ransomware Simulado

### Como funciona um ransomware real (conceito)

Um ransomware típico segue, em linhas gerais, este fluxo:

1. **Infecção**: o malware chega à máquina da vítima via phishing, anexos maliciosos, exploração de vulnerabilidades ou downloads comprometidos.
2. **Varredura**: o malware percorre o sistema de arquivos em busca de documentos, imagens, planilhas e bancos de dados.
3. **Criptografia**: cada arquivo-alvo é cifrado com um algoritmo simétrico (ex: AES), geralmente combinado com criptografia assimétrica (RSA) para proteger a chave simétrica.
4. **Retenção da chave**: a chave de descriptografia **não fica com a vítima** — ela é enviada ou gerada do lado do atacante.
5. **Nota de resgate**: um arquivo de texto ou pop-up informa a vítima sobre o sequestro e exige pagamento (geralmente em criptomoeda) em troca da chave.

> 🔑 **O ponto crucial**: a técnica criptográfica em si (Fernet, AES, RSA) é legítima e usada todos os dias para proteger dados. O que torna isso um ataque é o **controle assimétrico da chave** — a vítima perde acesso a algo que lhe pertence, sem consentimento.

### O que implementamos aqui

Para demonstrar esse conceito sem criar uma ferramenta de ataque real, este repositório contém `scripts/demo_criptografia_fernet.py`, que:

- Opera **apenas** dentro da pasta `test_files/` deste repositório;
- Usa a biblioteca [`cryptography`](https://cryptography.io/) (Fernet, que implementa AES-128 em modo CBC com HMAC);
- Gera uma chave e salva localmente, de forma visível (diferente de um ataque real, onde a chave fica oculta/no atacante);
- Permite criptografar e, em seguida, **descriptografar** os arquivos de teste com a mesma chave;
- Exibe uma mensagem explicativa ao final do processo (não uma nota de resgate funcional).

Isso permite observar, na prática, o **efeito** da criptografia sobre arquivos — e entender por que, sem a chave correta, os dados se tornam irrecuperáveis.

### Diferença entre esta demonstração e um ransomware real

| Aspecto | Este projeto | Ransomware real |
|---|---|---|
| Escopo de arquivos | Apenas pasta `test_files/` | Todo o sistema (Documentos, Desktop, rede, etc.) |
| Posse da chave | Fica salva localmente, visível ao usuário | Fica apenas com o atacante |
| Propagação | Nenhuma | Pode se espalhar pela rede, USB, e-mail |
| Persistência | Nenhuma | Geralmente se instala para rodar no boot |
| Finalidade | Educacional | Extorsão financeira |

---

## ⌨️ Parte 2 — Keylogger Simulado (documentação conceitual)

Optei por **documentar em profundidade** este tópico em vez de implementar o keylogger funcional, já que — diferentemente da demo de criptografia acima, que pode ser contida a uma pasta de testes — um keylogger funcional captura **toda** a entrada de teclado do sistema operacional, o que representa um risco real mesmo em ambiente "controlado", e seu código seria diretamente reaproveitável como ferramenta de espionagem.

### Como funciona um keylogger real (conceito)

1. **Captura**: o malware se registra como um "ouvinte" (hook) de eventos de teclado no sistema operacional. Em Python, isso é tipicamente feito com a biblioteca [`pynput`](https://pypi.org/project/pynput/), que escuta eventos `on_press`/`on_release` globalmente — ou seja, captura teclas digitadas em **qualquer** janela ou aplicativo, não apenas no programa do atacante.
2. **Armazenamento**: as teclas capturadas são gravadas, geralmente em um arquivo de log local (ex: `.txt`), muitas vezes com timestamps e nome da janela ativa (para associar senhas a sites/apps específicos).
3. **Furtividade (stealth)**: versões maliciosas tentam se esconder do usuário:
   - Rodando sem janela visível (ex.: `pythonw.exe` no Windows, em vez de `python.exe`);
   - Registrando-se para iniciar automaticamente com o sistema (persistência via registro do Windows, cron jobs, LaunchAgents no macOS, etc.);
   - Ofuscando ou criptografando o próprio arquivo de log;
   - Nomeando o processo/arquivo para parecer um processo legítimo do sistema.
4. **Exfiltração**: periodicamente, o log é enviado ao atacante — por e-mail (via `smtplib`), upload para um servidor remoto, ou por serviços de mensagens (bots do Telegram, por exemplo), permitindo coleta remota sem acesso físico à máquina da vítima.

### Por que não implementei o código operacional

- A captura de teclado **global** por natureza não pode ser restrita a "arquivos de teste" — ela intercepta tudo que é digitado no sistema, incluindo senhas, mensagens pessoais e dados de terceiros.
- Adicionar qualquer elemento de furtividade (processo oculto, persistência) ou exfiltração automática (envio por e-mail) transforma a demonstração em uma ferramenta diretamente reutilizável para vigilância não autorizada — e esse é exatamente o tipo de artefato que evito produzir, independentemente do contexto educacional.
- O valor de aprendizado sobre **arquitetura e riscos** de um keylogger pode ser obtido plenamente pela documentação acima, sem necessidade do artefato funcional.

### Sinais de alerta (para detecção manual)

- Processos desconhecidos consumindo CPU/rede constantemente em segundo plano;
- Entradas estranhas em programas de inicialização automática;
- Tráfego de rede incomum para destinos desconhecidos (especialmente SMTP);
- Lentidão inexplicada ao digitar em aplicativos;
- Arquivos de log ou `.txt` desconhecidos sendo criados/atualizados continuamente em pastas do sistema.

---

## 🛡️ Parte 3 — Reflexão sobre Defesa e Prevenção

### 1. Antivírus / EDR (Endpoint Detection and Response)
- Soluções modernas de antivírus usam **detecção comportamental** (não só assinaturas), identificando padrões como "processo criptografando dezenas de arquivos em sequência" ou "hook global de teclado sendo instalado por um processo não assinado".
- Soluções EDR corporativas permitem resposta automática: isolar a máquina da rede ao detectar comportamento suspeito.

### 2. Firewall e segmentação de rede
- Bloquear tráfego de saída não autorizado dificulta a exfiltração de dados (ex: um keylogger tentando enviar e-mails via SMTP não autorizado) e a comunicação com servidores de comando e controle (C2) usados por ransomware.
- Segmentação de rede limita a propagação lateral de um ransomware entre máquinas.

### 3. Backups (regra 3-2-1)
- Manter 3 cópias dos dados, em 2 mídias diferentes, com 1 cópia off-site/offline.
- Backups **imutáveis** ou desconectados da rede são a defesa mais eficaz contra ransomware: mesmo que os arquivos originais sejam cifrados, é possível restaurar sem pagar resgate.

### 4. Sandboxing
- Executar arquivos e anexos suspeitos em ambientes isolados (máquinas virtuais, containers, serviços como qualquer sandbox online) antes de permitir que rodem no ambiente de produção.
- Esse projeto, por exemplo, foi desenvolvido e testado inteiramente em um ambiente contido, sem acesso à internet ou a arquivos reais do sistema.

### 5. Atualizações e gestão de vulnerabilidades
- Muitos ransomwares se espalham explorando vulnerabilidades conhecidas e não corrigidas (ex: EternalBlue no caso do WannaCry). Manter sistemas atualizados reduz drasticamente a superfície de ataque.

### 6. Conscientização do usuário (o elo mais importante)
- A maioria das infecções começa com **engenharia social**: phishing, anexos maliciosos disfarçados de boletos/currículos, links encurtados.
- Treinamentos regulares, simulações de phishing e uma cultura de "pensar antes de clicar" reduzem significativamente o risco — mais do que qualquer ferramenta técnica isolada.
- Autenticação multifator (MFA) reduz o impacto de credenciais capturadas por keyloggers, já que a senha sozinha deixa de ser suficiente para acesso.

### 7. Princípio do menor privilégio
- Usuários e processos devem ter apenas as permissões estritamente necessárias. Isso limita o alcance de um ransomware que rode com uma conta de usuário comum (sem privilégios administrativos) a pastas específicas, em vez do sistema inteiro.

---

## 📁 Estrutura do Repositório

```
.
├── README.md
├── scripts/
│   └── demo_criptografia_fernet.py
├── test_files/
│   ├── arquivo_teste_1.txt
│   └── arquivo_teste_2.txt
└── images/
    └── (capturas de tela da execução, opcional)
```

---

## ▶️ Como Executar a Demonstração

```bash
# 1. Instale a dependência
pip install cryptography

# 2. Execute o script a partir da pasta scripts/
cd scripts
python demo_criptografia_fernet.py

# 3. Siga o menu:
#    1 - Gerar nova chave
#    2 - Criptografar arquivos de teste
#    3 - Descriptografar arquivos de teste
```

Após criptografar, abra os arquivos em `test_files/` para observar que o conteúdo virou texto cifrado ilegível. Em seguida, use a opção 3 para restaurá-los e confirmar que a chave salva em `scripts/chave.key` é indispensável para reverter o processo.

---

## 💡 Aprendizados

- A criptografia é uma ferramenta neutra — seu uso (proteger dados vs. sequestrá-los) depende de **quem controla a chave** e **com qual consentimento**.
- Malwares de captura de dados exploram APIs legítimas do sistema operacional (hooks de teclado, agendadores de tarefas, protocolos de e-mail); a malícia está no **uso não autorizado**, não na tecnologia em si.
- A defesa mais eficaz combina camadas técnicas (backup, EDR, firewall) com camadas humanas (treinamento, MFA, postura de menor privilégio) — nenhuma camada isolada é suficiente.
- Documentar e entender essas técnicas é essencial para profissionais de segurança, mas deve ser feito com limites éticos claros sobre o que se implementa e compartilha publicamente.

---

## 🔗 Referências

- [Python – Documentação Oficial](https://docs.python.org/3/)
- [Cryptography (Fernet)](https://cryptography.io/)
- [pynput – Biblioteca de Captura de Teclado](https://pypi.org/project/pynput/)
- [smtplib – Envio de E-mails com Python](https://docs.python.org/3/library/smtplib.html)
- [GitHub Quick Start](https://github.com/digitalinnovationone/github-quickstart)
- [Documentação do GitHub](https://docs.github.com/)

---

> Projeto desenvolvido para fins exclusivamente educacionais, no contexto de um desafio de cibersegurança da DIO (Digital Innovation One).
