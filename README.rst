django-fsm-admin-maintained
===========================

Mixin and template tags to integrate django-fsm_ state transitions into the
Django Admin.

Note: This is a fork of https://github.com/gadventures/django-fsm-admin
which is not maintained anymore. This fork is compatible with Django 4.

Installation
------------

.. code:: sh

   $ pip install django-fsm-admin-maintained

Or from GitHub:

.. code:: sh

   $ pip install -e git://github.com/7tg/django-fsm-admin-maintained.git#egg=django-fsm-admin-maintained

Usage
-----

1. Add ``fsm_admin`` to your ``INSTALLED_APPS``.

2. Ensure that you have ``"django.core.context_processors.request"`` in your
   ``TEMPLATE_CONTEXT_PROCESSORS`` in Django settings. If the setting variable
   is not yet defined, add:

.. code:: python

   from django.conf import settings

   TEMPLATE_CONTEXT_PROCESSORS = settings.TEMPLATE_CONTEXT_PROCESSORS + (
       "django.core.context_processors.request",
   )

3. In your ``admin.py`` file, use ``FSMTransitionMixin`` to add behaviour to your
   ModelAdmin. ``FSMTransitionMixin`` should be before ``ModelAdmin``, the order is
   important.

It assumes that your workflow state field is named ``state``, however you can
override it or add additional workflow state fields with the attribute
``fsm_field``.

.. code:: python

   from fsm_admin.mixins import FSMTransitionMixin

   class YourModelAdmin(FSMTransitionMixin, admin.ModelAdmin):
       # The name of one or more FSMFields on the model to transition
       fsm_field = ['wf_state',]

   admin.site.register(YourModel, YourModelAdmin)

4. By adding ``custom=dict(admin=False)`` to the transition decorator, one can
   disallow a transition to show up in the admin interface. This specially is
   useful, if the transition method accepts parameters without default values,
   since in **django-fsm-admin-maintained** no arguments can be passed into the transition
   method.

.. code:: python

    @transition(
       field='state',
       source=['startstate'],
       target='finalstate',
       custom=dict(admin=False),
    )
    def do_something(self, param):
       # will not add a button "Do Something" to your admin model interface

By adding ``FSM_ADMIN_FORCE_PERMIT = True`` to your configuration settings, the
above restriction becomes the default. Then one must explicitly allow that a
transition method shows up in the admin interface.

.. code:: python

   @transition(
       field='state',
       source=['startstate'],
       target='finalstate',
       custom=dict(admin=True),
   )
   def proceed(self):
       # will add a button "Proceed" to your admin model interface

This is useful, if most of your state transitions are handled by other means,
such as external events communicating with the API of your application.

Try the example
---------------

.. code:: sh

   $ git clone git@github.com:7tg/django-fsm-admin-maintained.git
   $ cd django-fsm-admin-maintained
   $ mkvirtualenv fsm_admin
   $ pip install -r requirements.txt
   $ python setup.py develop
   $ cd example
   $ python manage.py syncdb
   $ python manage.py runserver


.. _django-fsm: https://github.com/kmmbvnr/django-fsm
