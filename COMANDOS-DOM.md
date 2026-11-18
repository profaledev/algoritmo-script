# 🧰 COMANDOS-DOM.md — o "dicionário" dos comandos básicos de JavaScript + DOM

> **Turma!** Fiz esta lista pensando naquele momento da lição de casa: *"qual era o comando pra tirar aquela classe?"* 😄
>
> Aqui está **tudo que vamos precisar no início** da nossa fase de aplicações. Cada comando tem: **o que faz**, **como escreve-se** e **um exemplo de verdade**. Não precisa decorar — quando esquecer, volte aqui (ou digite os primeiros caracteres no VS Code e deixe o autocomplete lembrar por você).
>
> Lembram do combinado: prefixo **`el`** para variáveis que guardam elementos da página, e ids com **`in-`** (entrada) e **`out-`** (saída).

---

## 📑 Índice

- [🧰 COMANDOS-DOM.md — o "dicionário" dos comandos básicos de JavaScript + DOM](#-comandos-dommd--o-dicionário-dos-comandos-básicos-de-javascript--dom)
  - [📑 Índice](#-índice)
  - [1. Pegar elementos (seletores)](#1-pegar-elementos-seletores)
  - [2. Ler o que foi digitado (`.value`)](#2-ler-o-que-foi-digitado-value)
  - [3. Escrever texto simples](#3-escrever-texto-simples)
  - [4. Escrever HTML de verdade (`.innerHTML`)](#4-escrever-html-de-verdade-innerhtml)
  - [5. Colocar e tirar classes (`.classList`)](#5-colocar-e-tirar-classes-classlist)
  - [6. Mudar estilo direto (`.style`)](#6-mudar-estilo-direto-style)
  - [7. Mostrar/esconder (`.style.display`)](#7-mostraresconder-styledisplay)
  - [8. Botões: ligar o clique](#8-botões-ligar-o-clique)
  - [9. Ativar/desativar e limpar campos](#9-ativardesativar-e-limpar-campos)
  - [10. Tabela-resumo de bolso](#10-tabela-resumo-de-bolso)

---

## 1. Pegar elementos (seletores)

Antes de qualquer coisa: para mexer numa tag do HTML, o JavaScript precisa **encontrá-la**.

**O padrão da turma — por `id`:**

```html
<input type="number" id="in-nota">
<button id="btn-enviar">Enviar</button>
<div id="out-resposta">...</div>
```

```js
const elNota     = document.getElementById('in-nota');    // quadrinho de entrada
const elBotao    = document.getElementById('btn-enviar'); // botão
const elResposta = document.getElementById('out-resposta'); // área de saída
```

**O extra (seletor CSS) — id usa `#`, classe usa `.`:**

```js
document.querySelector('#in-nota');    // mesmo resultado do getElementById
document.querySelector('.resposta');   // pega a PRIMEIRA tag com class="resposta"
```

| Comando | O que faz |
|---|---|
| `document.getElementById('...')` | acha **uma** tag pelo `id` (o indispensável) |
| `document.querySelector('...')` | acha **a primeira** tag que combine com o seletor (`#id`, `.classe`, `div`) |

> ⚠️ Se o `id` não existir ou escrever errado, o resultado é `null` — e o comando seguinte quebra. Console em vermelho? Confira o `id` letra por letra.

---

## 2. Ler o que foi digitado (`.value`)

O que a pessoa **digitou** no input fica guardado no `.value` — e **sempre é texto** (`string`), mesmo parendo número.

```html
<input type="number" id="in-valor" placeholder="Digite...">
```

```js
const elValor = document.getElementById('in-valor');

let texto   = elValor.value;          // "7"  ← string!
let numero  = Number(elValor.value);  // 7    ← número, pronto para calcular

elValor.value === ""                  // true → campo está vazio (dá para validar!)
```

**Regras de ouro:**

- Vai **calcular**? → `Number(elCampo.value)`. Sempre. É hábito, não exceção.
- É **texto** de verdade (nome, senha)? → pegue direto: `elCampo.value`.
- Só `<input>` e `<select>` têm `.value` — em `<div>`/`<p>` não existe (aí é `textContent`).

> 💡 No `<select>`, o `.value` é o valor do `<option>` escolhido:
> ```html
> <select id="in-op">
>     <option value="soma">➕ Soma</option>
> </select>
> ```
> ```js
> const operacao = elOp.value;  // "soma"
> ```

---

## 3. Escrever texto simples

Coloca **texto puro** dentro da tag de saída. É a nossa escrita padrão (o `cout <<` de vocês).

```html
<div id="out-resposta">A resposta aparece aqui...</div>
```

```js
const elResposta = document.getElementById('out-resposta');

elResposta.textContent = "Aprovado!";
elResposta.textContent = `Média: ${media}`;   // template string funciona lindamente
elResposta.textContent = "";                  // limpar a saída
```

**`.textContent` vs `.innerText`** (os dois existem e quase fazem a mesma coisa):

```js
elResposta.textContent = "Olá";   // escreve o texto bruto do JS (nosso padrão)
elResposta.innerText  = "Olá";    // escreve o texto como ele APARECERIA na tela
```

| | `.textContent` | `.innerText` |
|---|---|---|
| Pega/escreve todo o conteúdo | ✅ sim | só o **visível** (respeita o CSS — algo com `display:none` não entra) |
| Velocidade | mais rápido | um tiquinho mais lento |
| Na turma | **padrão** | use quando precisar considerar só o que está **visível** na tela |

> 💡 Honestidade: no nosso nível, os dois vão parecer iguais na prática. Usaremos `textContent` por ser o mais previsível — mas agora vocês sabem que o primo `innerText` existe.

---

## 4. Escrever HTML de verdade (`.innerHTML`)

Quando a saída precisa **conter tags** (negrito, linhas de lista, quebras), o `innerHTML` entra. Ele interpreta o texto que você escreve como **HTML de verdade**.

```html
<div id="out-resposta">...</div>
<ul id="out-lista"></ul>
```

```js
const elResposta = document.getElementById('out-resposta');
const elLista    = document.getElementById('out-lista');

// (a) + formato: negrito, linha quebrada...
elResposta.innerHTML = "Média: <strong>7,5</strong><br>Parabéns!";
// aparece na tela:  Média: **7,5**  (em negrito) e quebra de linha

// (b) listas montadas por laço — a receita completa:
elLista.innerHTML = "";   // 1º LIMPAR (senão empilha de clique em clique!)

for (let i = 1; i <= 10; i++) {
    elLista.innerHTML += `<li>${n} x ${i} = ${n * i}</li>`;  // 2º ADICIONAR cada linha
}
```

> ⚠️ **Com o `+=` esquecendo o primeiro passo** = tabuada duplicada a cada clique. A sequência manda: **limpai → adicionou**.
> ⚠️ innerHTML **apaga** o que estiver lá antes! Se a saída tinha um `<span id="out-situacao">` dentro, e você escrever `elResposta.innerHTML = "..."`, esse span some junto. Por isso: troque **a menor tag possível** (`elMedia.textContent = ...` em vez de jogar tudo na caixa grande).

---

## 5. Colocar e tirar classes (`.classList`)

As classes entram no CSS de vocês e são a forma mais "certa" de mudar visual por JavaScript: você **não pinta na mão no JS** — você **liga/desliga uma classe que o CSS já sabe pintar**.

```html
<!-- o CSS de vocês define por exemplo: -->
<style>
    .escondido { display: none; }
    .aprovado  { background: #059669; color: #fff; }
    .reprovado { background: #dc2626; color: #fff; }
    .destaque  { border: 2px solid gold; }
</style>

<div id="out-situacao">Aguardando...</div>
```

```js
const elSituacao = document.getElementById('out-situacao');

elSituacao.classList.add('aprovado');      // COLOCA a classe (fica verde)
elSituacao.classList.remove('aprovado');   // TIRA a classe
elSituacao.classList.toggle('destaque');   // liga/desliga: tinha? tira. não tinha? põe.
elSituacao.classList.contains('aprovado'); // perguntA: tem? (true / false — ótimo p/ if)
```

**Exemplo real (o exercício 05):**

```js
if (media >= 6) {
    elSituacao.textContent = "Aprovado ✓";
    elSituacao.classList.add('aprovado');
    elSituacao.classList.remove('reprovado');   // POR SE um clique anterior tinha deixado vermelho
} else {
    elSituacao.textContent = "Reprovado";
    elSituacao.classList.add('reprovado');
    elSituacao.classList.remove('aprovado');
}
```

> 💡 **Troca de classes sempre em par:** `add` de um lado, `remove` do outro — senão a cor do clique anterior "vaza" para o próximo resultado.
> 🧠 Guardem a versão linda para depois: `el.classList.toggle('classe', condicao)` — põe quando a condição é true e tira quando é false. (Deixo de fora no começo porque o `if` ensina melhor.)

---

## 6. Mudar estilo direto (`.style`)

Atalho rápido para mexer em **uma** propriedade CSS por JavaScript. O nome da propriedade vira **camelCase** (o `border-radius` do CSS vira `.borderRadius`).

```js
elResposta.style.backgroundColor = "#059669";
elResposta.style.display = "none";        // (fala na seção 7!)
elResposta.style.fontSize = "22px";
elResposta.style.borderRadius = "12px";
```

| No CSS | No JS (camelCase) |
|---|---|
| `background-color` | `.style.backgroundColor` |
| `font-size` | `.style.fontSize` |
| `border-radius` | `.style.borderRadius` |
| `display` | `.style.display` |

> ⚠️ **Regra de aula:** `classList` > `style` no JS. Se a mudança for complexa ou permanente, faça a classe no CSS e use os comandos da seção 5. O `.style` fica para pequenos ajustes pontuais.

---

## 7. Mostrar/esconder (`.style.display`)

A mudança mais comum do início: **um bloco aparece ou some**.

```html
<div id="out-carregando">Carregando dados...</div>
```

```js
const elCarregando = document.getElementById('out-carregando');

elCarregando.style.display = "none";    // ESCONDE (o bloco sai do fluxo, sem deixar buraco)
elCarregando.style.display = "block";   // MOSTRA de novo (div e p = "block")
```

> 💡 Completo em fases:
> | `.display` | efeito |
> |---|---|
> | `"none"` | some da tela (não ocupa espaço nenhum) |
> | `"block"` | aparece e ocupa linha inteira (o padrão de `div`, `p`, botão...) |
> | `"inline"` | aparece SEM quebrar linha (para `<span>`) |
> | `"grid"` / `"flex"` / ... | quando o CSS de vocês já define o layout — esconda com `none` e faça voltar com o valor que era antes |

---

## 8. Botões: ligar o clique

O botão é clicado e... nada? Pois ele não faz nada até ligarmos o **escutador de clique** nele:

```html
<button id="btn-calcular">Calcular</button>
```

```js
const elBotao = document.getElementById('btn-calcular');

function calcular() {
    // seu algoritmo — roda A CADA CLIQUE
}

elBotao.addEventListener('click', calcular);   // SEM os parênteses! mande a função, não o resultado dela
```

> ⚠️ Pegadinha de quem está começando: `addEventListener('click', calcular())` roda a função **uma vez só, na carga da página**, e não liga nada ao clique. O comando recebe a função **sem chamá-la** — mande `calcular`, não `calcular()`.

---

## 9. Ativar/desativar e limpar campos

O comportamento "bloquear o jogo ao ganhar" (exercício 10) e "limpar o formulário" (o exercício 09) usam estes comandos:

```html
<button id="btn-chutar">Chutar</button>
<input id="in-palpite" placeholder="?">
```

```js
const elPalpite = document.getElementById('in-palpite');
const elBotao   = document.getElementById('btn-chutar');

// Desabilitar/abilitar o BOTÃO (fica quase acinzentado e não clica)
elBotao.disabled = true;    // sem uso
elBotao.disabled = false;   // habilita

// Bloquear/liberar a digitação no INPUT (o campo fica visible, mas não aceita digitar)
elPalpite.readOnly = true;  // o campo "congela"
elPalpite.readOnly = false; // liberado

// Foco no campo (o cursor pisa automaticamente — ótimo para o usuário já digitar o próximo valor)
elPalpite.focus();

// Limpar o conteúdo
elPalpite.value = "";
```

| Comando | O que faz | Usado em |
|---|---|---|
| `.disabled = true/false` | congela/libera o **botão** | bloquear novo chute após o acerto |
| `.readOnly = true/false` | congela/libera a **digitação** | o input do chute |
| `.focus()` | coloca o cursor no campo | reiniciar o jogo, já preparando o próximo palpite |
| `.value = ""` | limpa o conteúdo | limpar tudo (botão "limpar") |

---

## 10. Tabela-resumo de bolso

| Preciso... | Comando |
|---|---|
| achar a tag | `document.getElementById('id')` |
| achar pela classe | `document.querySelector('.classe')` |
| ler o que foi digitado | `elCampo.value` (→ `Number()` se calcula) |
| escrever texto | `elSaida.textContent = ...` |
| escrever com tags (`<strong>`, `<li>`...) | `elSaida.innerHTML = ...` (e `+=` para montar listas) |
| limpar saída/lista | `elSaida.textContent = "";` / `elLista.innerHTML = "";` |
| colocar classe | `el.classList.add('classe')` |
| tirar classe | `el.classList.remove('classe')` |
| alternar classe | `el.classList.toggle('classe')` |
| perguntar se tem classe | `el.classList.contains('classe')` → true/false |
| mudar 1 estilo | `el.style.backgroundColor = ...` (camelCase!) |
| esconder | `el.style.display = "none"` |
| reaparecer | `el.style.display = "block"` |
| ativar o clique | `elBotao.addEventListener('click', funcao)` (sem parênteses!) |
| ligar/desabilitar botão | `elBotao.disabled = true/false` |
| travar/liberar digitação | `elCampo.readOnly = true/false` |
| cursor no campo | `elCampo.focus()` |
| esvaziar campo | `elCampo.value = "";` |

---

> **Finalizando:** esses ~15 comandos cobrem 100% da fase de nivelamento. Conforme entrarmos em Web I (eventos de verdade, formulários, lista dinâmica de elementos), a lista cresce — mas essa base até parece maior prática que as linhas acima! Qualquer comando esquecido: volte aqui. 🚀
