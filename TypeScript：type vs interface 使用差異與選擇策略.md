---
title: TypeScript：type vs interface 使用差異與選擇策略
tags: [TypeScript, type, interface]

---

# TypeScript：type vs interface 使用差異與選擇策略

[toc]

在 TypeScript 中，`type`（型別別名）與 `interface`（介面宣告）都可以用來描述資料的型別結構。

在定義 Object Types（物件型別）時，兩者寫法看起來幾乎相同，許多情況下還可以互換使用，但它們在設計目的與使用範圍上仍存在一點差異。

本文的結論其實很簡單：

> **只在 `type` 做不到的時候，才使用 `interface`。**

這篇文章將從以下幾個角度說明為什麼：

- `type` 與 `interface` 的核心差異
- `interface` 不可取代的場景
- 實務上的使用判斷流程

---

## `type` 與 `interface`

在進入正題前，試著判斷以下三題「是否能編譯」，感受 `type` 和 `interface` 的差異：

```typescript
// Q1:
type A = { name: string }
type A = { age: number }            // X:　Duplicate identifier 'A'. (同名報錯)


// Q2:
interface B { name: string }
interface B { age: number }         // O: Declaration Merging (自動合併)


// Q3:
type C = string | number            // O: 定義為 Union 聯合型別

interface D = string | number       // X: interface 只能是物件格式
```

乍看之下，兩者都是在定義型別，為什麼會出現不同的行為？

以下參考官方原文：

> "A **type alias** is exactly that — **a name for any type**."

- `type` 是 **[型別別名（Type Alias）](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases)**，用來為任意型別取一個名字

> "An interface declaration is another way to name an object type." 

- `interface`  是 **[物件型別的另一種命名方式](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#interfaces)**

> "The key distinction is that a type cannot be re-opened to add new properties vs an **interface which is always extendable**."

- 且 `interface` 可被重複宣告，用來 **[擴展屬性](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)（Declaration Merging）**

---

### 基本語法比較

在最常見的物件型別定義中，兩者寫法幾乎相同：

```typescript
// 使用 type
type User = {
  id: string
  name: string
}

// 使用 interface
interface User {
  id: string
  name: string
}
```

官方原文也指出兩者的相似性：

> "Type aliases and interfaces are **very similar**, and in many cases you can choose between them freely."

- 在一般物件型別中，兩者通常可以 **[自由選擇](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)**

---

### `type` 才能做到的事

`type` 能為任意型別取一個名字，適用範圍比 `interface` 更廣：

```typescript
// Union（聯合型別）
type Status = 'active' | 'inactive'

// Tuple（元組）
type Pair = [string, number]

// Conditional Type（條件型別）
type IsString<T> = T extends string ? true : false

// Mapped Type（映射型別）
type MyReadonly<T> = { readonly [K in keyof T]: T[K] }
```

官方原文明確說明了 `interface` 的限制：

> "Unlike a type alias, an interface can **only** describe object shapes; it **cannot** be used to rename primitives."

- `interface` **[只能描述物件的形狀](https://www.typescriptlang.org/cheatsheets/)**，無法用於定義 Primitive（基本型別）、Union、Tuple 等型別

簡單來說：

- **`interface`** → 專門描述「物件結構」
- **`type`** → TypeScript 型別系統的通用工具

那麼，什麼情況下必須使用 `interface` 呢？

---

## `interface` 不可取代的場景

### Declaration Merging 宣告合併

同名的 `interface` 會被 TypeScript 自動合併：

```typescript
interface Config { debug: boolean }
interface Config { lang: string }

// 結果：Config = { debug: boolean; lang: string }
```

使用 `type` 則會直接報錯：

```typescript
type Config = { debug: boolean }
type Config = { lang: string }   // ❌ Duplicate identifier
```

官方原文定義：

> "The simplest, and perhaps most common, type of declaration merging is interface merging. At the most basic level, the merge mechanically **joins the members of both declarations into a single interface** with the same name."

- Declaration Merging 會將同名 `interface` 的成員 **[機械式地結合為單一介面](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)**

---

#### 實務應用：Module Augmentation 擴充第三方型別

Declaration Merging 最重要的實務用途是「擴充第三方套件的型別」，這個做法稱為 Module Augmentation（擴充第三方型別）。

* 使用 `interface` 擴充型別

```typescript
// ✅ interface → merge 成功
declare module 'react-i18next' {
  interface CustomTypeOptions {
    defaultNS: 'common'
    resources: typeof resources
  }
}
```

* 使用 `type` 則會同名報錯

```typescript
// ❌ type → Duplicate identifier
declare module 'react-i18next' {
  type CustomTypeOptions = {
    defaultNS: 'common'
  }
}
```

官方原文：

> "Although JavaScript modules do not support merging, you can **patch existing objects** by importing and then updating them."

- Module Augmentation 透過 Declaration Merging 來 **[對現有物件進行擴充](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation)**

---

### class implements 類別契約

- `implements` 用於檢查類別是否 **[滿足特定介面的契約](https://www.typescriptlang.org/docs/handbook/2/classes.html#implements-clauses)**

```typescript
interface Pingable {
  ping(): void
}

class Sonar implements Pingable {
  ping() {
    console.log("ping!")
  }
}
```

官方原文：

> "You can use an implements clause to check that a class **satisfies a particular interface**. An error will be issued if a class fails to correctly implement it."

技術上 `type` 也能搭配 `implements` 使用，但 `interface` 更能表達 **「這是類別必須遵守的契約」** 這層語意，且轉換為 `type` 沒有實質好處，因此建議維持 `interface`。

---

## 總結

以下整理前面提到的所有差異：

| 能力 | `type` | `interface` |
|---|:---:|:---:|
| 物件型別 | ✅ | ✅ |
| Union 聯合 / Tuple 元組 | ✅ | ❌ |
| Conditional 條件 / Mapped 映射 | ✅ | ❌ |
| Primitive 基本型別 | ✅ | ❌ |
| Declaration Merging 宣告合併 | ❌ | ✅ |

根據使用差異，可以透過以下流程快速判斷：

```
要定義型別
  │
  ▼
在 declare module 內嗎？（Module Augmentation）
  ├─ Yes → 使用 interface（必須）：避免報錯
  │
  ▼
是 class implements 的契約嗎？
  ├─ Yes → 使用 interface（推薦）：語意明確
  │
  ▼
其他所有情況
  └─ 使用 type（預設選擇）：統一風格
```

在實務開發中，`type` 與 `interface` 經常會混合出現在同一個專案中。
當團隊嘗試統一型別定義風格時，才會發現兩者的行為其實並不完全相同。

理解差異後，選擇的標準其實很簡單：

> **只在 `type` 做不到的時候，才使用 `interface`。**

這樣既能維持型別工具的一致性，也能在需要擴充型別時保留 `interface` 的優勢。

### 補充：`interface extends` vs `type &` 的效能差異

TypeScript 官方的 [Performance Wiki](https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections) 中提到：

> "Interfaces create a single flat object type that detects property conflicts...
> Intersections on the other hand just recursively merge properties, and in some cases produce `never`."

簡單來說：

- `interface extends` 會建立 **扁平化（flat）的物件型別**
- `type A & B`（intersection）則會 **遞迴地合併型別結構**

在複雜型別組合時，intersection 有時需要重新展開計算，甚至可能產生 `never`。因此 TypeScript 官方建議，在物件型別的繼承或組合場景中，優先使用 `interface extends`。

不過這個差異通常只會在 **大型專案中或複雜型別組合** 中才比較明顯，對於一般規模的專案而言，幾乎不會感受到實際影響，因此這裡僅作為補充說明。

> 延伸閱讀：[【結論】TypeScriptの型定義はtypeよりinterfaceを使うべき理由](https://zenn.dev/bmth/articles/interface-props-extends)

## 參考資料

- [Everyday Types — Type Aliases](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases)
- [Everyday Types — Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#interfaces)
- [Everyday Types — Differences Between Type Aliases and Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)
- [TypeScript Cheat Sheets — Interfaces](https://www.typescriptlang.org/cheatsheets/)
- [Declaration Merging](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)
- [Declaration Merging — Module Augmentation](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation)
- [Classes — implements Clauses](https://www.typescriptlang.org/docs/handbook/2/classes.html#implements-clauses)
# TypeScript：type vs interface 使用差異與選擇策略

[toc]

在 TypeScript 中，`type`（型別別名）與 `interface`（介面宣告）都可以用來描述資料的型別結構。

在定義 Object Types（物件型別）時，兩者寫法看起來幾乎相同，許多情況下還可以互換使用，但它們在設計目的與使用範圍上仍存在一點差異。

本文的結論其實很簡單：

> **只在 `type` 做不到的時候，才使用 `interface`。**

這篇文章將從以下幾個角度說明為什麼：

- `type` 與 `interface` 的核心差異
- `interface` 不可取代的場景
- 實務上的使用判斷流程

---

## `type` 與 `interface`

在進入正題前，試著判斷以下三題「是否能編譯」，感受 `type` 和 `interface` 的差異：

```typescript
// Q1:
type A = { name: string }
type A = { age: number }            // X:　Duplicate identifier 'A'. (同名報錯)


// Q2:
interface B { name: string }
interface B { age: number }         // O: Declaration Merging (自動合併)


// Q3:
type C = string | number            // O: 定義為 Union 聯合型別

interface D = string | number       // X: interface 只能是物件格式
```

乍看之下，兩者都是在定義型別，為什麼會出現不同的行為？

以下參考官方原文：

> "A **type alias** is exactly that — **a name for any type**."

- `type` 是 **[型別別名（Type Alias）](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases)**，用來為任意型別取一個名字

> "An interface declaration is another way to name an object type." 

- `interface`  是 **[物件型別的另一種命名方式](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#interfaces)**

> "The key distinction is that a type cannot be re-opened to add new properties vs an **interface which is always extendable**."

- 且 `interface` 可被重複宣告，用來 **[擴展屬性](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)（Declaration Merging）**

---

### 基本語法比較

在最常見的物件型別定義中，兩者寫法幾乎相同：

```typescript
// 使用 type
type User = {
  id: string
  name: string
}

// 使用 interface
interface User {
  id: string
  name: string
}
```

官方原文也指出兩者的相似性：

> "Type aliases and interfaces are **very similar**, and in many cases you can choose between them freely."

- 在一般物件型別中，兩者通常可以 **[自由選擇](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)**

---

### `type` 才能做到的事

`type` 能為任意型別取一個名字，適用範圍比 `interface` 更廣：

```typescript
// Union（聯合型別）
type Status = 'active' | 'inactive'

// Tuple（元組）
type Pair = [string, number]

// Conditional Type（條件型別）
type IsString<T> = T extends string ? true : false

// Mapped Type（映射型別）
type MyReadonly<T> = { readonly [K in keyof T]: T[K] }
```

官方原文明確說明了 `interface` 的限制：

> "Unlike a type alias, an interface can **only** describe object shapes; it **cannot** be used to rename primitives."

- `interface` **[只能描述物件的形狀](https://www.typescriptlang.org/cheatsheets/)**，無法用於定義 Primitive（基本型別）、Union、Tuple 等型別

簡單來說：

- **`interface`** → 專門描述「物件結構」
- **`type`** → TypeScript 型別系統的通用工具

那麼，什麼情況下必須使用 `interface` 呢？

---

## `interface` 不可取代的場景

### Declaration Merging 宣告合併

同名的 `interface` 會被 TypeScript 自動合併：

```typescript
interface Config { debug: boolean }
interface Config { lang: string }

// 結果：Config = { debug: boolean; lang: string }
```

使用 `type` 則會直接報錯：

```typescript
type Config = { debug: boolean }
type Config = { lang: string }   // ❌ Duplicate identifier
```

官方原文定義：

> "The simplest, and perhaps most common, type of declaration merging is interface merging. At the most basic level, the merge mechanically **joins the members of both declarations into a single interface** with the same name."

- Declaration Merging 會將同名 `interface` 的成員 **[機械式地結合為單一介面](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)**

---

#### 實務應用：Module Augmentation 擴充第三方型別

Declaration Merging 最重要的實務用途是「擴充第三方套件的型別」，這個做法稱為 Module Augmentation（擴充第三方型別）。

* 使用 `interface` 擴充型別

```typescript
// ✅ interface → merge 成功
declare module 'react-i18next' {
  interface CustomTypeOptions {
    defaultNS: 'common'
    resources: typeof resources
  }
}
```

* 使用 `type` 則會同名報錯

```typescript
// ❌ type → Duplicate identifier
declare module 'react-i18next' {
  type CustomTypeOptions = {
    defaultNS: 'common'
  }
}
```

官方原文：

> "Although JavaScript modules do not support merging, you can **patch existing objects** by importing and then updating them."

- Module Augmentation 透過 Declaration Merging 來 **[對現有物件進行擴充](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation)**

---

### class implements 類別契約

- `implements` 用於檢查類別是否 **[滿足特定介面的契約](https://www.typescriptlang.org/docs/handbook/2/classes.html#implements-clauses)**

```typescript
interface Pingable {
  ping(): void
}

class Sonar implements Pingable {
  ping() {
    console.log("ping!")
  }
}
```

官方原文：

> "You can use an implements clause to check that a class **satisfies a particular interface**. An error will be issued if a class fails to correctly implement it."

技術上 `type` 也能搭配 `implements` 使用，但 `interface` 更能表達 **「這是類別必須遵守的契約」** 這層語意，且轉換為 `type` 沒有實質好處，因此建議維持 `interface`。

---

## 總結

以下整理前面提到的所有差異：

| 能力 | `type` | `interface` |
|---|:---:|:---:|
| 物件型別 | ✅ | ✅ |
| Union 聯合 / Tuple 元組 | ✅ | ❌ |
| Conditional 條件 / Mapped 映射 | ✅ | ❌ |
| Primitive 基本型別 | ✅ | ❌ |
| Declaration Merging 宣告合併 | ❌ | ✅ |

根據使用差異，可以透過以下流程快速判斷：

```
要定義型別
  │
  ▼
在 declare module 內嗎？（Module Augmentation）
  ├─ Yes → 使用 interface（必須）：避免報錯
  │
  ▼
是 class implements 的契約嗎？
  ├─ Yes → 使用 interface（推薦）：語意明確
  │
  ▼
其他所有情況
  └─ 使用 type（預設選擇）：統一風格
```

在實務開發中，`type` 與 `interface` 經常會混合出現在同一個專案中。
當團隊嘗試統一型別定義風格時，才會發現兩者的行為其實並不完全相同。

理解差異後，選擇的標準其實很簡單：

> **只在 `type` 做不到的時候，才使用 `interface`。**

這樣既能維持型別工具的一致性，也能在需要擴充型別時保留 `interface` 的優勢。

### 補充：`interface extends` vs `type &` 的效能差異

TypeScript 官方的 [Performance Wiki](https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections) 中提到：

> "Interfaces create a single flat object type that detects property conflicts...
> Intersections on the other hand just recursively merge properties, and in some cases produce `never`."

簡單來說：

- `interface extends` 會建立 **扁平化（flat）的物件型別**
- `type A & B`（intersection）則會 **遞迴地合併型別結構**

在複雜型別組合時，intersection 有時需要重新展開計算，甚至可能產生 `never`。因此 TypeScript 官方建議，在物件型別的繼承或組合場景中，優先使用 `interface extends`。

不過這個差異通常只會在 **大型專案中或複雜型別組合** 中才比較明顯，對於一般規模的專案而言，幾乎不會感受到實際影響，因此這裡僅作為補充說明。

> 延伸閱讀：[【結論】TypeScriptの型定義はtypeよりinterfaceを使うべき理由](https://zenn.dev/bmth/articles/interface-props-extends)

## 參考資料

- [Everyday Types — Type Aliases](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases)
- [Everyday Types — Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#interfaces)
- [Everyday Types — Differences Between Type Aliases and Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)
- [TypeScript Cheat Sheets — Interfaces](https://www.typescriptlang.org/cheatsheets/)
- [Declaration Merging](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)
- [Declaration Merging — Module Augmentation](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation)
- [Classes — implements Clauses](https://www.typescriptlang.org/docs/handbook/2/classes.html#implements-clauses)
