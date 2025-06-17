
# 📡 Projeto PAL6 – Protocolo Alternativo Leve para IPv6

## Introdução

O IPv6 foi projetado para substituir o IPv4 e resolver seu esgotamento de endereços, mas trouxe novos problemas que afetam privacidade, anonimato e segurança. O modelo PAL6 (Protocolo Alternativo Leve para IPv6) é uma proposta prática que busca resolver essas deficiências sem depender de estruturas pesadas como VPNs ou redes como o Tor, mantendo compatibilidade com sistemas modernos e legados.

---

## 🧠 Proposta Geral

PAL6 é uma arquitetura que insere um "relay local inteligente" (ex: um Raspberry Pi) entre os dispositivos do usuário e a internet. Esse relay faz a ponte entre:

- **Dispositivos internos** (IPv6 ou IPv4)
- **Um servidor unificado de retransmissão** (também chamado de *hub externo*)
- **Os servidores finais (públicos ou privados)**

O tráfego é encapsulado, roteado via o relay local, criptografado e então encaminhado para o servidor externo, que realiza a requisição final.

---

## 🎯 Objetivos

- Proteger a identidade real do usuário (IP)
- Garantir compatibilidade com serviços IPv4 e IPv6
- Eliminar dependência de NAT, mas manter seus benefícios
- Oferecer roteamento flexível, sem exposição direta
- Tornar desnecessário o uso constante de VPNs

---

## 🧩 Estrutura Técnica

1. **Relay local (hub)**: dispositivo que recebe todo o tráfego interno, faz controle, ofuscação e criptografia.
2. **Servidor de retransmissão (externo)**: interface com a internet pública, usando IPv4 ou IPv6 conforme necessário.
3. **Protocolo leve de encapsulamento**: ex: WireGuard, DTLS, HTTP/2-TLS camuflado
4. **Roteamento inteligente**: cada dispositivo pode ser configurado manualmente para seguir rotas específicas através do hub.

---

## 🛡️ Soluções para Problemas Comuns do IPv6

### 1. Exposição direta do IP
**Problema:** No IPv6, cada dispositivo tem um IP público e fixo.

**Solução PAL6:** Encapsula o tráfego e oculta o IP real. O servidor final vê apenas o IP do relay externo.

---

### 2. Rastreabilidade por prefixo /64
**Problema:** Mesmo com IP dinâmico, o prefixo /64 permite rastrear usuários de forma persistente.

**Solução PAL6:** O tráfego sai com um IP do servidor externo. O prefixo original nunca é revelado.

---

### 3. Ausência de NAT
**Problema:** O IPv6 elimina o NAT, o que expõe cada dispositivo diretamente.

**Solução PAL6:** O hub funciona como um NAT funcional, mesmo sem traduzir IPs, pois centraliza e separa o tráfego.

---

### 4. Falta de firewall padrão
**Problema:** Muitos roteadores não implementam firewalls adequados para IPv6.

**Solução PAL6:** O hub pode ser configurado com políticas de firewall estritas, funcionando como filtro.

---

### 5. Bloqueio de túneis por infraestruturas restritivas
**Problema:** Firewalls empresariais ou censura podem bloquear VPNs e túneis.

**Status:** ❌ Ainda não resolvido nativamente.

**Possível expansão:** Implementar transportes camuflados (ex: meek, obfs4, HTTP/2 over TLS).

---

### 6. Incompatibilidade com sites IPv4-only
**Problema:** Alguns sites só funcionam com IPv4.

**Solução PAL6:** O relay externo pode fazer a conexão com esses serviços via IPv4 mesmo que o usuário use IPv6.

---

### 7. Incompatibilidade com serviços legados internos (IPv4)
**Problema:** Alguns dispositivos ou serviços locais só entendem IPv4.

**Solução PAL6:** O hub local pode fazer ponte entre IPv4 e IPv6, permitindo compatibilidade total.

---

### 8. Redes mistas IPv4 ↔ IPv6
**Problema:** Comunicação local entre IPv4 e IPv6 pode falhar.

**Solução PAL6:** O relay atua como bridge proxy entre os protocolos, permitindo coexistência.

---

### 9. Dependência de VPNs
**Problema:** VPNs são usadas para privacidade básica, mas trazem sobrecarga.

**Solução PAL6:** O modelo já oferece IP masking e criptografia no núcleo. VPNs se tornam opcionais e podem ser integradas diretamente no relay.

---

## 🔁 Compatibilidade

PAL6 é compatível com:
- IPv6 nativo (entrada ou saída)
- IPv4 legado (via dual-stack no relay)
- Dispositivos modernos e antigos
- Servidores que não suportam IPv6

> Mesmo que o provedor do usuário bloqueie IPv6, o tráfego pode ser encapsulado e redirecionado por IPv4.

---

## 📜 Tabela Final de Problemas e Soluções

| Nº | Problema                                                                 | Resolvido? | Observações                                                                 |
|----|--------------------------------------------------------------------------|------------|------------------------------------------------------------------------------|
| 1  | Exposição direta do IP no IPv6                                           | ✅ Sim     | Túnel até o relay protege                                                    |
| 2  | Rastreabilidade por prefixo fixo /64                                     | ✅ Sim     | IP externo sempre dinâmico e externo ao usuário                             |
| 3  | Ausência de NAT no IPv6                                                  | ✅ Sim     | Relay centraliza e segmenta tráfego                                          |
| 4  | Falta de firewall padrão em roteadores domésticos                        | ✅ Sim     | Relay atua como barreira e ponto único de saída                              |
| 5  | Redes que bloqueiam túneis                                               | ❌ Não     | Exige camuflagem adicional (ex: meek, obfs4)                                 |
| 6  | Sites e serviços que só aceitam IPv4                                     | ✅ Sim     | Relay finaliza com IPv4 transparente                                         |
| 7  | Incompatibilidade com serviços IPv4-only                                 | ✅ Sim     | Relay resolve via dual-stack                                                 |
| 8  | Falta de compatibilidade entre dispositivos IPv4 ↔ IPv6                  | ✅ Sim     | Hub local atua como bridge ou proxy                                          |
| 9  | Necessidade de VPNs para privacidade básica                              | ✅ Sim     | Modelo já provê ofuscação, criptografia e IP masking por padrão             |

---

## Integração com Roteadores e Provedores

A arquitetura PAL6 pode ser implementada nativamente em **roteadores residenciais ou comerciais**, eliminando a necessidade de dispositivos externos para atuar como relay local.

### Benefícios da integração direta:
- **Sem configuração manual**: o roteador assume automaticamente a função de relay.
- **Privacidade plug-and-play**: o tráfego IPv6 é redirecionado via um servidor unificado, ocultando o IP real do cliente.
- **Desempenho otimizado**: com suporte a cache, compressão e roteamento inteligente.
- **Diferencial de mercado**: ISPs podem oferecer o PAL6 como recurso de “modo privacidade avançada”.

### Implementações possíveis:
- Firmwares como **OpenWRT** podem incluir suporte ao PAL6 nativamente.
- Provedores podem operar seus próprios **servidores de retransmissão** (ou federar com redes confiáveis).
- É possível adaptar a solução para funcionar de forma híbrida com **fallback para IPv4**, quando necessário.

Essa integração representa um avanço estratégico para devolver aos usuários o controle sobre suas conexões em redes IPv6 — com simplicidade, desempenho e segurança.

---

## 🧩 Considerações finais

- O modelo é **teoricamente compatível também com IPv4**, mas com utilidade reduzida nesse cenário, já que o NAT tradicional cobre muitas dessas lacunas.
- Pode ser integrado a soluções existentes como DNSCrypt, Tor ou proxies HTTPS como camada extra.
- Potencial para implementação rápida com ferramentas já existentes: WireGuard, dnsmasq, nftables, etc.

---

## 📌 Conclusão

PAL6 é um modelo simples, modular e eficaz que resolve as fragilidades centrais do IPv6 sem reinventar a roda, mantendo compatibilidade, privacidade e controle total para o usuário. É especialmente adequado para ativistas, jornalistas, usuários avançados e qualquer um que valorize o anonimato e segurança digital sem sacrificar desempenho ou interoperabilidade.

---

> Proposto por Henrique Gabriel © 2025 – Licenciado sob Creative Commons Attribution 4.0 International (CC BY 4.0).
> 
