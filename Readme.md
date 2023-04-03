# Type challenges

## Introduction

L'objectif de cet article est de fournir un guide progressif de la gestion des types en typescript en se basant sur les 
[type challenges](https://github.com/type-challenges/type-challenges).
Nous présenterons chaque challenge, puis les différents concepts associés, et enfin une explication détaillée de la 
solution proposée.

## Les challenges "easy"

### [4 - Pick](https://github.com/type-challenges/type-challenges/blob/main/questions/00004-easy-pick/README.md)


#### Présentation
```typescript
interface Todo {
  title: string
  description: string
  completed: boolean
}

type TodoPreview = MyPick<Todo, 'title' | 'completed'>

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
}
```

L'objectif est d'implémenter le générique "Pick" déjà présent dans typescript. Il permet de créer un type à partir d'un
sous-ensemble des propriétés d'un autre type.

#### Concepts

- L'union de type `'title' | 'completed'` signifie que la valeur peut prendre l'un ou l'autre des types présentés.
- `keyof` permet de générer une union de types autorisant chaque clé de l'objet.
```typescript
interface Todo {
    title: string
    description: string
    completed: boolean
}

type TodoKeys = keyof Todo;
// TodoKeys = 'title' | 'description' | 'completed'
```
- Typescript permet de créer des objets à partir d'un ensemble de clé. Exemple :
```typescript
type Key = 'title' | 'completed'
type MyObj = {
    [key in Key]: string
}

type MyObj2 = {
    title: string;
    completed: string;
}
```
Les 2 types `MyObj` et `MyObj2` sont équivalents.

- L'extraction de propriétés
```typescript
interface Todo {
    title: string
    description: string
    completed: boolean
}

type Title = Todo["title"];
// Title sera de type string
```

#### Implémentation

Dans un premier temps, nous limitons les valeurs possibles de la liste des clés demandées aux clés de l'objet `Objet`.

```typescript
type MyPick<Objet, K extends keyof Objet> = any
```

Ensuite, nous générons un objet à partir des clés fournies
```typescript
type MyPick<Objet, K extends keyof Objet> = {
  [key in K]: any
}
```

Il nous reste à typer la valeur des clés de notre objet

```typescript
type MyPick<Objet, K extends keyof Objet> = {
  [key in K]: Objet[key]
}
```

### [7 - Readonly](https://github.com/type-challenges/type-challenges/blob/main/questions/00007-easy-readonly/README.md)

#### Présentation

```typescript
interface Todo {
  title: string
  description: string
}

const todo: MyReadonly<Todo> = {
  title: "Hey",
  description: "foobar"
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
```

Il faut réimplémenter le générique `Readonly<T>` déjà présent dans typescript. 
Il empêche de modifier l'ensemble des valeurs des clés de l'objet.

#### Concepts
- `readonly` permet d'empêcher la modification d'une clé d'un objet
```typescript
type ReadonlyTodo = {
    readonly title: string
    readonly description: string
}
```

#### Implémentation

Commençons par générer l'objet en tant que tel
```typescript
type MyReadonly<T> = {
    [key in keyof T]: T[key]
}
```

Il nous reste uniquement à passer chacune des valeurs en readonly :
```typescript
type MyReadonly<T> = {
    readonly [key in keyof T]: T[key]
}
```

### [11 - Tuple to Object](https://github.com/type-challenges/type-challenges/blob/main/questions/00011-easy-tuple-to-object/README.md)

#### Présentation

```typescript
const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const

type result = TupleToObject<typeof tuple> 
// expected { 'tesla': 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

#### Concepts
- `as const` signifie que l'objet ne pourra plus être modifié. Dans le cadre de notre exemple :
```typescript
const tesla: (typeof tuple)[0] = "tesla"
//    ^? const tesla: "tesla"
```

#### Implémentation

Commençons par extraire les différentes valeurs de notre tuple.

```typescript
type ValeursTuple<T extends Record<number, any>> = T[number]

const keys: ValeursTuple<typeof tuple> = "tesla"
//     ^? "tesla" | "model 3" | "model X" | "model Y"
```

Ensuite, nous n'avons plus qu'à créer notre objet à partir de ces valeurs

```typescript
type TupleToObject<T extends readonly any[]> = {
  [key in T[number]]: key
}
```

### [14 - First of Array](https://github.com/type-challenges/type-challenges/blob/main/questions/00014-easy-first/README.md)

#### Présentation

```typescript
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type head1 = First<arr1> // expected to be 'a'
type head2 = First<arr2> // expected to be 3
```

Implémenter un généric qui retourne le type du premier élément d'un tableau.

#### Concepts

- Les assertions de type : Typescript permet de faire des assertions sur des types et, 
suivant la véracité de l'assertion, retourner un type ou un autre.
```typescript
type OnlyString<T> = T extends string ? T : never

type Ok = OnlyString<"str">
// type Ok = "str"
type Ko = OnlyString<{}>
// type Ko = never
```

#### Implémentation

À partir des assertions de type, l'implémentation est plutôt directe

```typescript
type First<T extends any[]> = T extends [] ? never : T[0];
```

### [18 - Length of Tuple](https://github.com/type-challenges/type-challenges/blob/main/questions/00018-easy-tuple-length/README.md)

#### Présentation

```typescript
type tesla = ['tesla', 'model 3', 'model X', 'model Y']
type spaceX = ['FALCON 9', 'FALCON HEAVY', 'DRAGON', 'STARSHIP', 'HUMAN SPACEFLIGHT']

type teslaLength = Length<tesla>  // expected 4
type spaceXLength = Length<spaceX> // expected 5
```

#### Implémentation

````typescript
type Length<T extends readonly any[]> = T["length"]
````

### [43 - Exclude](https://github.com/type-challenges/type-challenges/blob/main/questions/00043-easy-exclude/README.md)

#### Présentation

```typescript
type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
```

Créer un générique permettant de retirer un pour plusieurs types d'une union de types.

#### Implémentation

```typescript
type MyExclude<T, U> = T extends U ? never : T
```

### [189 - Awaited](https://github.com/type-challenges/type-challenges/blob/main/questions/00189-easy-awaited/README.md)

#### Présentation

```typescript
type ExampleType = Promise<string>

type Result = MyAwaited<ExampleType> // string
```

Créer un générique permettant de récupérer le type d'une promise

#### Concepts
- `infer` permet de récupérer le type dans une assertion.
```typescript
type Example<T extends Array<any>> = T extends Array<infer R> ? R : never
type Str = Example<string[]>
// Str = string
```
- `PromiseLike` est une implémentation minimaliste de la promise. C'est simplement un objet ayant une méthode `then()`
valide pour une promise.

#### Implémentation

Dans un premier temps, nous gérons le cas de promesse non "nested".

```typescript
type MyAwaited<T extends PromiseLike<any>> =
    T extends PromiseLike<infer R> ?
        R :
        never;
```

Dans le cas ou `R` est une promesse, nous voulons récupérer son résultat. Il nous suffit d'ajouter un appel récursif à
MyAwaited si `R` est de type `PromiseLike`.

```typescript
type MyAwaited<T extends PromiseLike<any>> =
    T extends PromiseLike<infer R> ?
        R extends PromiseLike<any> ?
            MyAwaited<R> :
            R:
        never;
```

### [268 - If](https://github.com/type-challenges/type-challenges/blob/main/questions/00268-easy-if/README.md)

#### Présentation

```typescript
type A = If<true, 'a', 'b'>  // expected to be 'a'
type B = If<false, 'a', 'b'> // expected to be 'b'
```

Créer un générique `If` retournant le deuxième argument si le premier est `true`, et le 3ème sinon

#### Implémentation

Implémentation plutôt directe, RAS.

```typescript
type If<C extends boolean, T, F> = C extends true ? T : F;
```

### [533 - Concat](https://github.com/type-challenges/type-challenges/blob/main/questions/00533-easy-concat/README.md)

#### Présentation

```typescript
type Result = Concat<[1], [2]> // expected to be [1, 2]
```

Créer un générique `Concat` retournant la concaténation des 2 tableaux en paramètre

#### Concepts

- Array Spread. Typescript permet de `spread` des tuples. Par exemple, pour ajouter un élément à la fin d'un tuple :
```typescript
type AddElementToTuple<T extends any[], U> = [...T, U]; 
```

#### Implémentation

Implémentation plutôt directe, RAS.

```typescript
type Concat<T extends any[], U extends any[]> = [...T, ...U];
```

### [898 - Includes](https://github.com/type-challenges/type-challenges/blob/main/questions/00898-easy-includes/README.md)

#### Présentation

```typescript
type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false`
```

Créer un générique `Includes` déterminant si un tuple contient un type donné.

#### Concepts

- Vous pouvez utiliser des génériques dans les opérateurs ternaires de types. Exemple :
```typescript

type assertion = Equal<T1, T2> extends true ? "egaux" : "non-égaux"
```

#### Implémentation

Gérons d'abord le cas où le premier élément est du bon type :

```typescript
type Includes<T extends readonly any[], U> = T extends readonly [infer Head, ...infer Tail] ?
    Equal<Head, U> :
    false
```

Si le premier élement n'est pas du bon type, il faut faire un appel récursif à `Includes` sur le reste du tuple. Pour
faire notre ternaire, nous utilisons : `Equal<Head, U> extends true`

```typescript
type Includes<T extends readonly any[], U> = T extends readonly [infer Head, ...infer Tail] ?
    Equal<Head, U> extends true ?
        true:
        Includes<Tail, U> :
    false
```

### [3057 - Push](https://github.com/type-challenges/type-challenges/blob/main/questions/03057-easy-push/README.md)

#### Présentation

```typescript
type Result = Push<[1, 2], '3'> // [1, 2, '3']
```

Créer un générique pour ajouter un élément à la fin d'un tuple

#### Implémentation

En utilisant le spread operator, rien de bien compliqué

```typescript
type Push<T extends any[], U> = [...T, U]
```

### [3060 - Unshift](https://github.com/type-challenges/type-challenges/blob/main/questions/03060-easy-unshift/README.md)

#### Présentation

```typescript
type Result = Unshift<[1, 2], 0> // [0, 1, 2,]
```

Créer un générique pour ajouter un élément au début d'un tuple

#### Implémentation

En utilisant le spread operator, rien de bien compliqué

```typescript
type Unshift<T extends any[], U> = [U, ...T]
```

### [3312 - Parameters](https://github.com/type-challenges/type-challenges/blob/main/questions/03312-easy-parameters/README.md)

#### Présentation

```typescript
const foo = (arg1: string, arg2: number): void => {}

type FunctionParamsType = MyParameters<typeof foo> // [arg1: string, arg2: number]
```

Créer un générique permettant d'extraire le type des paramètres d'une fonction

#### Concepts

- L'opérateur `infer` peut aussi être utilisé sur les arguments d'une fonction.

#### Implémentation

En sachant que `infer` peut être utilisé sur les arguments d'une fonction, l'implémentation est plutôt directe.

```typescript
type MyParameters<T extends (...args: any[]) => any> = 
    T extends (...args: infer Args) => any ? 
        Args : 
        never;
```

## Les challenges "medium"

### [2 - Get Return Type](https://github.com/type-challenges/type-challenges/blob/main/questions/00002-medium-return-type/README.md)

#### Présentation

```typescript
const fn = (v: boolean) => {
    if (v)
        return 1
    else
        return 2
}

type a = MyReturnType<typeof fn> // should be "1 | 2"
```

Implémenter le générique `ReturnType` de typescript qui récupère le type de retour d'une fonction.

#### Concepts

- L'opérateur `infer` peut aussi être utilisé sur le type de retour d'une fonction

#### Implémentation

En sachant que `infer` peut être utilisé sur le type de retour d'une fonction, l'implémentation est plutôt directe.

```typescript
type MyReturnType<T extends (...args: any) => any> =
    T extends (...args: any) => infer Return ?
        Return :
        never
```

### [3 - Omit](https://github.com/type-challenges/type-challenges/blob/main/questions/00003-medium-omit/README.md)

#### Présentation

```typescript
interface Todo {
    title: string
    description: string
    completed: boolean
}

type TodoPreview = MyOmit<Todo, 'description' | 'title'>

const todo: TodoPreview = {
    completed: false,
}

```

Implémenter le générique `Omit` de typescript qui retire des propriétés d'un objet.

#### Concepts

- Le générique `Exclude` est built-in dans Typescript

#### Implémentation

Il nous suffit de retirer les clés devant être ignorées grâce à `Exclude`

```typescript
type MyOmit<T, K extends keyof T> = {
    [Key in Exclude<keyof T, K>]: T[Key]
}
```

### [8 - Readonly 2](https://github.com/type-challenges/type-challenges/blob/main/questions/00008-medium-readonly-2/README.md)

#### Présentation

```typescript
interface Todo {
    title: string
    description: string
    completed: boolean
}

const todo: MyReadonly2<Todo, 'title' | 'description'> = {
    title: "Hey",
    description: "foobar",
    completed: false,
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
todo.completed = true // OK
```

Implémenter un générique `MyReadonly2<T, K>` qui permet de transformer les propriétés `K` de `T` en readonly.

#### Concepts

- Les génériques `Pick` et `Omit` sont built-in dans Typescript

#### Implémentation

Créons un objet contenant les propriétés à marquer en readonly

```typescript
type MyReadonly2<T, K extends keyof T> = {
    readonly [Key in K]: T[Key]
} 
```

Lorsque le 2ème paramètre n'est pas fourni, toutes les valeurs passent en readonly. Mettons lui une valeur par
défaut.

```typescript
type MyReadonly2<T, K extends keyof T = keyof T> = {
    readonly [Key in K]: T[Key]
} 
```

Ajoutons ensuite les éléments qui ne seront pas en readonly

```typescript
type MyReadonly2<T, K extends keyof T = keyof T> = {
  readonly [Key in K]: T[Key]
} & {
  [Key in Exclude<keyof T, K>]: T[Key]
}
```

Il semble y avoir un problème sur le dernier test.

```typescript
type T = MyReadonly2<Todo2, 'description'>
/*
type T = {
    readonly description?: string | undefined;
} & {
    title: string;
    completed: boolean;
}
 */
```

Or nous voulons :
```typescript
interface Expected {
  readonly title: string
  readonly description?: string
  completed: boolean
}
```

Le readonly du title a disparu. Pour le conserver, nous avons l'astuce suivante. En effet,
lorsque le `keyof` est accolé au `in`, le modificateur `readonly` est conservé.

```typescript
type MyReadonly2<T, K extends keyof T = keyof T> = {
    readonly [Key in K]: T[Key]
} & {
    [Key in keyof Omit<T, K>]: T[Key]
}
```
