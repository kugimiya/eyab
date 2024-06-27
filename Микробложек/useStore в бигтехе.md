Это настолько простая вещь, что даже объяснять нечего. Для контекста: состояние приложения храним в [[MobX]]-сторах ([[Store State]]), приложение использует React.

Для примера, давайте приведём код [[React]]-странички, выводящей текущее время (оно хранится в MobX-сторе):

```tsx
import { observer } from 'mobx-react-lite';
import React from 'react';

import { useStore } from '@hooks/useStore';

export const TimePage = observer(() => {
  const { timeStore } = useStore();
  
  return (
    <div>Текущее время: {timeStore.formatted}</div>
  );
});
```

**Обратите внимание**, мы вызываем хук [[useStore]]: он возвращает нам инстанс корневого стора. О нём чуть позже.

Далее, приведу код нашей MobX-сторы:

```tsx
import { makeAutoObservable } from 'mobx';
import { RootStore } from '@stores/RootStore';

export class TimeStore {
  currentTime = new Date();

  constructor(private rootStore: RootStore) {
    makeAutoObservable(this);
  }
  
  get formatted(): string {
    return this.currentTime.toString();
  }
}
```

**Обратите внимание**, мы передаём в конструкторе инстанс корневого стора: это позволит нам иметь аналог useStore внутри нашей сторы. Мы передаём его как private field, что позволит ему сразу попасть в поля класса: доступ к другим сторам будет выглядеть так: `this.rootStore.otherStore.foobar()`

Далее, приведу код реализации RootStore:

```tsx
import { TimeStore } from '@stores/TimeStore';

export class RootStore {
  timeStore: TimeStore;
  
  constructor() {
    this.timeStore = new TimeStore(this);
  }
}
```

**Обратите внимание**, здесь мы создаём все наши сторы, и передаём в них инстанс корневой сторы как `this`

Ну и конечная вишенка на торте, реализация хука useStore:

```tsx
import { useLocalObservable } from 'mobx-react-lite';
import React, { ReactNode } from 'react';

import RootStore from './RootStore';

const storeContext = React.createContext<RootStore | undefined>(undefined);

export const StoreProvider = ({ children }: { children: ReactNode }): JSX.Element => {
  const store = useLocalObservable(() => new RootStore());
  return <storeContext.Provider value={store}>{children}</storeContext.Provider>;
};

export const useStore = (): RootStore => {
  const store = React.useContext(storeContext);

  if (!store) {
    throw new Error('useStore must be used within a StoreProvider.');
  }

  return store;
};
```

**Обратите внимание**, вместе с реализацией хука здесь присутствует создание React-контекста для этого хука. Я уверен, что не нужно объяснять зачем нужен контекст.

Ну и совсем уже конечный листинг кода, собирающий всё воедино. Допустим, у вас есть app.tsx, в котором перечислены все странички завернутые в роутер. Давайте такое приведём для примера:

```tsx
import { render } from 'react-dom';

import {
  createBrowserRouter, createRoutesFromElements,
  Route, RouterProvider
} from 'react-router-dom';

import { StoreProvider } from '@hooks/useStore';
import { TimePage } from '@pages/TimePage';
import { CommonLayout } from '@layouts/CommonLayout';

// Роутер
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path='/' element={<CommonLayout />} >
      <Route path='/time' element={<TimePage />} />
    </Route>  
  )
);

// Приложение
const App = () => (
  <StoreProvider>
    <RouterProvider router={router} />
  </StoreProvider>
);

render(<App />, document.getElementById('root'));
```