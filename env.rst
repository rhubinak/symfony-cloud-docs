Environment Variables
=====================

Platform.sh exposes information to your application via `environment variables`_, including database credentials, the SMTP address, and more.

Platform.sh Environment Variables
----------------------------------

Platform.sh `exposes environment variables`_ about the application and infrastructure. In addition, Symfony projects exposes some more:

* ``SYMFONY_CACHE_DIR`` (only available during the build hook execution): The absolute path to a subdirectory of our build cache directory.

  The build cache directory is persisted between builds, but is **not** deployed. It’s a good place to store build artifacts, such as downloaded files, that can be reused between builds.

  .. note::

     This directory is shared by **all** builds on **all** branches, make sure your build hook accounts for that.

  .. tip::

     If you need to clear the build cache directory, use the ``cloud:project:clear-build-cache`` command.

* ``SYMFONY_PROJECT_DEFAULT_ROUTE_URL`` (only defined at **runtime**): The default endpoint serving your project. It can be used to avoid hard-coding domains that can be used to reach non-production environments. Parts of the URL are also exposed as their own variables whose name starts with ``SYMFONY_PROJECT_DEFAULT_ROUTE_`` followed by the name of the part (``SCHEME``, ``DOMAIN``, ``PORT``, and ``PATH``).

  Guessing the default endpoint is usually straightforward but can become complicated for multi-routes or multi-applications projects. For these cases, the following   preference order is used:

  #. project wide route defined only by ``{default}`` or ``{all}`` (no path)
  #. project wide route defined by ``www.{default}`` or ``www.{all}`` (no path)
  #. route for the **current application** including ``{default}`` or ``{all}`` (might include a path)
  #. route for the **current application** including ``www.{default}`` or ``www.{all}`` (might include a path)
  #. first route for the current application
  #. first route for the whole project

  When several routes match a rule, the first one wins, the user order is kept. There's no preference regarding protocols.

  .. tip::

    For multi-applications projects where several applications are publicly reachable, but one needs to determine the current application endpoint (for webhooks for example) and the project endpoint (to send emails for instance), an additional ``SYMFONY_APPLICATION_DEFAULT_ROUTE_*`` environment variables set is available. The same rules are applied to determine their value but only routes matching the current application are evaluated.

For Symfony projects, information about services are also exposed via environment variables (see below to learn more about the specifics for each service type). List all available environment variables with the following command:

.. code-block:: terminal

    $ symfony ssh -- symfony var:export --multiline

The following sections lists all exposed environment variables that are automatically defined for each service. Each environment variable is prefixed by the relationship name. For instance, given the following relationships:

.. code-block:: yaml

    relationships:
        database: "securitydb:postgresql"

Environment variables for the database will be prefixed by ``DATABASE_``, the upper-cased version of the key defined in the relationship.

.. _email-env-vars:

Emails
------

The configuration is exposed via the following environment variables:

* ``MAILER_ENABLED``: 1 if outgoing emails are enabled, 0 otherwise
* ``MAILER_DSN``/``MAILER_URL``: The Symfony-compatible mailer URL
* ``MAILER_HOST``: The SMTP server host
* ``MAILER_PORT``: The SMTP server port
* ``MAILER_TRANSPORT``: The SMTP transport mode (``smtp``)
* ``MAILER_AUTH_MODE``: The SMTP auth mode (``plain``)
* ``MAILER_USER``: The SMTP server user
* ``MAILER_PASSWORD``: The SMTP server password

Symfony Mailer uses the value of ``MAILER_DSN`` automatically.

HTTP
----

If your project has multiple applications, the configuration is exposed via the following environment variables (where ``SOME_SERVICE`` is the upper-cased version of the key defined in the relationship):

* ``SOME_SERVICE_URL``: The full URL of the service
* ``SOME_SERVICE_IP``: The HTTP service IP
* ``SOME_SERVICE_PORT``: The HTTP service port
* ``SOME_SERVICE_SCHEME``: The HTTP service scheme
* ``SOME_SERVICE_HOST``: The HTTP service host

MySQL/MariaDB
-------------

`MySQL/MariaDB`_ configuration is exposed via the following environment variables (where ``DATABASE`` is the upper-cased version of the key defined in the relationship above):

* ``DATABASE_URL``: The database URL (in the PHP or Go format depending on your application)
* ``DATABASE_SERVER``: The database server
* ``DATABASE_DRIVER``: The database driver
* ``DATABASE_VERSION``: The database version
* ``DATABASE_HOST``: The database host
* ``DATABASE_PORT``: The database port
* ``DATABASE_NAME``: The database name
* ``DATABASE_DATABASE``: Alias for ``DATABASE_NAME``
* ``DATABASE_USERNAME``: The database username
* ``DATABASE_PASSWORD``: The database password

.. tip::

    The database version and a default charset is included in the database URL. Override them using the ``DATABASE_VERSION`` and ``DATABASE_CHARSET`` environment variables respectively.

PostgreSQL
----------

`PostgreSQL`_ configuration is exposed via the following environment variables (where ``DATABASE`` is the upper-cased version of the key defined in the relationship):

* ``DATABASE_URL``: The database URL (in the PHP or Go format depending on your application)
* ``DATABASE_SERVER``: The database server
* ``DATABASE_DRIVER``: The database driver
* ``DATABASE_VERSION``: The database version
* ``DATABASE_HOST``: The database host
* ``DATABASE_PORT``: The database port
* ``DATABASE_NAME``: The database name
* ``DATABASE_DATABASE``: Alias for ``DATABASE_NAME``
* ``DATABASE_USERNAME``: The database username
* ``DATABASE_PASSWORD``: The database password

.. tip::

    The database version and a default charset is included in the database URL. Override them using the ``DATABASE_VERSION`` and ``DATABASE_CHARSET`` environment variables respectively.

Redis
-----

`Redis`_ configuration is exposed via the following environment variables (where ``REDIS`` is the upper-cased version of the key defined in the relationship):

* ``REDIS_URL``: The Redis URL
* ``REDIS_HOST``: The Redis host
* ``REDIS_PORT``: The Redis port
* ``REDIS_SCHEME``: The Redis scheme

Memcached
---------

`Memcached`_ configuration is exposed via the following environment variables (where ``CACHE`` is the upper-cased version of the key defined in the relationship):

* ``CACHE_HOST``
* ``CACHE_PORT``
* ``CACHE_IP``

Elasticsearch
-------------

`Elasticsearch`_ configuration is exposed via the following environment variables (where ``ELASTICSEARCH`` is the upper-cased version of the key defined in the relationship):

* ``ELASTICSEARCH_URL``: The full URL of the Elasticsearch service
* ``ELASTICSEARCH_HOST``: The Elasticsearch host
* ``ELASTICSEARCH_PORT``: The Elasticsearch port
* ``ELASTICSEARCH_SCHEME``: The Elasticsearch protocol scheme (``http`` or ``https``)

Solr
----

`Apache Solr`_ configuration is exposed via the following environment variables (where ``SOLR`` is the upper-cased version of the key defined in the relationship):

* ``SOLR_HOST``: The Solr host
* ``SOLR_PORT``: The Solr port
* ``SOLR_NAME``: The Solr name
* ``SOLR_DATABASE``: An alias for ``SOLR_NAME``

RabbitMQ
--------

`RabbitMQ`_ configuration is exposed via the following environment variables (where ``RABBITMQ`` is the upper-cased version of the key defined in the relationship):

* ``RABBITMQ_URL``: The RabbitMQ standardized URL
* ``RABBITMQ_SERVER``: The RabbitMQ server
* ``RABBITMQ_HOST``: The RabbitMQ host
* ``RABBITMQ_PORT``: The RabbitMQ port
* ``RABBITMQ_SCHEME``: The RabbitMQ scheme
* ``RABBITMQ_USER``: The RabbitMQ username
* ``RABBITMQ_USERNAME``: The RabbitMQ username
* ``RABBITMQ_PASSWORD``: The RabbitMQ password

MongoDB
-------

`MongoDB`_ configuration is exposed via the following environment variables (where ``MONGODB`` is the upper-cased version of the key defined in the relationship):

* ``MONGODB_SERVER``
* ``MONGODB_HOST``
* ``MONGODB_PORT``
* ``MONGODB_SCHEME``
* ``MONGODB_NAME``
* ``MONGODB_DATABASE``
* ``MONGODB_USER``
* ``MONGODB_USERNAME``
* ``MONGODB_PASSWORD``

InfluxDB
--------

`InfluxDB`_ configuration is exposed via the following environment variables (where ``TIMEDB`` is the upper-cased version of the key defined in the relationship):

* ``TIMEDB_SCHEME``
* ``TIMEDB_HOST``
* ``TIMEDB_PORT``
* ``TIMEDB_IP``

Kafka
-----

`Apache Kafka`_ configuration is exposed via the following environment variables (where ``KAFKA`` is the upper-cased version of the key defined in the relationship):

* ``KAFKA_URL``
* ``KAFKA_SCHEME``
* ``KAFKA_HOST``
* ``KAFKA_PORT``
* ``KAFKA_IP``

.. _`environment variables`: https://docs.platform.sh/development/variables.html
.. _`exposes environment variables`: https://docs.platform.sh/development/variables/use-variables.html#use-platformsh-provided-variables
.. _`MySQL/MariaDB`: https://docs.platform.sh/configuration/services/mysql.html
.. _`Apache Kafka`: https://docs.platform.sh/configuration/services/kafka.html
.. _`InfluxDB`: https://docs.platform.sh/configuration/services/influxdb.html
.. _`MongoDB`: https://docs.platform.sh/configuration/services/mongodb.html
.. _`RabbitMQ`: https://docs.platform.sh/configuration/services/rabbitmq.html
.. _`Elasticsearch`: https://docs.platform.sh/configuration/services/elasticsearch.html
.. _`Apache Solr`: https://docs.platform.sh/configuration/services/solr.html
.. _`Memcached`: https://docs.platform.sh/configuration/services/memcached.html
.. _`Redis`: https://docs.platform.sh/configuration/services/redis.html
.. _`PostgreSQL`: https://docs.platform.sh/configuration/services/postgresql.html
