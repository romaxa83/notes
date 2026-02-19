#js #vue #ts

##### 🔸 Базовый компонент с TypeScript
``` vue
<template>
  <div>
    <h1>{{ message }}</h1>
    <button @click="increment">Клики: {{ count }}</button>
    <p>Сумма: {{ sum }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

// Типизированные ref
const count = ref<number>(0);
const message = ref<string>("Hello, TypeScript!");

// Вычисляемое свойство с типом
const sum = computed<number>(() => count.value * 2);

// Функция с типами
const increment = (): void => {
  count.value++;
};
</script>
```

##### 🔸 Работа с объектами и интерфейсами
``` vue
<template>
  <div>
    <h2>{{ user.name }}</h2>
    <p>Email: {{ user.email }}</p>
    <p>Возраст: {{ user.age }}</p>
    <button @click="updateUser">Обновить</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

// Интерфейс для пользователя
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Типизированный ref с интерфейсом
const user = ref<User>({
  id: 1,
  name: "Иван Петров",
  email: "ivan@example.com",
  age: 25
});

// Функция с параметром интерфейса
const updateUser = (): void => {
  user.value.age++;
};
</script>
```

##### 🔸 Массивы в Vue 3
``` vue
<template>
  <div>
    <h2>Список пользователей</h2>
    <ul>
      <li v-for="user in users" :key="user.id">
        {{ user.name }} ({{ user.email }})
        <button @click="removeUser(user.id)">Удалить</button>
      </li>
    </ul>
    <button @click="addUser">Добавить пользователя</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

interface User {
  id: number;
  name: string;
  email: string;
}

const users = ref<User[]>([
  { id: 1, name: "Иван", email: "ivan@example.com" },
  { id: 2, name: "Мария", email: "maria@example.com" }
]);

let nextId = 3;

const addUser = (): void => {
  users.value.push({
    id: nextId++,
    name: "Новый пользователь",
    email: `user${nextId}@example.com`
  });
};

const removeUser = (id: number): void => {
  users.value = users.value.filter(u => u.id !== id);
};
</script>
```

##### 🔸 Props и Emits
```vue
<!-- Компонент UserCard.vue -->
<template>
  <div class="card">
    <h3>{{ user.name }}</h3>
    <p>{{ user.email }}</p>
    <button @click="editUser">Редактировать</button>
    <button @click="deleteUser" style="color: red">Удалить</button>
  </div>
</template>

<script setup lang="ts">
// Определяем интерфейс для props
interface User {
  id: number;
  name: string;
  email: string;
}

// Props с типизацией
withDefaults(defineProps<{
  user: User;
  canEdit?: boolean;
}>(), {
  canEdit: true
});

// Emits с типизацией
const emit = defineEmits<{
  edit: [id: number];
  delete: [id: number];
}>();

const editUser = (): void => {
  emit('edit', props.user.id);
};

const deleteUser = (): void => {
  emit('delete', props.user.id);
};
</script>

<style scoped>
.card {
  border: 1px solid #ddd;
  padding: 15px;
  border-radius: 8px;
  margin: 10px 0;
}
</style>
```

##### 🔸 Использование компонента с props
```vue
<!-- Главный компонент UserList.vue -->
<template>
  <div>
    <h1>Список пользователей</h1>
    <UserCard
      v-for="user in users"
      :key="user.id"
      :user="user"
      :can-edit="true"
      @edit="handleEdit"
      @delete="handleDelete"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import UserCard from './UserCard.vue';

interface User {
  id: number;
  name: string;
  email: string;
}

const users = ref<User[]>([
  { id: 1, name: "Иван", email: "ivan@example.com" },
  { id: 2, name: "Мария", email: "maria@example.com" }
]);

const handleEdit = (id: number): void => {
  console.log(`Редактировать пользователя ${id}`);
};

const handleDelete = (id: number): void => {
  users.value = users.value.filter(u => u.id !== id);
};
</script>
```

##### 🔸 Composables (Переиспользуемая логика)
``` typescript
// src/composables/useFetch.ts
import { ref, computed } from 'vue';

interface FetchState<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
}

export function useFetch<T>(url: string) {
  const data = ref<T | null>(null);
  const loading = ref<boolean>(false);
  const error = ref<Error | null>(null);

  const fetch = async (): Promise<void> => {
    loading.value = true;
    error.value = null;

    try {
      const response = await window.fetch(url);
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      data.value = await response.json();
    } catch (err) {
      error.value = err instanceof Error ? err : new Error(String(err));
    } finally {
      loading.value = false;
    }
  };

  return {
    data,
    loading,
    error,
    fetch
  };
}
```

```typescript
// src/composables/useCounter.ts
import { ref, computed } from 'vue';

export function useCounter(initialValue: number = 0) {
  const count = ref<number>(initialValue);

  const doubled = computed<number>(() => count.value * 2);
  const isEven = computed<boolean>(() => count.value % 2 === 0);

  const increment = (): void => {
    count.value++;
  };

  const decrement = (): void => {
    count.value--;
  };

  const reset = (): void => {
    count.value = initialValue;
  };

  return {
    count,
    doubled,
    isEven,
    increment,
    decrement,
    reset
  };
}
```

``` vue
<!-- Использование composable -->
<template>
  <div>
    <p>Счетчик: {{ count }}</p>
    <p>Удвоенное: {{ doubled }}</p>
    <p>Четное: {{ isEven }}</p>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
    <button @click="reset">Сброс</button>
  </div>
</template>

<script setup lang="ts">
import { useCounter } from '../composables/useCounter';

const { count, doubled, isEven, increment, decrement, reset } = useCounter(10);
</script>
```

##### 🔸 Работа с формами
``` vue
<template>
  <form @submit.prevent="submitForm">
    <div>
      <label>
        Имя:
        <input v-model="form.name" type="text" required />
      </label>
    </div>

    <div>
      <label>
        Email:
        <input v-model="form.email" type="email" required />
      </label>
    </div>

    <div>
      <label>
        Возраст:
        <input v-model.number="form.age" type="number" required />
      </label>
    </div>

    <div>
      <label>
        <input v-model="form.subscribe" type="checkbox" />
        Подписаться на новости
      </label>
    </div>

    <div>
      <label>
        Роль:
        <select v-model="form.role">
          <option value="user">Пользователь</option>
          <option value="admin">Администратор</option>
          <option value="moderator">Модератор</option>
        </select>
      </label>
    </div>

    <button type="submit">Отправить</button>
    <p v-if="submitted" style="color: green">
      Форма отправлена успешно!
    </p>
  </form>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue';

// Интерфейс для формы
interface FormData {
  name: string;
  email: string;
  age: number;
  subscribe: boolean;
  role: 'user' | 'admin' | 'moderator';
}

// Используем reactive для объектов форм
const form = reactive<FormData>({
  name: '',
  email: '',
  age: 0,
  subscribe: false,
  role: 'user'
});

const submitted = ref<boolean>(false);

const submitForm = async (): Promise<void> => {
  console.log('Отправка формы:', form);

  // Валидация
  if (!form.name || !form.email) {
    alert('Заполните все поля');
    return;
  }

  // Здесь можно отправить данные на сервер
  submitted.value = true;

  // Сброс через 2 секунды
  setTimeout(() => {
    submitted.value = false;
  }, 2000);
};
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  gap: 15px;
  max-width: 400px;
}

div {
  display: flex;
  flex-direction: column;
}

label {
  font-weight: bold;
  margin-bottom: 5px;
}

input,
select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

button {
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

button:hover {
  background-color: #0056b3;
}
</style>
```

##### 🔸 Жизненный цикл с TypeScript
``` vue
<template>
  <div>
    <h2>{{ message }}</h2>
    <button @click="updateMessage">Обновить</button>
  </div>
</template>

<script setup lang="ts">
import {
  ref,
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
  onErrorCaptured
} from 'vue';

const message = ref<string>('Начальное значение');

// До монтирования
onBeforeMount((): void => {
  console.log('До монтирования');
});

// После монтирования
onMounted((): void => {
  console.log('Компонент смонтирован');
  // Загрузка данных, подписки и т.д.
});

// До обновления
onBeforeUpdate((): void => {
  console.log('До обновления DOM');
});

// После обновления
onUpdated((): void => {
  console.log('DOM обновлен');
});

// Перед удалением
onBeforeUnmount((): void => {
  console.log('Перед удалением');
});

// После удаления
onUnmounted((): void => {
  console.log('Компонент удален');
  // Очистка подписок, таймеров и т.д.
});

// Обработка ошибок
onErrorCaptured((err: unknown, instance, info): boolean | void => {
  console.error('Ошибка в компоненте:', err);
  // Возврат false предотвращает распространение ошибки
  return false;
});

const updateMessage = (): void => {
  message.value = `Обновлено: ${new Date().toLocaleTimeString()}`;
};
</script>
```

##### 🔸 Работа с API
``` typescript
// src/services/api.ts
interface ApiConfig {
  baseURL: string;
  timeout: number;
  headers: Record<string, string>;
}

interface ApiResponse<T> {
  status: number;
  data: T;
  message: string;
}

class ApiService {
  private config: ApiConfig;

  constructor(baseURL: string = 'https://api.example.com') {
    this.config = {
      baseURL,
      timeout: 5000,
      headers: {
        'Content-Type': 'application/json'
      }
    };
  }

  async get<T>(endpoint: string): Promise<T> {
    const response = await fetch(`${this.config.baseURL}${endpoint}`, {
      method: 'GET',
      headers: this.config.headers
    });

    if (!response.ok) {
      throw new Error(`API Error: ${response.statusText}`);
    }

    const data: ApiResponse<T> = await response.json();
    return data.data;
  }

  async post<T, D>(endpoint: string, data: D): Promise<T> {
    const response = await fetch(`${this.config.baseURL}${endpoint}`, {
      method: 'POST',
      headers: this.config.headers,
      body: JSON.stringify(data)
    });

    if (!response.ok) {
      throw new Error(`API Error: ${response.statusText}`);
    }

    const result: ApiResponse<T> = await response.json();
    return result.data;
  }

  async put<T, D>(endpoint: string, data: D): Promise<T> {
    const response = await fetch(`${this.config.baseURL}${endpoint}`, {
      method: 'PUT',
      headers: this.config.headers,
      body: JSON.stringify(data)
    });

    if (!response.ok) {
      throw new Error(`API Error: ${response.statusText}`);
    }

    const result: ApiResponse<T> = await response.json();
    return result.data;
  }

  async delete<T>(endpoint: string): Promise<T> {
    const response = await fetch(`${this.config.baseURL}${endpoint}`, {
      method: 'DELETE',
      headers: this.config.headers
    });

    if (!response.ok) {
      throw new Error(`API Error: ${response.statusText}`);
    }

    const result: ApiResponse<T> = await response.json();
    return result.data;
  }
}

export default new ApiService('https://api.example.com');
```

``` vue
<!-- Компонент, использующий API -->
<template>
  <div>
    <h2>Список постов</h2>

    <div v-if="loading" class="spinner">
      Загрузка...
    </div>

    <div v-else-if="error" class="error">
      Ошибка: {{ error.message }}
      <button @click="loadPosts">Повторить</button>
    </div>

    <div v-else>
      <div v-for="post in posts" :key="post.id" class="post">
        <h3>{{ post.title }}</h3>
        <p>{{ post.body }}</p>
        <small>Автор: {{ post.userId }}</small>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import api from '../services/api';

interface Post {
  id: number;
  userId: number;
  title: string;
  body: string;
}

const posts = ref<Post[]>([]);
const loading = ref<boolean>(false);
const error = ref<Error | null>(null);

const loadPosts = async (): Promise<void> => {
  loading.value = true;
  error.value = null;

  try {
    posts.value = await api.get<Post[]>('/posts');
  } catch (err) {
    error.value = err instanceof Error ? err : new Error(String(err));
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  loadPosts();
});
</script>

<style scoped>
.spinner {
  text-align: center;
  padding: 20px;
  color: #666;
}

.error {
  background-color: #fee;
  color: #c33;
  padding: 15px;
  border-radius: 4px;
  margin-bottom: 15px;
}

.error button {
  margin-top: 10px;
  padding: 8px 16px;
  background-color: #c33;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.post {
  border: 1px solid #ddd;
  padding: 15px;
  margin-bottom: 15px;
  border-radius: 4px;
}

.post h3 {
  margin: 0 0 10px 0;
}

.post p {
  margin: 0 0 10px 0;
  color: #666;
}
</style>
```

##### 🔸 Pinia Store с TypeScript
``` typescript
// src/stores/userStore.ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export interface User {
  id: number;
  name: string;
  email: string;
  role: 'user' | 'admin' | 'moderator';
}

export const useUserStore = defineStore('user', () => {
  const users = ref<User[]>([
    { id: 1, name: 'Иван', email: 'ivan@example.com', role: 'user' },
    { id: 2, name: 'Мария', email: 'maria@example.com', role: 'admin' }
  ]);

  const currentUser = ref<User | null>(null);

  // Вычисляемые свойства
  const totalUsers = computed<number>(() => users.value.length);
  const adminCount = computed<number>(() =>
    users.value.filter(u => u.role === 'admin').length
  );

  // Методы
  const addUser = (user: Omit<User, 'id'>): void => {
    const newId = Math.max(...users.value.map(u => u.id), 0) + 1;
    users.value.push({ ...user, id: newId });
  };

  const removeUser = (id: number): void => {
    users.value = users.value.filter(u => u.id !== id);
  };

  const setCurrentUser = (user: User): void => {
    currentUser.value = user;
  };

  const updateUser = (id: number, updates: Partial<User>): void => {
    const user = users.value.find(u => u.id === id);
    if (user) {
      Object.assign(user, updates);
    }
  };

  return {
    users,
    currentUser,
    totalUsers,
    adminCount,
    addUser,
    removeUser,
    setCurrentUser,
    updateUser
  };
});
```

``` vue
<!-- Использование Pinia Store -->
<template>
  <div>
    <h2>Пользователи ({{ totalUsers }})</h2>
    <p>Администраторов: {{ adminCount }}</p>

    <div class="add-user">
      <input v-model="newUser.name" placeholder="Имя" />
      <input v-model="newUser.email" placeholder="Email" />
      <select v-model="newUser.role">
        <option value="user">Пользователь</option>
        <option value="admin">Администратор</option>
        <option value="moderator">Модератор</option>
      </select>
      <button @click="addNewUser">Добавить</button>
    </div>

    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Имя</th>
          <th>Email</th>
          <th>Роль</th>
          <th>Действия</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.name }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.role }}</td>
          <td>
            <button @click="removeUser(user.id)">Удалить</button>
            <button @click="setCurrentUser(user)">Выбрать</button>
          </td>
        </tr>
      </tbody>
    </table>

    <div v-if="currentUser" class="current-user">
      <h3>Текущий пользователь</h3>
      <p>{{ currentUser.name }} ({{ currentUser.email }})</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue';
import { useUserStore } from '../stores/userStore';
import type { User } from '../stores/userStore';

const userStore = useUserStore();
const { users, currentUser, totalUsers, adminCount, addUser, removeUser, setCurrentUser } =
  userStore;

const newUser = reactive<Omit<User, 'id'>>({
  name: '',
  email: '',
  role: 'user'
});

const addNewUser = (): void => {
  if (newUser.name && newUser.email) {
    addUser(newUser);
    newUser.name = '';
    newUser.email = '';
    newUser.role = 'user';
  }
};
</script>

<style scoped>
.add-user {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

input,
select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

button {
  padding: 8px 16px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

th,
td {
  border: 1px solid #ddd;
  padding: 12px;
  text-align: left;
}

th {
  background-color: #f5f5f5;
  font-weight: bold;
}

.current-user {
  background-color: #e7f3ff;
  padding: 15px;
  border-radius: 4px;
  margin-top: 20px;
}
</style>
```

##### 🔸 Утилиты для работы с типами
``` typescript
// Частичное обновление
type Partial<T> = {
  [K in keyof T]?: T[K];
};

// Все свойства становятся обязательными
type Required<T> = {
  [K in keyof T]-?: T[K];
};

// Все свойства становятся только для чтения
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

// Выбрать определенные свойства
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

// Исключить определенные свойства
type Omit<T, K extends keyof T> = {
  [P in Exclude<keyof T, K>]: T[P];
};

// Запись (объект с ключами типа K и значениями типа T)
type Record<K extends string | number | symbol, T> = {
  [P in K]: T;
};

// Практический пример
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Только id и name
type UserPreview = Pick<User, 'id' | 'name'>;

// Все кроме password
type UserPublic = Omit<User, 'age'>;

// Возможное частичное обновление
type UserUpdate = Partial<User>;

// Словарь пользователей
type UserDictionary = Record<number, User>;
```

##### 🔸 Тестирование с TypeScript
``` typescript
// src/__tests__/composables.test.ts
import { describe, it, expect } from 'vitest';
import { useCounter } from '../composables/useCounter';

describe('useCounter', () => {
  it('должен инициализировать счетчик', () => {
    const { count } = useCounter(0);
    expect(count.value).toBe(0);
  });

  it('должен увеличивать счетчик', () => {
    const { count, increment } = useCounter(0);
    increment();
    expect(count.value).toBe(1);
  });

  it('должен вычислять удвоенное значение', () => {
    const { count, increment, doubled } = useCounter(5);
    expect(doubled.value).toBe(10);
    increment();
    expect(doubled.value).toBe(12);
  });

  it('должен определять четность', () => {
    const { count, isEven, increment } = useCounter(0);
    expect(isEven.value).toBe(true);
    increment();
    expect(isEven.value).toBe(false);
  });

  it('должен сбрасывать счетчик', () => {
    const { count, increment, reset } = useCounter(10);
    increment();
    increment();
    expect(count.value).toBe(12);
    reset();
    expect(count.value).toBe(10);
  });
});
```

##### 🔸 📚 Лучшие практики

- **Избегайте any** — используйте unknown или конкретные типы
- **Используйте интерфейсы для контрактов** — для определения структуры данных
- **Типизируйте Props и Emits** — для лучшей поддержки IDE
- **Создавайте типы для API ответов** — для безопасности данных
- **Используйте Generics** — для переиспользуемого кода
- **Включите strict mode** — в tsconfig.json для максимальной безопасности
``` json
{
  "compilerOptions": {
    "strict": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitAny": true,
    "noImplicitThis": true,
    "alwaysStrict": true
  }
}
```
