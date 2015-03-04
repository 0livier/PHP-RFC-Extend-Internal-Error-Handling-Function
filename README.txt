====== PHP RFC: Your Title Here ======
  * Version: 0.1
  * Create Date: 2015-03-03
  * Modify Date: 2015-03-03
  * Author: Patrick Allaert patrick@catchy.io, Olivier Garcia, olivier@catchy.io
  * Status: Draft
  * First Published at: Not yet


===== Introduction =====

There are several extensions that replaces the internal PHP error callback, either to customize the error output or to add additionnal logging features (e.g. apm log errors in database). Unfortunatly they do not play well with each others (as in segfaults or unexpected behaviour) because PHP has no mechanism that allow proper callback chaining or separation of concern. 

This RFC aims to ease interactions between PHP and such extensions to make them register their callbacks without interfering with each others.

===== Proposal =====
php_error_cb has the following purposes:
  * display/log error
  * check if the error is recoverable and bail out if needed

We think it could be interesting to split formally those features in three categories - display, log, error recovery/handling - and allow extensions to hook themselves : the php_error_cb would have to go call each hook. That would allow use cases where a user would like to enhance its display with xdebug while having several logging mecanism (say statsd, database).

===== Backward incompatible changes =====

===== Proposed PHP Version(s) =====

PHP 7.0

===== RFC Impact =====

==== To SAPIs ====

==== To existing extensions ====

==== To Opcache ====

==== New Constants ====

==== php.ini Defaults ====

===== Open issues =====

===== Proposed Voting Choices  =====


===== Patches and tests =====
The patch has yet to be done, by Patrick Allaert and Olivier Garcia

===== Implementation =====
To be done.

===== References =====
[1] Mail with Derick

===== Rejected features =====
None at the moment.



