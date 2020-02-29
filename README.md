Контроль отображения записей
Есть много вариантов выбора, какие элементы отображаются в логе. Поиграйте со следующими параметрами:

git log --pretty=oneline --max-count=2
git log --pretty=oneline --since='5 minutes ago'
git log --pretty=oneline --until='5 minutes ago'
git log --pretty=oneline --author=<your name>
git log --pretty=oneline --all
В unix-системах доступна справочная страница man git log.

03Изощряемся
Вот что я использую для просмотра изменений, сделанных за последнюю неделю. Я добавлю --author=alex, если я хочу увидеть только изменения, которые сделал я.

git log --all --pretty=format:"%h %cd %s (%an)" --since='7 days ago'
04Конечный формат лога
Со временем, я решил, что для большей части моей работы мне подходит следующий формат лога.

ВЫПОЛНИТЕ:
git log --pretty=format:"%h %ad | %s%d [%an]" --graph --date=short
Выглядит это примерно так:

РЕЗУЛЬТАТ:
$ git log --pretty=format:"%h %ad | %s%d [%an]" --graph --date=short
* fa3c141 2011-03-09 | Added HTML header (HEAD, master) [Alexander Shvets]
* 8c32287 2011-03-09 | Added standard HTML page tags [Alexander Shvets]
* 43628f7 2011-03-09 | Added h1 tag [Alexander Shvets]
* 911e8c9 2011-03-09 | First Commit [Alexander Shvets]
Давайте рассмотрим его в деталях:

--pretty="..." — определяет формат вывода.
%h — укороченный хэш коммита
%d — дополнения коммита («головы» веток или теги)
%ad — дата коммита
%s — комментарий
%an — имя автора
--graph — отображает дерево коммитов в виде ASCII-графика
--date=short — сохраняет формат даты коротким и симпатичным
Таким образом, каждый раз, когда вы захотите посмотреть лог, вам придется много печатать. К счастью, мы узнаем о git алиасах в следующем уроке.

git config --global alias.ci commit
----------this command will do shortcut to "commit", now it will be "ci"

git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
* 447fff5 2020-02-28 | Added h2 to hello.html (HEAD -> master) [Tatiana]
* 8739bc7 2020-02-28 | Added HTML header [Tatiana]
* dd308b2 2020-02-28 | Added standard HTML page tags [Tatiana]
* 612b4a9 2020-02-28 | commit of c.html file [Tatiana]
* 22ebb5b 2020-02-28 | commit of changed hello.html [Tatiana]
* 3b11e75 2020-02-28 | commit of a and b files [Tatiana]
* c702a1e 2020-02-28 | 1st commit [Tatiana]

git checkout c702a1e
cat hello.html
--------this will checkout * c702a1e 2020-02-28 | 1st commit [Tatiana]. cat will show what was version about

Вернитесь к последней версии в ветке master
ВЫПОЛНИТЕ:
git checkout master
cat hello.html
Вы увидите…

РЕЗУЛЬТАТ:
$ git checkout master
Previous HEAD position was 911e8c9... First Commit
Switched to branch 'master'
$ cat hello.html
<html>
  <head>
  </head>
  <body>
    <h1>Hello, World!</h1>
  </body>
</html>
«master» — имя ветки по умолчанию. Переключая имена веток, вы попадаете на последнюю версию выбранной ветки.

Отмена изменений в рабочем каталоге
Используйте команду checkout для переключения в версию файла hello.html в репозитории.

ВЫПОЛНИТЕ:
git checkout hello.html
git status
cat hello.html
РЕЗУЛЬТАТ:
$ git checkout hello.html
$ git status
# On branch master
nothing to commit (working directory clean)
$ cat hello.html
<html>
  <head>
  </head>
  <body>
    <h1>Hello, World!</h1>
  </body>
</html>

Выполните сброс буферной зоны
К счастью, вывод состояния показывает нам именно то, что мы должны сделать для отмены индексации изменения.

ВЫПОЛНИТЕ:
git reset HEAD hello.html
РЕЗУЛЬТАТ:
$ git reset HEAD hello.html
Unstaged changes after reset:
M   hello.html


Сделайте коммит с новыми изменениями, отменяющими предыдущие
Чтобы отменить коммит, нам необходимо сделать коммит, который удаляет изменения, сохраненные нежелательным коммитом.

ВЫПОЛНИТЕ:
git revert HEAD
Перейдите в редактор, где вы можете отредактировать коммит-сообщение по умолчанию или оставить все как есть. Сохраните и закройте файл. Вы увидите…

РЕЗУЛЬТАТ:
$ git revert HEAD --no-edit
[master 45fa96b] Revert "Oops, we didn't want this commit"
 1 files changed, 1 insertions(+), 1 deletions(-)


Сброс коммитов к предшествующим коммиту Oops
Глядя на историю лога (см. выше), мы видим, что коммит с тегом «v1» является коммитом, предшествующим ошибочному коммиту. Давайте сбросим ветку до этой точки. Поскольку ветка имеет тег, мы можем использовать имя тега в команде сброса (если она не имеет тега, мы можем использовать хэш-значение).

ВЫПОЛНИТЕ:
git reset --hard v1
git hist

РЕЗУЛЬТАТ:
HEAD is now at 447fff5 Added h2 to hello.html

Удаление тега oops
Тег oops свою функцию выполнил. Давайте удалим его и коммиты, на которые он ссылался, сборщиком мусора.

ВЫПОЛНИТЕ:
git tag -d oops
git hist --all

РЕЗУЛЬТАТ:
Deleted tag 'oops' (was 4b40f58)

Измените предыдущий коммит
Мы действительно не хотим создавать отдельный коммит только ради электронной почты. Давайте изменим предыдущий коммит, включив в него адрес электронной почты.

ВЫПОЛНИТЕ:
git add hello.html
git commit --amend -m "Add an author/email comment"
РЕЗУЛЬТАТ:
$ git add hello.html
$ git commit --amend -m "Add an author/email comment"
[master 6a78635] Add an author/email comment
 1 files changed, 2 insertions(+), 1 deletions(-)
04Просмотр истории
ВЫПОЛНИТЕ:
git hist
РЕЗУЛЬТАТ:
* b11a2f4 2020-02-28 | Add an author/email/phone_number comments (HEAD) [Tatiana]
* 7fef4e0 2020-02-28 | Add an author comment [Tatiana]
* 447fff5 2020-02-28 | Added h2 to hello.html (tag: v1, master) [Tatiana]
* 8739bc7 2020-02-28 | Added HTML header (tag: v1-beta) [Tatiana]
* dd308b2 2020-02-28 | Added standard HTML page tags [Tatiana]
* 612b4a9 2020-02-28 | commit of c.html file [Tatiana]
* 22ebb5b 2020-02-28 | commit of changed hello.html [Tatiana]
* 3b11e75 2020-02-28 | commit of a and b files [Tatiana]
* c702a1e 2020-02-28 | 1st commit [Tatiana]

Переместите файл hello.html в каталог lib
Сейчас мы собираемся создать структуру нашего репозитория. Давайте перенесем страницу в каталог lib.

ВЫПОЛНИТЕ:
mkdir lib
git mv hello.html lib
git status
РЕЗУЛЬТАТ:
$ mkdir lib
$ git mv hello.html lib
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#   renamed:    hello.html -> lib/hello.html
#

Каталог .git
Настало время провести небольшое исследование. Для начала, из корневого каталога вашего проекта…

ВЫПОЛНИТЕ:
ls -C .git
РЕЗУЛЬТАТ:
$ ls -C .git
COMMIT_EDITMSG  MERGE_RR    config      hooks       info        objects     rr-cache
HEAD        ORIG_HEAD   description index       logs        refs

Поиск последнего коммита
ВЫПОЛНИТЕ:
git hist --max-count=1


