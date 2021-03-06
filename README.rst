.. image:: https://travis-ci.org/yunojuno/django-appmail.svg?branch=master
    :target: https://travis-ci.org/yunojuno/django-appmail

.. image:: https://badge.fury.io/py/django-appmail.svg
    :target: https://badge.fury.io/py/django-appmail

.. image:: https://codecov.io/gh/yunojuno/django-appmail/branch/master/graph/badge.svg
    :target: https://codecov.io/gh/yunojuno/django-appmail

Django-AppMail
==============

Django app for managing transactional email templates

This project arose out of a project to integrate a large transactional Django
application with Mandrill, and the lessons learned. It also owes a minor h/t
to this project from 2011 (https://github.com/hugorodgerbrown/AppMail).

The core requirement is to provide an easy way to add / edit email templates
to a Django project, in such a way that it doesn't require a developer to make
changes. The easiest way to use templated emails in Django is to rely on the
in-built template structure, but that means that the templates are held in
files, under version control, which makes it very hard for non-developers to
edit.

This is **not** a WYSIWYG HTML editor, and it doesn't do anything clever. It
doesn't handle the sending of the emails - it simply provides a convenient
mechanism for storing and rendering email content.

.. code:: python

    from appmail.models import EmailTemplate

    def send_order_confirmation(order_id):
        order = Orders.objects.get(id=order_id)
        template = EmailTemplate.objects.current('order_confirmation')
        payload = { "order": order }
        message = template.create_message(
            context={'order': order},
            to=[order.recipient.email]
        )
        message.send()

The core requirements are:

1. List / preview existing templates
2. Edit subject line, plain text and HTML content
3. Use standard Django template syntax
4. Support base templates
5. Template versioning
6. Language support

**Template properties**

Individual templates are stored as model objects in the database. The
standard Django admin site is used to view / filter templates. The templates
are ordered by name, language and version. This combination is unique. The
language and version properties have sensible defaults (
`version=settings.LANGUAGE_CODE` and `version=0`) so don't need to set if you
don't require it. There is no inheritance or relationship between different
languages and versions - they are stored as independent objects.

.. code:: python

    # get the default order_summary email
    template = EmailTemplate.objects.current('order_summary')
    # get the french version
    template = EmailTemplate.objects.current('order_summary', language='fr')
    # get a specific version
    template = EmailTemplate.objects.version('order_summary', 1)

**Template syntax**

The templates themselves use standard Django template syntax, including
the use of tags, filters. There is nothing special about them, however there
is one caveat - template inheritance.

**Template inheritance**

Although the template content is not stored on disk, without re-engineering
the template rendering methods any parent templates must be. This is annoying,
but there is a valid assumption behind it - if you are changing your base
templates you are probably involving designers and developers already, so
having to rely on a developer to make the changes is acceptable.

Tests
-----

There is a test suite for the app, which is best run through `tox`.

Licence
-------

MIT

Contributing
------------

Usual rules apply:

1. Fork to your own account
2. Create a branch, fix the issue / add the feature
3. Submit PR

Please take care to follow the coding style - and PEP8.
