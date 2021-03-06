pyspkac: Routines for supporting netscape / html5 SPKAC
=======================================================

:Author: Ralf Schlatterbeck <rsc@runtux.com>

This library supports the format of browser-generated SPKAC certificate
requests.

The HTML `keygen`_ tag which previously was supported by most
Mozilla-based browsers is now in the HTML-5 standard. The keygen tag is
used in a HTML form element and displays a key generation widget to the
user. When submitted, the browser locally generates a public key pair,
locally stores the private key, and submits the public key to the web
server in the `SPKAC`_ format.

.. _`keygen`: http://www.w3.org/TR/html5/forms.html#the-keygen-element
.. _`SPKAC`:  https://en.wikipedia.org/wiki/Spkac

When generating a form with a keygen element, the web server can include
a challenge string in the form. This challenge string is returned in the
SPKAC generated by the browser. It is signed together with the public
key by the private key retained by the browser.

This package is intended for server-side support of SPKAC. It uses a
submitted SPKAC and checks for correct challenge string and that the
public key and challenge are properly signed by the browser. It supports
a method for generating a certificate from the data in the SPKAC and
additional data supplied by the web application.

The generated certificate can then typically be used for client
authentication to a web application and is therefore called a client
certificate. The web browser (or other client-side application) supplies
the certificate in the SSL handshake and proves it is in the posession
of the necessary private key.

When building certificates it is also a good idea to have a certificate
revocation list (CRL). The package provides routines to generate a
revocation list from scratch using a list of certificate serial numbers
and revocation dates.

Installation
------------

Install using the standard python setup.py mechanism, e.g.::

 python setup.py install --prefix=/usr/local

You need `pyasn1`_ and `M2Crypto`_ installed for this package to work.

.. _`pyasn1`:   http://pyasn1.sourceforge.net/
.. _`M2Crypto`: http://chandlerproject.org/Projects/MeTooCrypto

Examples
--------

For a usage example see the doctests in `spkac.py`_ and `crl.py`

.. _`spkac.py`: https://github.com/FFM/pyspkac/blob/master/pyspkac/spkac.py
.. _`crl.py`: https://github.com/FFM/pyspkac/blob/master/pyspkac/crl.py

Notes for Building
------------------

When doing a release the following commands should be issued::

 python setup.py clean --all
 python setup.py sdist --manifest-only
 git status
 python setup.py sdist --quiet
 git status
 python setup.py register
 python setup.py sdist upload --sign

The interleaved ``git status`` commands work around a bug in older git
versions that, when ``git describe`` with ``--dirty`` option is used,
will erroneously detect *dirty* status when the directory is stat-dirty.
Since ``setup.py sdist`` will create symlinks this will make the
directory stat-dirty.

The --manifest-only will re-create the ``MANIFEST`` file even if neither
``setup.py`` nor ``MANIFEST.in`` have changed.

Changes
-------

Version 0.6: Bug fix

Release now contains missing files from 0.5.

- Add *Notes for Building* section and document release procedure to
  avoid missing files in the future
- Remove relative ``import`` statements
- Add version import to ``__init__.py``

Version 0.5: Major feature enhancements

Implement Certificate Revocation Lists (CRL)

- Implementation of CRL (with MD5 hash, this probably needs to be fixed
  later on, don't know yet if there is a format with a stronger hash)
- factor pem_object
- Fix timezone of default valid_from for generated certificate: We used
  a timezone correction on a timestamp which is already UTC.

Version 0.4: Bug fix

Fix incompatibility with newer versions of pyasn1.

- BitString serialisation in pyasn1 changed method names, the old
  inofficial serialisation method with '_' is now official without the
  '_'.

Version 0.3: Bug fix

Import of version.py failed if dependecies were missing. This prevented
a ``pip install`` from succeeding in case of missing dependencies.

- Guard import in __init__.py to not fail for missing dependencies

Version 0.2: Minor feature additions

Move example from end of spkac.py into a doctest. Change of API:
set_email no longer supported. Now extensions can be given as unnamed
arguments in the constructor. Named arguments are interpreted as
subject parameters for the resulting certificate. All this is shown in
the doctest.

- Remove set_email
- Change constructor: Allow extensions as args and subject parameters as
  keyword args
- Add doctest
- Fix setup.py to include install_requires to auto-install required
  packages
- Easier import:
  from pyspkac import SPKAC
  works now.

Version 0.1: Initial Release
