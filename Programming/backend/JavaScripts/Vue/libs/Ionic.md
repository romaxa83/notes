#js #vue #package #mobile

==Ionic== — это UI-фреймворк для создания кроссплатформенных мобильных приложений с использованием веб-технологий. Сочетание `Ionic + Vue 3` дает мощный инструмент для разработки.
- Работает на iOS, Android, веб (с одним кодом)
- Использует Capacitor для доступа к нативным APIs
- Предоставляет готовые UI-компоненты, оптимизированные для мобильных устройств
- Поддерживает Vue 3, React, Angular, Svelte

#### 🔹 Установка и настройка проекта

```bash
node --version
npm --version

# Глобальная установка Ionic CLI
npm install -g @ionic/cli
ionic --version

# или устанавливаем локально и тогда команды нужно начинать с приставки npx
npm install @ionic/cli --save-dev

# Создание нового проекта Ionic + Vue 3
ionic start my-app tabs --type vue
# Варианты шаблонов:
# - blank (пустой проект)
# - tabs (навигация через вкладки)
# - sidemenu (боковое меню)

cd my-app

# Установка Capacitor (для доступа к нативным APIs, типа камера, уведлмлеия и ...)
npm install @capacitor/core @capacitor/cli
npx cap init
```

 ==Запуск приложения==
```bash
# В браузере
npm run dev

# На устройстве iOS
npm run build
npx cap add ios
npx cap open ios

# На устройстве Android
npm run build
npx cap add android
npx cap open android
```

==Структура проекта Ionic + Vue 3==
```
my-app/
├── src/
│   ├── components/          # Переиспользуемые компоненты
│   ├── views/              # Страницы приложения
│   ├── App.vue             # Главный компонент
│   ├── main.ts             # Точка входа
│   └── router/
│       └── index.ts        # Настройка маршрутизации
├── public/                 # Статические файлы
├── android/                # Нативный код Android (после cap add)
├── ios/                    # Нативный код iOS (после cap add)
├── capacitor.config.ts     # Конфиг Capacitor
├── ionic.config.json       # Конфиг Ionic
└── vite.config.ts          # Конфиг Vite
```
---
#### 🔹 Основные компоненты Ionic

##### 🔸 IonApp — корневой контейнер
```vue
<template>
  <ion-app>
    <ion-router-outlet />
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp, IonRouterOutlet } from '@ionic/vue';
</script>
```

##### 🔸 IonHeader и IonContent — структура страницы
```vue
<template>
  <ion-page>
    <!-- Шапка с кнопками и заголовком -->
    <ion-header>
      <ion-toolbar>
        <ion-title>Моё приложение</ion-title>
        <ion-buttons slot="end">
          <ion-button>
            <ion-icon icon="menu"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <!-- Основное содержимое -->
    <ion-content>
      <h1>Добро пожаловать!</h1>
      <p>Это содержимое страницы</p>
    </ion-content>

    <!-- Нижняя шапка (опционально) -->
    <ion-footer>
      <ion-toolbar>
        <ion-text>© 2024 Мое приложение</ion-text>
      </ion-toolbar>
    </ion-footer>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonFooter,
  IonButtons,
  IonButton,
  IonIcon,
  IonText
} from '@ionic/vue';
</script>
```

##### 🔸 IonButton — кнопки
```vue
<template>
  <!-- Различные стили кнопок -->
  <ion-button>Обычная кнопка</ion-button>
  <ion-button color="primary">Первичная</ion-button>
  <ion-button color="success">Успех</ion-button>
  <ion-button color="danger">Опасно</ion-button>
  <ion-button color="warning">Предупреждение</ion-button>
  <ion-button color="dark">Темная</ion-button>

  <!-- Варианты отображения -->
  <ion-button fill="solid">Solid</ion-button>
  <ion-button fill="outline">Outline</ion-button>
  <ion-button fill="clear">Clear</ion-button>

  <!-- Размеры -->
  <ion-button size="large">Большая</ion-button>
  <ion-button size="small">Маленькая</ion-button>

  <!-- С иконкой -->
  <ion-button>
    <ion-icon slot="start" icon="heart"></ion-icon>
    Нравится
  </ion-button>

  <!-- Отключенная кнопка -->
  <ion-button disabled>Отключено</ion-button>

  <!-- Развернутая кнопка -->
  <ion-button expand="block">На всю ширину</ion-button>
</template>

<script setup lang="ts">
import { IonButton, IonIcon } from '@ionic/vue';
</script>
```

##### 🔸 IonList и IonItem — списки
```vue
<template>
  <ion-list>
    <ion-item>
      <ion-label>Простой элемент</ion-label>
    </ion-item>

    <!-- С деталями -->
    <ion-item detail>
      <ion-label>
        <h2>Заголовок</h2>
        <p>Описание</p>
      </ion-label>
    </ion-item>

    <!-- С иконкой -->
    <ion-item>
      <ion-icon slot="start" icon="person"></ion-icon>
      <ion-label>Профиль</ion-label>
    </ion-item>

    <!-- С переключателем -->
    <ion-item>
      <ion-label>Включить уведомления</ion-label>
      <ion-toggle slot="end"></ion-toggle>
    </ion-item>

    <!-- С кнопкой удаления (sliding item) -->
    <ion-item-sliding>
      <ion-item>
        <ion-label>Элемент списка</ion-label>
      </ion-item>
      <ion-item-options side="end">
        <ion-item-option color="danger">Удалить</ion-item-option>
      </ion-item-options>
    </ion-item-sliding>
  </ion-list>
</template>

<script setup lang="ts">
import {
  IonList,
  IonItem,
  IonLabel,
  IonIcon,
  IonToggle,
  IonItemSliding,
  IonItemOptions,
  IonItemOption
} from '@ionic/vue';
</script>
```

##### 🔸 IonInput и IonTextarea — поля ввода
```vue
<template>
  <ion-item>
    <ion-label position="floating">Email</ion-label>
    <ion-input
      v-model="email"
      type="email"
      placeholder="you@example.com"
    ></ion-input>
  </ion-item>

  <ion-item>
    <ion-label position="floating">Пароль</ion-label>
    <ion-input
      v-model="password"
      type="password"
      placeholder="Введите пароль"
    ></ion-input>
  </ion-item>

  <ion-item>
    <ion-label position="floating">Комментарий</ion-label>
    <ion-textarea
      v-model="comment"
      placeholder="Напишите комментарий"
      rows="4"
    ></ion-textarea>
  </ion-item>

  <!-- Select (выпадающий список) -->
  <ion-item>
    <ion-label position="floating">Страна</ion-label>
    <ion-select v-model="selectedCountry">
      <ion-select-option value="ru">Россия</ion-select-option>
      <ion-select-option value="us">США</ion-select-option>
      <ion-select-option value="uk">Великобритания</ion-select-option>
    </ion-select>
  </ion-item>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import {
  IonItem,
  IonLabel,
  IonInput,
  IonTextarea,
  IonSelect,
  IonSelectOption
} from '@ionic/vue';

const email = ref('');
const password = ref('');
const comment = ref('');
const selectedCountry = ref('ru');
</script>
```

##### 🔸 IonTabs — вкладки навигации
```vue
<template>
  <ion-tabs>
    <ion-tab-bar slot="bottom">
      <!-- Вкладка 1 -->
      <ion-tab-button tab="home" href="/tabs/home">
        <ion-icon icon="home"></ion-icon>
        <ion-label>Главная</ion-label>
      </ion-tab-button>

      <!-- Вкладка 2 -->
      <ion-tab-button tab="search" href="/tabs/search">
        <ion-icon icon="search"></ion-icon>
        <ion-label>Поиск</ion-label>
      </ion-tab-button>

      <!-- Вкладка 3 -->
      <ion-tab-button tab="profile" href="/tabs/profile">
        <ion-icon icon="person"></ion-icon>
        <ion-label>Профиль</ion-label>
      </ion-tab-button>
    </ion-tab-bar>

    <!-- Содержимое вкладок -->
    <ion-router-outlet></ion-router-outlet>
  </ion-tabs>
</template>

<script setup lang="ts">
import {
  IonTabs,
  IonTabBar,
  IonTabButton,
  IonIcon,
  IonLabel,
  IonRouterOutlet
} from '@ionic/vue';
</script>
```

##### 🔸 IonModal — модальное окно
```vue
<template>
  <ion-button @click="isModalOpen = true">Открыть модаль</ion-button>

  <ion-modal :is-open="isModalOpen" @did-dismiss="isModalOpen = false">
    <ion-header>
      <ion-toolbar>
        <ion-title>Модальное окно</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="isModalOpen = false">Закрыть</ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <p>Содержимое модального окна</p>
    </ion-content>
  </ion-modal>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import {
  IonButton,
  IonModal,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonButtons,
  IonContent
} from '@ionic/vue';

const isModalOpen = ref(false);
</script>
```

##### 🔸 IonAlert и IonToast — уведомления
```vue
<template>
  <!-- Alert диалог -->
  <ion-button @click="showAlert = true">Показать Alert</ion-button>
  <ion-alert
    :is-open="showAlert"
    header="Подтверждение"
    message="Вы уверены?"
    :buttons="[
      { text: 'Отмена', role: 'cancel' },
      { text: 'Удалить', role: 'destructive' }
    ]"
    @did-dismiss="showAlert = false"
  ></ion-alert>

  <!-- Toast уведомление -->
  <ion-button @click="showToast">Показать уведомление</ion-button>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { toastController, IonButton, IonAlert } from '@ionic/vue';

const showAlert = ref(false);

const showToast = async () => {
  const toast = await toastController.create({
    message: 'Операция успешно завершена!',
    duration: 2000,
    position: 'bottom',
    color: 'success'
  });
  await toast.present();
};
</script>
```

##### 🔸 IonSpinner — загрузка
```vue
<template>
  <!-- Различные типы спиннеров -->
  <ion-spinner name="circular"></ion-spinner>
  <ion-spinner name="crescent"></ion-spinner>
  <ion-spinner name="dots"></ion-spinner>
  <ion-spinner name="lines"></ion-spinner>

  <!-- С кастомным цветом -->
  <ion-spinner name="bubbles" color="primary"></ion-spinner>
</template>

<script setup lang="ts">
import { IonSpinner } from '@ionic/vue';
</script>
```
---
#### 🔹 Маршрутизация (Routing)
##### 🔸 Настройка роутера
```ts
// src/router/index.ts
import { createRouter, createWebHistory } from '@ionic/vue-router';
import { RouteRecordRaw } from 'vue-router';
import Tabs from '../views/Tabs.vue';
import Home from '../views/Home.vue';
import Search from '../views/Search.vue';
import Profile from '../views/Profile.vue';
import Details from '../views/Details.vue';

const routes: Array<RouteRecordRaw> = [
  {
    path: '/',
    redirect: '/tabs/home'
  },
  {
    path: '/tabs/',
    component: Tabs,
    children: [
      {
        path: 'home',
        component: Home
      },
      {
        path: 'search',
        component: Search
      },
      {
        path: 'profile',
        component: Profile
      }
    ]
  },
  {
    path: '/details/:id',
    component: Details
  }
];

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes
});

export default router;
```

##### 🔸 Навигация в компонентах
```vue
<template>
  <!-- Путем к маршруту -->
  <ion-button router-link="/tabs/profile">Профиль</ion-button>

  <!-- С параметрами -->
  <ion-button :router-link="`/details/${id}`">Детали</ion-button>

  <!-- Программная навигация -->
  <ion-button @click="goToProfile">Перейти в профиль</ion-button>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router';
import { IonButton } from '@ionic/vue';

const router = useRouter();
const id = 123;

const goToProfile = () => {
  router.push('/tabs/profile');
};
</script>
```

##### 🔸 Получение параметров маршрута
```vue
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Детали товара #{{ productId }}</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <p>ID товара: {{ productId }}</p>
      <p>Категория: {{ category }}</p>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { useRoute } from 'vue-router';
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/vue';

const route = useRoute();

const productId = route.params.id;
const category = route.query.category;
</script>
```
---
#### 🔹 Capacitor: Доступ к нативным возможностям
##### 🔸 Камера

``` bash
npm install @capacitor/camera
npx cap sync
```

```vue
<template>
  <ion-button @click="takePhoto">Сфотографировать</ion-button>
  <img v-if="photo" :src="photo" />
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { Camera, CameraResultType } from '@capacitor/camera';
import { IonButton } from '@ionic/vue';

const photo = ref('');

const takePhoto = async () => {
  const image = await Camera.getPhoto({
    quality: 90,
    allowEditing: true,
    resultType: CameraResultType.Uri
  });

  photo.value = image.webPath || '';
};
</script>
```

##### 🔸 Геолокация
``` bash
npm install @capacitor/geolocation
npx cap sync
```

```vue
<template>
  <ion-button @click="getLocation">Получить координаты</ion-button>
  <div v-if="location">
    <p>Широта: {{ location.latitude }}</p>
    <p>Долгота: {{ location.longitude }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { Geolocation } from '@capacitor/geolocation';
import { IonButton } from '@ionic/vue';

const location = ref<any>(null);

const getLocation = async () => {
  try {
    const coordinates = await Geolocation.getCurrentPosition();
    location.value = coordinates.coords;
  } catch (error) {
    console.error('Ошибка при получении координат:', error);
  }
};
</script>
```

##### 🔸 Работа с файлами
```bash
npm install @capacitor/filesystem
npx cap sync
```

```vue
<template>
  <ion-button @click="saveFile">Сохранить файл</ion-button>
  <ion-button @click="readFile">Прочитать файл</ion-button>
  <p v-if="fileContent">{{ fileContent }}</p>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { Filesystem, Directory, Encoding } from '@capacitor/filesystem';
import { IonButton } from '@ionic/vue';

const fileContent = ref('');

const saveFile = async () => {
  await Filesystem.writeFile({
    path: 'test.txt',
    data: 'Привет, мир!',
    directory: Directory.Documents,
    encoding: Encoding.UTF8
  });
  console.log('Файл сохранен');
};

const readFile = async () => {
  const result = await Filesystem.readFile({
    path: 'test.txt',
    directory: Directory.Documents,
    encoding: Encoding.UTF8
  });
  fileContent.value = result.data as string;
};
</script>
```

##### 🔸 Локальное хранилище
```bash
npm install @capacitor/preferences
npx cap sync
```

```vue
<template>
  <ion-item>
    <ion-label>Ваше имя</ion-label>
    <ion-input v-model="name"></ion-input>
  </ion-item>
  <ion-button @click="saveName">Сохранить</ion-button>
  <ion-button @click="loadName">Загрузить</ion-button>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { Preferences } from '@capacitor/preferences';
import { IonItem, IonLabel, IonInput, IonButton } from '@ionic/vue';

const name = ref('');

const saveName = async () => {
  await Preferences.set({
    key: 'userName',
    value: name.value
  });
};

const loadName = async () => {
  const { value } = await Preferences.get({ key: 'userName' });
  name.value = value || '';
};

onMounted(async () => {
  await loadName();
});
</script>
```

##### 🔸 Уведомления (Push)
```bash
npm install @capacitor/push-notifications
npx cap sync
```

```vue
<script setup lang="ts">
import { onMounted } from 'vue';
import { PushNotifications } from '@capacitor/push-notifications';
import { Toast } from '@capacitor/toast';

onMounted(async () => {
  // Запрос разрешения на уведомления
  await PushNotifications.requestPermissions();

  // Регистрация
  await PushNotifications.register();

  // Слушаем входящие уведомления
  PushNotifications.addListener(
    'pushNotificationReceived',
    (notification) => {
      console.log('Уведомление получено:', notification);
      Toast.show({
        text: notification.title || 'Новое уведомление'
      });
    }
  );
});
</script>
```
---
#### 🔹 Сборка и развертывание

##### 🔸 Сборка для продакшена
``` bash
# Веб-версия
npm run build

# iOS
npm run build
npx cap add ios
npx cap copy
npx cap open ios
# Затем в Xcode: Product → Archive → Distribute App

# Android
npm run build
npx cap add android
npx cap copy
npx cap open android
# Затем в Android Studio: Build → Build Bundle(s) / APK(s)
```

##### 🔸 Подготовка к публикации
``` bash
# Проверить конфиг
ionic info

# Обновить зависимости
npm update

# Построить оптимизированную версию
npm run build -- --mode production

# Проверить размер бандла
npm run build -- --analyze
```
---
#### 🔹 Темизация и кастомизация
##### 🔸 Кастомные переменные CSS
```css
/* src/theme/variables.css */
:root {
  --ion-color-primary: #3880ff;
  --ion-color-primary-rgb: 56, 128, 255;
  --ion-color-primary-contrast: #ffffff;
  --ion-color-primary-contrast-rgb: 255, 255, 255;
  --ion-color-primary-shade: #3171e0;
  --ion-color-primary-tint: #4c8dff;

  --ion-font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue",
    sans-serif;

  --ion-padding: 16px;
  --ion-margin: 16px;
}

@media (prefers-color-scheme: dark) {
  :root {
    --ion-color-primary: #3a96ff;
  }
}
```
---
#### 🔹 Оптимизация производительности
##### 🔸 Ленивая загрузка маршрутов
``` typescript
// src/router/index.ts
{
  path: '/details/:id',
  component: () => import('../views/Details.vue')
}
```

##### 🔸 Виртуализация списков
```vue
<template>
  <ion-virtual-scroll :items="largeList">
    <ion-item v-for="item in largeList" :key="item.id">
      {{ item.name }}
    </ion-item>
  </ion-virtual-scroll>
</template>

<script setup lang="ts">
import { IonVirtualScroll, IonItem } from '@ionic/vue';
</script>
```
---
#### 🔹 📚 Полезные ресурсы

- Официальная документация: https://ionicframework.com/docs
- Capacitor плагины: https://capacitorjs.com/docs/plugins
- Иконки: https://ionicons.com/
- Примеры проектов: https://github.com/ionic-team/ionic-framework
- Форум сообщества: https://forum.ionicframework.com/
 ---

