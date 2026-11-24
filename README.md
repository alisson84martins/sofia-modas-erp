# Sofia Modas ERP

**Gestão de estoque para uma loja de moda feminina, pensada para rodar no celular das vendedoras.**

> 🔒 **Vitrine do projeto.** O código-fonte é privado porque o sistema está em uso real numa loja de São Paulo. Aqui estão o problema, as decisões e a arquitetura. O código pode ser apresentado sob demanda.

## O problema

Numa loja de calças e bermudas, cada peça existe em várias cores e tamanhos. Controlar isso em caderno ou planilha gera saldo errado, venda de peça que não existe mais e nenhuma pista de quem mexeu no estoque.

## O que o sistema faz

- **Produto com variações:** cada combinação cor × tamanho tem SKU, saldo e código de barras próprios (ex.: `BER-GOR-PRE-G-01`, Code 128)
- **Estoque como livro-razão:** o saldo só muda por movimentos (entrada, saída, venda, ajuste), sempre com usuário, data e motivo. Nunca fica negativo
- **Importação de planilha** (.xlsx/.csv) com simulação antes de gravar, erro apontado por linha e segurança para reimportar sem duplicar
- **Quatro perfis** (dono, admin, gerente, vendedora) com permissões checadas no servidor
- **Auditoria** de login, usuários, preços, ajustes, importações e exportações
- **PWA mobile-first:** instala no celular, lê código de barras pela câmera ou por leitor USB/Bluetooth e imprime etiqueta

> A vendedora não vê custo nem margem, e não é porque a tela esconde: **a API simplesmente não envia esses dados** para o perfil dela.

## Arquitetura

| Camada | Tecnologia |
|---|---|
| **Front** | React 18, Vite, PWA (service worker), JsBarcode |
| **API** | FastAPI, SQLAlchemy 2, Pydantic 2, JWT, bcrypt |
| **Banco** | PostgreSQL 16 (SQLite nos testes rápidos), migrações com Alembic |
| **Qualidade** | pytest (46 testes), Ruff, GitHub Actions |
| **Entrega** | Docker Compose e script de backup |

**CI:** a cada envio rodam lint, os testes nos dois bancos, a sequência completa de migrações (upgrade → downgrade → upgrade → check) e o build do front.

## Decisões de arquitetura

Cada escolha foi registrada como ADR (Architecture Decision Record), com o problema, as alternativas e o motivo:

- **PWA em vez de app nativo:** instala direto do navegador, sem loja de aplicativos
- **Estoque como livro-razão:** o saldo é consequência dos movimentos, nunca editado na mão
- **Produto com variações:** a grade cor × tamanho é parte do modelo, não um campo de texto
- **Permissões no servidor:** a regra vale mesmo se alguém mexer na tela
- **SQLite × PostgreSQL:** testes rápidos locais e produção robusta, no mesmo código

## Segurança

- Senha em bcrypt, política mínima e troca obrigatória no primeiro acesso
- Bloqueio após 5 tentativas erradas, com a mesma mensagem para usuário inexistente
- Trocar ou redefinir a senha derruba as sessões abertas
- Ninguém altera o próprio perfil, e o sistema nunca fica sem dono
- Em produção, a aplicação **se recusa a subir** com segredo padrão ou CORS aberto

## Próximos passos

Fotos dos produtos, cadastro de clientes (com consentimento LGPD), vendas/PDV, inventário por contagem, relatórios de giro e curva ABC e integração com marketplace.

---

**Alisson Martins** · Levantamento, modelagem e desenvolvimento
[LinkedIn](https://www.linkedin.com/in/alisson-martins-2a350238a) · [E-mail](mailto:alisson84martins@gmail.com)
