# Estrutura do `iptables`

O `iptables` é composto por três componentes principais: **tabelas**, **cadeias (chains)** e **regras (rules)**. Vamos explorar cada um deles em detalhes:

## 1. Tabelas (Tables)

As **tabelas** definem o tipo de processamento que será realizado nos pacotes. As três principais tabelas são:

### 1.1. Tabela `filter`

A tabela `filter` é a mais usada e é responsável por **filtrar pacotes**. Nela, você define as regras que controlam o tráfego de entrada, saída e roteamento. Contém as seguintes cadeias:

- **`INPUT`**: Gerencia pacotes de entrada (destinados ao sistema local).
- **`FORWARD`**: Lida com pacotes que estão sendo roteados (não destinados ao sistema local).
- **`OUTPUT`**: Gerencia pacotes de saída (originados no sistema local).

### 1.2. Tabela `nat`

A tabela `nat` é usada para realizar a **tradução de endereços de rede (NAT)**. Isso permite, por exemplo, mascarar pacotes com o IP da interface de saída. Suas cadeias são:

- **`PREROUTING`**: Modifica pacotes antes de decidir para onde eles irão.
- **`POSTROUTING`**: Modifica pacotes após o roteamento, antes de eles saírem do sistema.
- **`OUTPUT`**: Modifica pacotes gerados localmente.

### 1.3. Tabela `mangle`

A tabela `mangle` é usada para **modificar campos específicos dos pacotes**, como o TOS (Type of Service) ou para outros ajustes avançados. Suas cadeias incluem:

- **`PREROUTING`**: Modifica pacotes antes de chegarem ao roteador.
- **`POSTROUTING`**: Modifica pacotes após o roteamento, antes de serem enviados para a interface de saída.
- **`INPUT`**: Modifica pacotes recebidos pelo sistema local.
- **`FORWARD`**: Modifica pacotes que estão sendo roteados através do sistema.
- **`OUTPUT`**: Modifica pacotes gerados localmente.

## 2. Cadeias (Chains)

As **cadeias** são conjuntos de regras aplicadas a pacotes em determinados pontos do fluxo de dados. Cada tabela contém suas próprias cadeias. As cadeias mais comuns são:

### 2.1. `INPUT`

Esta cadeia trata de pacotes que estão **entrando** no sistema, ou seja, pacotes destinados ao servidor local. Por exemplo, se alguém tenta acessar seu servidor via SSH ou HTTP, o pacote será verificado pela cadeia `INPUT`.

### 2.2. `OUTPUT`

A cadeia `OUTPUT` trata de pacotes que **saem** do sistema, ou seja, pacotes originados localmente e destinados a um endereço remoto. Por exemplo, quando seu servidor envia uma requisição HTTP para outro servidor.

### 2.3. `FORWARD`

A cadeia `FORWARD` gerencia pacotes que **passam pelo sistema** (não são nem destinados ao sistema nem originados nele). Essa cadeia é usada principalmente em roteadores ou firewalls que encaminham pacotes de uma rede para outra.

### 2.4. `PREROUTING`

Esta cadeia é usada em algumas tabelas (como `nat` e `mangle`) para processar pacotes **antes que o roteamento seja decidido**. Isso é útil para alterar o destino dos pacotes ou modificar outros atributos antes de determinar para onde o pacote deve ser enviado.

### 2.5. `POSTROUTING`

A cadeia `POSTROUTING` lida com pacotes **depois que o roteamento foi decidido**, mas antes de serem enviados pela interface de saída. Esta cadeia é normalmente usada para mascarar ou modificar pacotes após o roteamento.

## 3. Regras (Rules)

As **regras** são aplicadas a pacotes que passam por uma cadeia. Cada regra especifica critérios para os pacotes (como endereço IP, porta, protocolo) e uma **ação** que deve ser tomada se os pacotes atenderem a esses critérios. As ações mais comuns (também chamadas de **alvos**) são:

### 3.1. `ACCEPT`

Permite que o pacote siga seu curso e seja entregue ao destino final.

### 3.2. `DROP`

Descarta o pacote silenciosamente, sem notificar o remetente.

### 3.3. `REJECT`

Rejeita o pacote e envia uma mensagem de erro ao remetente, informando que a conexão foi recusada.

### 3.4. `LOG`

Registra o pacote no log do sistema para análise posterior.

### 3.5. `MASQUERADE`

Usada na tabela `nat` para mascarar pacotes com o endereço IP da interface de saída, geralmente em configurações de NAT.

### 3.6. `DNAT`

Modifica o endereço de destino dos pacotes, geralmente em configurações de redirecionamento de portas.

### 3.7. `SNAT`

Modifica o endereço de origem dos pacotes, geralmente em configurações de roteamento de saída.

## Fluxo de um pacote no `iptables`

Um pacote passa por diferentes cadeias dependendo da direção que ele toma:

- Pacotes que **entram** no sistema passam pelas cadeias `PREROUTING` e `INPUT`.
- Pacotes que **saem** do sistema passam pela cadeia `OUTPUT` e, possivelmente, `POSTROUTING`.
- Pacotes que são **roteados** através do sistema (em um roteador, por exemplo) passam pelas cadeias `PREROUTING`, `FORWARD` e `POSTROUTING`.

## Resumo

- **Tabelas**: Definem o tipo de processamento aplicado aos pacotes (ex.: filtragem, NAT).
- **Cadeias**: São pontos onde as regras são aplicadas para manipular ou bloquear pacotes.
- **Regras**: Definem ações baseadas em critérios específicos, como endereços IP, portas e protocolos.

Essa estrutura modular do `iptables` oferece flexibilidade e controle sobre o tráfego de rede, permitindo a criação de firewalls robustos para diversas situações.
