---
title: Funções - unitárias, binárias, ternárias, poliádicas, variádicas, aninhadas, de alta ordem, anônimas, puras, impuras e recursivas
date: 2020-06-15
published: true
tags: ['Javascript', 'Programação funcional']
series: false
cover_image: ./images/Funções - unitárias, binárias, ternárias, poliádicas, variádicas, aninhadas, de alta ordem, anônimas, puras, impuras e recursivas.jpg
canonical_url: false
description: "Você deve já deve ter visto, em algum momento da sua vida, uma função matemática similar a seguinte: f(x) = 4x + 10. No universo da programação, funções são similares as funções matemáticas"
---

## Introdução

Você deve já deve ter visto, em algum momento da sua vida, uma função matemática similar a seguinte: f(x) = 4x + 10.

Acredito que não seria um problema pedir para você resolvê-la, considerando os seguintes valores para x:

- f(2);
- f(5);
- f(0).

No universo da programação, funções são similares as funções matemáticas, podemos representar estes cenários com o código a seguir:

```javascript
const f = x => 4 * x + 10;

f(2); // 18
f(5); // 30
f(0); // 10
```

Simples, concorda? No entanto, existem múltiplas categorias de funções. E neste artigo veremos algumas delas. Borá lá!

## 1. Unitárias

Aceitam apenas um valor parametrizado:

```javascript
const printPersonalInformation = name => {
  console.log(`Name: ${name}`);
};

printPersonalInformation('Rodrigo');
```

## 2. Binárias

Aceitam dois valores parametrizados:

```javascript
const printPersonalInformation = (name, surname) => {
  console.log(`Name: ${name}`);
  console.log(`Surname: ${surname}`);
};

printPersonalInformation('Rodrigo', 'Ghiorzi');
```

## 3. Ternárias

Aceitam três valores parametrizados:

```javascript
const printPersonalInformation = (name, surname, birthday) => {
  console.log(`Name: ${name}`);
  console.log(`Surname: ${surname}`);
  console.log(`Birthday: ${birthday}`)
};

printPersonalInformation('Rodrigo', 'Ghiorzi', new Date(1900, 0, 1));
```

## 4. Poliádicas

Aceitam múltiplos valores parametrizados. Ou seja, funções binárias e ternárias também se categorizam como poliádicas, entretanto, temos vocábulos específicos para elas.

## 5. Variádicas

Aceitam uma quantidade indeterminada de valores parametrizados.

No JS, podemos criar uma função variádica, através de rest parameters (ou três pontinhos para os mais íntimos). Portanto, pouco importa se parametrizarmos um valor ou dez. Como a função é variádica, saberá lidar com uma **quantidade indeterminada** de valores. Veja abaixo:

```javascript
const printTasks = (...tasks) => {
  tasks.forEach(task => console.log(task));
}

printTasks(
  'Escrever sobre funções unitárias',
  'Escrever sobre funções binárias',
  'Escrever sobre funções ternárias',
  'Escrever sobre funções poliádicas',
)
```

## 6. Aninhadas

Funções aninhadas são funções encapsuladas internamente em outra função.

Como exemplo podemos criar algumas funções **aninhadas** como:

- print: função **binária** responsável por interpolar os valores no console;
- printName: função **unitária** que reutiliza a função print para imprimir o nome;
- printSurname: função **unitária**  que reutiliza a função print para imprimir o sobrenome;
- printBirthday: função **unitária**  que reutiliza a função print para imprimir a data de aniversário.

```javascript
const printPersonalInformation = (name, surname, birthday) => {

  const print = (info, value) => console.log(`${info}: ${value}`);
  
  const printName = () => print('Name', name);
  const printSurname = () => print('Surname', surname);
  const printBirthday = () => print('Birthday', birthday);

  printName();
  printSurname();
  printBirthday();
};

printPersonalInformation('Rodrigo', 'Ghiorzi', new Date(1900, 0, 1));
```

## 7. De alta ordem

Funções de alta ordem são funções que:

- Recebem uma ou mais funções como parâmetros;
- Ou retornam uma função como output;
- Ou recebem uma ou mais funções como parâmetros e retornam uma função como output.
- 
No código abaixo, vemos que isEven é uma função **unitária** responsável por verificar se um número é classificado como par. Em seguida, vemos o seu uso na **função de alta ordem pura** filter. Em outras palavras, isEven é uma função usada como input de outra função (filter).

```javascript
const numbers = [1, 4, 7, 10];

// função unitária
const isEven = number => number % 2 === 0;

// filter: função de alta ordem
const evenNumbers = numbers.filter(isEven);

console.log(evenNumbers); // 4, 10
```

## 8. Anônimas

Funções anônimas são funções sem nomes, definidas on the spot. Utilizando o exemplo anterior, podemos transformar a função isEven em uma função anônima desta forma:

```javascript

const evenNumbers = numbers.filter(number => number % 2 === 0);
```
Ou seja, neste cenário, temos uma função de alta ordem que recebe uma função anônima unitária por parâmetro.

## 9. Puras e impuras

Funções puras são funções que:

- Não produzem efeitos colaterais;
- Não dependem de valores externos, apenas dos valores parametrizados;
- Retornam sempre o mesmo output dado os mesmos valores inputados.

Por consequência, funções impuras são funções que não seguem estes preceitos.

Uma função pura disponível no JS é a função slice, pois podemos executar o trecho "numbers.slice(0,2)" diversas vezes que o output será sempre o mesmo, além de não produzir efeitos colaterais, afinal o valor numbers se mantém inalterável.

```javascript
const numbers = [1, 4, 7, 10];
const oneAndFour = numbers.slice(0, 2);

console.log(numbers); // 1, 4, 7, 10
console.log(oneAndFour); // 1, 4
```

Uma função impura disponível no JS é a função splice. Veja que oneAndFour recebeu os valores esperados, entretanto, houve um efeito colateral, o valor numbers foi alterado… consequentemente, os valores 1 e 4 foram removidos dele.

Note também que, se executarmos a função splice mais de uma vez, ainda que, com os mesmos inputs, não teremos o mesmo output como retorno.

```javascript
const numbers = [1, 4, 7, 10];
const oneAndFour = numbers.splice(0, 2);

console.log(numbers); // 7, 10
console.log(oneAndFour); // 1, 4

numbers.splice(0, 2);

console.log(numbers); // []
```

Como supracitado, uma função é categorizada como impura caso dependa de valores externos, por exemplo:

```javascript
// valor externo
const DISCOUNT = 0.15;

const calculatePriceWithDiscount = price => price - (price * DISCOUNT);
```

Se alterarmos o valor da constante discount, teremos o efeito colateral da nossa função retornar outros valores. Além disso, caso tenhamos sido boas crianças e feitos testes unitários, os mesmos passariam a falhar também. E por fim, da forma que o código está definido, não seria possível reutilizar essa função com valores divergentes de descontos.

Outra forma de escrever essa função é torná-la pura, sem que a mesma dependa de valores externos:

```javascript
// valor externo
const calculatePriceWithDiscount = (price, discount) => price - (price * (discount / 100));
```

Assim, podemos:

- Testar a função com diversos valores de desconto;
- Reutilizar a função com valores diferentes de desconto;

## 10. Recursivas

Funções recursivas são funções que chamam a si mesmas até atingirem suas condições de parada.

Como cenário, vamos desenvolver um código capaz de fazer uma contagem regressiva de N até 1, sendo N qualquer inteiro maior que 1.

Com estrutura de repetição podemos facilmente desenvolver desta maneira:

```javascript
const countDownFrom = number => {
    for(let i = number; i > 0; i--)
      console.log(i);
}
```

Com recursividade a ideia não é muito diferente disto.

Primeiro precisamos definir nossa condição de parada: se a contagem vai de N até 1, então basta parar as chamadas recursivas após a contagem ter chego em 1. Em outras palavras, enquanto o próximo número da contagem for maior que zero, continuamos a recursividade.

Isto feito, precisamos apenas desenvolver a lógica e transformar nossa função em uma função recursiva, assim:

```javascript
const countDownFrom = number => {
    console.log(number);

    const next = number - 1;

    if(next > 0)
      countDownFrom(next);
}
```

Desta forma, temos uma função recursiva unitária.

## Palavras finais

O objetivo deste artigo é apresentar, de forma introdutória, algumas categorias de funções. Visto que, no quotidiano, nos habituamos a olharmos nossas funções sem diferenciá-las desta maneira.

