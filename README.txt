====== PHP RFC: Improved Error Callback Mechanism ======
  * Version: 0.1
  * Create Date: 2015-03-03
  * Modify Date: 2015-03-03
  * Author: Patrick Allaert <patrick@catchy.io>, Olivier Garcia <olivier@catchy.io>
  * Status: Draft
  * First Published at: http://wiki.php.net/rfc/improved_error_callback_mechanism

===== Introduction =====
There are several extensions that replaces the internal PHP error callback (''zend_error_cb'', initially set to ''php_error_cb''), either to customize one of the behaviour (e.g. [[http://xdebug.org/|XDebug] changes the generated output) or to add additionnal features (e.g. [[http://pecl.php.net/package/APM|PECL/apm]] logs errors in database). Unfortunatly they do not play well with each others because PHP has no mechanism that allows proper callback chaining or separation of concern. It also forces extension authors to reimplement parts of the original error callback if this one cannot be called.

The current behaviours of ''php_error_cb'' are:
  - Detect whether the error is repeated
  - Transform the error into an exception depending on the error handling
  - Display the error
  - Log the error
  - Check if the error is recoverable and bail out if needed
  - Track the error in a user accessible variable

This RFC aims to ease interactions between PHP and such extensions to make them register their callbacks without interfering with each others. In order to achieve that, an extension must be able to **replace** an existing behaviour as well as **extending** it.

===== Proposal =====

We propose to split ''php_error_cb'' into multiple hookable parts corresponding to the following behaviours:
  - Display the error (**display** part)
  - Log the error (**log** part)
  - Do additional processing if needed (**process** part), empty by default
  - Check if the error is recoverable and bail out if needed (**bailout** part)

We believe that there is no interesting use case for hooking the other parts of ''php_error_cb''.

An extension that want to **extend** one of the part can do it so with:
    PHP_ERROR_HOOK_API my_logging_hook(PHP_ERROR_HOOK_ARGS) {
        /* Do some logging with error line number, filename, message, ... */
    }

    php_add_error_hook(E_HOOK_LOG, my_logging_hook);

An extension that want to **clear** one of the part can do it so with:
    php_clear_error_hook(E_HOOK_DISPLAY);

''php_add_error_hook'' and ''php_clear_error_hook'' will operate on one of the internal linked lists of error hooks. There will be one linked list for each parts, referred to with one of the constant: ''E_HOOK_DISPLAY'', ''E_HOOK_LOG'', ''E_HOOK_PROCESS'' or ''E_HOOK_BAILOUT''. Those linked lists will, by default, contain the original implementation to avoid any BC break.

===== Backward incompatible changes =====
None.

===== Proposed PHP Version(s) =====
Next PHP version, 5.7 or 7.0, which ever comes first.

===== RFC Impact =====

==== To existing extensions ====
Extensions that want to replace ''zend_error_cb'' are still free to do so, however they are strongly encouraged to benefit from those new hooks to play easily together.

===== Open issues =====
None at the moment.

===== Proposed Voting Choices  =====
This RFC does not suggest a language change, so a 50%+1 majority is required for passage.

===== Patches and tests =====
The patch is under creation by Patrick Allaert and Olivier Garcia

===== Implementation =====
After the project is implemented, this section should contain:
 - the version(s) it was merged to
 - a link to the git commit(s)

===== References =====
None at the moment.

===== Rejected features =====
==== SOAP hooks ====
Providing specific hook(s) for the *soap* extension that implements its own error callback is not considered as it explicitly call the previously set error callback.

==== User land changes ====
It may be interesting to provide changes in the userland so that a user function can be registered as a part of one of those hooks. However, that would complicate this RFC and we consider this as out of the actual scope while considering it as a future enhancement.
