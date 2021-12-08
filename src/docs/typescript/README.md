# Typescript

## Cos'è Typescript

E' un superset di Javscript, quindi permette di avere funzioni in più rispetto a quest'ultimo.
Il browser non può però eseguire codice typescript, quindi dev'essere compilato per ottenere il codice javascript da far eseguire al browser

::: tip INFO
Typescript è un **tool** che ci aiuta a scrivere codice migliore.
:::

Typescript, come dice il nome, permette di scrivere codice javscript tipizzato, quindi un pezzo di codice come questo sotto da errore, poiché sto cercando di sommare due valori di tipo stringa:

```ts
function add(num1, num2) {
  return num1 + num2;
}
console.log(add("1", "2"));
```

Oppure un codice di questo tipo:

```ts
const button = document.querySelector("button");
const input1 = document.getElementById("num1");
const input2 = document.getElementById("num2");

function add(num1, num2) {
  return num1 + num2;
}

button.addEventListener("click", function () {
  console.log(add(input1.value, input2.value));
});
```

in Typescript da errore poiché non sappiamo se `input1` e `input2` avranno davvero il `value`.
Per ovviare a questa segnalazione di Typescript posso scrivere le inizializzazioni dei due valori in questo modo:

```ts
const input1 = document.getElementById("num1")! as HTMLInputElement;
const input2 = document.getElementById("num2")! as HTMLInputElement;
```

con il `!` dico a typescript che ci sarà sempre un elemento con `id` `num1` e `num2`, e con `as HTMLInputElement` faccio il cast.

:::tip INFO
Chiaramente, queste due modifiche sono permesse solo perché sto scrivendo codice typescript. In javascript non sono disponibili queste funzionalità.
:::

Oltre a permettere di scrivere codice javascript tipizzato, typescript permette anche di:

- usare funzioni javascript next-gen, che poi verranno compilate per funzionare anche sui browser più vecchi
- usare **interfacce** o **generici**, che in javascript non sono supportati
- una configurazione che serve per dire a typescript come deve comportarsi in certi casi

## Compiler

Per compilare il codice typescript si usa il compilatore `tsc`, quindi nel progetto se ho un file `script.ts`, uso il comando:

```
tsc script.ts
```

Per stare in ascolto su tutte le modifiche che vengono fatte al file typescript, si usa il seguente comando:

```
tsc script.ts --watch
```

## Tipi di variabile

- [**number**](/docs/typescript/#number-string-e-boolean): racchiude tutti i tipi di numero, quindi int, float...
- [**string**](/docs/typescript/#number-string-e-boolean): racciuhde tutte le variabili con valore tra apici, virgolette e backtick
- [**boolean**](/docs/typescript/#number-string-e-boolean): racchiude solamente le variabili con valori **true** e **false**. I valori 1, 0 o qualsiasi valore che viene definito "**truthy**" o "**falsy**", non vengono ammessi
- [**object**](/docs/typescript/#object): chiaramente è come l'object in javascript, solo più potente, e senza la possibilità di "accedere" a proprietà che in realtà non esistono nell'oggetto
- **array**: può essere:
  - **strict**: `string[]`, `number[]`...quindi si possono avere solamente valori del tipo dichiarato (chiaramente anche definendo `let myArray = [1, 2, 3]`, si avrà un'array di tipo `number[]`)
  - **flexible**: `any[]`, permette di avere valori di diversi tipi, oppure semplicemente definirlo così `let myArray = [1, 'Prova', true, [1, 2, 3]];`
- [**tuple**](/docs/typescript/#tuple): definisce un'array con il numero di valori e il tipo
- [**enum**](/docs/typescript/#enum): gli enum sono utili per quando si vogliono avere delle costanti da riutilizzare nel codice, ma invece di definirle separatamente, si definiscono come enum
- **any**: si può usare in due modi:
  - `any`: non conta il tipo del primo valore che viene assegnato alla variabile, in seguito si possono assegnare anche altri tipi di valori
  - `any[]`: non conta che tipo di dati ci sono dentro, basta che sia sempre un array, quindi non si può riassegnare come valore una `string` o un `number`

---

### Number, String e Boolean

```ts{4}
function add(n1: number, n2: number) {
  return n1 + n2;
}
const result = add(5, "10");
```

In questo pezzo di codice c'è un errore: non si può passare una stringa come parametro se la funzione ha esplicitamente dichiarato il tipo di variabile in entrata.

::: warning ATTENZIONE
Il codice sopra viene compilato comunque, anche se è presente questo errore, poiché a livello di codice, non c'è alcun errore. Se si passa un numero e una stringa, questi valori verranno semplicemente concatenati. Nonostante a livello di codice non ci sono errori, `tsc` segnalerà comunque questo errore.
:::

::: warning ATTENZIONE
In typescript si possono assegnare i tipi delle variabili al momento dell'inizializzazione:

```ts
const number1: number = 5;
let number2: number;
number2 = 10;
```

Però, almeno che la variabile non venga dichiarata come variabile (`let`), e poi in un secondo momento le venga assegnato un valore, non ha senso che venga dichiarato il tipo di variabile, poiché typescript riesce a capirlo da solo. Nel caso invece venga dichiarata come costante (`const`), non ha davvero alcun senso poiché non si può assegnare in un secondo momento il valore, lo si assegna subito al momento dell'inizializzazione. Quindi l'esempio sopra si riscrive meglio così:

```ts
const number1 = 5;
```

:::

### Object

Per dichiarare un oggetto si può usare questa notazione:

```js
const person: object = { name: "Mario", age: 30 };
```

Tramite la notazione sopra però non si può accedere alle proprietà tipo `person.name`oppure `person.age`, bisogna esplicitamente dichiarare i tipi delle proprietà dell'oggetto, per poterci accedere:

```js
const person: { name: string, age: number } = {
  name: "Mario",
  age: 30,
};
```

Chiaramente come nei casi di number, string o boolean, anche qui si può inizializzare l'oggetto senza tipo o dichiarazione delle proprietà:

```js
const person: {
  name: "Mario",
  age: 30
};
```

### Tuple

```js{4}
const person: { name: string, age: number, role: [number, string] } = {
  name: "Mario",
  age: 30,
  role: [2, "ADMIN"],
};
```

La tupla è un tipo di array che ha un numero ben definito di valori possibili, con anche i tipi definiti.
Posso riassegnare il valore del role in questo modo: `person.role = [5, 'USER'];`, ma non posso riassegnarlo in questo modo: `person.role = [5, 'USER', 'ADMIN'];` oppure `person.role = ['ADMIN', 10];`, poiché questo mi darebbe errore sul numero di elementi, e sul tipo dei valori
::: danger ATTENZIONE
Si può però aggiungere un valore con il `push`, quindi si può benissimo fare `person.role.push(10)`, in questo caso typescript non lo segnalerà come errore.
:::

### Enum

```js{1}
enum Role { ADMIN, USER, READ_ONLY };
const person: { name: string, age: number, role: [number, string] } = {
  name: "Mario",
  age: 30,
  role: Role.ADMIN,
};
```

Quando si definisce un `enum`, a ognuno dei valori viene assegnato un identificativo progressivo, quindi `ADMIN(0)`, `USER(1)`, `READ_ONLY(2)`

::: tip INFO
Se si vuole dare un identificativo diverso ai valori dell'enum basta scrivere:

```js
enum Role { ADMIN = 5, USER = 8, READ_ONLY = 1};
```

oppure anche:

```js
enum Role { ADMIN = 5, USER, READ_ONLY};
```

assegnando un identificativo custom a un valore dell'enum, i seguenti avranno come identificativo quello custom incrementato,
quindi `USER(6)` e `READ_ONLY(7)`

Oppure, ancora, si può anche usare un identifacativo stringa:

```js
enum Role { ADMIN = 'ADMIN', USER = 100, READ_ONLY = 200};
```

::: warning ATTENZIONE
Se si usa come identificativo una stringa, almeno il valore seguente a quello con identificativo stringa, deve essere inizializzato (se sempre come `string`, il discorso vale anche per il valore seguente, se invece come `number`, non importa anche i seguenti, verrà automaticamente assegnato a ciascuno un identificativo progressivo)
:::

## Union

A volte in alcune funzioni si potrebbe avere bisogno di passare un dato che potrebbe essere di due tipi diversi (es. `number` e `string`). Per fare questo si definisce il parametro, mettendo tutti i tipi che vogliamo separati da `|`:

```js{2}
function combine(input1: number | string, input2: number | string) {
  const result = input1 + input2;
  return result;
}
const combinedAges = combine(30, 25);

const combinedNames = combine('Mario', 'Luigi');
```

In questo caso typescript segnalerà un errore poiché non si può fare la somma tra due stringhe. Per ovviare a questo problema si può fare in questo modo:

```js
function combine(input1: number | string, input2: number | string) {
  let result;
  if (typeof input1 === "number" && typeof input2 === "number") {
    result = input1 + input2;
  } else {
    result = input1.toString() + input2.toString();
  }
  return result;
}

const combinedAges = combine(30, 25);

const combinedNames = combine("Mario", "Luigi");
```

Si possono anche definire i parametri in questo modo:

```js
function combine(
  input1: number | string,
  input2: number | string,
  resultConversion: "as-number" | "as-text"
) {
  let result;
  if (
    (typeof input1 === "number" && typeof input2 === "number") ||
    resultConversion === "as-number"
  ) {
    result = +input1 + +input2;
  } else {
    result = input1.toString() + input2.toString();
  }
  return result;
}

const combinedAges = combine(30, 25, "as-number");

const combinedStringAges = combine("30", "25", "as-number");

const combinedNames = combine("Mario", "Luigi", "as-text");
```

In questo caso, non sto passando dei tipi di variabile, ma due stringhe e quando chiamo la funzione `combine`, non potrò passare valori diversi da `as-number` e `as-text` come terzo parametro.

### Tipi di variabile custom

A volte, per evitare di riscrivere ogni volta le union che vogliamo utilizzare, possiamo usare degli **alias** per definirle. Quindi nel caso precedente potremmo definire la union `number | string` così `type CombinableNumberString = number | string`, e la union `as-number | as-text` così `type ConversionDescriptor = 'as-number' | 'as-text'`, e quindi il codice sopra diventa:

```ts
type CombinableNumberString = number | string;
type ConversionDescriptor = "as-number" | "as-text";

function combine(
  input1: CombinableNumberString,
  input2: CombinableNumberString,
  resultConversion: ConversionDescriptor
) {
  let result;
  if (
    (typeof input1 === "number" && typeof input2 === "number") ||
    resultConversion === "as-number"
  ) {
    result = +input1 + +input2;
  } else {
    result = input1.toString() + input2.toString();
  }
  return result;
}

const combinedAges = combine(30, 25, "as-number");

const combinedStringAges = combine("30", "25", "as-number");

const combinedNames = combine("Mario", "Luigi", "as-text");
```

Gli alias si possono usare anche per gli oggetti, tipo:

```js
type User = { name: string, surname: string, age: number };

function greet(user: User) {
  console.log("Hi, I am " + user.name);
}

function isOlder(user: User, checkAge: number) {
  return checkAge > user.age;
}
```

## Funzioni

### Tipi di ritorno

Le funzioni, come le variabili, hanno anche esse un tipo di ritorno, che può anche essere omesso, poiché typescript saprà dedurre da solo il tipo.
Ci sono però due tipi particolari che sono `void` e `undefined`.
::: warning ATTENZIONE

```ts
function printResult(num: number) {
  console.log("Result:" + num);
}
```

Questa funzione è di tipo `void`, però se viene chiamata da qualche altra parte, darà `undefined`.

```ts
function printResult(num: number): undefined {
  console.log("Result:" + num);
}
```

Si può anche mettere come tipo `undefined`.
:::

## Il tipo Function

Le variabili possono essere inizializzate passando come valore anche una funzione:

```ts{12}
function add(n1: number, n2: number2) {
  return n1 + n2;
}

function printResult(num: number): undefined {
  console.log("Result:" + num);
}

let combineValues: Function;
combineValues = add;

combineValues = printResult;
```

::: danger ATTENZIONE
Il problema dell'assegnazione del tipo `Function` è che si può passare qualsiasi funzione presente nel codice
:::

Per ovviare a questo problema, invece di assegnare il tipo `Function`, si può assegnare così il tipo:

```ts{9,12}
function add(n1: number, n2: number2) {
  return n1 + n2;
}

function printResult(num: number): undefined {
  console.log("Result:" + num);
}

let combineValues: (a: number, b: number) => number;
combineValues = add;

combineValues = printResult;
```
