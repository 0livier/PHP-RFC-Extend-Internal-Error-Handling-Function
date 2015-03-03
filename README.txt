====== PHP RFC: Your Title Here ======
  * Version: 0.1
  * Create Date: 2015-03-03
  * Modify Date: 2015-03-03
  * Author: Patrick Allaert patrick@catchy.io, Olivier Garcia, olivier@catchy.io
  * Status: Draft
  * First Published at: Not yet


===== Introduction =====

There are several extensions that replaces the internal PHP error callback, either to customize the error output or to add additionnal logging features (e.g. apm log errors in database). Unfortunatly they do not play well with each others (as in segfaults or not working) because PHP has no mechanism that allow proper callback chaining or separation of concern. 

This RFC aims to ease interactions between PHP and such extensions to make them register their callbacks without interfering with each others.
