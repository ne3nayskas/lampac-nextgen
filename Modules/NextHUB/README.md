# NextHUB

Дополнительный модуль для просмотра контента **18+** по описаниям сайтов в **YAML** (списки, поиск, карточки). Работает рядом с **SISI** и проектами **`Modules/Adult/*`**: там фиксированные C#-платформы и `/sisi.js`, здесь — гибкие сценарии без отдельного контроллера на каждый сайт.

Проект в решении: **`Modules/NextHUB/`** ([`NextGen.slnx`](../../NextGen.slnx)). Обзор сервера — [корневой README](../../README.md).

## Маршруты

| Метод | Путь | Назначение |
| --- | --- | --- |
| `GET` | `/nexthub` | Список/поиск/меню по `plugin` и query-параметрам |
| `GET` | `/nexthub/vidosik` | Карточка просмотра: параметры `uri` (зашифрованная пара `plugin_-:-_url`), `related` (см. `ViewController`) |

Основные query-параметры списка: `plugin` (имя YAML без `.yaml`), `search`, `sort`, `cat`, `model`, `pg`.

## Файлы

| Путь | Назначение |
| --- | --- |
| `sites/*.yaml` | Описание сайта (меню, парсинг HTML, кеш и т.д.) |
| `override/{plugin}.yaml` или `override/_.yaml` | Пользовательские дополнения; мержатся поверх базового YAML при загрузке |
| `examples/base.yaml`, `examples/gpt.txt` | Примеры структуры и подсказки по полям моделей (`Shared.Models.SISI.NextHUB`) |

В каталоге **`sites/`** — десятки готовых описаний `.yaml` (имя файла без расширения = значение `plugin` в URL). Пример структуры полей — **`examples/base.yaml`**.

## Конфигурация (`init.conf`)

Секция **NextHUB**:

```json
{
  "NextHUB": {
    "sites_enabled": "pornhub,beeg"
  }
}
```

Если `sites_enabled` **задан**, плагин допускается только если его имя **входит в эту строку как подстрока** (`String.Contains`), например `pornhub,beeg` для двух сайтов. Пустое значение или отсутствие ключа — все YAML из `sites/`. Реализация: `Root.goInit` в `Root.cs`.

## Безопасность и нагрузка

При загрузке модуля в `ModInit.cs` в `WAF.limit_map` добавляется правило для `^/nexthub` (**5** запросов в **1** секунду, учёт по `plugin` в query).

## Зависимости

Используются **HtmlAgilityPack**, **Playwright** (часть сценариев), **YamlDotNet**, общие типы SISI/NextHUB из **Shared** (`Shared.Models.SISI.NextHUB`).
