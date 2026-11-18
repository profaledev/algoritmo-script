# Guia: nossas "aplicações" — entrada e saída por interface, sem `prompt` e sem `alert`

> **Turma, atenção!** A partir deste nivelamento nossos algoritmos ganham cara de aplicação de verdade. Esqueçam aqueles popups esquisitos pulando na tela: agora **a entrada de dados vem de campos de formulário (`<input>`)** e **a saída aparece dentro de uma tag na própria página**.
>
> Fiz essa mudança de propósito, por três motivos:
>
> 1. **A saída fica permanentemente na tela.** No `prompt`/`alert`, o resultado pisca e some. Numa interface, ele fica lá — dá para comentar em aula, comparar respostas entre colegas e mostrar a correção.
> 2. **Você vai se acostumar com o ambiente real da Web I** desde o primeiro dia (inputs, botões, labels). Nada de DOM em excesso — só o mínimo necessário agora.
> 3. **Fica mais bonito de olhar**, e como vocês já perceberam, exercício bonito desestimula aquele "no C++ é melhor" que aparece quando a gente está só vendo texto cinza no terminal.
>
> O único vínculo que mantemos com o "mundo terminal" é o `console.log()`: ele **deixa de ser a saída oficial do programa** e vira ferramenta de **depuração** (investigação) — lá no F12 você inspeciona cálculos por dentro, sem poluir a tela.

---

## 🧠 A mentalidade nova: "cada clique = uma execução do seu programa"

No C++ você compilava, o terminal pulsava uma vez e o programa terminava.

Agora, com interface, funciona assim:

- **O que está fora das funções** no `<script>` = a estrutura (quais campos e tags a página tem, estados guardados). Roda uma vez, quando a página abre.
- **O que está dentro da função ligada no botão** = o seu algoritmo (o que você desenhava `main()` do C++). **Roda EM CADA CLIQUE** no botão.

Ou seja: **clicar no botão é o nosso novo "compilar e rodar".** Encheu os inputs, clicou, o algoritmo leu, calculou e escreveu na saída. Quer rodar de novo? Troca o valor e clica outra vez. 😄

---

> 🛠️ **Companheiro desta leitura:** o arquivo **[COMANDOS-DOM.md](COMANDOS-DOM.md)** — o dicionário com todos os comandos básicos (seletores, pegar dados, escrever na página, classes, mostrar/esconder...), cada um com explicação e exemplo.

---

## 🌐 O DOM no tamanho certo (antes de tudo: como a página "conversa" com o JS)

**O que é o DOM?** Quando o navegador abre seu HTML, ele transforma **cada tag em um objeto** que o JavaScript consegue alcançar. Esse conjunto de objetos é o **DOM** (Document Object Model — o "modelo de objetos do documento"). Para alcançar uma tag específica, garantimos que ela tenha um `id` e buscamos por ele.

É isso. Nada de teoria pesada agora — você só precisa dominar **3 habilidades**:

1. Encontrar a tag na página (**seletores**)
2. Pegar o que foi digitado (**`.value`**)
3. Escrever na página (**`textContent` e `innerHTML`**)

### 1. Seletores: encontrar a tag

A turma vai usar **só estes dois**:

```js
// (a) O PADRÃO: buscar por id
const elNota     = document.getElementById('in-nota');     // acha <input  id="in-nota">
const elResposta = document.getElementById('out-resposta'); // acha <div id="out-resposta">
const elBotao    = document.getElementById('btn-enviar');  // acha <button id="btn-enviar">

// (b) O EXTRA: buscar por seletor CSS (id usa "#", classe usa ".")
const elBotao2 = document.querySelector('#in-nota');        // "#" para id
const primeira = document.querySelector('.resposta');       // "." para classe (pega a PRIMEIRA)
```

Como ler essa linha: `document` = "a página aberta agora"; `getElementById(...)` = "me entregue a tag cujo id é ...". Guardamos o resultado numa variável com prefixo `el` (de **el**emento — combinado da turma) para não digitar a busca toda em cada uso.

> ⚠️ **Erro comum de quem está começando:** `document.getElementById('in-nota')` sem `id` na tag, ou escrevendo o nome diferente (`in-Nota` ≠ `in-nota`). Se a busca falhar, o resultado vem `null` e o próximo comando quebra — a dica é sempre conferir se o `id` do HTML é **exatamente igual** ao da busca.

### 2. Elemento ≠ valor (a regra de ouro)

A variável `elNota` guarda **a tag**, não o que foi digitado. O valor digitado vive **dentro** da tag, no `.value`:

```js
elNota              // a TAG (objeto completo do input)
elNota.value        // o que FOI DIGITADO — sempre texto!
elNota.value === "" // true significa "o campo está vazio"

Number(elNota.value)   // 👈 assim se usa em conta (converte texto em número)
```

- De **`<input>`** pegamos `.value`.
- De **`<button>`** não pegamos nada — o botão só **dispara** a função ao ser clicado.
- O texto numa tag de saída não se lê com `.value` (não existe!) — é com `.textContent`.

### 3. Escrever na página: `textContent` vs `innerHTML`

As duas formas de colocar conteúdo **dentro** de uma tag de saída:

| | `.textContent` | `.innerHTML` |
|---|---|---|
| O que escreve | **texto puro** | **HTML de verdade** (interpreta as tags) |
| Aceita `<strong>`, `<li>`, `<br>`? | não — `<li>A</li>` apareceria literal na tela | sim |
| Quando usar | resposta curta: `"Aprovado!"`, a média | listas montadas por laço, negrito, quebra de linha |

```js
// Saída simples → textContent
elResposta.textContent = "Aprovado!";

// Saída formatada → innerHTML (as tags viram visual, não texto)
elResposta.innerHTML = "Média: <strong>7,5</strong>";
```

### A receita do `innerHTML` para montar listas

```html
<ul id="out-lista"></ul>
```

```js
// 1. LIMPE a lista (senão os cliques empilham!)
elLista.innerHTML = "";

// 2. A CADA volta do laço, ADICIONE uma linha (o += "gruda" mais um <li>)
for (let i = 1; i <= 10; i++) {
    elLista.innerHTML += `<li>${n} x ${i} = ${n * i}</li>`;
}
```

O que acontece em **cada** `innerHTML +=`: você concatena um texto novo ao conteúdo atual da tag. Como o texto contém `<li>`, o navegador **interpreta como linha de lista**. Sem o passo 1, o conteúdo anterior continua lá — é a pegadinha número 1 dos iniciantes.

### Tradução mental C++ → interface

| No C++ de vocês | |
|---|---|
| `cin >> nota;` | `Number(elNota.value)` |
| `cout << x;` | `elResposta.textContent = x;` |
| `cout` com formatação | `elResposta.innerHTML = ...` |

---

## 🧰 O kit básico: os 4 verbos

Todo exercício nosso usa os 4 passos abaixo:

| Verbo | Como se faz | Equivalente C++ |
|---|---|---|
| **1. Pegar o valor** | `document.getElementById('in-nota').value` | `cin >> nota` |
| **2. Converter o valor** | `Number(...)` (o `.value` é sempre texto) | *(em C++, o tipo já era garantido na declaração)* |
| **3. Processar** | sua lógica: `if`, `for`, `while`, funções... | *(idêntico — é aqui que você já sabe!)* |
| **4. Escrever a saída** | `document.getElementById('out-resposta').textContent = ...` | `cout << ...` |

> 💡 Nomear bem os `id` está nos nossos padrões: `in-` para **inputs de entrada** e `out-` para **tags de saída**. Em 10 segundos qualquer pessoa (inclusive você daqui a 3 meses) acha onde está cada coisa.

---

## 🗿 O esqueleto padrão (o nosso novo "playground")

Salve como `interface.html` e reutilize em qualquer experimento próprio:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Minha Aplicação</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Arial, sans-serif; }
        body { min-height: 100vh; display: grid; place-items: center; background: #1e3a8a; }
        .card { background: #fff; max-width: 420px; width: 100%; padding: 32px; border-radius: 20px; box-shadow: 0 20px 60px rgba(0,0,0,.25); }
        label { font-size: 13px; font-weight: 600; color: #334155; }
        input { width: 100%; padding: 12px 14px; margin: 6px 0 14px; border: 2px solid #e2e8f0; border-radius: 10px; font-size: 15px; outline: none; }
        button { width: 100%; padding: 13px; border: 0; border-radius: 10px; background: #2563eb; color: #fff; font-size: 15px; font-weight: 700; cursor: pointer; }
        .resposta { margin-top: 20px; padding: 14px; background: #f8fafc; border: 2px dashed #2563eb; border-radius: 10px; text-align: center; min-height: 48px; }
    </style>
</head>
<body>
    <main class="card">
        <h1>Título aqui</h1>
        <label for="in-valor">Mensagem da entrada</label>
        <input type="number" id="in-valor" placeholder="Ex.: 7">

        <button id="btn-calcular">Calcular</button>

        <div class="resposta" id="out-resposta">A saída aparece aqui...</div>
    </main>

    <script>
        const elValor = document.getElementById('in-valor');
        const elResposta = document.getElementById('out-resposta');
        const elBotao = document.getElementById('btn-calcular');

        function calcular() {
            const valor = Number(elValor.value);

            // ===== SUA LÓGICA COMEÇA AQUI =====
            // use if, for, funções...
            // ===== SUA LÓGICA TERMINA AQUI =====

            elResposta.textContent = `O resultado é ${valor * 2}`;
        }

        elBotao.addEventListener('click', calcular);
    </script>
</body>
</html>
```

> 📌 **Render/base:** o `el` na frente de cada variável (`elValor`, `elResposta`) indica "elemento da página". Não é regra do JS — é combinado nosso, para você não confundir **elemento** (a tag) com **valor** (o que foi digitado).
> O `elResposta.value` não existe — valores vêm de inputs apenas. `elResposta.textContent` escreve dentro da tag de saída.

---

## 📡 Como escolher a tag de saída

A "tag apropriada" depende do tipo de resposta:

| Tipo de resposta | Tag para usar | Exemplo de uso |
|---|---|---|
| Frase/dado curto | `<div>` ou `<p>` com `class="resposta"` | "Aprovado!" → `.textContent = ...` |
| Número que merece destaque | `<strong>` ou `<output>` | média em negrito |
| Várias linhas geradas por laço | `<ul id="out-lista">` (lista de pontinhos) / `<ol>` (numerada) | tabuada, contagem, listagem |
| Valor calculado com rótulo | `<span>` dentro de texto | "Total: R$ 57,50" |

### Como preencher uma lista com um laço `for`

Padrão: uma `ul` vazia na página + um laço que **concatena** linhas:

```html
<ul id="out-lista" class="resposta"></ul>
```

```js
// sempre limpe a lista no começo (podem existir cliques anteriores!)
elLista.innerHTML = "";

for (let i = 1; i <= 10; i++) {
    elLista.innerHTML += `<li>${n} x ${i} = ${n * i}</li>`;
}
```

> ⚠️ **A pegadinha clássica:** esquecer de zerar `elLista.innerHTML` no início → cada clique **empilha** a tabuada de novo. Sempre limpe antes de montar.

---

## 🔒 Estado entre cliques (importante!)

Variáveis declaradas **dentro** da função nascem e morrem a cada clique. Variáveis declaradas **fora** (na "zona de estrutura" do `<script>`) **persistem entre cliques**:

```js
const gastos = [];      // persiste entre cliques — o array continua cheio
let tentativas = 0;     // persiste entre cliques

function adicionar() {
    gastos.push(Number(elGasto.value));   // alimento o array
    // ... montar a saída
}
```

É assim que a gente faz **listas que crescem** (array + `push`) e **contadores de tentativas** precisam valer entre os cliques.

---

## 🏡 Nossos 10 exercícios prontos

Na pasta [`exercicios/`](./exercicios/) estarão 10 arquivos HTML **já montados**: CSS pronto (cada um com o seu visual, para você não perder o fôlego com "layout"), inputs configurados, botão já ligado à função e a **saída já mapeada** — o que sobra **só a sua lógica**, entre os comentários `===== SUA LÓGICA COMEÇA AQUI =====`.

Dentro de cada arquivo:

1. **Enunciado** no topo da página (assim você não precisa abrir um txt separado).
2. **Dificuldade** no badge (Fácil / Moderada) — em geral: começam fáceis, sobem devagar.
3. **Bloco de lógica marcado** com os comentários `===== ... =====`, indicando qual valor pegar, qual regra aplicar e onde escrever.
4. Um bloco `<details>` "Preciso de um empurrão?" com **dicas escondidas** — abra só até o nível da dica que resolve a dúvida (use como último recurso). 😉

**Como entregar:** salvar o arquivo com a sua lógica e me mandar. Estilo com conta — detalhes de layout são bônus, não obrigação.

**Peça pro Console:** F12 sempre à mão. Dica de ouro para achar erro: `console.log()` no meio da lógica para ver o valor de cada variável. Ele agora é ferramenta de investigação, não de apresentação.

---

## ✅ Checklist antes de dizer "está pronto"

- [ ] A saída aparece **na página** (em `<div>`, `<strong>`, `<ul>` etc.), não só no Console.
- [ ] Usei `Number(...)` em todo valor de `<input>` que precisa **calcular**.
- [ ] Testei com valores **normais** e também com valores **limítrofes** (idade 17 e 18; nota 0 e 10...).
- [ ] Se montei lista com laço, **limpei a saída** (`elLista.innerHTML = ""`) antes de preencher.
- [ ] Não deixei `prompt`, `alert` nem `console.log` de apresentação no código final.

---

> **Resumindo o combinado:** entrada = **campo preenchido**; saída = **tag preenchida**; a lógica = aquela que você já domina; e o "rodar o programa" agora é **clicar no botão**. Qualquer coisa F12. 🚀
