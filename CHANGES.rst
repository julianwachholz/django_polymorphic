*django_polymorphic*
++++++++++++++++++++
Changelog
++++++++++

.

------------------------------------------------------------------

2010-10-18
==========

This is a V1.0 Beta/Testing Release
-----------------------------------

This release is mostly a cleanup and maintenance release that also
improves a number of minor things and fixes one (non-critical) bug.

Some pending API changes and corrections have been folded into this release
in order to make the upcoming V1.0 API as stable as possible.

This release is also about getting feedback from you in case you don't
approve of any of these changes or would like to get additional
API fixes into V1.0.

The release contains a considerable amount of changes in some of the more
critical parts of the software. It's intended for testing and development
environments and not for production environments. For these, it's best to
wait a few weeks for the proper V1.0 release, to allow some time for any
potential problems to turn up (if they exist).

If you encounter any such problems, please post them in the discussion group
or open an issue on GitHub or BitBucket (or send me an email).

There also have been a number of minor API changes.
Please see the README for more information.

New Features
------------------------

*   official Django 1.3 alpha compatibility

*   ``PolymorphicModel.__getattribute__`` hack removed.
    The python __getattribute__ hack generally causes a considerable
    overhead and to have this in the performance-sensitive PolymorphicModel
    class was somewhat problematic. It's gone for good now.

*   ``polymorphic_dumpdata`` management command functionality removed:
    The regular Django dumpdata command now automatically works correctly
    for polymorphic models with all Django versions.

*   .get_real_instances() has been elevated to an official part of the API::

        real_objects = ModelA.objects.get_real_instances(base_objects_list_or_queryset)

    allows you to turn a queryset or list of base objects into a list of the real instances.
    This is useful if e.g. you use ``ModelA.base_objects.extra(...)`` and then want to
    transform the result to its polymorphic equivalent.

*   ``translate_polymorphic_Q_object``  (see DOCS)

*   improved testing

*   Changelog added: CHANGES.rst/html

Bugfixes
------------------------

*   removed requirement for primary key to be an IntegerField.
    Thanks to Mathieu Steele and Malthe Borch.

API Changes
-----------

polymorphic_dumpdata
####################

The polymorphic_dumpdata management command is not needed anymore
and has been removed, as the regular Django dumpdata command now automatically
works correctly with polymorphic models (for all supported versions of Django).

Output of Queryset or Object Printing
#####################################

In order to improve compatibility with vanilla Django, printing quersets does not use
django_polymorphic's pretty printing by default anymore.
To get the old behaviour when printing querysets, you need to replace your model definition:

>>> class Project(PolymorphicModel):

by:

>>> class Project(PolymorphicModel, ShowFieldType):

The mixin classes for pretty output have been renamed:

    ``ShowFieldTypes, ShowFields, ShowFieldsAndTypes``

are now:

    ``ShowFieldType, ShowFieldContent and ShowFieldTypeAndContent``

(the old ones still exist for compatibility)

Running the Test suite with Django 1.3
######################################

Django 1.3 requires ``python manage.py test polymorphic`` instead of
just ``python manage.py test``.


------------------------------------------------------------------

2010-2-22
==========

IMPORTANT: API Changed (import path changed), and Installation Note

The django_polymorphic source code has been restructured
and as a result needs to be installed like a normal Django App
- either via copying the "polymorphic" directory into your
Django project or by running setup.py. Adding 'polymorphic'
to INSTALLED_APPS in settings.py is still optional, however.

The file `polymorphic.py` cannot be used as a standalone
extension module anymore, as is has been split into a number
of smaller files.

Importing works slightly different now: All relevant symbols are
imported directly from 'polymorphic' instead from
'polymorphic.models'::

    # new way
    from polymorphic import PolymorphicModel, ...

    # old way, doesn't work anymore
    from polymorphic.models import PolymorphicModel, ...

+ minor API addition: 'from polymorphic import VERSION, get_version'

New Features
------------------------

Python 2.4 compatibility, contributed by Charles Leifer. Thanks!

Bugfixes
------------------------

Fix: The exception "...has no attribute 'sub_and_superclass_dict'"
could be raised. (This occurred if a subclass defined __init__
and accessed class members before calling the superclass __init__).
Thanks to Mattias Brändström.

Fix: There could be name conflicts if
field_name == model_name.lower() or similar.
Now it is possible to give a field the same name as the class
(like with normal Django models).
(Found through the example provided by Mattias Brändström)



------------------------------------------------------------------

2010-2-4
==========

New features (and documentation)
-----------------------------------------

queryset order_by method added

queryset aggregate() and extra() methods implemented

queryset annotate() method implemented

queryset values(), values_list(), distinct() documented; defer(),
only() allowed (but not yet supported)

setup.py added. Thanks to Andrew Ingram.

More about these additions in the docs:
http://bserve.webhop.org/wiki/django_polymorphic/doc

Bugfixes
------------------------

*   fix remaining potential accessor name clashes (but this only works
    with Django 1.2+, for 1.1 no changes). Thanks to Andrew Ingram.

*   fix use of 'id' model field, replaced with 'pk'.

*   fix select_related bug for objects from derived classes (till now
    sel.-r. was just ignored)

"Restrictions & Caveats" updated
----------------------------------------

*   Django 1.1 only - the names of polymorphic models must be unique
    in the whole project, even if they are in two different apps.
    This results from a restriction in the Django 1.1 "related_name"
    option (fixed in Django 1.2).

*   Django 1.1 only - when ContentType is used in models, Django's
    seralisation or fixtures cannot be used. This issue seems to be
    resolved for Django 1.2 (changeset 11863: Fixed #7052, Added
    support for natural keys in serialization).



------------------------------------------------------------------

2010-1-30
==========

Fixed ContentType related field accessor clash (an error emitted
by model validation) by adding related_name to the ContentType
ForeignKey. This happened if your polymorphc model used a ContentType
ForeignKey. Thanks to Andrew Ingram.



------------------------------------------------------------------

2010-1-29
==========

Restructured django_polymorphic into a regular Django add-on
application. This is needed for the management commands, and
also seems to be a generally good idea for future enhancements
as well (and it makes sure the tests are always included).

The ``poly`` app - until now being used for test purposes only
- has been renamed to ``polymorphic``. See DOCS.rst
("installation/testing") for more info.



------------------------------------------------------------------

2010-1-28
==========

Added the polymorphic_dumpdata management command (github issue 4),
for creating fixtures, this should be used instead of
the normal Django dumpdata command.
Thanks to Charles Leifer.

Important: Using ContentType together with dumpdata generally
needs Django 1.2 (important as any polymorphic model uses
ContentType).



------------------------------------------------------------------

2010-1-26
==========

IMPORTANT - database schema change (more info in change log).
I hope I got this change in early enough before anyone started
to use polymorphic.py in earnest. Sorry for any inconvenience.
This should be the final DB schema now.

Django's ContentType is now used instead of app-label and model-name
This is a cleaner and more efficient solution
Thanks to Ilya Semenov for the suggestion.