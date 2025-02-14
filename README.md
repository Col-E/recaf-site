# Recaf Site

This repo contains the source for the Recaf home page, and user/developer documentation.

## Stack

[`mdbook`](https://github.com/rust-lang/mdBook) is used to convert markdown files into the static site you see at [recaf.coley.software](https://recaf.coley.software/home.html).

mdbook plugins used:

- [emojicodes](https://github.com/blyxyas/mdbook-emojicodes) - For converting things like `:x:` into :x:
- [mermaid](https://github.com/badboy/mdbook-mermaid) - For rendering [`mermaid.js` diagram models](https://mermaid.js.org/intro/)