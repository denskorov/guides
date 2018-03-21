#Docker

>Перед установкой
Для того, чтобы следовать инструкциям, описанным в этой статье, вам потребуются:

**Дроплет с 64-битной Ubuntu 16.04.
Не-рутовый пользователь с привилегиями sudo. Настроить такого пользователя вы можете с помощью инструкций в статье "Начальная настройка сервера на Ubuntu 16.04".
Внимание: Для установки Docker необходима 64-битная версия Ubuntu, а также версия ядра не ниже 3.10. 64-битный дроплет по умолчанию с Ubuntu 16.04 отвечает этим требованиям.**

Все команды, упомянутые в этой статье, должны выполняться от имени не-рутового пользователя. Если для выполнения команды необходимы привилегии root, эта команда будет выполняться с sudo. Статья Начальная настройка сервера с Ubuntu 16.04 описывает процесс добавления пользователей и наделения их привилегиями sudo.

>Шаг 1 — Установка Docker
Пакет установки Docker, доступный в официальном репозитории Ubuntu 16.04, может быть не самой последней версии. Для получения последней версии необходимо устанавливать Docker из официального репозитория Docker. Далее мы опишем процесс такой установки.

Для начала обновим базу данных пакетов:

<pre>sudo apt-get update</pre>

Теперь установим Docker. Добавьте ключ GPG официального репозитория Docker в вашу систему:

<pre>sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D</pre>

Добавим репозиторий Docker в список источников пакетов утилиты APT:

<pre>sudo apt-add-repository 'deb https://apt.dockerproject.org/repo ubuntu-xenial main'</pre>

Обновим базу данных пакетов информацией о пакетах Docker из вновь добавленного репозитория:

<pre>sudo apt-get update</pre>

Убедимся, что мы собираемся установить Docker из репозитория Docker, а не из репозитория по умолчанию Ubuntu 16.04:

<pre>apt-cache policy docker-engine</pre>

В результате вы должны увидеть вывод, похожий на этот:

Вывод команды **apt-cache policy docker-engine**

<pre>docker-engine:
  Installed: (none)
  Candidate: 1.11.1-0~xenial
  Version table:
     1.11.1-0~xenial 500
        500 https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages
     1.11.0-0~xenial 500
        500 https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages</pre>
        

Обратите внимание, что docker-engine не установлен, для установки будет использован репозиторий Docker для Ubuntu 16.04. Версия docker-engine может отличаться от указанной в нашем примере.

Далее, наконец-то, установим Docker:

<pre>sudo apt-get install -y docker-engine</pre>

>После завершения выполнения этой команды Docker должен быть установлен, демон запущен, и процесс должен запускаться при загрузке системы. Проверим, что процесс запущен:

**sudo systemctl status docker**
Вывод должен быть похож на представленный ниже, сервис должен быть запущен и активен:

Вывод
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2016-05-01 06:53:52 CDT; 1 weeks 3 days ago
     Docs: https://docs.docker.com
 Main PID: 749 (docker)
При установке Docker мы получаем не только сервис (демон) Docker, но и утилиту командной строки docker или клиент Docker. Мы рассмотрим использование утилиты docker далее в этой статье.

>Шаг 2 — Использование команды Docker без прав sudo (опционально)
По умолчанию, запуск команды docker требует привилегий root, что означает, что вы должны использовать sudo. Также эта команда может запускаться пользователем, включённым в группу docker, которая автоматически создаётся при установке Docker. При попытке использования команды docker пользователем без привилегий sudo или пользователем, не входящим в группу docker, вы получите такой результат:

Вывод
<pre>docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.</pre>
See 'docker run --help'.
Для того, чтобы не вводить sudo каждый раз при запуске docker, добавьте имя своего пользователя в группу docker:

<pre>sudo usermod -aG docker $USER</pre>
Для применения этих изменений вам необходимо разлогиниться и залогиниться в ваш дроплет.

Если вы хотите добавить произвольного пользователя в группу docker, вы можете указать имя пользователя в явном виде:

<pre>sudo usermod -aG docker username</pre>
>Далее в этой статье мы будем считать, что вы используете команду docker пользователем, находящимся в группе docker. Если вы не хотите добавлять своего пользователя в группу docker, пишите sudo для выполнения команд docker.

>Шаг 3 — Использование команды Docker
Теперь, когда Docker установлен, ознакомимся с возможностями его утилиты командной строки. Утилита позволяет использовать различные опции, команды и аргументы. Общий вид синтаксиса выглядит следующим образом:

**docker [опция] [команда] [аргументы]**

Для просмотра всех доступных подкоманд введите:

`docker`

Для Docker 1.11.1 полный список подкоманд включает:

Вывод
<pre>
attach    Attach to a running container
build     Build an image from a Dockerfile
commit    Create a new image from a container's changes
cp        Copy files/folders between a container and the local filesystem
create    Create a new container
diff      Inspect changes on a container's filesystem
events    Get real time events from the server
exec      Run a command in a running container
export    Export a container's filesystem as a tar archive
history   Show the history of an image
images    List images
import    Import the contents from a tarball to create a filesystem image
info      Display system-wide information
inspect   Return low-level information on a container or image
kill      Kill a running container
load      Load an image from a tar archive or STDIN
login     Log in to a Docker registry
logout    Log out from a Docker registry
logs      Fetch the logs of a container
network   Manage Docker networks
pause     Pause all processes within a container
port      List port mappings or a specific mapping for the CONTAINER
ps        List containers
pull      Pull an image or a repository from a registry
push      Push an image or a repository to a registry
rename    Rename a container
restart   Restart a container
rm        Remove one or more containers
rmi       Remove one or more images
run       Run a command in a new container
save      Save one or more images to a tar archive
search    Search the Docker Hub for images
start     Start one or more stopped containers
stats     Display a live stream of container(s) resource usage statistics
stop      Stop a running container
tag       Tag an image into a repository
top       Display the running processes of a container
unpause   Unpause all processes within a container
update    Update configuration of one or more containers
version   Show the Docker version information
volume    Manage Docker volumes
wait      Block until a container stops, then print its exit code</pre>
Для просмотра подробностей использования каждой из подкоманд используйте следующий формат:

<pre>docker docker-subcommand --help</pre>
Для просмотра общей справки Docker используйте следующую команду:

<pre>docker info</pre>

##[Docker compose](https://docs.docker.com/compose/install/#install-compose)
 
For install docker-compose you need:
<pre>sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose</pre>

And for give access to execute docker-compose command need:
<pre>sudo chmod +x /usr/local/bin/docker-compose</pre>

<small>[Guide](https://docs.docker.com/compose/gettingstarted/) for detail learning</small>

###For more comfortable use docker, include this command to your system. After you can use name container in browser address line.

<pre>sed -i.old -r '/docker-ips/d' /etc/hosts
docker inspect -f "{{.NetworkSettings.IPAddress}}   {{.Name}}   #docker-ips" $(docker ps -q) | sed -e 's/\///' >> /etc/hosts</pre>