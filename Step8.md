[Вернуться в основной файл](https://github.com/Yambai/OOP_L2/blob/main/README.md)
# **Шаг 8**

---  

# 🔥 Разница между объектами и указателями на базовый класс в C++

В языке C++ можно работать с классами двумя основными способами:
1. **Создавать объект в стеке** (без указателей):  
   ```cpp
   MyBase obj = MyDeriv();
   ```
   - Создаётся **объект типа `MyBase`**.
   - **Происходит "урезание объекта" (object slicing)** – теряются специфичные данные `MyDeriv`.
   - Вызов методов ограничен `MyBase`.

2. **Создавать объект в куче (heap) и работать через указатель:**  
   ```cpp
   MyBase* obj = new MyDeriv();
   ```
   - Объект создаётся в **куче (`heap`)**.
   - `obj` остаётся **указателем на `MyBase`**, но фактически управляет `MyDeriv`.
   - Если методы **виртуальные (`virtual`)**, вызовы методов происходят через **полиморфизм**.
   - Объект **нужно удалять вручную** (`delete obj;`).

---

## 🛠 Основы синтаксиса C++ (Объяснение `*`, `&`, `->`, `new`)

### 📌 1. Что такое `*` (звёздочка) в C++?  

В C++ символ `*` имеет **два основных значения**:

1. **Объявление указателя**  
   ```cpp
   int* ptr;
   ```
   - `ptr` – **указатель** на целое число (`int`).
   - В памяти он будет **хранить адрес** другого объекта.

2. **Разыменование указателя (доступ к значению, на которое он указывает)**  
   ```cpp
   int x = 5;
   int* ptr = &x; // ptr хранит адрес переменной x
   std::cout << *ptr; // Разыменовываем ptr -> получаем значение x (5)
   ```
   - `ptr` **содержит адрес** `x`, а `*ptr` **даёт доступ к значению**.

---

## 🔹 Разница между объектами и указателями на объекты

### ✅ **Создание объекта в стеке**
```cpp
MyBase obj;
```
- `obj` – **обычная переменная** типа `MyBase`, созданная в **стеке** (stack).
- Она автоматически удалится, когда выйдет из области видимости.

```cpp
MyBase obj = MyDeriv();
```
- `obj` – объект **типа `MyBase`**, а `MyDeriv()` – временный объект.
- **Происходит object slicing (урезание объекта)** – `obj` получает **только** базовую часть.

---

### ✅ **Создание объекта в куче (heap)**
```cpp
MyBase* obj = new MyDeriv();
```
- `MyBase*` – **указатель** на объект `MyBase`.
- `new MyDeriv();` создаёт **объект в куче**.
- Указатель `obj` остаётся типа `MyBase*`, но фактически управляет объектом `MyDeriv`.

> ❗ **Важно:** Всё, что создаётся через `new`, **нужно удалять вручную**:
```cpp
delete obj;
```

---

# 🔥 Разбираем примеры ПОШАГОВО  

## ✅ **Пример 1: Object Slicing (урезание объекта)**

```cpp
#include <iostream> // Подключаем библиотеку для ввода/вывода

class MyBase {
public:
    int baseValue;

    MyBase() : baseValue(10) {} // Конструктор

    void show() { // Метод (НЕ виртуальный!)
        std::cout << "MyBase: baseValue = " << baseValue << std::endl;
    }
};

class MyDeriv : public MyBase {
public:
    int derivedValue;

    MyDeriv() : derivedValue(20) {} // Конструктор

    void show() { // Переопределение метода (но не виртуального)
        std::cout << "MyDeriv: baseValue = " << baseValue 
                  << ", derivedValue = " << derivedValue << std::endl;
    }
};

int main() {
    MyDeriv derivedObj;    // Создаём объект производного класса
    MyBase baseObj = derivedObj; // Присваиваем его базовому объекту

    baseObj.show(); // Вызов метода (будет вызываться show() из MyBase!)

    return 0;
}
```

### 🔎 **Разбор кода**
1. **Объявляем класс `MyBase`**  
   - Содержит переменную `baseValue` (10) и метод `show()`.
   
2. **Объявляем класс `MyDeriv`, который наследует `MyBase`**  
   - Добавляет `derivedValue` (20).
   - Переопределяет метод `show()`.

3. **В `main()`**:
   - `MyBase baseObj = derivedObj;`  
     🔥 **Здесь происходит object slicing** – `baseObj` получает **только** часть `MyBase`, `derivedValue` теряется!

4. **Вызываем `baseObj.show();`**  
   - Так как `baseObj` — это объект **типа `MyBase`**, вызывается `show()` из `MyBase`.

**Вывод:**
```
MyBase: baseValue = 10
```
👉 **Производная часть (`derivedValue`) была отрезана!**

---

## ✅ **Пример 2: Использование указателя (без object slicing)**

```cpp
#include <iostream>

class MyBase {
public:
    int baseValue;

    MyBase() : baseValue(10) {}

    virtual void show() { // Делаем метод виртуальным!
        std::cout << "MyBase: baseValue = " << baseValue << std::endl;
    }
};

class MyDeriv : public MyBase {
public:
    int derivedValue;

    MyDeriv() : derivedValue(20) {}

    void show() override { // Переопределяем метод с virtual
        std::cout << "MyDeriv: baseValue = " << baseValue 
                  << ", derivedValue = " << derivedValue << std::endl;
    }
};

int main() {
    MyBase* ptr = new MyDeriv(); // Указатель на базовый класс, объект - производный
    ptr->show(); // Вызов виртуального метода (используется метод из MyDeriv!)

    delete ptr; // Освобождаем память!
    return 0;
}
```

### 🔎 **Разбор кода**
1. **Добавляем `virtual` перед `show()` в `MyBase`**  
   - Это включает **полиморфизм**: компилятор будет искать правильный метод в `MyDeriv`.

2. **Создаём `MyBase* ptr = new MyDeriv();`**  
   - `ptr` — это **указатель** типа `MyBase`, который указывает на объект `MyDeriv`.
   - `new MyDeriv();` создаёт объект **в куче**.

3. **Вызываем `ptr->show();`**  
   - Так как `show()` **виртуальный**, вызывается **переопределённый метод** из `MyDeriv`!

4. **Освобождаем память:**
   ```cpp
   delete ptr;
   ```

**Вывод:**
```
MyDeriv: baseValue = 10, derivedValue = 20
```
👉 **Теперь всё работает правильно! Полиморфизм сработал, object slicing нет.**

---

## 📌 Итог:
| Способ | Object Slicing? | Полиморфизм? |
|--------|---------------|-------------|
| `MyBase obj = MyDeriv();` | ✅ (теряются данные) | ❌ |
| `MyBase* obj = new MyDeriv();` | ❌ (сохраняются все данные) | ✅ (если `virtual`) |

🔥 **Главное правило:**  
Если нужен **полиморфизм**, всегда используйте **указатели** и `virtual`-методы!

