# Padronização para flow de projetos de desenvolvimento NodeJS, React e React Native

Apenas uma forma de organizar o flow de trabalho para novos projetos nesta stack.

## Ambiente de Desenvolvimento

### VS Code

- VS Code - [Download](https://code.visualstudio.com/download)
- Thema: Drácula - [Download](https://draculatheme.com/visual-studio/)
- Fonts (ligatures): fira code - [Download](https://github.com/tonsky/FiraCode)
- Icons: Material Icon Theme - [Download](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)
- Color: Color Highlight - [Download](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)
- Rocketseat ReactJS e React Native
- MarkDown All in One - [Download](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)
- Live Server: [Download](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)
- 

#### Rullers and Settings (cmd + shift + p)

```
{
  "editor.fontSize": 16,
  "javascript.updateImportsOnFileMove.enabled": "always",
  "workbench.colorTheme": "Dracula",
  "workbench.iconTheme": "material-icon-theme",
  "explorer.confirmDelete": false,
  "editor.fontFamily": "Fira Code",
  "editor.fontLigatures": true,
  "editor.rulers": [80, 120],
  //Elint preferences
  "eslint.autoFixOnSave": true,
  "eslint.validate": [
    {
      "language": "javascript",
      "autoFix": true
    },
    {
      "language": "javascriptreact",
      "autoFix": true
    }
    
  ],
  //
  "editor.renderLineHighlight": "gutter",
  "editor.tabSize": 2,
  "terminal.integrated.fontSize": 14,
  "emmet.includeLanguages": {
    "javascript": "javascriptreact"
  },
  "emmet.syntaxProfiles": {
    "javascript": "jsx"
  },
  "javascript.updateImportsOnFileMove.enabled": "never",
  "editor.parameterHints.enabled": false,
  "breadcrumbs.enabled": true,
  "javascript.suggest.autoImports": false,
  "window.zoomLevel": 0
}

```
### Terminal

- Font: Fira Code - [Download](https://github.com/tonsky/FiraCode)
- Theme: Drácula Theme [Download](https://draculatheme.com/terminal/)
- Oh My Zsh: 
  - [Download](https://ohmyz.sh/)
  - [Download SpacesShip](https://github.com/denysdovhan/spaceship-prompt)
  - [Gerenciador de plugins Zsh](https://github.com/zdharma/zplugin)

#### Settings Zsh

```
SPACESHIP_PROMPT_ORDER=(
  user
  dir
  host
  git
  exec_time
  line_sep
  vi_mode
  jobs
  exit_code
  char
)

SPACESHIP_PROMPT_ADD_NEWLINE=false
SPACESHIP_CHAR_SYMBOL="goAndy |>"
SPACESHIP_CHAR_SUFFIX=" "
### Added by Zplugin's installer
source '/Users/USERNAME/.zplugin/bin/zplugin.zsh'
autoload -Uz _zplugin
(( ${+_comps} )) && _comps[zplugin]=_zplugin
### End of Zplugin installer's chunk

zplugin light zsh-users/zsh-autosuggestions
zplugin light zsh-users/zsh-completions
zplugin light zdharma/fast-syntax-highlighting
```
### Chrome Extensions

- React Developer Tools: [Download](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- Dracula Theme for DevTools: [Download](https://chrome.google.com/webstore/detail/dracula-theme-for-devtool/gedipeckgflanbhlcglokjjacilfidda?hl=pt-BR&)
- Dark Reader: [Download](https://chrome.google.com/webstore/detail/dark-reader/eimadpbcbfnmbkopoojfekhnkhdbieeh?hl=pt-BR)
- Live Server: [Download](https://chrome.google.com/webstore/detail/live-server-web-extension/fiegdmejfepffgpnejdinekhfieaogmj)
- Json Viewer: [Download](https://github.com/tulios/json-viewer)

### Tools
- Insomnia: [Download](https://insomnia.rest/download/)
- Devdocs [Download](https://devdocs.egoist.moe/)
- 