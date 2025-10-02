# 📘 Guia Detalhado dos Fluxos do OAuth 2.0

Este documento apresenta de forma resumida e organizada os principais fluxos de autorização da framework **OAuth 2.0**, explicando seus usos, vantagens, limitações e boas práticas. Embora muitas vezes confundidos com **fluxos de autenticação**, é importante destacar que o OAuth 2.0 trata essencialmente de **autorização**, enquanto a autenticação de usuários é abordada pelo protocolo de autenticação **OpenID Connect (OIDC)**, uma extensão sobre o OAuth 2.0.

---

## 📑 Sumário

1. [Papéis no OAuth 2.0](#-papéis-no-oauth-20)
2. [Tipos de Tokens](#️-tipos-de-tokens)
3. [Fluxos OAuth 2.0](#-fluxos-oauth-20)

   * [Authorization Code com PKCE](#1-authorization-code-com-pkce-recomendado)
   * [Authorization Code (sem PKCE)](#2-authorization-code-sem-pkce)
   * [Client Credentials](#3-client-credentials)
   * [Device Code](#4-device-code)
   * [Refresh Token](#5-refresh-token)
   * [Implicit Flow](#6-implicit-flow--obsoleto)
   * [ROPC](#7-resource-owner-password-credentials-ropc--obsoleto)
4. [Checklist: quando usar cada fluxo](#-checklist-quando-usar-qual-fluxo)
5. [Boas práticas de segurança](#-boas-práticas-de-segurança)

---

## 🔑 Papéis no OAuth 2.0

* **Resource Owner**: Usuário dono dos dados.
* **Client**: Aplicação que deseja acessar os recursos.
* **Authorization Server (AS)**: Responsável pela autenticação/autorização e emissão dos tokens.
* **Resource Server (RS)**: API protegida que valida tokens.

---

## 🎟️ Tipos de Tokens

* **Access Token**: curto prazo, usado em chamadas à API.
* **Refresh Token**: longo prazo, usado para renovar o access token.
* **ID Token (OIDC)**: identidade do usuário (JWT), não é token de acesso.

---

## 🔐 Fluxos OAuth 2.0

### 1. Authorization Code com PKCE (Recomendado)

* **Uso**: SPAs, Mobile, Web.
* **Passos**:

  1. Cliente gera `code_verifier` e `code_challenge`.
  2. Redireciona o usuário ao AS com `response_type=code`.
  3. Usuário autentica → AS retorna `code`.
  4. Cliente troca `code` + `code_verifier` por **access token**.
  5. Cliente chama a API com `Bearer <access_token>`.
* **Vantagem**: Proteção contra interceptação de código.

---

### 2. Authorization Code (Sem PKCE)

* **Uso**: Apps confidenciais (backend com segredo seguro).
* **Diferença**: Necessita `client_secret`.

---

### 3. Client Credentials

* **Uso**: Comunicação máquina→máquina (jobs, microsserviços).
* **Fluxo**: Apenas `client_id` e `client_secret` são utilizados para obter o token.

---

### 4. Device Code

* **Uso**: TVs, consoles, IoT.
* **Fluxo**:

  * Device obtém `device_code` e exibe `user_code` + URL.
  * Usuário autentica em outro dispositivo.
  * Device consulta até receber o token.

---

### 5. Refresh Token

* **Uso**: Renovar access token sem re-login.
* **Boas práticas**:

  * Rotação de tokens.
  * Em SPAs: evitar persistência local, usar cookies HttpOnly.

---

### 6. Implicit Flow ❌ (Obsoleto)

* **Problema**: Access token exposto na URL.
* **Não recomendado**. Use Authorization Code + PKCE.

---

### 7. Resource Owner Password Credentials (ROPC) ❌ (Obsoleto)

* **Problema**: Cliente coleta login/senha diretamente.
* **Não recomendado**.

---

## ✅ Checklist: Quando usar qual fluxo

* 🌐 **Web Backend**: Authorization Code + PKCE (com secret).
* 🖥️ **SPA**: Authorization Code + PKCE.
* 📱 **Mobile**: Authorization Code + PKCE.
* 🤝 **Serviço→Serviço**: Client Credentials.
* 📺 **TV/IoT**: Device Code.
* ❌ **Implicit/ROPC**: evitar.

---

## 🔒 Boas Práticas de Segurança

* Sempre usar **HTTPS**.
* Tokens com **curto TTL**.
* Usar **escopos mínimos**.
* Rotacionar secrets e refresh tokens.
* Configurar **CORS** restrito.
* Revogar tokens no logout.
