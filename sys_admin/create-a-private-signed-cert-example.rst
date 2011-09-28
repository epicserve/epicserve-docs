Create a Private Signed Cert Example
====================================

Last time I created a privatly signed cert I did it this way::

    $ cd /usr/share/ssl/
    $ openssl req -config openssl.cnf -new -out /usr/local/ssl/certs/webmail.epicserve.com.csr

Answer the following prompts::

    Enter PEM pass phrase: <enter something that is 4 chars>
    Verifying - Enter PEM pass phrase: <re-enter pass phrase>
    Country Name (2 letter code) [GB]: US
    State or Province Name (full name) [Berkshire]: Kansas
    Locality Name (eg, city) [Newbury]: Manhattan
    Organization Name (eg, company) [My Company Ltd]: Epicserve
    Organizational Unit Name (eg, section) []: Web Hosting
    Common Name (eg, your name or your server's hostname) []: webmail.epicserve.com
    Email Address []:
    A challenge password []:
    An optional company name []:

Run the following command::

    $ openssl rsa -in privkey.pem -out /usr/local/ssl/private/webmail.epicserve.com.key

Answer the following prompts::

    Enter pass phrase for privkey.pem: <enter the same pass phrase you enterned in the last step>

Then run the following::

    $ openssl x509 -in /usr/local/ssl/certs/webmail.epicserve.com.csr \
    -out /usr/local/ssl/certs/webmail.epicserve.com.crt \
    -req -signkey /usr/local/ssl/private/webmail.epicserve.com.key \
    -days 365

Restart Apache
