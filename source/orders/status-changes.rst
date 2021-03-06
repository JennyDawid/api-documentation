Order status changes
====================

Orders and order lines can go through a number of different statuses. First we will discuss the difference between
two possible flows for orders. After that we will list all the possible statuses for both **orders** and **order lines**.

Two flows: authorized and paid
------------------------------
There are basically two different flows for an order, depending on the payment method that is used to pay the order.

Authorized
^^^^^^^^^^
Some payment methods support *authorizations*. This means that a consumer can authorize a payment, but it will not be
executed immediately. It will give us all the needed information to process the payment at a later point. This execution
is called a *capture*.

If the payment method supports authorizations, the consumer will authorize a payment when the order is created. If the
authorization is successful, the order will have status ``authorized``.

Each time a shipment is created, we will automatically execute a capture too. The shipment can be for the complete order
or for only part of the order. Only the amount that is shipped will be captured.

Paid
^^^^
If a payment method does not support authorizations, the payment will be paid immediately when the order is created. You
can create shipments for these orders just like in the *authorized* flow, but it won't have any effect on the payment.

Possible statuses for orders
----------------------------
The following diagram shows how one order status leads to another:

.. image:: images/order-status-flow@2x.png

.. _order-status-created:

``created``
^^^^^^^^^^^
    The order has been created, but nothing else has happened yet.

    * This is not a status Mollie will call your webhook for.
    * All order lines will also be in the ``created`` state.
    * Can transition to: ``paid``, ``authorized`` and ``canceled``.

.. _order-status-paid:

``paid``
^^^^^^^^
    The order status will be set to this status when the order's payment is successfully completed with a payment method
    that does not support authorizations.

    * Mollie will call your webhook when the order reaches this state.
    * Order lines can be in the state ``paid`` or ``refunded``. Not all lines are ``refunded``.
    * Can transition to: ``shipping`` and ``refunded``.

.. _order-status-authorized:

``authorized``
^^^^^^^^^^^^^^
    If the order's payment is successfully completed with a payment method that does support authorizations, the order
    is set to this status. The money will only be transferred once a shipment is created for the order. Currently only
    *Klarna Pay later* and *Klarna Slice it* use this status.

    * Mollie will call your webhook when the order reaches this state.
    * Order lines can be in the state ``authorized`` or ``canceled``. Not all lines are ``canceled``.
    * Can transition to: ``shipping``, ``canceled`` and ``expired``.

.. _order-status-shipping:

``shipping``
^^^^^^^^^^^^
    The order will move into this state when you start shipping your first order lines. When the order is in this state,
    it means that you still have some order lines that are not shipped yet.

    * This is not a status Mollie will call your webhook for.
    * Order lines can be in the states ``paid``, ``authorized``, ``shipping``, ``completed``, ``refunded`` or
      ``canceled``. At least one line should be in ``paid`` or ``authorized`` and at least one other line should be
      ``completed``.
    * Can transition to: ``completed``.

.. _order-status-completed:

``completed``
^^^^^^^^^^^^^
    When all order lines are shipped or canceled, the order will be set to this status. At least one line should be
    shipped. If all lines are canceled, the status of the order will change to ``canceled`` instead.

    * Mollie will call your webhook when the order reaches this state.
    * Order lines can be in the states ``completed``, ``canceled`` or ``refunded``. At least one line should be
      ``completed``.
    * This is a final state, the order can't transition to another state.

.. _order-status-canceled:

``canceled``
^^^^^^^^^^^^
    When all order lines are canceled, the order is also set to canceled. Orders can only be canceled by the merchant,
    not by the consumer.

    * Mollie will call your webhook when the order reaches this state.
    * All order lines will also be in the ``canceled`` state.
    * This is a final state, the order can't transition to another state.

.. _order-status-refunded:

``refunded``
^^^^^^^^^^^^
    When an order was paid with a payment method that transfers the money immediately, but the order was completely
    canceled, the order will be set to this status.

    .. note:: This status is *not* used when you refund orders or order lines after you have shipped them. In that case
              the status will stay at ``completed``.

    * Mollie will call your webhook when the order reaches this state.
    * All order lines will also be in the ``refunded`` state.
    * This is a final state, the order can't transition to another state.

.. _order-status-expired:

``expired``
^^^^^^^^^^^
    The order will expire when an order is not shipped in time after an authorization. This is currently 28 days but
    might change in the future.

    * Mollie will call your webhook when the order reaches this state.
    * All order lines will be ``canceled``.
    * This is a final state, the order can't transition to another state.

Possible statuses for order lines
---------------------------------
The following diagram shows how one order line status leads to another:

.. image:: images/orderline-status-flow@2x.png

.. _orderline-status-created:

``created``
^^^^^^^^^^^
    The order line has been created, but nothing else has happened yet.

    * The order will also be in the ``created`` state.
    * Can transition to: ``paid``, ``authorized`` and ``canceled``.

.. _orderline-status-paid:

``paid``
^^^^^^^^
    The order line status will be set to this status when the order's payment is successfully completed with a payment
    method that does not support authorizations.

    * The order has status ``paid`` or ``shipping``.
    * Can transition to: ``shipping`` or ``refunded``.

.. _orderline-status-authorized:

``authorized``
^^^^^^^^^^^^^^
    If the order's payment is successfully completed with a payment method that does support authorizations, the order
    lines are set to this status. The money will only be transferred once a shipment is created for the order line.
    Currently only *Klarna Pay later* and *Klarna Slice it* use this status.

    * The order has status ``authorized`` or ``shipping``.
    * Can transition to: ``shipping`` or ``canceled``.

.. _orderline-status-shipping:

``shipping``
^^^^^^^^^^^^
    The order line will move into this status when you ship only a part of the order line. If you ship the complete
    order line, the status will move to ``completed`` immediately.

    * The order has status ``shipping``.
    * Can transition to: ``completed``.

.. _orderline-status-completed:

``completed``
^^^^^^^^^^^^^
    When the order line is completely shipped, it will get this status. The order line will also get this status when it
    is partially shipped and the rest of the line is ``canceled`` or ``refunded``.

    * The order has status ``shipping`` or ``completed``.
    * This is a final state, the order line can't transition to another state.

.. _orderline-status-canceled:

``canceled``
^^^^^^^^^^^^
    When the complete order line is canceled, the line gets this status. If only part of the order line is canceled, the
    status will stay at its previous status. Only merchants can cancel orders and order lines, the consumer can't do
    this.

    * The order has status ``authorized``, ``shipping``, ``completed``, ``expired`` or ``canceled``.
    * This is a final state, the order line can't transition to another state.

.. _orderline-status-refunded:

``refunded``
^^^^^^^^^^^^
    When an order line has status ``paid`` and is completely canceled, the order line will be set to this status.

    This is only possible for payment methods that don't support captures. If the payment does support captures, the
    order line would have been in status ``authorized`` and canceling would cause it to go to status ``canceled``.

    .. note:: This status is *not* used when you refund order lines after you have shipped them. In that case the status
              will stay at ``completed``.

    * The order has status ``completed`` or ``refunded``.
    * This is a final state, the order line can't transition to another state.
