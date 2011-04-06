.. _faq:

==========================
Frequently Asked Questions
==========================

This FAQ serves two purposes. Firstly, it does what a FAQ generally does --
answer frequently asked questions. Secondly, it is also a place to dump
fragments of documentation which haven't matured enough to be moved into
their own documentation file.



Should I extend the builtin modules and contents, or should I write my own?
===========================================================================

The answer is, as often, the nearly useless "It depends". The built-in modules
serve two purposes: On one hand, they should be ready to use and demonstrate
the power of FeinCMS. On the other hand, they should be simple enough to serve
as examples for you if you want to build your own CMS-like system using the
tools provided by FeinCMS.

If a proposed feature greatly enhances the modules' or content types'
abilities without adding heaps of code, chances are pretty good that it will
be accepted into FeinCMS core. Anyway, the tools included should be so easy
to use that you might still want to build your own page CMS, if your needs
are very different from those of the original authors. If you don't like
monkey patching at all, or if the list of extensions you want to use grows
too big, it might be time to reconsider whether you really want to use the
extension mechanism or if it might not be easier to start freshly, only
using the editor admin classes, feincms.models.Base and maybe parts of the
included PageManager...



I get a ``mptt.AlreadyRegistered`` exception when I try using the :mod:`~feincms.module.page` module!
=====================================================================================================

This happens when the Django model loading code encounters an ``ImportError``
when it loads the :mod:`~feincms.module.page` module for the first time. The
module is added to a list of postponed modules, and the import is retried
after all other applications have been successfully imported. This will
cause the ``mptt.register`` call to be executed twice though, which is why
you see this exception. We could catch this exception and go on as if
nothing had happened, but this hides a deeper problem somewhere which should
be fixed for good instead of papering over the issue.

There are several ways how you might find out what's going wrong. Raise the
debugging level, try importing the page module (and other modules) from the
shell you get when you use ``./manage.py shell`` are some possibilities how
you might debug this problem.



I run ``syncdb`` and get a message about missing columns in the page table
==========================================================================

You enabled the page module (added :mod:`feincms.module.page` to
``INSTALLED_APPS``), run syncdb, and afterwards registered a few
extensions. The extensions you activated
(:mod:`~feincms.module.page.extensions.datepublisher` and
:mod:`~feincms.module.page.extensions.translations`) add new fields to
the page model, but your first ``syncdb`` did not know about them and
therefore did not create the columns for those extensions.

You can either remove the line ``Page.register_extensions(...)`` from
your code or drop the page_page table and re-run ``syncdb``. If you want
to keep the pages you've already created, you need to figure out the
correct ALTER TABLE statements for your database yourself.