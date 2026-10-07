# Git и Pull Request'ы

В `main` попадает только код, прошедший ревью. Прямой пуш в `main` запрещён правилами репозитория.

## Ветки

Одна задача — одна ветка от свежего `main`.

Формат: `<тип>/<номер-issue>-<коротко-латиницей>`

| Тип | Когда | Пример |
| --- | --- | --- |
| `feat` | Новая функциональность | `feat/3-design-tokens` |
| `fix` | Исправление бага | `fix/14-like-counter` |
| `chore` | Настройка, зависимости, конфиги | `chore/1-project-setup` |
| `refactor` | Переделка без изменения поведения | `refactor/20-api-client` |
| `docs` | Документация | `docs/2-readme` |

## Коммиты

Пишем по [Conventional Commits](https://www.conventionalcommits.org/ru/v1.0.0/): `<тип>(<область>): <что сделано>`. По-английски, в повелительном наклонении, со строчной буквы, без точки в конце.

```
feat(header): add mobile burger menu
fix(records): handle empty search results
chore: configure prettier
```

Плохо: `fix`, `правки`, `Updated stuff.`, `final final 2`.

Один коммит — одно логическое изменение. Файлы, не относящиеся к задаче, в PR не тащим.

## Pull Request

1. Перед открытием PR подтяни свежий `main` и сделай rebase своей ветки на него. Конфликты решаешь ты.
2. **Заголовок PR** — в формате коммита: `feat: add design tokens`. После Squash-слияния он станет сообщением коммита в `main`.
3. **Описание** — по шаблону: что сделано, как проверить, скриншоты для любых визуальных изменений.
4. В описании обязательно строка **`Closes #<номер issue>`**. Тогда после мёржа issue закроется сам и уедет в Done.
5. Перенеси карточку на доске в Review и напиши тимлиду, что PR готов.
6. Пока идёт ревью, на замечания отвечай **новыми коммитами** в ту же ветку. Без force-push: так видно, что поменялось.
7. Сливаешь ты сам, но только после «Approve». Способ слияния — **Squash and merge**. После слияния удали ветку.

## Шпаргалка

```bash
git switch main && git pull                  # свежий main
git switch -c feat/3-design-tokens           # новая ветка под задачу
git add -p                                   # добавлять по кускам, глядя на диф
git commit -m "feat(styles): add color tokens"
git push -u origin feat/3-design-tokens      # первый пуш ветки

git fetch origin && git rebase origin/main   # подтянуть main перед PR
git push --force-with-lease                  # после rebase — только так, и только до начала ревью
```
