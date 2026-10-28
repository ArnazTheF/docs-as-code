# Диаграммы

## Диаграмма последовательности

```mermaid
sequenceDiagram
    actor U as Зритель
    participant UI as Веб-приложение
    participant API as Backend API
    participant DB as База данных
    participant Model as ML-модель (эмбеддинги)

    U->>UI: Нажать «Подобрать фильмы»
    UI->>API: POST /recommendations {userId, limit}
    API->>DB: Получить оценки пользователя
    DB-->>API: Список оценок
    alt Оценок меньше 5
        API-->>UI: 422 NOT_ENOUGH_RATINGS
        UI-->>U: «Оцените ещё несколько фильмов»
    else Оценок достаточно
        API->>Model: Тексты понравившихся и кандидатных фильмов
        Model-->>API: Векторы (эмбеддинги)
        API->>API: Найти похожие непросмотренные фильмы
        API->>DB: Сохранить подборку
        API-->>UI: 201 Created {items: movieId, score}
        UI-->>U: Показать список фильмов
    end
```

## Диаграмма классов

```mermaid
classDiagram
    class User {
        +int id
        +string name
    }
    class Movie {
        +int id
        +string title
        +int year
        +string[] genres
    }
    class Rating {
        +int userId
        +int movieId
        +float value
        +datetime createdAt
    }
    class Recommendation {
        +int id
        +int userId
        +string modelVersion
        +datetime createdAt
    }
    class RecommendationItem {
        +int movieId
        +float score
        +int position
    }
    User "1" --> "*" Rating : ставит
    Movie "1" --> "*" Rating : получает
    User "1" --> "*" Recommendation : получает
    Recommendation "1" *-- "1..*" RecommendationItem : содержит
    RecommendationItem "*" --> "1" Movie : ссылается
```