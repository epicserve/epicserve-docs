Django-celery + Redis on OSX Lion
=================================

Installation and Setup
----------------------

1. Install redis on OSX (10.7) Lion::

        $ brew install redis

2. In the project and virtualenv I wanted to use django-celery in I installed the following::

        $ pip install django-celery
        $ pip install redis

3. Add ``djcelery`` to your ``INSTALLED_APPS`` in your Django ``settings.py`` file.

4. Added the following django-celery settings toward the end of my Django ``settings.py`` file. ::

        BROKER_HOST = "localhost"
        BROKER_BACKEND="redis"
        REDIS_PORT=6379
        REDIS_HOST = "localhost"
        BROKER_USER = ""
        BROKER_PASSWORD =""
        BROKER_VHOST = "0"
        REDIS_DB = 0
        REDIS_CONNECT_RETRY = True
        CELERY_SEND_EVENTS=True
        CELERY_RESULT_BACKEND='redis'
        CELERY_TASK_RESULT_EXPIRES =  10
        CELERYBEAT_SCHEDULER="djcelery.schedulers.DatabaseScheduler"

5. In your local development settings file it might be good to add ``CELERY_ALWAYS_EAGER = True``. This blocks the run tests (sans celery) that way you can test and develop easier.

6. Open a terminal window and start redis. ::

        $ redis-server /usr/local/etc/redis.conf

7. Open another terminal window and start a celery worker server for testing. ::

        $ python manage.py celeryd -l info


Example Task
------------

- Add the following code in a ``tasks.py`` file in a folder for one of your apps that's in your ``INSTALLED_APPS`` in your Django ``settings.py`` file. ::

        from celery.decorators import task

        @task()
        def add(x, y):
            return x + y

- Now you should be able to play around with Django-celery from the command line. Open another terminal window and do the following::

        $ django-admin.py shell
        >>> result = add.delay(4, 4)
        >>> result.ready() # returns True if the task has finished processing.
        False
        >>> result.result # task is not ready, so no return value yet.
        None
        >>> result.get()   # Waits until the task is done and returns the retval.
        8
        >>> result.result # direct access to result, doesn't re-raise errors.
        8
        >>> result.successful() # returns True if the task didn't end in failure.
        True
