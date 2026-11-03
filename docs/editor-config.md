# Спільні налаштування: `.editorconfig` і `.gitignore`

Відповідальна: @AlexandraTyshchenko

## `.editorconfig`

[EditorConfig](https://editorconfig.org) задає однакові правила форматування для всіх редакторів
команди. VS Code читає його лише з розширенням `editorconfig.editorconfig`, JetBrains — вбудовано.

### Секція `[*]` — для всіх файлів

| Ключ | Значення | Чому саме так |
|---|---|---|
| `root` | `true` | Не шукати `.editorconfig` у батьківських теках: правила проєкту не залежать від того, де лежить клон |
| `charset` | `utf-8` | Українські коментарі й `README` без «кракозябр» у будь-якій ОС |
| `end_of_line` | `lf` | Скрипти виконуються в Linux-контейнерах і CI; `CRLF` ламає shebang (`/bin/bash^M`) |
| `insert_final_newline` | `true` | POSIX-визначення рядка; без нього `cat` склеює файли, а diff показує `\ No newline at end of file` |
| `trim_trailing_whitespace` | `true` | Пробіли в кінці рядка — шум у diff і причина фіктивних конфліктів |
| `indent_style`, `indent_size` | `space`, `2` | Типова глибина для YAML, JSON, Compose і GitHub Actions — основних конфігів курсу |

### Перевизначення за типом файла

| Секція | Що змінено | Чому |
|---|---|---|
| `[*.py]` | `indent_size = 4`, `max_line_length = 100` | PEP 8 вимагає 4 пробіли; 100 замість 79 — компроміс, який використовують black/ruff-конфіги більшості проєктів |
| `[*.sh]` | `indent_size = 2` | Google Shell Style Guide — найпоширеніший стиль bash |
| `[*.go]`, `[Makefile]` | `indent_style = tab` | `gofmt` форматує табами; `make` **вимагає** таб перед командою, з пробілами — помилка `missing separator` |
| `[*.md]` | `trim_trailing_whitespace = false` | Два пробіли в кінці рядка Markdown — примусовий перенос, їх обрізання змінює вигляд документа |
| `[*.{bat,cmd,ps1}]` | `end_of_line = crlf` | `cmd.exe` некоректно обробляє мітки й `goto` у файлах з `LF` |

## `.gitignore`

Згенеровано сервісом [gitignore.io](https://www.toptal.com/developers/gitignore), а не написано вручну:

```bash
curl -sL "https://www.toptal.com/developers/gitignore/api/linux,macos,windows,python,node,go,visualstudiocode,jetbrains+all,dotenv" -o .gitignore
```

| Шаблон | Навіщо |
|---|---|
| `linux`, `macos`, `windows` | Службові файли ОС (`.DS_Store`, `Thumbs.db`) — у команді різні системи |
| `python`, `node`, `go` | Мови консольних утиліт курсу: `__pycache__/`, `node_modules/`, зібрані `*.exe` |
| `visualstudiocode`, `jetbrains+all` | Локальні налаштування редакторів |
| `dotenv` | `.env` із секретами ніколи не потрапляє в репозиторій |
