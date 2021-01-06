---
title: Métodos de extensão
date: 2020-03-09
published: true
tags: ['C#', 'Feature']
series: false
cover_image: ./images/Metodos-de-extensao.jpg
canonical_url: false
description: "Métodos de extensão pertencem a categoria de métodos estáticos, no entanto, possuem a sutil divergência de serem usados como métodos de instancias da tipagem estendida."
---

## Introdução

O presente artigo objetiva demonstrar o uso de métodos de extensão em paralelo com: DRY, Legibilidade e HOF 

Métodos de extensão pertencem a categoria de métodos estáticos, no entanto, possuem a sutil divergência de serem usados como métodos de instancias da tipagem estendida

## 1. Demonstração

Supondo que precisássemos escrever um código que exibisse o segundo caractere de uma string (sim, já existe o método First, por isso decide obter o segundo caractere. Sim, eu sou uma pessoa muito criativa), poderíamos escrevê-lo da seguinte maneira:

```csharp
string text = "Linkedin";

char secondLetter = text[1];
```

Agora, apenas para exemplificar o conceito, iremos **estender** a funcionalidade para obter o segundo caractere de uma string. Primeiro, criaremos uma classe estática que ira conter nosso método de extensão. Em seguida, declararemos o método propriamente dito. Veja a seguir:

```csharp
public static class Extensions 
{
    public static char Second(this string text)
        => text[1];
}
```

O pulo do gato está na palavra reservada this, a qual antecede o primeiro parâmetro do nosso método. Basicamente, estamos proferindo que: toda valor da tipagem string, seja uma constante ou uma variável, terá o comportamento Second. 

Portanto, podemos realizar o mesmo comportamento do seguinte modo:

```csharp
string text = "Linkedin";

char secondLetter = text.Second();
```

## 2. DRY (Don't repeat yourself)

Métodos de extensão podem auxiliar a evadir a duplicação de código.

Vamos usar o Dictionary do C# como exemplo. É bastante comum gerir quando um valor deve ser inserido ou atualizado, afinal de contas, não podemos atualizar um valor caso a chave inexista no dicionário. Esse controle pode ser representado como na maneira abaixo:

```csharp
var peopleByAge = new Dictionary<string, int>()
{
    ["Ayame"] = 25,
    ["Zenki"] = 37
};

string key = "Ayame";
bool notContains = !peopleByAge.ContainsKey(key);

if (notContains)
    peopleByAge.Add(key, 35);
else
    peopleByAge[key] = 35;
```

Temos um dicionário que engloba chaves do tipo string e valores do tipo inteiro. Esse dicionário representa nomes como chaves e idades como valores. E para gerir se uma pessoa deve ser inserida ou atualizada, fazemos um controle simples: verificamos se a chave existe no dicionário, se ela não existir então inserimos uma pessoa, caso contrário, acessamos o dicionário pelo nome dela e sobrescrevemos a sua idade.

Certo, mas será que esse comportamento não ocorre em diversas partes de um sistema? E será que em todas essas partes essa lógica não acaba sendo duplicada?

Pois é, apesar do Dictionary não prover um método que exerça esta ação, nada nos impede de **estendermos** este comportamento. Basta criamos um método de extensão! Porém, desta vez, faremos um **método de extensão genérico**, segue abaixo:


```csharp
public static TValue Upsert<TKey, TValue>(this IDictionary<TKey, TValue> @this, TKey key, TValue value)
{
    bool notContains = !@this.ContainsKey(key);

    if (notContains)
        @this.Add(new KeyValuePair<TKey, TValue>(key, value));
     else
        @this[key] = value;

    return @this[key];
}
```

```csharp
var peopleByAge = new Dictionary<string, int>()
{
    ["Ayame"] = 25,
    ["Zenki"] = 37
};

string key = "Ayame";

peopleByAge.Upsert(key, 35);
```
Assim obtemos o mesmo resultado com apenas uma linha, além de evitar repetir o mesmo fluxo em todas as partes do sistema.

## 3. Legibilidade

Existe uma polêmica milenar de desenvolvedores que negam variáveis booleanas com o operador de negação enquanto outros utilizam o == false. Eu discordo de ambos.

Acredito que, por questão semântica, toda verificação booleana deva ser auferida tanto por um comportamento que ratifica o caso verdadeiro quanto por um comportamento que certifica o caso falso. Para elucidar minha visão, usaremos como exemplo, o método Contains de uma string.

```csharp
string message = "Hello LinkedIn";

message.Contains("LinkedIn");
!message.Contains("LinkedIn");
message.Contains("LinkedIn") == false;
```

Ao invés de usar ! ou == false, não fica mais claro escrevermos do jeito abaixo?

```csharp
public static bool NotContains(this string @this, string value)
    => !@this.Contains(value);
```

```csharp
string message = "Hello LinkedIn";

message.Contains("LinkedIn");
message.NotContains("LinkedIn");
```

Um outro exemplo clássico: Any. Podemos usar o método Any para verificar se há algum elemento que condiga com predicado parametrizado. Entretanto, quando precisamos confirmar o contrario, ou seja, verificar que não há elemento algum que corresponda com predicado parametrizado, novamente seria usado a negação, porém não faria mais sentido estender um método genérico chamado None que fizesse isso por nós?

## 4. HOF (Higher order function)

Higher order function ou função de alta ordem para os lusófonos, pode ser categorizada como:

- Uma função que recebe uma ou mais funções como argumento;
- Ou uma função que retorna uma função;
- Ou ambos os casos.
  
Primeiro, vamos usar o seguinte cenário como exemplo: precisamos escrever um código que receba um número inteiro como entrada e então devemos exibir na tela se este número é maior ou menor do que 10.

Pois bem, uma forma de resolver esse problema seria a seguinte:

```csharp
int number = 20;
bool isGreaterThanTen = number > 10;

if (isGreaterThanTen)
    Console.WriteLine("Number is greater than 10");
else
    Console.WriteLine("Number is less than 10");
```

Poderíamos usar o operador ternário para alternar a mensagem que seria mostrada, visto que se trata de um cenário simples, mas vamos usar if e else para facilitar as coisas.

Certo, agora veja como conseguimos deixar nosso código com uma pegada mais funcional usando métodos de extensão.

Primeiro iremos **estender** um comportamento para validar se um inteiro é maior que outro:

```csharp
public static bool GreaterThan(this int @this, int number) 
    => @this > number;
```

Receberemos um booleano. Agora iremos estender um comportamento para executar uma função caso o valor booleano seja verdadeiro:

```csharp
public static bool Then(this bool @this, Action action)
{
    if (@this)
        action();

    return @this;
}
```

Além disso, estenderemos também um comportamento para executar uma função caso o valor booleano seja falso:

```csharp
public static void Otherwise(this bool @this, Action action)
{
    if (!@this)
        action();
}
```

Agora, veja o resultado final:

```csharp
int number = 20;

number
      .GreaterThan(4)
      .Then(() => Console.WriteLine($"{number} is greater than 10")) // if
      .Otherwise(() => Console.WriteLine($"{number} is less than 10")); // else
```

GreaterThan retorna um booleano, como temos um booleano de retorno podemos usar o comportamento Then, o qual irá exibir a mensagem informando que o número é de fato maior que dez, mas note que o Then além de usar @this no if, também o retorna ao final da função, com isso nós temos um booleano, portanto, podemos usar o comportamento Otherwise, o qual irá exibir a mensagem informando que o número é de fato menor do que dez. Com apenas três métodos de extensão conseguimos montar um encadeamento simples para resolver o problema com um código completamente diferente.


## Palavras finais

Estes foram alguns exemplos simples que visam expor a diferença que os métodos de extensão podem trazer em uma base de código.