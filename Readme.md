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

```typescript
type MyAwaited<T extends PromiseLike<any>> =
    T extends PromiseLike<infer R> ?
        R extends PromiseLike<any> ?
            MyAwaited<R> :
            R:
        never;
```