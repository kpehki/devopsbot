<details>
<summary>
1. Что такое Bash?
</summary>
Bash — это командная оболочка для UNIX-подобных операционных систем (UNIX, GNU/Linux, MacOS). Она дает пользователю систему команд для работы с файлами и папками, поиском, настройкой окружения и позволяет управлять ОС прямо из командной строки. Слово bash читается как «баш» и расшифровывается как Bourne-Again Shell
</details>
<details>
<summary>
2. Где задается оболочка скрипта?
</summary>
Создайте пустой файл с использованием команды touch. В его первой строке нужно указать, какую именно оболочку мы собираемся использовать. Нас интересует bash, поэтому первая строка файла будет такой:

#!/bin/bash  (эту последовательность называют шебанг)
</details>
<details>
<summary>
3. Запускаем скрипт и получаем Permission denied. Что забыли сделать?
</summary>
Нужно сделать файл исполняемым:

chmod +x ./myscript

Теперь попытаемся его выполнить:

./myscript

После настройки разрешений всё работает как надо.
</details>
<details>
<summary>
4. Какие типы переменных используютс в Bash?
</summary>
Переменные среды
Пользовательские переменные

Переменные среды:

#!/bin/bash
echo "Home for the current user is: $HOME"

Пользовательские переменные:

#!/bin/bash
grade=5
person="Adam"
echo "$person is a good boy, he is in grade $grade"
</details>
<details>
<summary>
5. Какие есть оболочки помимо bash?
</summary>
В Linux доступны следующие командные оболочки:
Bash — самая распространённая оболочка под Linux. ...
pdksh — клон korn shell, хорошо известной оболочки в системах UNIX;
tcsh — улучшенная версия >C shell;
zsh — новейшая из перечисленных здесь оболочек; реализует улучшенное дополнение и другие удобные функции.
fish - (friendly interactive shell) это удобная оболочка командной строки, предназначенная в основном для интерактивного использования.
</details>
<details>
<summary>
6. Что такое STDIN?
</summary>
STDIN — это стандартный поток ввода оболочки. Для терминала стандартный ввод — это клавиатура. Когда в сценариях используют символ перенаправления ввода — <, Linux заменяет дескриптор файла стандартного ввода на тот, который указан в команде. Система читает файл и обрабатывает данные так, будто они введены с клавиатуры.

Многие команды bash принимают ввод из STDIN, если в командной строке не указан файл, из которого надо брать данные. Например, это справедливо для команды cat.

Когда вы вводите команду cat в командной строке, не задавая параметров, она принимает ввод из STDIN. После того, как вы вводите очередную строку, cat просто выводит её на экран.
</details>
<details>
<summary>
7. Что такое STDOUT?
</summary>
STDOUT — стандартный поток вывода оболочки. По умолчанию это — экран. Большинство bash-команд выводят данные в STDOUT, что приводит к их появлению в консоли. Данные можно перенаправить в файл, присоединяя их к его содержимому, для этого служит команда >>.

Итак, у нас есть некий файл с данными, к которому мы можем добавить другие данные с помощью этой команды:

pwd >> myfile

То, что выведет pwd, будет добавлено к файлу myfile, при этом уже имеющиеся в нём данные никуда не денутся.

Пока всё хорошо, но что если попытаться выполнить что-то вроде показанного ниже, обратившись к несуществующему файлу xfile, задумывая всё это для того, чтобы в файл myfile попало сообщение об ошибке.

ls –l xfile > myfile

После выполнения этой команды мы увидим сообщения об ошибках на экране.

При попытке обращения к несуществующему файлу генерируется ошибка, но оболочка не перенаправила сообщения об ошибках в файл, выведя их на экран. Но мы-то хотели, чтобы сообщения об ошибках попали в файл. Что делать? Ответ прост — воспользоваться третьим стандартным дескриптором.
</details>
<details>
<summary>
8. Что такое STDERR?
</summary>
STDERR представляет собой стандартный поток ошибок оболочки. По умолчанию этот дескриптор указывает на то же самое, на что указывает STDOUT, именно поэтому при возникновении ошибки мы видим сообщение на экране.

Итак, предположим, что надо перенаправить сообщения об ошибках, скажем, в лог-файл, или куда-нибудь ещё, вместо того, чтобы выводить их на экран.

Перенаправление потока ошибок

Как вы уже знаете, дескриптор файла STDERR — 2. Мы можем перенаправить ошибки, разместив этот дескриптор перед командой перенаправления:

ls -l xfile 2>myfile
cat ./myfile

Сообщение об ошибке теперь попадёт в файл myfile.
</details>
<details>
<summary>
9. Какие потоки резервирует bash?
</summary>
Всё в Linux — это файлы, в том числе — ввод и вывод. Операционная система идентифицирует файлы с использованием дескрипторов.

Каждому процессу позволено иметь до девяти открытых дескрипторов файлов. Оболочка bash резервирует первые три дескриптора с идентификаторами 0, 1 и 2. Вот что они означают.

0, STDIN — стандартный поток ввода.
1, STDOUT — стандартный поток вывода.
2, STDERR — стандартный поток ошибок.

Эти три специальных дескриптора обрабатывают ввод и вывод данных в сценарии.
Вам нужно как следует разобраться в стандартных потоках. Их можно сравнить с фундаментом, на котором строится взаимодействие скриптов с внешним миром.
</details>
