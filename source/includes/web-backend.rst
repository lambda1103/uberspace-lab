To make the application accessible from the outside, configure a :manual:`web backend <web-backends>`:

::

  [isabell@stardust ~]$ uberspace web backend set / --http --port <port>
  Set backend for / to port <port>; please make sure something is listening!
  You can always check the status of your backend using "uberspace web backend list".
  [isabell@stardust ~]$
