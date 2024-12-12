# Ponderada-vulnerabilidades-mqtt-IPT

# Análise de Segurança do Projeto IoT

## 1. Vulnerabilidades do Projeto:

### 1.1 Comunicação Não Segura (HTTP e MQTT sem Criptografia)
- O dispositivo ESP32 está utilizando HTTP ao invés de HTTPS para comunicação com o servidor. Isso significa que os dados são transmitidos em texto claro, vulneráveis a interceptações (man-in-the-middle).
- A comunicação MQTT não utiliza TLS/SSL, expondo as mensagens a ataques de interceptação.

**Exemplos:**
- **Sniffing de Rede:** Um atacante pode capturar pacotes de rede para acessar informações confidenciais, como credenciais ou dados do dispositivo.
- **Hijacking de Sessão:** Um invasor pode interceptar sessões HTTP e assumir o controle da comunicação entre o dispositivo e o servidor.
- **MITM em MQTT:** Sem TLS, um atacante pode interceptar e modificar mensagens MQTT, causando comportamentos inesperados nos dispositivos IoT.

### 1.2 Falta de Autenticação e Controle de Acesso
- O dashboard não tem verificação de login ou autenticação de usuário, permitindo que qualquer pessoa acesse os dados sem controle de acesso.
- A falta de autenticação também possibilita que um atacante manipule os dados ou envie comandos falsificados ao sistema.
- Ausência de controle de acesso granular: Todos os usuários podem acessar os mesmos níveis de dados e comandos, aumentando o risco de uso indevido ou acesso não autorizado.

**Exemplos:**
- **Brute Force:** Um atacante pode testar múltiplas combinações de credenciais para acessar o sistema.
- **Acesso Não Autorizado:** Sem autenticação, qualquer pessoa com acesso à URL do dashboard pode visualizar e manipular dados.
- **Ataque de Escalada de Privilégio:** Sem controle granular, um usuário mal-intencionado pode executar comandos com privilégios elevados.

### 1.3 Banco de Dados sem Proteção
- O banco de dados PostgreSQL não possui camadas de segurança adicionais, tornando-se vulnerável a acessos não autorizados.
- A ausência de criptografia e autenticação adequada no banco de dados pode permitir que dados sensíveis sejam comprometidos.

**Exemplos:**
- **Injeção de SQL:** Um atacante pode manipular consultas ao banco de dados para obter informações ou alterar registros.
- **Exfiltração de Dados:** Sem criptografia, um invasor pode acessar dados diretamente do banco em caso de acesso não autorizado.
- **Ataques de Força Bruta em Credenciais do Banco:** Utilizando ferramentas automatizadas para tentar combinações de usuário e senha.

### 1.4 Gestão de Alertas sem Verificação ou Mitigação
- O sistema de alertas depende apenas de variáveis, sem verificação adicional para garantir a integridade dos dados.
- Isso pode permitir que alertas falsos ou dados manipulados sejam enviados, impactando a eficácia do sistema de monitoramento.

**Exemplos:**
- **Envio de Alertas Falsos:** Um atacante pode manipular as variáveis para criar alertas que distraiam a equipe de monitoramento.
- **Negação de Serviço (DoS):** Gerando uma sobrecarga de alertas falsos que tornam o sistema inoperante.
- **Modificação de Dados de Entrada:** Alterando variáveis críticas para suprimir alertas importantes, comprometendo a segurança do sistema.

### 1.5 Atualizações de Software Inseguras
- **Descrição:** Não há processo definido para atualização segura de firmware, expondo o sistema a pacotes maliciosos.
- **Impactos:**
  - Firmware comprometido pode permitir acesso remoto não autorizado.
  - Atualizações inválidas podem causar falhas em dispositivos.
- **Exemplo Real:** Em 2021, dispositivos IoT vulneráveis foram explorados após a distribuição de firmware falsificado.

## 2. Tabela de Ataques

| Título do Ataque                        | Nível de Impacto | Nível de Risco |
|-----------------------------------------|------------------|----------------|
| Man-in-the-middle (MITM)                | Alto             | Alto           |
| Intercepção de Dados MQTT               | Alto             | Alto           |
| Acesso Não Autorizado ao Dashboard      | Alto             | Alto           |
| Injeção de SQL                          | Médio            | Médio          |
| Manipulação de Alertas                  | Médio            | Médio          |
| Exposição de Dados do Banco sem Criptografia | Alto             | Alto           |

## 3. Medidas de Mitigação

### 3.1 Criptografia de Dados
- **Ações:**
  - Implementar **HTTPS** para toda a comunicação entre o ESP32 e o servidor.
  - Configurar o protocolo **MQTT** com **TLS/SSL** para proteger as mensagens transmitidas.
- **Benefícios:**
  - Garante a confidencialidade e integridade dos dados em trânsito.
  - Previne ataques de interceptação, como *man-in-the-middle (MITM)*.

### 3.2 Autenticação e Controle de Acesso
- **Ações:**
  - Introduzir autenticação robusta no dashboard, como **OAuth 2.0** ou **JWT**.
  - Implementar controle de acesso granular, permitindo diferentes permissões de acordo com o perfil do usuário.
- **Benefícios:**
  - Restringe o acesso apenas a usuários autorizados.
  - Reduz a possibilidade de ações maliciosas ou acessos indevidos.

### 3.3 Segurança no Banco de Dados
- **Ações:**
  - Configurar autenticação forte no PostgreSQL, utilizando senhas complexas e verificações adicionais.
  - Implementar criptografia para dados em trânsito e em repouso.
- **Benefícios:**
  - Protege os dados contra acessos não autorizados.
  - Assegura conformidade com regulamentações, como LGPD e GDPR.

### 3.4 Validação de Dados e Alertas
- **Ações:**
  - Criar mecanismos de validação para garantir que os dados não possam ser manipulados.
  - Estabelecer redundância em variáveis críticas para evitar falsos positivos ou alarmes manipulados.
- **Benefícios:**
  - Melhora a confiabilidade dos alertas emitidos pelo sistema.
  - Reduz o impacto de erros operacionais causados por dados inconsistentes.

### 3.5 Atualizações Seguras
- **Ações:**
  - Adotar assinatura digital para os pacotes de firmware, garantindo autenticidade.
  - Implementar verificações de integridade dos pacotes antes da aplicação.
  - Estabelecer um canal seguro de distribuição utilizando **HTTPS**.
- **Benefícios:**
  - Evita a instalação de firmware falsificado ou malicioso.
  - Garante a continuidade operacional após atualizações.

## 4. Outros Pontos de Atenção:

### 4.1 Atualizações e Manutenção do Sistema
- Estabelecer um processo seguro de *atualização de firmware e software*, garantindo que atualizações de fontes não confiáveis não sejam aplicadas.

### 4.2 Monitoramento de Segurança
- Implementar *monitoramento contínuo de segurança*, como logs de acesso e auditoria, para detectar atividades suspeitas no sistema.

## Conclusão:
O projeto, conforme está, possui várias vulnerabilidades críticas, especialmente no que diz respeito à falta de criptografia, autenticação e controle de acesso. Implementar as medidas de mitigação sugeridas ajudará a garantir que o sistema seja seguro e confiável para uso em ambientes de monitoramento de equipamentos de laboratório.

## Contribuição individual:
Cada membro do grupo desempenhou um papel na construção desse repositório, segue a seguir o papel de cada membro:

- **Ana:** Construção da estrutura e ideias iniciais do arquivo.
- **Eduardo:** Estruturou a falta de autenticação e controle de acesso.
- **Igor:** Apresentação.
- **Larissa:** Construção e adição de exemplos.
- **Pedro:** Divisão de tarefas e revisão geral.
- **Raphael:** Organização do repositório e formatação do texto para .md.
- **Ryan:** Revisão e adicionou 1.5 e adicionou os benefícios da seção 3