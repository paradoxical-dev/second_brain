---
tags:
  - Lua
  - Ollama
  - neollama
---


## Use Case
- Creates response from compiled information
- Treated as standard model as far as output handling

## WIP
### Appending 
- Need to append the sources and queries used during web search
- This will require me to store the used urls and queries; likely using the callbacks to transfer data

Got these done in a proof of concept. Would like to add configurable options for section title highlights. Works good so far.