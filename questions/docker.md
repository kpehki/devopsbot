<details>
<summary>
1. Что такое Docker? В чем отличие контейнера от образа?
</summary>
Docker - программное обеспечение для автоматизации развёртывания и управления приложениями в средах с поддержкой контейнеризации.

Образ - шаблон приложения, который содержит слои файловой системы в режиме "только-чтение".

Контейнер - запущенный образ приложения, который кроме нижних слоев в режиме "только чтение" содержит верхний слой в режиме "чтение-запись".
</details>
<details>
<summary>
2. Какие инструкции есть у Dockerfile?
</summary>

| Инструкция | Описание |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FROM | Задаёт базовый (родительский) образ. |
| LABEL | Описывает метаданные. Например — сведения о том, кто создал и поддерживает образ. |
| ENV | Устанавливает постоянные переменные среды. |
| RUN | Выполняет команду и создаёт слой образа. Используется для установки в контейнер пакетов. |
| COPY | Копирует в контейнер файлы и директории. |
| ADD | Копирует файлы и директории в контейнер, может распаковывать локальные .tar-файлы. |
| CMD | Описывает команду с аргументами, которую нужно выполнить когда контейнер будет запущен. Аргументы могут быть переопределены при запуске контейнера. В файле может присутствовать лишь одна инструкция CMD. |
| WORKDIR | Задаёт рабочую директорию для следующей инструкции. |
| ARG | Задаёт переменные для передачи Docker во время сборки образа. |
| ENTRYPOINT | Предоставляет команду с аргументами для вызова во время выполнения контейнера. Аргументы не переопределяются. |
| EXPOSE | Указывает на необходимость открыть порт. |
| VOLUME | Создаёт точку монтирования для работы с постоянным хранилищем. |
</details>
<details>
<summary>
3. Чем отличается *CMD* от *ENTRYPOINT* в Dockerfile?
</summary>
Инструкции CMD и ENTRYPOINT выполняются в момент запуска контейнера, тольо инструкция CMD позволяет переопределить передаваемые команде аргументы.
</details>
<details>
<summary>
4. Чем отличается *COPY* от *ADD* в Dockerfile?
</summary>
Инструкция *COPY* копируют файлы и директории с хостовой машины внутрь контейнера, инструкция *ADD* копирует файлы и директории с хостовой машины внутрь контейнера и может распаковывать .tar архивы.
</details>
<details>
<summary>
5. Какие есть best practices для написания Dockerfile?
</summary>
1. Запускать только один процесс на контейнер.
2. Стараться объединять несколько команд RUN в одну для уменьшения количества слоёв образа.
3. Частоизменяемые слои образа необходимо располагать ниже по уровню, чтобы ускорить процесс сборки, т.к. при изменении верхнего слоя, все нижеследующие слои будут пересобираться.
4. Указывать явные версии образов в инструкции FROM, чтобы избежать случая, когда выйдет новая версия образа с тегом latest.
5. При установке пакетов указывать версии пакетов.
6. Очищать кеш пакетного менеджера и удалять ненужные файлы после выполненной инструкции.
7. Использовать multistage build для сборки артифакта в одном контейнере и размещении его в другом.
</details>
<details>
<summary>
6. Какие типы сетевых драйверов используются в docker?
</summary>
Основные драйвера сетей docker: bridge, host, overlay, ipvlan, macvlan, none

**bridge:** это сетевой драйвер по умолчанию. Бридж сеть используется, когда ваши приложения запускаются в автономных контейнерах, которые должны взаимодействовать между собой. 
![docker-bridge](imgs/docker-bridge.png)
Взаимодействие с хостом выполняется через мост docker0 и конфигурацию таблицы iptables nat. В этом режиме будет выделено сетевое пространство имен, задан IP-адрес для каждого контейнера, а контейнер Docker на хосте будет подключен к виртуальному мосту. Виртуальный мост работает как физический коммутатор, поэтому все контейнеры на хосте подключены к сети уровня 2 через коммутатор.

**host:** использует сеть хоста напрямую без изоляции контейнера и хоста.

**none:** этот режим помещает контейнер в свой собственный сетевой стек, но не выполняет никакой настройки. Фактически, этот режим отключает сетевую функцию контейнера, что полезно в следующих двух ситуациях: контейнер не требует сети (например, только для пакетной задачи записи дисковых томов).

**macvlan:** в режиме Macvlan Bridge каждый контейнер имеет уникальный MAC-адрес, который используется для отслеживания сопоставления MAC-адреса с портом хоста Docker. Сеть драйвера Macvlan подключается к родительскому интерфейсу хоста Docker. Примерами являются физические интерфейсы, такие как eth0, субинтерфейс eth0.10 для тегирования VLAN 802.1q (.10 означает VLAN 10) или даже связанный хост-адаптер, который объединяет два интерфейса Ethernet в единый логический интерфейс. Назначенный шлюз является внешним по отношению к хосту, предоставляемому сетевой инфраструктурой. Каждая сеть Docker в режиме Macvlan Bridge изолирована друг от друга, и только одна сеть может быть подключена к родительскому узлу одновременно. Каждый хост-адаптер имеет теоретический предел, и каждый хост-адаптер может подключаться к сети Docker. Любой контейнер в той же подсети может взаимодействовать с любым другим контейнером в той же сети без шлюзового моста macvlan. Та же сетевая команда docker применяется к драйверу vlan. В режиме Macvlan без внешней маршрутизации процессов между двумя сетями / подсетями контейнеры в разных сетях не могут получить доступ друг к другу. Это также относится к нескольким подсетям в одной и той же терминальной сети.

**overlay:** Оверлейные сети соединяют несколько демонов Docker вместе и позволяют сервисам swarm взаимодействовать друг с другом. Вы также можете использовать оверлейные сети для облегчения связи между сервисом swarm и автономным контейнером или между двумя автономными контейнерами в разных демонах Docker. Эта стратегия устраняет необходимость выполнять маршрутизацию между этими контейнерами на уровне ОС.

**ipvlan:** Сети ipvlan предоставляют пользователям полный контроль над адресацией IPv4 и IPv6. Драйвер VLAN построен на основе этой возможности, предоставляя операторам полный контроль над тегированием VLAN уровня 2 и даже маршрутизацией IPvlan L3 для пользователей.
</details>
<details>
<summary>
7. Что такое эфемерные контейнеры?
</summary>
[Эфемерные контейнеры](https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/) стали бета-функцией в Kubernetes v1.23 и теперь включены по умолчанию.
Эфемерные контейнеры предназначены для транзитных задач, когда вам нужно временно [подключить дополнительный контейнер к существующему поду](https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/#ephemeral-container). Это идеально подходит для отладочных операций, когда вы хотите проверить поды, не затрагивая живые экземпляры контейнеров.
</details>
<details>
<summary>
8. Какие команды не порождают новый слой?
</summary>
Команда LABEL не создает новый слой в Docker
</details>
<details>
<summary>
9. cmd entry point - для чего они нужны?
</summary>
CMD - список команд которые будут запускаться при каждом старте контейнера c переопределяемыми аргументами
ENTRYPOINT - тоже самое, только нельзя переопределять аргументы
</details>
<details>
<summary>
10. Слои в докере - что это?
</summary>
Сущность которая накладывается друг на друга при создании контейнера. Пример с пиццой - тесто, томаты, сыр. Самый нижний слой - базовый образ. Все слои кроме самого верхнего read only.
Каждая инструкция в Dockerfile добавляет отдельный слой 
Можно переиспользовать. Почти каждый слой добавляет места. 
</details>
<details>
<summary>
11. В слой делаем run yum update. Как докер понимает что нужно пересобирать кэш, сбросить кэш или он yum update один раз сделал и больше делать его не будет?
</summary>
Выполнит команду и сравнит с хеш суммой.
</details>
<details>
<summary>
12. Понятие multi-stage.
</summary>
Для уменьшения размера контейнера. Можно например скопировать только исполняемые файлы и собрать новый образ включив только их и не тащить все предыдущие слои.
</details>
<details>
<summary>
13. Команда "copy . ." Мы можем ограничить что будет копироваться?
</summary>
КОпирует все что у нас в корне в корень контейнера.
Можно с помощью .dockerignore
</details>
<details>
<summary>
14. Как посмотреть логи работающего контейнера? Как посмотреть определённое количество строк лога?
</summary>
Docker logs nginx -n 10
</details>
<details>
<summary>
15. Как посмотреть только ошибки в файле лога? Как посмотреть саму ошибку и 10 строчек до и после неё?
</summary>
error logs:
docker logs nginx -f 1>/dev/null | grep 'info' -i -A 1 -n
access logs:
docker logs nginx -f 2>/dev/null
</details>
<details>
<summary>
16. Как попасть в bash работающего контейнера?
</summary>
Docker exec -it bash/sh
</details>
<details>
<summary>
17. Volume в докере. Монтирование директории и использование volume - в чём разница?
</summary>
Связанными папками нельзя управлять из Docker CLI.
Абсолютные пути на разных компьютерах могут быть разными.
Из контейнера можно заблокировать доступ к папке и поломать остовую систему.
Volume можно шифровать и монтировать нескольким контейнерам.
</details>
<details>
<summary>
18. Механизм ограничений доступа docker volume.
</summary>
readonly, readwrite
</details>
<details>
<summary>
19. За счет чего достигается изоляция в Docker?
</summary>
В Docker изоляция также достигается за счет использования неймспейсов, которые позволяют разделять ресурсы между контейнерами. Например, каждый контейнер имеет свой собственный неймспейс для процессов, сетевых интерфейсов, IPC (межпроцессного взаимодействия) и многих других ресурсов. Это позволяет контейнерам работать в изолированном окружении, не взаимодействуя с другими контейнерами или хост-системой. Кроме того, Docker поддерживает возможность создания пользовательских неймспейсов, что позволяет управлять доступом к ресурсам внутри контейнера на более гранулярном уровне.
</details>
