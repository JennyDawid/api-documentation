.. _v2/methods-get:

Methods API v2: Get payment method
==================================
``GET`` ``https://api.mollie.com/v2/methods/*id*``

Authentication: :ref:`API keys <guides/authentication>`, :ref:`OAuth access tokens <oauth/overview>`

Retrieve a single method by its ID. Note that if a method is not available on the payment profile a status
``404 Not found`` is returned. When the method is not enabled, a status ``403 Forbidden`` is returned.

If you do not know the method's ID, you can use the :ref:`methods list endpoint <v2/methods-list>` to retrieve all
payment methods that are available.

Parameters
----------
Replace ``id`` in the endpoint URL by the methods's ID. For example: ``https://api.mollie.com/v2/methods/ideal``.

.. list-table::
   :widths: auto

   * - | ``locale``
       | string
     - Optional – Passing a locale will translate the payment method name in the corresponding language.

       Possible values: ``en_US`` ``de_AT`` ``de_CH`` ``de_DE`` ``es_ES`` ``fr_BE`` ``fr_FR`` ``nl_BE`` ``nl_NL``

Mollie Connect/OAuth parameters
-------------------------------
If you're creating an app with Mollie Connect/OAuth, the following parameters are also available. With the ``profileId``
parameter, you must specify which profile you want to look at when listing methods. Organizations can have multiple
profiles for each of their websites. See :ref:`Profiles API <v1/profiles-get>` for more information.

.. list-table::
   :widths: auto

   * - | ``profileId``
       | string
     - The payment profile's unique identifier, for example ``pfl_3RkSN1zuPE``. This field is mandatory.

   * - | ``testmode``
       | boolean
     - Optional – Set this to ``true`` to list all methods available in testmode.

Includes
^^^^^^^^
This endpoint allows you to include additional information by appending the following values via the ``include``
querystring parameter.

* ``issuers`` Include issuers available for the payment method (e.g. for iDEAL, KBC/CBC payment button or gift cards).

Response
--------
``200`` ``application/json; charset=utf-8``

.. list-table::
   :widths: auto

   * - | ``resource``
       | string
     - Indicates the response contains a method object. Will always contain ``method`` for this endpoint.

   * - | ``id``
       | string
     - The unique identifier of the payment method. When used during :ref:`payment creation <v2/payments-create>`,
       the payment method selection screen will be skipped.

   * - | ``description``
       | string
     - The full name of the payment method.

   * - | ``image``
       | image object
     - The URLs of images representing the payment method.

       .. list-table::
          :widths: auto

          * - | ``size1x``
              | string
            - The URL for a payment method icon of 55x37 pixels.

          * - | ``size2x``
              | string
            - The URL for a payment method icon of 110x74 pixels.

Example
-------

Request
^^^^^^^
.. code-block:: bash

   curl -X GET https://api.mollie.com/v2/methods/ideal \
       -H "Authorization: Bearer live_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM"

Response
^^^^^^^^
.. code-block:: http

   HTTP/1.1 200 OK
   Content-Type: application/json; charset=utf-8

   {
       "resource": "method",
       "id": "ideal",
       "description": "iDEAL",
       "image": {
           "size1x": "https://mollie.com/images/payscreen/methods/ideal.png",
           "size2x": "https://mollie.com/images/payscreen/methods/ideal%402x.png"
       },
       "_links": {
           "self": {
               "href": "https://api.mollie.com/v2/methods/ideal",
               "type": "application/json"
           },
           "documentation": {
               "href": "https://mollie.com/en/docs/reference/methods/get",
               "type": "text/html"
           }
       }
   }