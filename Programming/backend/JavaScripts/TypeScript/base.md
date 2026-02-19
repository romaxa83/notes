#js #ts

**TypeScript** — это надмножество **JavaScript**, которое добавляет статическую типизацию.

- Книга по TypeScript - https://typescript-handbook.ru
#### 🔹 Основы TypeScript

##### 🔸 Базовые типы
``` typescript
// Примитивные типы
const name: string = "Иван";
const age: number = 25;
const isActive: boolean = true;
const nothing: null = null;
const undefined_value: undefined = undefined;

// Literalные типы (точные значения)
let status: "active" | "inactive" | "pending" = "active";
status = "inactive"; // ✅ OK
// status = "unknown"; // ❌ Ошибка

// Union типы (объединение нескольких типов)
let id: string | number;
id = "123"; // ✅
id = 123; // ✅
// id = true; // ❌

// Any (избегайте если возможно)
let anything: any = "может быть чем угодно";
anything = 123;
anything = true;

// Unknown (безопаснее, чем any)
let unknownValue: unknown = "строка";
// unknownValue.toUpperCase(); // ❌ Ошибка
if (typeof unknownValue === "string") {
  unknownValue.toUpperCase(); // ✅ OK
}
```

##### 🔸 Массивы и кортежи
``` typescript
// Массивы
const numbers: number[] = [1, 2, 3];
const strings: Array<string> = ["a", "b", "c"];
const mixed: (string | number)[] = ["a", 1, "b"];

// Массив объектов
interface User {
  id: number;
  name: string;
}
const users: User[] = [
  { id: 1, name: "Иван" },
  { id: 2, name: "Мария" }
];

// Кортежи (массив фиксированной длины с определенными типами)
const tuple: [string, number] = ["hello", 42];
const tuple2: [string, number, boolean] = ["test", 1, true];

// Опциональные элементы в кортеже
const optional: [string, number?] = ["hello"];
const optional2: [string, number?] = ["hello", 42];

// Rest элементы
const variadic: [string, ...number[]] = ["id", 1, 2, 3];
```

##### 🔸 Объекты и интерфейсы
```typescript
// Простой объект
const user: { name: string; age: number } = {
  name: "Иван",
  age: 25
};

// Интерфейс (рекомендуется)
interface Person {
  name: string;
  age: number;
  email?: string; // опциональное свойство
  readonly id: number; // только для чтения
  [key: string]: any; // дополнительные свойства
}

const person: Person = {
  name: "Иван",
  age: 25,
  id: 1
  // email не обязателен
};

// Type (альтернатива интерфейсу)
type PersonType = {
  name: string;
  age: number;
};

// Расширение интерфейса
interface Employee extends Person {
  position: string;
  salary: number;
}

const employee: Employee = {
  name: "Иван",
  age: 25,
  id: 1,
  position: "Разработчик",
  salary: 100000
};
```

##### 🔸 Функции
``` typescript
// Типизация параметров и возвращаемого значения
function add(a: number, b: number): number {
  return a + b;
}

// Стрелочная функция
const multiply = (a: number, b: number): number => a * b;

// Опциональные параметры
function greet(name: string, age?: number): string {
  return age ? `${name}, ${age} лет` : name;
}

// Параметры по умолчанию
function repeat(text: string, times: number = 2): string {
  return text.repeat(times);
}

// Rest параметры
function sum(...numbers: number[]): number {
  return numbers.reduce((acc, num) => acc + num, 0);
}

// Перегрузка функций
function format(value: string): string;
function format(value: number): string;
function format(value: string | number): string {
  return String(value).toUpperCase();
}

// Функция без возвращаемого значения
function logMessage(msg: string): void {
  console.log(msg);
}

// Функция, которая никогда не возвращает значение (бросает ошибку или бесконечный цикл)
function throwError(msg: string): never {
  throw new Error(msg);
}

// Тип функции
type MathOperation = (a: number, b: number) => number;
const divide: MathOperation = (a, b) => a / b;
```

##### 🔸 Generics (Обобщенные типы)
``` typescript
// Простой generic
function getArray<T>(items: T[]): T[] {
  return items;
}

getArray<string>(["a", "b", "c"]);
getArray<number>([1, 2, 3]);

// Generic с констрейнтом
function getLength<T extends { length: number }>(value: T): number {
  return value.length;
}

getLength("hello"); // ✅
getLength([1, 2, 3]); // ✅
// getLength(123); // ❌

// Generic класс
class Container<T> {
  private data: T;

  constructor(value: T) {
    this.data = value;
  }

  getData(): T {
    return this.data;
  }

  setData(value: T): void {
    this.data = value;
  }
}

const stringContainer = new Container<string>("hello");
const numberContainer = new Container<number>(42);

// Generic interface
interface ApiResponse<T> {
  status: number;
  data: T;
  message: string;
}

interface User {
  id: number;
  name: string;
}

const response: ApiResponse<User> = {
  status: 200,
  data: { id: 1, name: "Иван" },
  message: "Success"
};

// Keyof
interface Config {
  host: string;
  port: number;
  debug: boolean;
}

function getConfigValue<K extends keyof Config>(key: K): Config[K] {
  const config: Config = {
    host: "localhost",
    port: 3000,
    debug: true
  };
  return config[key];
}

getConfigValue("host"); // ✅ возвращает string
getConfigValue("port"); // ✅ возвращает number
// getConfigValue("unknown"); // ❌ Ошибка
```

##### 🔸 Классы
``` typescript
// Базовый класс
class Animal {
  protected name: string; // доступно только в классе и потомках
  private age: number; // доступно только в классе
  public species: string; // доступно везде (default)

  constructor(name: string, age: number, species: string) {
    this.name = name;
    this.age = age;
    this.species = species;
  }

  protected getInfo(): string {
    return `${this.name} (${this.age})`;
  }

  public speak(): void {
    console.log(`${this.name} издает звук`);
  }
}

// Наследование
class Dog extends Animal {
  constructor(name: string, age: number) {
    super(name, age, "Собака");
  }

  public speak(): void {
    console.log(`${this.name} лает`);
  }

  public getDetails(): string {
    return this.getInfo(); // доступно как protected
  }
}

// Абстрактный класс
abstract class Shape {
  abstract getArea(): number;

  abstract getPerimeter(): number;

  describe(): void {
    console.log(`Площадь: ${this.getArea()}`);
  }
}

class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }

  getArea(): number {
    return Math.PI * this.radius ** 2;
  }

  getPerimeter(): number {
    return 2 * Math.PI * this.radius;
  }
}

// Статические методы и свойства
class Counter {
  private static count: number = 0;

  static increment(): void {
    Counter.count++;
  }

  static getCount(): number {
    return Counter.count;
  }
}

Counter.increment();
console.log(Counter.getCount()); // 1

// Getters и Setters
class Temperature {
  private _celsius: number;

  constructor(celsius: number) {
    this._celsius = celsius;
  }

  get fahrenheit(): number {
    return (this._celsius * 9) / 5 + 32;
  }

  set fahrenheit(value: number) {
    this._celsius = ((value - 32) * 5) / 9;
  }
}

const temp = new Temperature(0);
console.log(temp.fahrenheit); // 32
temp.fahrenheit = 68;
console.log(temp._celsius); // примерно 20
```

##### 🔸 Enum
``` typescript
// Числовой enum
enum Direction {
  Up = 0,
  Down = 1,
  Left = 2,
  Right = 3
}

// Строковый enum
enum Status {
  Active = "ACTIVE",
  Inactive = "INACTIVE",
  Pending = "PENDING"
}

// Heterogeneous enum (смешанный)
enum Mixed {
  No = 0,
  Yes = "YES"
}

// Enum в функции
function moveCharacter(direction: Direction): void {
  if (direction === Direction.Up) {
    console.log("Движение вверх");
  }
}

// Обратное отображение (только для числовых enum)
const directionName = Direction[0]; // "Up"
const directionValue = Direction.Up; // 0
```