Приложение TODO

##### 🔸 Структура проекта
``` 
src/
├── views/
│   ├── Tabs.vue          # Основной макет с вкладками
│   ├── TodoList.vue      # Список дел
│   ├── AddTodo.vue       # Добавление задачи
│   └── Settings.vue      # Настройки
├── components/
│   └── TodoItem.vue      # Компонент элемента списка
└── stores/
    └── todoStore.ts      # Pinia Store для состояния
```

##### 🔸 TodoStore (Pinia)
```typescript
// src/stores/todoStore.ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';
import { Preferences } from '@capacitor/preferences';

export interface Todo {
  id: number;
  title: string;
  description: string;
  completed: boolean;
  createdAt: string;
}

export const useTodoStore = defineStore('todo', () => {
  const todos = ref<Todo[]>([]);
  const nextId = ref(1);

  // Загрузить из хранилища при инициализации
  const loadTodos = async () => {
    const { value } = await Preferences.get({ key: 'todos' });
    if (value) {
      todos.value = JSON.parse(value);
      nextId.value = Math.max(...todos.value.map(t => t.id), 0) + 1;
    }
  };

  // Сохранить в хранилище
  const saveTodos = async () => {
    await Preferences.set({
      key: 'todos',
      value: JSON.stringify(todos.value)
    });
  };

  // Добавить задачу
  const addTodo = async (title: string, description: string = '') => {
    const newTodo: Todo = {
      id: nextId.value++,
      title,
      description,
      completed: false,
      createdAt: new Date().toISOString()
    };
    todos.value.unshift(newTodo);
    await saveTodos();
  };

  // Удалить задачу
  const deleteTodo = async (id: number) => {
    todos.value = todos.value.filter(todo => todo.id !== id);
    await saveTodos();
  };

  // Переключить завершение
  const toggleTodo = async (id: number) => {
    const todo = todos.value.find(t => t.id === id);
    if (todo) {
      todo.completed = !todo.completed;
      await saveTodos();
    }
  };

  // Вычисляемые свойства
  const completedCount = computed(() =>
    todos.value.filter(t => t.completed).length
  );

  const pendingCount = computed(() =>
    todos.value.filter(t => !t.completed).length
  );

  return {
    todos,
    loadTodos,
    addTodo,
    deleteTodo,
    toggleTodo,
    completedCount,
    pendingCount
  };
});
```

##### 🔸 TodoItem компонент
``` vue
<!-- src/components/TodoItem.vue -->
<template>
  <ion-item-sliding>
    <ion-item>
      <ion-checkbox
        slot="start"
        :model-value="todo.completed"
        @update:model-value="toggleTodo"
      ></ion-checkbox>
      <ion-label>
        <h2 :class="{ completed: todo.completed }">{{ todo.title }}</h2>
        <p v-if="todo.description">{{ todo.description }}</p>
        <p class="date">{{ formatDate(todo.createdAt) }}</p>
      </ion-label>
    </ion-item>
    <ion-item-options side="end">
      <ion-item-option color="danger" @click="deleteTodo">
        Удалить
      </ion-item-option>
    </ion-item-options>
  </ion-item-sliding>
</template>

<script setup lang="ts">
import { useTodoStore } from '../stores/todoStore';
import { IonItemSliding, IonItem, IonCheckbox, IonLabel, IonItemOptions, IonItemOption } from '@ionic/vue';
import type { Todo } from '../stores/todoStore';

defineProps<{
  todo: Todo;
}>();

const todoStore = useTodoStore();

const toggleTodo = () => {
  todoStore.toggleTodo(props.todo.id);
};

const deleteTodo = () => {
  todoStore.deleteTodo(props.todo.id);
};

const formatDate = (date: string) => {
  return new Date(date).toLocaleDateString('ru-RU');
};
</script>

<style scoped>
.completed {
  text-decoration: line-through;
  opacity: 0.6;
}

.date {
  font-size: 0.8em;
  color: #888;
}
</style>
```

##### 🔸 TodoList вид
``` vue
<!-- src/views/TodoList.vue -->
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Мои дела</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="openModal">
            <ion-icon icon="add"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <ion-card v-if="todoStore.todos.length > 0">
        <ion-card-header>
          <ion-card-title>
            Всего: {{ todoStore.todos.length }} | 
            Выполнено: {{ todoStore.completedCount }} |
            Осталось: {{ todoStore.pendingCount }}
          </ion-card-title>
        </ion-card-header>
      </ion-card>

      <ion-list v-if="todoStore.todos.length > 0">
        <todo-item
          v-for="todo in todoStore.todos"
          :key="todo.id"
          :todo="todo"
        />
      </ion-list>

      <ion-card v-else>
        <ion-card-header>
          <ion-card-title>Нет задач</ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <p>Нажмите кнопку +, чтобы добавить новую задачу</p>
        </ion-card-content>
      </ion-card>

      <!-- Модальное окно для добавления -->
      <ion-modal :is-open="isModalOpen" @did-dismiss="isModalOpen = false">
        <ion-header>
          <ion-toolbar>
            <ion-title>Новая задача</ion-title>
            <ion-buttons slot="end">
              <ion-button @click="isModalOpen = false">Отмена</ion-button>
            </ion-buttons>
          </ion-toolbar>
        </ion-header>
        <ion-content>
          <ion-item>
            <ion-label position="floating">Название</ion-label>
            <ion-input v-model="newTodoTitle"></ion-input>
          </ion-item>
          <ion-item>
            <ion-label position="floating">Описание</ion-label>
            <ion-textarea v-model="newTodoDesc"></ion-textarea>
          </ion-item>
          <ion-button
            expand="block"
            color="primary"
            @click="addNewTodo"
          >
            Добавить
          </ion-button>
        </ion-content>
      </ion-modal>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useTodoStore } from '../stores/todoStore';
import TodoItem from '../components/TodoItem.vue';
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonButtons,
  IonButton,
  IonIcon,
  IonList,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardContent,
  IonModal,
  IonItem,
  IonLabel,
  IonInput,
  IonTextarea
} from '@ionic/vue';

const todoStore = useTodoStore();
const isModalOpen = ref(false);
const newTodoTitle = ref('');
const newTodoDesc = ref('');

onMounted(async () => {
  await todoStore.loadTodos();
});

const addNewTodo = async () => {
  if (newTodoTitle.value.trim()) {
    await todoStore.addTodo(newTodoTitle.value, newTodoDesc.value);
    newTodoTitle.value = '';
    newTodoDesc.value = '';
    isModalOpen.value = false;
  }
};

const openModal = () => {
  isModalOpen.value = true;
};
</script>
```

##### 🔸 Settings вид
``` vue
<!-- src/views/Settings.vue -->
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Настройки</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <ion-card>
        <ion-card-header>
          <ion-card-title>Уведомления</ion-card-title>
        </ion-card-header>
        <ion-item>
          <ion-label>Включить уведомления</ion-label>
          <ion-toggle slot="end" v-model="notificationsEnabled"></ion-toggle>
        </ion-item>
      </ion-card>

      <ion-card>
        <ion-card-header>
          <ion-card-title>О приложении</ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <p><strong>Todo App</strong></p>
          <p>Версия: 1.0.0</p>
          <p>© 2024 Все права защищены</p>
        </ion-card-content>
      </ion-card>

      <ion-button expand="block" color="danger" @click="clearAllData">
        Очистить все данные
      </ion-button>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { Preferences } from '@capacitor/preferences';
import { useTodoStore } from '../stores/todoStore';
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardContent,
  IonItem,
  IonLabel,
  IonToggle,
  IonButton,
  alertController
} from '@ionic/vue';

const notificationsEnabled = ref(true);
const todoStore = useTodoStore();

onMounted(async () => {
  const { value } = await Preferences.get({ key: 'notificationsEnabled' });
  notificationsEnabled.value = value !== 'false';
});

const clearAllData = async () => {
  const alert = await alertController.create({
    header: 'Очистить данные',
    message: 'Это действие нельзя отменить!',
    buttons: [
      { text: 'Отмена', role: 'cancel' },
      {
        text: 'Удалить',
        role: 'destructive',
        handler: async () => {
          await Preferences.clear();
          todoStore.todos = [];
        }
      }
    ]
  });
  await alert.present();
};
</script>
```

