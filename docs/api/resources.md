# Ресурсы API (ЛР 2Г)

| Требование | Ресурс | Операция | Коды ответов | Роль |
|---|---|---|---|---|
| `FR-01` | `recipes` | `POST /api/v1/recipes/search` | 200, 422 | Пользователь |
| `FR-02` | `recipes/{id}` | `GET /api/v1/recipes/{id}` | 200, 404 | Пользователь |
| `FR-03` | `prices` | `GET /api/v1/prices?ingredients=...` | 200 | Пользователь |
