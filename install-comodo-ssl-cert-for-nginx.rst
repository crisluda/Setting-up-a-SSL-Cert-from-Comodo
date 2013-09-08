Setting up a SSL Cert from Comodo
=================================

I use `Namecheap.com <https://namecheap.com>`_ as a registrar, and they resale
SSL Certs from a number of other companies, including `Comodo <http://www.comodo.com/>`.

These are the steps I went through to set up an SSL cert.

Purchase the cert
-----------------

Prior to purchasing a cert, you need to generate a private key, and a CSR file
(Certificate Signing Request). You'll be asked for the content of the CSR file
when ordering the certificate.

::

    openssl req -nodes -newkey rsa:2048 -keyout example_com.key -out example_com.csr

This gives you two files:

    * ``example_com.key`` -- your Private key. You'll need this later to configure ngxin.
    * ``example_com.csr`` -- Your CSR file.

Now, purchase the certificate[1]_, wait *forever* for them to review your purchase.
You'll eventually get an email with your *PositiveSSL Certificate*. It contains
a zip file with the following:

    * Root CA Certificate - `AddTrustExternalCARoot.crt`
    * Intermediate CA Certificate - `PositiveSSLCA2.crt`
    * Your PositiveSSL Certificate - `example_com.crt`

Install the Commodo SSL cert
----------------------------

Combine everything for nxinx[2]_:

1. Combine the above crt files into a bundle (the order matters, here)::

    cat example_com.crt PositiveSSLCA2.crt AddTrustExternalCARoot.crt >> ssl-bundle.crt

2. Store the bundle wherever nginx expects to find it::

    mkdir -p /etc/nginx/ssl/example_com/
    mv ssl-bundle.crt /etc/nginx/ssl/example_com/

3. Make sure your nginx config points to the right cert file and to the private
   key you generated earlier::

    server {
        listen 443;

        ssl on;
        ssl_certificate /etc/nginx/ssl/example_com/ssl-bundle.crt;
        ssl_certificate_key /etc/nginx/ssl/example_com/example_com.key;

        # ...

    }

4. Restart nginx.


.. [1] I purchased mine through the Namecheap.com website.
.. [2] Based on these instructions: http://goo.gl/4zJc8