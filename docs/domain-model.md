# Модель предметной области

Здесь описаны ключевые сущности (агрегаты) системы, их атрибуты и связи.

## 1. Ингредиент (Ingredient)
Базовая единица продукта.
* **ID** (UUID): уникальный идентификатор.
* **Название** (String): например, «Куриная грудка», «Рис», «Помидор».
* **Категория** (Enum): мясо, овощи, крупы, специи и т.д.
* **КБЖУ на 100г** (Float): калории, белки, жиры, углеводы.

## 2. Рецепт (Recipe)
Совокупность инструкций и ингредиентов.
* **ID** (UUID): уникальный идентификатор.
* **Название** (String): например, «Курица с рисом и помидорами».
* **Время приготовления** (Integer): в минутах.
* **Инструкция** (Text): пошаговое описание (может быть массивом шагов).
* **Порции** (Integer): на сколько порций рассчитан рецепт.
* **Связь с ингредиентами** (Many-to-Many): каждый рецепт содержит список ингредиентов с указанием **количества** (граммы, штуки) и признака **"основной/второстепенный"**.
* **Связь с автором** (Many-to-One): кто добавил рецепт (может быть системный пользователь или реальный).

## 3. Магазин (Store)
Торговая точка поблизости.
* **ID** (UUID).
* **Название** (String): «Пятерочка», «Магнит» и т.д.
* **Координаты** (Point/Geo): широта и долгота для расчета дистанции.
* **Адрес** (String).

## 4. Цена продукта (ProductPrice)
Связь ингредиента и магазина.
* **ID** (UUID).
* **Ингредиент** (Foreign Key): какой продукт.
* **Магазин** (Foreign Key): где продается.
* **Цена** (Decimal): стоимость за единицу (кг, шт).
* **Дата обновления** (Timestamp): когда цена последний раз парсилась.

## 5. Пользователь / Профиль (UserProfile)
* **ID** (UUID).
* **Цель по КБЖУ** (JSON/Float): суточные нормы (например, 2500 ккал, 150г белка).
* **Бюджет** (Decimal): ограничение по цене на порцию (опционально).
* **История поиска** (Array of IDs): для рекомендательной системы.

---

## Связи (ER-модель)
* **Ингредиент ↔ Рецепт**: Многие-ко-многим (через таблицу RecipeIngredients с полями: количество, единица измерения).
* **Ингредиент ↔ Магазин**: Многие-ко-многим (через таблицу ProductPrice).
* **Рецепт → Пользователь**: Один-ко-многим (один пользователь может иметь много рецептов).

## ER-диаграмма (связи сущностей)

```mermaid
erDiagram
    RECIPE ||--o{ RECIPE_INGREDIENT : contains
    INGREDIENT ||--o{ RECIPE_INGREDIENT : used_in
    INGREDIENT ||--o{ PRODUCT_PRICE : has_price
    STORE ||--o{ PRODUCT_PRICE : sells
    USER_PROFILE ||--o{ RECIPE : creates

    RECIPE {
        uuid id PK
        string name
        int cooking_time
        text instructions
        int portions
    }
    INGREDIENT {
        uuid id PK
        string name
        string category
        float calories_per_100g
    }
    STORE {
        uuid id PK
        string name
        point location
        string address
    }
    PRODUCT_PRICE {
        uuid id PK
        uuid ingredient_id FK
        uuid store_id FK
        decimal price
        timestamp updated_at
    }
    RECIPE_INGREDIENT {
        uuid recipe_id FK
        uuid ingredient_id FK
        float amount
        string unit
        boolean is_main
    }
    USER_PROFILE {
        uuid id PK
        float target_calories
        float target_protein
        decimal budget_limit
    }
