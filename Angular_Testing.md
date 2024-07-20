# Angular Testing

> Загалом логіка написання тестів полягає в тому, щоб тестувати не `return value` а поведінку, логіку коду.

`describe('component name or service name', () => {})` - основний метод і секція де ми описуємо тест. Також цей метод можна робити вкладеним по відношенню до самого себе.

`it('name of a testing action', () => {})` - блок, де ми описуємо, які дії повинні бути виконані і їх результат виконання

`utils.ts`

```typescript
export const range = (start: number, end: number): number[] => {
  return [...Array(end - start).keys()].map((el) => el + start);
};

export const pluck = (elements: any[], field: string) => {
  return elements.map((el) => el[field]);
};
```

`utils.spec.ts`

```typescript
import { pluck, range } from './utils';

describe('utils', () => {
  describe('range', () => {
    it('returns correct range from 1 to 5', () => {
      expect(range(1, 5)).toEqual([1, 2, 3, 4]);
    });
    it('returns correct range from 41 to 44', () => {
      expect(range(41, 44)).toEqual([41, 42, 43]);
    });
  });

  describe('pluck', () => {
    it('returns correct result', () => {
      const data = [
        { id: '1', name: 'foo' },
        { id: '2', name: 'bar' },
        { id: '3', name: 'baz' },
      ];
      expect(pluck(data, 'id')).toEqual(['1', '2', '3']);
    });
  });
});
```

`beforeEach(() => {})` - блок, який виконується перед кожним тестом

## Mock and Spy Angular deps

- Підхід з використанням `mock` сервісу, який є залежністю сервісу, який ми тестуємо

```typescript
describe('UsersService', () => {
  let usersService: UsersService;
  const UtilsServiceMock = {
    pluck: jest.fn(), //мок метода
  }; //створення моку

  beforeEach(() => {
    //створення тестового модуля
    TestBed.configureTestingModule({
      providers: [
        UsersService,
        {
          provide: UtilsService,
          useValue: UtilsServiceMock, //передаємо мок для тестування UsersService в ізоляції замість надання реального екземпляру UtilsService
        },
      ],
    });
    //додавання в модуль сервісу
    usersService = TestBed.inject(UsersService);
  });

  describe('getUserNames', () => {
    it('should return user names', () => {
      UtilsServiceMock.pluck.mockReturnValue(['foo']); //мок значення, яке метод повертає
      expect(usersService.getUserNames()).toEqual(['foo']);
    });
  });
});
```

- Підхід з використанням `spy`, де використовується справжній екземпляр сервісу, метод якого ми будемо відслідковувати

```typescript
describe('UsersService', () => {
  let usersService: UsersService;
  let utilsService: UtilsService;

  beforeEach(() => {
    //створення тестового модуля
    TestBed.configureTestingModule({
      providers: [UsersService, UtilsService],
    });
    //додавання в модуль сервісу
    usersService = TestBed.inject(UsersService);
    utilsService = TestBed.inject(UtilsService);
  });

  it('creates a service', () => {
    //перевірка чи сервіс існує
    expect(usersService).toBeTruthy();
  });

  describe('getUserNames', () => {
    it('should return user names', () => {
      jest.spyOn(utilsService, 'pluck');
      usersService.users = [{ id: '3', name: 'foo' }];
      usersService.getUserNames();
      expect(utilsService.pluck).toHaveBeenCalledWith(usersService.users, 'name');
    });
  });
});
```
