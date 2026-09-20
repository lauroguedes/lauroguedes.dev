---
featured: false
category: templates-and-starter-kits
title: Laravel SSO Starter Kit
description: >-
  A self-hosted OpenID Connect provider built as a readable Laravel application,
  with OAuth 2.0, per-application roles, user management, sessions, and audit tools.
image: '@assets/projects/laravel-sso/image.png'
startDate: 2026-09-08
endDate: 2026-09-19
skills:
  - Laravel
  - PHP
  - Inertia JS
  - Vue 3
  - Tailwind CSS
  - Pest
demoLink: https://sso.lauroguedes.dev/
sourceLink: https://github.com/lauroguedes/laravel-sso
contentSidebar:
  discriminant: false
---
Laravel SSO started with a gap in my own understanding. I had integrated with identity providers, but reading the OpenID Connect specifications was not the same as knowing how the protocol fits together. I built the server to work through those details in code and leave behind an application that other Laravel developers could read, deploy, and own.

It is a self-hosted identity provider rather than a package added to an existing application. You create a project from the starter kit, configure the issuer, generate signing keys, and run it as its own Laravel application. The services that connect to it can use any maintained OpenID Connect client library. They do not need to use Laravel or PHP.

## Protocol surface

Laravel Passport provides the OAuth 2.0 foundation. The application owns the OpenID Connect layer for discovery, the published key set, ID Tokens, UserInfo, introspection, revocation, and RP-initiated logout.

Version 2.0 moved that layer into the application behind interfaces. That made protocol behavior easier to follow and removed a package boundary that had become harder to reason about than the implementation itself.

The authorization flow handles details that are easy to miss when working only from integration guides:

- PKCE for public clients
- `nonce` from the authorization request carried into the ID Token
- `auth_time`, `max_age`, and login prompts tied to the user's real session
- remembered consent with configurable scope descriptions
- token introspection and revocation
- logout with validated post-logout redirects

The scope is deliberate. Laravel SSO does not implement SAML, LDAP, identity brokering, or federation between providers. It is smaller than an enterprise IAM platform because its job is narrower.

## Applications and access boundaries

Applications are registered through the administration interface. A server-rendered web application can use a confidential client and client secret. A browser or mobile application can use authorization code with PKCE and no stored secret.

Each application owns its redirect URIs, scopes, roles, managers, and user access. Tokens contain only the roles a person holds in the application requesting them. One application does not learn about roles assigned in another.

Client secrets are shown once and stored as hashes. Redirect URIs are matched exactly. These rules keep the configuration visible without making convenience more important than the protocol boundary.

## Running the identity service

The interface covers the work needed after installation:

- user accounts, email verification, password resets, passkeys, and two-factor authentication
- browser sessions and issued tokens with individual revocation
- application managers and scoped delegation
- audit records for administrative and security activity
- branding, color, layout, consent wording, and policy links

The installer generates signing keys, creates the platform roles, optionally creates the first administrator, and prints the endpoints needed by relying applications. Full documentation ships with the application and is served from `/docs`.

## Stack and validation

The project uses Laravel 13, PHP 8.3+, Passport 13, Inertia 3, Vue 3, Tailwind CSS 4, and Pest 5. It supports SQLite, MySQL, MariaDB, and PostgreSQL.

The v2.0.0 release is covered by 514 tests. Continuous integration runs the main checks on SQLite, repeats the suite against MySQL and PostgreSQL, and performs a production-style installation without development packages. The release commit passed that complete workflow.

## Why I kept it as an application

An identity provider holds protocol rules, product decisions, and operational state. Treating it as a complete application keeps routes, migrations, documentation, user experience, and deployment together. Teams can inspect the whole system and change it without waiting for an external hosted tier or fitting their requirements into a package abstraction.

Laravel SSO is useful when a team needs standard OAuth 2.0 and OpenID Connect for applications it controls, but wants the identity layer to remain ordinary Laravel code.
