# virtualization

**Table of Contents** _generated with_ [_DocToc_](https://github.com/thlorenz/doctoc)

* [SSL Certificate](./#ssl-certificate)
  * [Adding trusted root certificates to the server](./#adding-trusted-root-certificates-to-the-server)

## SSL Certificate

### [Adding trusted root certificates to the server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)

#### MacOS

* add

  ```bash
  $ sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ~/new-root-certificate.crt
  ```

* remove

  ```bash
  $ sudo security delete-certificate -c "<name of existing certificate>"
  ```

#### Ubuntu

* add

  ```bash
  $ cp ca.crt /usr/local/share/ca-certificates/
  $ sudo cp foo.crt /usr/local/share/ca-certificates/foo.crt
  $ sudo update-ca-certificates
  ```

* remove

  ```bash
  $ sudo rm -rf /usr/local/share/ca-certificates/ca.crt
  $ sudo rm -rf /usr/local/share/ca-certificates/foo.crt
  $ sudo update-ca-certificates --fresh
  ```

#### CentOS 6

* add

  ```bash
  $ sudo yum install -y ca-certificates
  $ sudo update-ca-trust force-enable
  $ sudo cp foo.crt /etc/pki/ca-trust/source/anchors/
  $ update-ca-trust extract
  ```

#### CentOS 5

* add

  ```bash
  $ cat foo.crt >>/etc/pki/tls/certs/ca-bundle.crt
  ```

#### Windows

* add

  ```bash
  $ certutil -addstore -f "ROOT" new-root-certificate.crt
  ```

* remove

  ```bash
  $ certutil -delstore "ROOT" serial-number-hex
  ```
