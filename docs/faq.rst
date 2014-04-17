Frequently Asked Questions (FAQ)
################################

Here are some frequently asked questions about Pelican.

What's the best way to communicate a problem, question, or suggestion?
======================================================================

If you have a problem, question, or suggestion, please start by striking up a
conversation on `#pelican on Freenode <irc://irc.freenode.net/pelican>`_.
Those who don't have an IRC client handy can jump in immediately via
`IRC webchat <http://webchat.freenode.net/?channels=pelican&uio=d4>`_. Because
of differing time zones, you may not get an immediate response to your
question, but please be patient and stay logged into IRC — someone will almost
always respond if you wait long enough (it may take a few hours).

If you're unable to resolve your issue or if you have a feature request, please
refer to the `issue tracker <https://github.com/getpelican/pelican/issues>`_.

How can I help?
================

There are several ways to help out. First, you can report any Pelican
suggestions or problems you might have via IRC or the `issue tracker
<https://github.com/getpelican/pelican/issues>`_. If submitting an issue
report, please first check the existing issue list (both open and closed) in
order to avoid submitting a duplicate issue.

If you want to contribute, please fork `the git repository
<https://github.com/getpelican/pelican/>`_, create a new feature branch, make
your changes, and issue a pull request. Someone will review your changes as
soon as possible. Please refer to the :doc:`How to Contribute <contribute>`
section for more details.

You can also contribute by creating themes and improving the documentation.

Is it mandatory to have a configuration file?
=============================================

Configuration files are optional and are just an easy way to configure Pelican.
For basic operations, it's possible to specify options while invoking Pelican
via the command line. See ``pelican --help`` for more information.

I'm creating my own theme. How do I use Pygments for syntax highlighting?
=========================================================================

Pygments adds some classes to the generated content. These classes are used by
themes to style code syntax highlighting via CSS. Specifically, you can
customize the appearance of your syntax highlighting via the ``.highlight pre``
class in your theme's CSS file. To see how various styles can be used to render
Django code, for example, use the style selector drop-down at top-right on the
`Pygments project demo site <http://pygments.org/demo/>`_.

You can use the following example commands to generate a starting CSS file from
a Pygments built-in style (in this case, "monokai") and then copy the generated
CSS file to your new theme::

    pygmentize -S monokai -f html -a .highlight > pygment.css
    cp pygment.css path/to/theme/static/css/

Don't forget to import your ``pygment.css`` file from your main CSS file.

How do I create my own theme?
==============================

Please refer to :ref:`theming-pelican`.

I'm using Markdown and getting ``No valid files found in content`` errors.
==========================================================================

Markdown is not a hard dependency for Pelican, so if you have content in
Markdown format, you will need to explicitly install the Markdown library.
You can do so by typing the following command, prepending ``sudo`` if
permissions require it::

    pip install markdown

If you don't have ``pip`` installed, consider installing it via::

    easy_install pip

Can I use arbitrary metadata in my templates?
==============================================

Yes. For example, to include a modified date in a Markdown post, one could
include the following at the top of the article::

    Modified: 2012-08-08

For reStructuredText, this metadata should of course be prefixed with a colon::

    :Modified: 2012-08-08

This metadata can then be accessed in templates such as ``article.html`` via::

    {% if article.modified %}
    Last modified: {{ article.modified }}
    {% endif %}

If you want to include metadata in templates outside the article context (e.g.,
``base.html``), the ``if`` statement should instead be::

    {% if article and article.modified %}

How do I assign custom templates on a per-page basis?
=====================================================

It's as simple as adding an extra line of metadata to any page or article that
you want to have its own template. For example, this is how it would be handled
for content in reST format::

    :template: template_name

For content in Markdown format::

    Template: template_name

Then just make sure your theme contains the relevant template file (e.g.
``template_name.html``).

How can I override the generated URL of a specific page or article?
===================================================================

Include ``url`` and ``save_as`` metadata in any pages or articles that you want
to override the generated URL. Here is an example page in reST format::

    Override url/save_as page
    #########################

    :url: override/url/
    :save_as: override/url/index.html

With this metadata, the page will be written to ``override/url/index.html``
and Pelican will use url ``override/url/`` to link to this page.

How can I use a static page as my home page?
============================================

The override feature mentioned above can be used to specify a static page as
your home page. The following Markdown example could be stored in
``content/pages/home.md``::

    Title: Welcome to My Site
    URL: 
    save_as: index.html

    Thank you for visiting. Welcome!

What if I want to disable feed generation?
==========================================

To disable feed generation, all feed settings should be set to ``None``.
All but three feed settings already default to ``None``, so if you want to
disable all feed generation, you only need to specify the following settings::

    FEED_ALL_ATOM = None
    CATEGORY_FEED_ATOM = None
    TRANSLATION_FEED_ATOM = None

Please note that ``None`` and ``''`` are not the same thing. The word ``None``
should not be surrounded by quotes.

I'm getting a warning about feeds generated without SITEURL being set properly
==============================================================================

`RSS and Atom feeds require all URL links to be absolute
<http://validator.w3.org/feed/docs/rss2.html#comments>`_.
In order to properly generate links in Pelican you will need to set ``SITEURL``
to the full path of your site.

Feeds are still generated when this warning is displayed, but links within may
be malformed and thus the feed may not validate.

My feeds are broken since I upgraded to Pelican 3.x
===================================================

Starting in 3.0, some of the FEED setting names were changed to more explicitly
refer to the Atom feeds they inherently represent (much like the FEED_RSS
setting names). Here is an exact list of the renamed settings::

    FEED -> FEED_ATOM
    TAG_FEED -> TAG_FEED_ATOM
    CATEGORY_FEED -> CATEGORY_FEED_ATOM

Starting in 3.1, the new feed ``FEED_ALL_ATOM`` has been introduced: this
feed will aggregate all posts regardless of their language. This setting
generates ``'feeds/all.atom.xml'`` by default and ``FEED_ATOM`` now defaults to
``None``. The following feed setting has also been renamed::

    TRANSLATION_FEED -> TRANSLATION_FEED_ATOM

Older themes that referenced the old setting names may not link properly.
In order to rectify this, please update your theme for compatibility by changing
the relevant values in your template files. For an example of complete feed
headers and usage please check out the ``simple`` theme.

Is Pelican only suitable for blogs?
===================================

No. Pelican can be easily configured to create and maintain any type of static site.
This may require a little customization of your theme and Pelican configuration.
For example, if you are building a launch site for your product and do not need
tags on your site, you could remove the relevant HTML code from your theme. 
You can also disable generation of tag-related pages via::

    TAGS_SAVE_AS = ''
    TAG_SAVE_AS = ''

Why does Pelican always write all HTML files even with content caching enabled?
===============================================================================

In order to reliably determine whether the HTML output is different
before writing it, a large part of the generation environment
including the template contexts, imported plugins, etc. would have to
be saved and compared, at least in the form of a hash (which would
require special handling of unhashable types), because of all the
possible combinations of plugins, pagination, etc. which may change in
many different ways. This would require a lot more processing time
and memory and storage space. Simply writing the files each time is a
lot faster and a lot more reliable.

However, this means that the modification time of the files changes
every time, so a ``rsync`` based upload will transfer them even if
their content hasn't changed. A simple solution is to make ``rsync``
use the ``--checksum`` option, which will make it compare the file
checksums in a much faster way than Pelican would.

When only several specific output files are of interest (e.g. when
working on some specific page or the theme templates), the
`WRITE_ONLY_SELECTED` option may help, see
:ref:`writing_only_selected_content`.
