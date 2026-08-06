# Промпты скаутов для /plan

Скауты — обычные суб-агенты, модель Haiku или другая быстрая, запускать ПАРАЛЛЕЛЬНО.
Промпт собирается: <шаблон ниже> + конкретика задачи. Каждому скауту давать ограничение
на объём ответа — они возвращают выжимку, а не свалку.

---

## 1. Скаут контекста (всегда)

### Вариант «код» (задача в репозитории)

```
You are a repo context scout. Task context: <краткое описание задачи>.
Repo: <путь>.
Find and report (max 60 lines):
1. Conventions relevant to this task: naming, file layout, error handling, test style.
   Cite 2-3 concrete example files.
2. Places that already do something similar to the task. Exact paths + what they do.
3. Files the task will likely touch, with 1-line reason each.
4. Landmines: fragile areas, TODOs, known workarounds near the affected code.
Do NOT propose a solution. Facts and paths only.
```

### Вариант «некод» (документ / исследование / идеи / решение)

```
You are a notes scout. Task context: <краткое описание задачи>.
Sources: <папка с заметками пользователя>, <папка проекта>, docs/ of the relevant repo.
Find and report (max 60 lines):
1. Notes and documents directly relevant to the topic: path + 1-line what's inside.
2. Existing decisions and constraints that bound this task.
3. Raw materials available: data files, transcripts, research notes. Paths.
Do NOT write the plan. Facts and paths only.
```

## 2. Скаут прошлых уроков (всегда)

```
You are a lessons scout. Task context: <краткое описание задачи>.
Search: the user's notes and, if a repo is involved, its docs/ folder
(especially docs/plans/ and any solutions/decisions log).
Report (max 40 lines):
1. Have we solved something similar before? Where, what was the outcome?
2. Documented mistakes and gotchas that apply here.
3. Existing tools, scripts or skills that could be reused instead of building new.
If nothing found, say so explicitly — do not pad.
```

## 3. Скаут внешней доки (условный)

Запускать ТОЛЬКО если: локальных примеров мало (меньше трёх), или тема внешняя (новая
библиотека, чужой рынок, чужой продукт), или пользователь явно попросил посмотреть наружу.
Не запускать «на всякий случай» — это самый дорогой скаут.

```
You are an external research scout. Task context: <краткое описание задачи>.
Question to answer: <конкретный вопрос, НЕ «найди всё про X»>.
Use WebSearch/WebFetch. For library and framework APIs prefer official docs.
Report (max 50 lines):
1. Direct answer to the question with 2-3 source URLs.
2. Current best practice, noting the date/version it applies to.
3. Common mistakes people report with this approach.
Note version numbers explicitly.
Every figure carries the exact page URL it came from — a bare domain is not a source.
Mark each claim: stated directly in the source / your inference / assumption.
Say explicitly what you could NOT verify (JS-rendered page, blocked by anti-bot) —
silence about it reads as "checked and fine", which is a different claim.
```

---

## Правила синтеза (для основной сессии после возврата скаутов)

- Противоречие между скаутами → верить локальному контексту (репозиторий, свои заметки),
  внешнее — совещательно.
- Скаут вернул пустоту → зафиксировать это в плане («прошлых решений не нашли»),
  не перезапускать его в надежде на другой результат.
- В план попадают только факты, влияющие на решения. Сырые отчёты скаутов в план
  не вклеивать.
