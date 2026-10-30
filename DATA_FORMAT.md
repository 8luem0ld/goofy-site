# Формат файлов GOOFY v1

Весь переносимый снимок — UTF-8 JSON. Схема реализована нормализатором `GoofyCore.validate` в `core.js`; пример импорта — `example-catalog.json`. Посторонние поля отбрасываются. Ошибочные идентификаторы, повторные номера и сетевые пути отклоняют импорт целиком.

| Поле | Содержимое |
|---|---|
| `schemaVersion` | Число `1` |
| `shows[]` | `{id, title, original, year, theme, art, genre, description}` |
| `episodes[]` | `{id, showId, season, number, title, original, description, airDate, duration, updatedAt, locked, sources[]}` |
| `sources[]` внутри серии | `{id, voice, language, translation, fileName, size, lastModified, path, syncGroup, checkedAt, status}` |
| `preferences` | `{frame, dim, autoplay, voices[], volume}` |
| `progress` | Объект с ID серии в качестве ключа: `{position, duration, watched, sourceId, voice, updatedAt}` |
| `favorites[]` | ID сериалов |
| `events[]` | `{id, type, episodeId, text, at, read}` |
| `logs[]` | `{at, text}` |
| `conflicts[]` | `{cid, episode, reason}` — отложенные спорные сопоставления |

Минимальный каталог импорта содержит `schemaVersion`, `shows`, `episodes`. Для каждой серии нужен массив `sources`, в том числе пустой. Метаданные не требуют фиктивных дат, длительностей или источников.

Время прогресса и длительность — секунды. `updatedAt`, `checkedAt`, `at` — строки ISO 8601 UTC. `airDate` — `YYYY-MM-DD` или пустая строка. Год сериала — строка.

`season=0` — спецвыпуск. Источники имеют только локальный тип. `path` — безопасный относительный путь от `index.html`; внешние адреса и выход за папку запрещены. Файл, выбранный через браузерный диалог, имеет пустой `path`, а для восстановления доступа используются имя, размер и время изменения. Сам объект File и временный blob URL никогда не попадают в экспорт.

`syncGroup` — подтверждённое совпадение монтажа. Пустая строка означает неизвестное соответствие. `status` принимает `unknown`, `ok`, `error`; успех означает получение метаданных видео браузером, а не проверку каждого кадра файла.

Сезон вычисляется из серии, озвучка — из источника. Сущностей пользователей, паролей, ключей API и таблиц БД нет.

Тематические значения: `theme` = `peach | mint | yellow | lilac`; `art` = `couch | mountains | donut | space`. Иллюстрации построены локальными SVG в `app.js`.

Лимиты: до 500 сериалов, 50 000 серий, 100 источников на серию, импорт JSON до 25 МБ. Хранятся последние 2 000 событий и 200 строк журнала. Фактический объём browser localStorage ограничен браузером: при ошибке сохранения показывается предупреждение и остаётся экспорт JSON.
