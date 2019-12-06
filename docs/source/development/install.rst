
Установка и запуск
==================

Стандартное окружение для игры.

* Ubuntu
* Postgres
* RabbitMQ
* Redis
* Python + Django
* Postfix
* Nginx


Установка (для разработки)
**************************

Окружение для разработки подымается в виртуалке с помощью Vargant. По умолчанию в виртуалке 4Gb RAM и 2 ядра процессора. Если этого много или мало, можно поправить в файле ``Vagrantfile``.

.. code-block:: bash

   mkdir ./the-tale-project
   cd ./the-tale-project

.. tabs::

   .. code-tab:: Bash Clone with HTTPS

      git clone https://github.com/Tiendil/the-tale.git
      git clone https://github.com/Tiendil/deworld.git
      git clone https://github.com/Tiendil/questgen.git


   .. code-tab:: Bash Clone with SSH

      git clone git@github.com:the-tale/the-tale.git
      git clone git@github.com:the-tale/deworld.git
      git clone git@github.com:the-tale/questgen.git

.. code-block:: bash

   # при необходимости переключаем репозитории в ветки develop

   # устанавливаем Virtualbox отсюда: https://www.virtualbox.org/wiki/Linux_Downloads
   # возможно понадобится перезагрузка машины для загрузки ядром модуля virtualbox
   # устанавливаем Vagrant отсюда: https://www.vagrantup.com/downloads.html

   # доставляем необходимые пакеты
   sudo apt-get install build-essential libssl-dev libffi-dev python-dev ruby

   cd ./the-tale/deploy/

   sudo pip install ansible

   ansible-galaxy install -r requirements.yml

   vagrant plugin install vagrant-hostmanager
   vagrant plugin install vagrant-disksize

   # создаём виртуальную машину, запускаем и устанавливаем на неё всё необходимое
   # при первом запуске будет вызван vagrant provision
   vagrant up

   # для обновления софта на виртуальной машине
   vagrant provision


Опциональные репозитории
************************

Часть проектов, родившихся в рамках разработки, доросли до стабильной версии и хостятся на `pypi.org <http://pypi.org>`_.

Если необходимо делать правки в них (например, добавить новую функциональность), их следует клонировать по аналогии с обязательными репозиториями и вручную поставить из исходников в виртуальной машине.

Репозитории:

- генератор имён персонажей: https://github.com/Tiendil/pynames.git
- продвинутые перечисления: https://github.com/Tiendil/rels.git
- генератор текста: https://github.com/Tiendil/utg.git
- умные импорты для Python: https://github.com/Tiendil/smart-imports.git


Нюансы конфигурации
*******************

Настройка форума проводится через админку Django.

Права пользователей также настраиваются через админку Django.

Админка Django доступна по адресу ``https://local.the-tale/admin``

После настройки, в базе игры не будет фраз для лингвистики, вместо них будут отображаться заглушки, описывающие тип фразы и её параметры. Фразы необходимо добавлять руками.


Запуск веб-сервера
******************

Запуск веб-сервера осуществляется в самой виртуалке

.. code-block:: bash

   # для захода в виртуалку выполняем из папки deploy
   vagrant ssh

   sudo su the_tale
   cd ~/current
   . ./venv/bin/activate

   django-admin runserver 0.0.0.0:8000 --settings the_tale.settings


Сайт игры будет доступен локально по адресу ``https://local.the-tale``

.. warning::

   В окружении разработчика используется `самоподписанный сертификат <https://en.wikipedia.org/wiki/Self-signed_certificate>`_, поэтому браузеры будут сообщать о «небезопасном соединении». Это нормально (для окружения разработчика). Если вы хотите избавиться от этого предупреждения, импортируйте сертификат к себе в систему. Он находится тут: ``<repository>/deploy/provisioning/files/nginx_certificates/local.the-tale/``.


Управление фоновыми рабочими
****************************

Перед запуском рабочих, необходимо запустить supervisor

.. code-block:: bash

   sudo su
   systemctl start supervisor


Конфигурация supervisor для запуска рабочих находится в файле ``/etc/supervisor/conf.d/the-tale.conf``

Запуск рабочих осуществляется с помощью supervisor

.. code-block:: bash

   supervisorctl start all      # запустить все
   supervisorctl start game:    # запустить рабочих самой игры (логика игры)
   supervisorctl start portal:  # запустить сервисных рабочих (регистрация, рассылки, платежи и так далее)
   supervisorctl start service: # запустить остальных сервисов (рынок, личные сообщения, дневник героя, предметы игрока и так далее)


Если есть проблемы с запуском (нет вывода после ввода команды или пишет, что процесс не найден),
необходимо обновить конфигурацию виртуалки.

Текущая конфигурация рабочих описана в файле ``./the_tale/amqp_environment.py``

Каждый рабочий ведёт свой лог в каталоге ``/var/logs/the-tale/``

**Внимание:** каждый процесс рабочего сейчас занимает около 70mb оперативной памяти, если запускаете всех, убедитесь, что на виртуальной машине достаточно памяти.


Первый пользователь
*******************

Первый пользователь создаётся автоматически со следующими параметрами:

:ник: superuser
:почта: superuser@example.com
:пароль: 111111


Запуск тестов
*************

Тесты игры
----------

Для работы тестов необходимо запустить группу service: в супервизоре.

.. code-block:: bash

   sudo supervisorctl start service:


Запуск всех тестов (работают долго!):

.. code-block:: bash

   sudo su the_tale
   cd ~/current
   source ./venv/bin/activate
   django-admin utils_run_tests --settings the_tale.settings


Запуск тестов конкретного приложения (для пример, the_tale.game.jobs):

.. code-block:: bash

   sudo su the_tale
   cd ~/current
   source ./venv/bin/activate
   django-admin test --nomigrations the_tale.game.jobs.tests --settings the_tale.settings


Тесты сервисов
--------------

.. code-block:: bash

   sudo su <пользователь сервиса>
   cd ~/current
   source ./venv/bin/activate
   python -m unittest discover <основной python пакет сервиса>
