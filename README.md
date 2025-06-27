# Administração de Redes Virtuais no Azure

Este material é um guia completo sobre administração de Redes Virtuais (VNets) no Microsoft Azure, com foco em práticas, conceitos fundamentais, segurança, endereçamento IP, peering, DNS e questões frequentes de prova para a certificação AZ-104.

---

## 📌 1. Conceito Básico de Rede Virtual (VNet)

- VNet é uma representação lógica da sua rede privada na nuvem Azure.
- Permite criar um ambiente isolado para executar VMs e outros recursos.
- É possível conectar sua rede local (on-premises) à nuvem via **VPN** ou **ExpressRoute**, criando uma **nuvem híbrida**.

---

## 🧱 2. Redes Virtuais e Sub-redes

- A VNet utiliza um **bloco de endereçamento IP privado**, como `10.0.0.0/16`.
- Dentro dela, você cria **sub-redes menores**, como `10.0.1.0/24`.
- Uma **sub-rede é criada automaticamente** ao criar uma VNet (pode ser editada).
- Toda VNet precisa de pelo menos uma sub-rede.

---

## 🔗 3. Comunicação entre Máquinas Virtuais

- **Mesma sub-rede:** comunicação nativa.
- **Sub-redes diferentes (mesma VNet):** comunicação automática.
- **VNets diferentes:** é necessário configurar **VNet Peering**.

---

## 🧠 4. Planejamento de Endereçamento IP

- **Evite sobreposição de IPs** com redes locais.
  - Ex: se on-prem usa `192.168.1.0/24`, não use esse bloco na VNet.
- Use:
  - **IP Privado:** para comunicação interna (VNets, VPN).
  - **IP Público:** para comunicação com a internet.

---

## 🌐 5. Endereços IP no Azure

| Tipo de IP     | Usado para                        | Atribuição          | Exemplo de Uso                     |
|----------------|-----------------------------------|---------------------|------------------------------------|
| IP Privado     | Comunicação interna na VNet       | Dinâmico ou Estático| NIC de VM, Load Balancer interno   |
| IP Público     | Comunicação com a internet        | Dinâmico ou Estático| VMs públicas, Gateways, LB externo |

- Por padrão, o Azure atribui IP dinâmico. IP estático deve ser configurado.
- IPs públicos estáticos podem ser **reservados**.

---

## ⚙️ 6. Criação de VNet e Sub-rede no Portal Azure

Ao criar uma VNet, você define:

- Nome (ex: `VNET-APP01`)
- Espaço de endereço (ex: `10.0.0.0/16`)
- Sub-rede(s) (ex: `10.0.1.0/24`)
- Grupo de recursos e região

> 🎯 **Resumo:**  
> - **VNet:** isolamento lógico.  
> - **Sub-rede:** segmentação de recursos (ex: web, banco de dados).

---

## 🔁 7. Comunicação entre Redes

- VNets diferentes não se comunicam por padrão.
- Para comunicação entre VNets, use **VNet Peering**.
- Para conectar rede local ao Azure: **VPN Gateway** ou **ExpressRoute**.

---

## 🔒 8. Segurança e Desempenho

- Sub-redes permitem **segmentação** e **controle com NSGs**.
- Melhoram a **organização** e o **tráfego interno**.

---

## 📊 9. SKU e Associação de IPs

### IP Público

| Recurso            | Associação | Dinâmico | Estático |
|--------------------|------------|----------|----------|
| Máquina Virtual     | Sim        | Sim      | Sim      |
| Load Balancer       | Sim        | Sim      | Sim      |
| Gateway VPN         | Sim        | Sim      | Sim      |
| Gateway de Aplicativo | Sim      | Sim      | Sim      |

### IP Privado

| Recurso              | Associação | Dinâmico | Estático |
|----------------------|------------|----------|----------|
| Máquina Virtual       | Sim        | Sim      | Sim      |
| Load Balancer Interno | Sim        | Sim      | Sim      |
| Gateway de Aplicativo | Sim        | Sim      | ❌        |

### SKU de IP Público

- **Básico:** menor custo, funcionalidade limitada.
- **Standard:** mais segurança, alta disponibilidade.

---

## 💡 10. Cenário de Prova – IP

- Precisa de acesso público? 👉 **IP Público**
- Segurança interna? 👉 **IP Privado + JumpBox ou VPN**

---

## 🔄 11. Peering entre VNets

- Permite comunicação **privada** entre VNets (mesmo entre regiões).
- Utiliza o **backbone da Microsoft** → baixa latência e alta performance.

---

## 🔎 12. Resumo Rápido de IPs

- **IP Público:** acesso externo (dinâmico ou estático).
- **IP Privado:** comunicação interna.
- A escolha depende da **exposição** e **alta disponibilidade (Zonas)**.

---

## 🔐 13. NSG (Network Security Group)

- **Controle de tráfego** de entrada e saída.
- Aplicável em:
  - Sub-redes
  - NICs
- **Boas práticas:**
  - Prefira aplicar em **sub-redes**.
  - Evite NSGs duplicados.
  - Deixe espaços entre prioridades (100, 200, ...).

### Estrutura da Regra
![imagem1](https://github.com/DurezahGeek/az-104_Administrar-rede-virtual/blob/main/SRCARV/1.png)
**Assim que uma regra é atendida, as demais são ignoradas**

- Origem/Destino (IP, tag, grupo)
- Porta
- Protocolo
- Prioridade (menor = maior prioridade)
- Ação: permitir ou negar

---

## 🎯 14. ASG (Application Security Group)

- Agrupa VMs por **função lógica**, não por IP.
- Exemplo:

| Origem     | Destino   | Porta     |
|------------|-----------|-----------|
| Internet   | WebServers| 80, 443   |
| WebServers | DBServers | 1433      |

---

## 🔐 15. Azure Firewall, Bastion e DDoS

### 🔥 Azure Firewall

- Firewall gerenciado (Camadas 3 a 7)
- Suporte a FQDNs, regras SNAT/DNAT, integração com Azure Monitor

### 🧍 Azure Bastion

- Acesso RDP/SSH via navegador
- Sem IP público na VM

### 💣 Azure DDoS Protection

- **Basic:** ativado por padrão.
- **Standard:** proteção avançada, relatórios e alertas.

---

## 🌐 16. DNS no Azure

### Domínio Padrão

- Criado automaticamente: `nomedoconteudo.onmicrosoft.com`

### Domínio Personalizado

- Adicionar no Azure Entra ID
- Criar registro **TXT ou MX** no provedor externo
- Verificar no portal

### Azure DNS

- Hospeda zonas públicas ou privadas

#### Tipos de Zona

| Tipo       | Descrição                                 |
|------------|-------------------------------------------|
| Pública    | Acesso externo via internet               |
| Privada    | Comunicação interna entre VNets           |

#### Tipos de Registro

- A / AAAA: nome → IP (IPv4 / IPv6)
- MX: e-mail
- TXT: verificação
- CNAME: alias

---

## ✅ 17. Questões de Prova (Resumo)

1. Rede Virtual: base da rede privada no Azure  
2. Sub-redes se comunicam nativamente  
3. VNets diferentes não se comunicam → precisa de Peering  
4. VNet cria sub-rede padrão automaticamente  
5. Planeje IPs e evite sobreposição  
6. Regra NSG padrão: permite tráfego intra-sub-rede  
7. NSG aplica-se em sub-rede e NIC  
8. Regra de menor número tem maior prioridade  
9. NSG é o método mais econômico de segurança  
10. NSG controla o tráfego de rede  
11. Regras NSG seguem prioridade crescente  
12. Deixe espaços entre prioridades NSG (100, 200...)  
13. Registros DNS para IPs: A (IPv4), AAAA (IPv6)  
14. Azure DNS gerencia domínios e registros  
15. Domínio personalizado → Administrador Global  
16. Validação de domínio com registro TXT/MX  
17. Zona DNS pública: nomes acessíveis externamente  
18. Zona DNS privada: resolução entre VNets  
19. IP correto evita conflitos e falhas  
20. Liberar tráfego de entrada? → regras NSG (ex: 80/443)  
21. Criar VNet: nome, grupo, região, CIDR, sub-rede  
22. Peering conecta VNets para tráfego privado  
23. NSG preferencialmente em sub-rede  
24. ASG cria regras por função (não por IP)  
25. Validar domínio: registrar TXT/MX no provedor  
26. IP fixo: atribuir IP estático  
27. Proteger VM sem IP público: usar Bastion  
28. Ataques DDoS? Usar DDoS Protection Standard  

---

## 📚 Referências

- [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network)  
- [Criar VNet no Portal](https://learn.microsoft.com/azure/virtual-network/quick-create-portal)  
- [Visão Geral das VNets](https://learn.microsoft.com/azure/virtual-network/virtual-networks-overview)  
- [Segurança com NSG](https://learn.microsoft.com/azure/virtual-network/security-overview#network-security-groups)  
- [Gerenciar NSG](https://learn.microsoft.com/azure/virtual-network/manage-network-security-group)  
- [Filtrar Tráfego de Rede](https://learn.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)  
- [Visão Geral do Azure DNS](https://learn.microsoft.com/azure/dns/dns-overview)  
- [Introdução ao DNS no Azure](https://learn.microsoft.com/azure/dns/dns-getstarted-portal)  
- [Zonas e Registros DNS](https://learn.microsoft.com/azure/dns/dns-zones-records#dns-zones)

---

> 📘 Material criado como apoio aos estudos da certificação **AZ-104 – Microsoft Azure Administrator** feito por mim, assistindo as aulas de AZ-104.

