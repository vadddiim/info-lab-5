# Лабораторная работа №5

## Задание 1

Создайте bash-скрипт, который будет выполнять проверку того, что коммитится файл формата .txt и в файле присутствует какой-то текст (например, в конце файла есть подпись автора, неважно как она выглядит, главное чтобы была какая-нибудь проверка содержимого файла). Далее поместите этот скрипт в нужную папку и проверьте, что перед каждым коммитом проходит проверка, например, добавьте вывод о результате проверки в консоль. За этот функционал отвечает Git Hooks, там сказазно как автоматизировать такую проверку.

### Ход работы

Сначала мне необходимо было создать файл-хук, который будет проверять файлы на наличие подписи. Также, чтобы его можно было исполнять, нужно выдать права доступа.

```bash
nano .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

Файл содержит скрипт ниже.

```bash
#!/bin/bash

failed=false

# Получаем список всех файлов, добавленных в коммит
files=$(git diff --cached --name-only --diff-filter=ACMR | grep '\.txt$')

for file in $files; do
    if [ "$file" != "version.txt" ] && ! grep -q "Автор:" "$file"; then
        echo "Ошибка: файл '$file' не содержит подпись автора"
        failed=true
    fi
done

if [ $failed = true ]; then
    echo "Коммит отменен"
    exit 1
fi

echo "Все файлы соотвествуют требованиям ✅"
exit 0
```

Скрипт проходится по всем измененным файлам коммита, проверяет наличия подписи с помощью grep и, если подпись не находится, то выкидывает ошибку.

После написания, я проверил хук, создав коммит с файлом без автора и он корректно выдал ошибку. Я исправил файл, закоммитил изменения и проверка прошла успешно.

[скриншот1]

[скриншот2]

## Задание 2

Во втором задании необходимо было воспроизвести пример с использованием Git Flow.

### Ход работы

1. Я убедился, что Git Flow установлен на моей локальной машине:

   ```
   sudo apt-get install git-flow
   ```

2. В корне репозитория я инициализировал Git Flow:

   ```
   git flow init
   ```

3. Я создал ветку для новой функциональности под названием "task-management":

   ```
   git flow feature start task-management
   ```

4. Внес изменения в код для добавления функционала управления задачами. Например, я добавил функцию в файл `task_manager.py`:

   ```python
   def create_task(title, description):
       print(f"Создана новая задача: {title}")
   ```

   Я выполнял коммиты изменений по мере разработки:

   ```
   git add task_manager.py
   git commit -m "Добавлен функционал управления задачами"
   ```

5. После завершения разработки я завершил работу над фичей и объединил её с основной веткой:

   ```
   git flow feature finish task-management
   ```

   Git Flow автоматически переключился на ветку `develop` и выполнил слияние. Конфликты (если они были) я разрешил.

6. Я переключился на ветку `develop` и начал создание релиза:

   ```
   git checkout develop
   git flow release start v1.0.0
   ```

7. Внес изменения, связанные с релизом. Например, обновил версию в файле `version.txt`:

   ```
   echo "v1.0.0" > version.txt
   git add version.txt
   git commit -m "Обновлена версия для релиза v1.0.0"
   ```

8. Завершил релиз и объединил его с ветками `develop` и `main`:

   ```
   git flow release finish v1.0.0
   ```

9. Чтобы продемонстрировать работу с hotfix, я создал исправление (хотя ошибок в функциональности не было):

   ```
   git flow hotfix start hotfix-1.0.1
   ```

10. Внес изменения для исправления (или демонстрации) и закоммитил их:

```
# Исправление ошибки
git add file_with_error.py
git commit -m "Исправлена критическая ошибка"
```

11. Завершил hotfix и объединил его с ветками `develop` и `main`:

```
git flow hotfix finish hotfix-1.0.1
```

12. После завершения работы я отправил изменения на удалённый репозиторий:

```
git push origin develop
git push origin main
```

Теперь функциональность успешно добавлена, и релиз подготовлен с учётом всех этапов работы с Git Flow!