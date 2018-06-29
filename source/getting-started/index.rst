
.. _getting-started:

===================
Getting Started
===================

This guide will walk through the initial steps of getting account, and preparing yourself for the Abaco user guide tutorial.

.. contents:: :local:

------------------------------------------
Account Creation and Software Installation
------------------------------------------

Create a TACC account
^^^^^^^^^^^^^^^^^^^^^

Abaco is an API platform hosted by the Texas Adavanced Computing Center(TACC). TACC designs and deploys the world's most powerful advanced computing technologies and innovative software solutions to enable reseachers to anwser complex questions.

To use the hosted Abaco service, please create a `TACC account <https://portal.tacc.utexas.edu/account-request>`__ .

Create a Docker account
^^^^^^^^^^^^^^^^^^^^^^^^

**Docker** is a computer program that performs operating-system-level virtualization also known as containerization. it is developed by  `Docker,Inc <https://www.docker.com/what-docker>`__ .

Abaco pulls images for its actors from the public Docker Hub. To register actors you will need to publish images on Docker Hub, which requires a docker account `Click Here <https://hub.docker.com/>`__ . 

Install the TACC Cloud Python SDK
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To interact with the Tacc hosted Aboaco platform in Python, we will install the Python SDK. Simply run:

.. code-block:: bash

  $ pip install agavepy
  
-----------------------
Create an OAuth Client
-----------------------

Authentication and authorization to the TACC Cloud APIs uses OAuth2`_, a widely-adopted web standard. Our implementation of Oauth2 is designed to give you the flexibility you need to script and automate use of TACC Cloud while keeping your access credentials and digital assets secure.

This is covered in great detail in our Developer Documentation but some key concepts will be highlighted here,interleaved with Python code.

The first step is to create a python object called ``ag`` pointing to an API server. Your project likely has its own API server, which are discoverable using the ``tenants-list --rich`` command in the TACC cloud CLI. for now, we can assume (http://api.tacc.utexas.edu) the default value will work for you.

First, type in the following line in your shell:

.. code-block:: bash

  >>> from agavepy.agave import Agave


Next, type in the following line in your shell:

.. code-block:: bash

   >>> ag = Agave(api_server='http://api.tacc.utexas.edu')

Once the object is instantiated, interact with it according to the API documentation and your specific usage needs.Create a new Oauth client

.. code-block:: bash

  >>> ag = Agave(api_server='https://api.tacc.utexas.edu',
  ...            username='your username',
  ...            password='your password')
  >>> ag.clients.create(body={'clientName': 'enter a client name'})

You use the consumerKey and consumerSecret to generate Oauth tokens, which are temporary credentials that you can use in place of putting your real credentials into code that is scripting against the TACC APIs.

-------------------------------
Reuse an Existing Oauth Client
-------------------------------

Once you generate a client, you can re-use its key and secret. Clients can be created using the Python-based approach illustrated above, via the TACC Cloud CLI ``clients-create`` command, or by a direct, correctly-structured ``POST`` to the clients web service. No matter how you've created a client, setting AgavePy up to use it works the same way:

.. code-block:: bash

  >>> from agavepy.agave import Agave
  >>> ag = Agave(api_server='https://api.tacc.utexas.edu',
  ...            username='your username', password='your password',
  ...            client_name='my_client',
  ...            api_key='kV4XLPhVBAv9RTf7a2QyBHhQAXca',
  ...            api_secret='5EbjEOcyzzIsAAE3vBS7nspVqHQa')

Check client is working
^^^^^^^^^^^^^^^^^^^^^^^
The Agave object ``ag`` is now configured to talk to all TACC Cloud services. Here's an example: Let's retrieve a the curent user's **profile**.

.. code-block:: bash

 >>> ag.profiles.get()
 
------------------
Abaco Quickstart
------------------

Writing a Python Function
^^^^^^^^^^^^^^^^^^^^^^^^^^

Now, that we have the Agavpy package install, lets create a docker image that contains they python function and executes it as part of the default command. First create a python file called `Example.py` and paste the same code below in it.

.. code-block:: bash

  >>> def string_count():
         message = "Hey my name is john"
         words = message.split(' ')
         word_count = len(words)
         print('Number of words is: ' + str(word_count))
     string_count()

Building Images From a Dockerfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Next, create a docker image that contains the python function and execute it as part of the default command

We can build images from a text file called a Dockerfile. You can think of a Dockerfile as a recipe for creating images. The instructions within a dockerfile either add files/folders to the images, add metadata to the image, or both.

The FROM instruction
^^^^^^^^^^^^^^^^^^^^
we can use the ``FROM`` instruction to start our new image from a known image. this should be the first line of our Dockerfile.

.. code-block:: bash

  FROM python

The ADD instruction
^^^^^^^^^^^^^^^^^^^^^
We can also add local files to our image using the ``ADD`` instruction. We can add a the file ``Example.py`` in our local directory to the ``Users/kwhitley/PycharmProjects/Test`` directory in our container with the following instruction:

.. code-block:: bash

  ADD Example.py /Users/kwhitley/PycharmProjects/Test


The last step is write the command from running the application, which is simply - ``python ./Example.py``. We use the ``CMD`` command to do that:

.. code-block:: bash

  CMD ["python", "./Example.py"]

The primary purpose of ``CMD`` is to tell the container which command it should run when it is started. With that, our ``Dockerfile`` is now ready. This is what is looks like:

.. code-block:: bash

  FROM python

  ADD Example.py /Users/kwhitley/PycharmProjects/Test

  CMD ["python", "./Example.py"]


Now that we have our ``Dockerfile``, we can build our image. the `docker build` command does the heavy lifting of creating a Docker image from a ``Dockerfile``.

Actors
^^^^^^^

Now that we going to register a docker container as an actor, to do this we have to an API client and once we have this you only have to do the set up once!

To register a actor using the agavepy library, we use the `actors.add()` method and pass the same arguments through the `body` parameter. For example,

.. code-block:: bash

  >>> from agavepy.agave import Agave
  >>> ag = Agave(api_server='https://api.tacc.utexas.edu', token='<access_token>')
  >>> reactor = {"image": "abacosamples/test", "name": "test", "description": "My test actor using the abacosamples image r   egistered using agavepy.", "default_environment":{"key1": "value1", "key2": "value2"} }
  >>> ag.actors.add(body=reactor)
  
To get the message from `Abaco` do the following:

.. code-block:: bash

  >>> from agavepy.actors import get_contex
      def string_count():
          context = get_context()
          try:
              message = context['raw_message']
      except Exception as e:
              print("Got an exception parsing message. Aborting. Exception: {}".format(e))
        words = message = "Hey my name is john"
        words = message.split(' ')
        word_count = len(words)
        print('Number of words is: ' + str(word_count))
     string_count()
