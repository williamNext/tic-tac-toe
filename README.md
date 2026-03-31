# 🎮 Tic Tac Toe

Um jogo da velha moderno e elegante construído com **Vue 3** e **Vite**, com animações suaves, placar persistente e uma celebração épica ao vencer!

![Vue 3](https://img.shields.io/badge/Vue-3.x-42b883?style=flat-square&logo=vue.js)
![Vite](https://img.shields.io/badge/Vite-4.x-646cff?style=flat-square&logo=vite)
![License](https://img.shields.io/badge/licença-MIT-blue?style=flat-square)

---

## ✨ Funcionalidades

- 🎯 **Jogo da Velha completo** — dois jogadores, detecção automática de vitória e empate
- 🏆 **Animação de vitória** — overlay em tela cheia com troféu, texto pulsante e chuva de confete
- 📊 **Placar persistente** — pontuação de X, O e empates mantida durante a sessão
- ✨ **Animações fluidas** — peças entram com efeito "pop", células vencedoras brilham em dourado
- 🎨 **Design moderno** — tema escuro com gradiente, glassmorphism e efeitos de glow
- 📱 **Responsivo** — funciona bem em desktop e dispositivos móveis

---

## 🛠️ Tecnologias

| Tecnologia | Versão | Função |
|---|---|---|
| [Vue 3](https://vuejs.org/) | ^3.4 | Framework reativo (Composition API) |
| [Vite](https://vitejs.dev/) | ^4.0 | Bundler e servidor de desenvolvimento |
| [@vitejs/plugin-vue](https://github.com/vitejs/vite-plugin-vue) | ^4.0 | Suporte a Single File Components `.vue` |

---

## 📋 Pré-requisitos

Antes de começar, certifique-se de ter instalado:

- [Node.js](https://nodejs.org/) **v16 ou superior**
- [npm](https://www.npmjs.com/) (já vem com o Node.js)

Para verificar se estão instalados:

```bash
node --version
npm --version
```

---

## 🚀 Como rodar o projeto

### 1. Clone o repositório

```bash
git clone https://github.com/williamNext/tic-tac-toe.git
cd tic-tac-toe
```

### 2. Instale as dependências

```bash
npm install
```

### 3. Inicie o servidor de desenvolvimento

```bash
npm run dev
```

Acesse no navegador: **http://localhost:5173**

---

## 📦 Scripts disponíveis

| Comando | Descrição |
|---|---|
| `npm run dev` | Inicia o servidor de desenvolvimento com hot-reload |
| `npm run build` | Gera a versão otimizada para produção na pasta `dist/` |
| `npm run preview` | Pré-visualiza o build de produção localmente |

---

## 🎮 Como jogar

1. **Dois jogadores** se revezam no mesmo dispositivo — **Jogador X** sempre começa
2. Clique em qualquer **célula vazia** do tabuleiro para fazer sua jogada
3. O jogador que alinhar **3 símbolos** na horizontal, vertical ou diagonal **vence!**
4. Se todas as 9 células forem preenchidas sem vencedor, é **empate**
5. Ao vencer, uma **animação de celebração** toma a tela — clique em qualquer lugar para continuar
6. Use **"New Game"** para reiniciar a partida mantendo o placar
7. Use **"Clear Scores"** para zerar o placar e começar do zero

### Condições de vitória

```
X | X | X      X | . | .      X | . | .
---------      ---------      ---------
. | . | .      X | . | .      . | X | .
---------      ---------      ---------
. | . | .      X | . | .      . | . | X
 Horizontal      Vertical       Diagonal
```

---

## 📁 Estrutura do projeto

```
tic-tac-toe/
├── public/              # Arquivos estáticos públicos
├── src/
│   ├── App.vue          # Componente principal (toda a lógica e UI do jogo)
│   └── main.js          # Ponto de entrada — monta o app Vue no DOM
├── index.html           # HTML base da aplicação
├── vite.config.js       # Configuração do Vite
├── package.json         # Dependências e scripts do projeto
└── .gitignore           # Arquivos ignorados pelo Git
```

---

## 🏗️ Decisões de arquitetura

- **Single File Component** — toda a lógica, template e estilos vivem em `App.vue`, mantendo o projeto simples e fácil de entender
- **Composition API com `<script setup>`** — código reativo limpo e sem boilerplate
- **CSS puro** — sem bibliotecas de UI externas; todas as animações e estilos são feitos à mão com `@keyframes` e variáveis CSS
- **Confete gerado no módulo** — os 80 pedaços de confete são criados uma única vez quando o módulo carrega, evitando recalcular valores aleatórios a cada re-render

---

## 📄 Licença

Este projeto está sob a licença **MIT**. Sinta-se livre para usar, modificar e distribuir.

---

<p align="center">Feito com ❤️ usando Vue 3 + Vite</p>