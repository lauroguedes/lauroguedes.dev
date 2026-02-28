---
featured: true
title: Passphrase Generator
description: >-
  A cryptographically secure passphrase generator built with the EFF Dice-Roll
  Method. Generate memorable, high-entropy passphrases entirely in your browser.
  No data ever leaves your device.
image: '@assets/projects/passphrase-generator/image.png'
startDate: 2026-02-27
endDate: 2026-02-28
skills:
  - Next JS
  - TypeScript
  - Taiwind CSS
  - Framer Motion
  - Web Crypto API
demoLink: https://diceware.lauroguedes.dev/
sourceLink: https://github.com/lauroguedes/passphrase-gen
---
## Project Purpose

The Passphrase Generator project is a web-based application designed to generate secure and memorable passphrases directly in the browser. The main goal of the project is to provide a transparent and trustworthy way to create strong passwords using the Diceware method developed by the Electronic Frontier Foundation.

Unlike traditional password generators, this project focuses on generating passphrases composed of random words, which are easier to remember while still maintaining high entropy and strong resistance against brute-force attacks. All generation happens locally in the browser, ensuring that no sensitive data is sent to external servers.

## Technologies Used

The project was built using a modern web stack focused on performance and user experience:

- **Next.js** – Application framework with modern routing and rendering
- **TypeScript** – Type safety and maintainability
- **Tailwind CSS** – Utility-first styling
- **Framer Motion** – UI animations and transitions
- **Lucide React** – Icon system
- **Web Crypto API** – Cryptographically secure randomness via `crypto.getRandomValues()`

The application uses cryptographic randomness and rejection sampling to simulate fair dice rolls digitally, ensuring unbiased passphrase generation.

## Key Features

Some of the main features include:

- Generation of secure passphrases using EFF wordlists
- Configurable word count and separators
- Optional symbols, numbers, and capitalization
- Real-time entropy calculation
- Fully client-side generation
- Responsive interface
- Dark mode support

The generator supports multiple wordlists, including large and short lists, allowing users to balance between memorability and entropy.

## What I Learned

During the development of this project, several important concepts were explored:

- Implementing cryptographically secure randomness in the browser
- Understanding entropy and password strength
- Working with modern React architecture using Next.js
- Structuring a scalable frontend project
- Creating reusable components and hooks
- Designing user-friendly security tools
- Implementing client-side only applications without APIs

This project also helped deepen the understanding of how passphrase entropy works and why randomness quality is critical in security applications.

## Credits

This project was inspired by the Diceware methodology originally proposed by [Arnold G. Reinhold](https://theworld.com/~reinhold/diceware.html) and later popularized by the [Electronic Frontier Foundation (EFF)](https://www.eff.org/dice).

The project is open source and available on GitHub under the MIT License.
