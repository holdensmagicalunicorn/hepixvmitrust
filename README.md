# hepixvmitrust README

Introduction
---------------------------------------

hepixvmitrust is a package that contains a CLI tool, and a minimal 
implementation, in its documentation for X509 signing lists of 
virtual machine image metadata. The tools are generally reusable 
but were developed to satisfy the need to securely exchange virtual 
machine images between High Energy Physics sites, in a similar way 
to yum and apt repositories provide for rpms, this software provides 
for Virtual Maschines.


Installation on Redhat Enterprise Linux 5/ SL5 or Centos 5
---------------------------------------

First install Extended Packages for Enterprise Linux (EPEL).

    $ yum install yum-conf-epel

Install the dependencies from EPEL.

    $ yum install python-simplejson  python-hashlib m2crypto

Now the dependencies are resolved the script should now run

Installation on debian 6.0 (squeeze)
---------------------------------------

All the dependacies are available in the debian repository.

    $ aptitude install python-m2crypto


Basic usage
---------------------------------------
Basic usage vmilisttool
---------------------------------------

To get a complete list of available commands do:

    $ vmilisttool --help


Here is an example of how to create a signed image list:


(1) Create a template for the image list.

    $ vmilisttool --json image_list_template.json

Edit the file image_list_template.json adding your list metadata so no 'null'
entries exist.

    $ vim image_list_template.json

(2) Create a template for an image reference. This will compute the checksum of
the image file and create a JSON text file containing that checksum.

    $ vmilisttool --image /home/jdoe/rawdiskimage.img --generate VMmetadata.json
 
Edit the file VMmetadata.json adding your list metadata so no 'null' entries exist.

    $ vim  VMmetadata.json 

(3) Add your newly updated image metadata to the image list 

    $ vmilisttool  --template image_list_template.json  -add VMmetadata.json --json merged_image_list.json

(4) Sign the now assembled metadata list.

    $ vmilisttool  --template merged_image_list.json -s signed_image_list

Basic usage minimal.py
---------------------------------------

Make a working space

    $ mkdir minimalcode_test
    $ cd minimalcode_test
    $ cp ~/.globus/user
    usercert.pem  userkey.pem   
    $ cp ~/.globus/user*.pem .

The command is minimal so no options.

    $ python /usr/share/doc/vmilisttool/minimal.py 
    Enter passphrase:
    $ ls
    imagelist.smime  usercert.pem  userkey.pem

The new file contains you sigined image list.


Description of meta data fields
---------------------------------------

The metadata is in the initial versions stored in JSON. 

Future versions are intended to support JSON and XML RDF

The fields have a namespace like nomenclature so that translation to RDF will
be easier.

    dc: dublin core
    sl: stratuslab
    hv: hepix virtulisation

*Structure of message*

    hv:imagelist
      dc:date:created
      dc:date:expires
      hv:endorser
        hv:ca
        hv:dn_x509
        hv:email
        dc:creator
      dc:identifier
      dc:description
      dc:title
      hv:images
        hv:image
          dc:title
          dc:description
          hv:size
          sl:checksum:sha512
          sl:arch
          hv:uri
          dc:identifier
          sl:os
          sl:osversion
          sl:comments
          hv:hypervisor
          hv:version
      dc:source
      hv:version
      hv:uri

*Details of field*

    hv:imagelist
      comment)
        container for metadata
      xml example)
        <hv:imagelist>...
        </hv:imagelist>
    
    hv:imagelist/dc:date:created
      comment)
        date in format year-month-day
      xml example)
        <dc:date type="created">2011-03-03</dc:date>
    
    hv:imagelist/dc:date:expires
      comment)
        date in format year-month-day when imagelist will no longer be trusted
      xml example)
        <dc:date type="created">2011-03-03</dc:date>
    
    hv:imagelist/hv:endorser
      comment)
        container for endorser metadata
      xml example)
        <hv:endorser>...
        </hv:endorser>
    
    hv:imagelist/hv:endorser/hv:ca
      comment)
        standard hash id for the CA, corresponds with the CA directory files.
      xml example)
        <hv:ca>/C=GB/ST=Greater Manchester/L=Salford/O=Comodo CA Limited/CN=AAA Certificate Services</hv:ca>
    
    hv:imagelist/hv:endorser/hv:dn_x509
      comment)
        x509 DN of the endorser
      xml example)
        <hv:dn_x509>/C=DE/O=GermanGrid/OU=DESY/CN=Owen Synge</hv:dn_x509>
    
    
    hv:imagelist/hv:endorser/hv:email
      comment)
        email address of the endorser
      xml example)
        <hv:email>owen.synge@desy.de</hv:email>
    
    hv:imagelist/hv:endorser/dc:creator
      comment)
        Name of the endorser
      xml example)
        <hv:email>Owen Synge</hv:email>
    
    hv:imagelist/dc:identifier
      comment)
        RFC 4122 UUID for imagelist this allows updating the list, and uniqueness.
      xml example)
        <dc:identifier>9ac60a74-c67f-457f-bd80-04c8ff6ecf3d</dc:identifier>
    
    hv:imagelist/dc:description
      comment)
        description of the image list
      xml example)
        <dc:description>Owens list of images from DESY quattor.</dc:description>
    
    hv:imagelist/dc:title
      comment)
        title of image list
      xml example)
        <dc:description>DISH service</dc:description>
    
    
    hv:imagelist/hv:images
      comment)
        Container of the list of images
      xml example)
        <hv:images>....
        </hv:images>
    
    hv:imagelist/hv:images/hv:image
      comment)
        Container of the image
      xml example)
        <hv:image>....
        </hv:image>
    
    hv:imagelist/hv:images/hv:image/dc:title
      comment)
        Title of image
      xml example)
        <dc:title>generic:sl5</dc:title>
    
    hv:imagelist/hv:images/hv:image/dc:title
      comment)
        Name of the image
      xml example)
        <dc:title>generic:sl5</dc:title>
    
    hv:imagelist/hv:images/hv:image/dc:description
      comment)
        Free text describing the image
      xml example)
        <dc:description>Desy generated generic sl5 image</dc:description>
    
    
    hv:imagelist/hv:images/hv:image/hv:size
      comment)
        size of the image in bytes
      xml example)
        <hv:size>4619442176</hv:size>
    
    hv:imagelist/hv:images/hv:image/sl:checksum:sha512
      comment)
        size of the image in bytes
      xml example)
        <sl:checksum type="sha512">8b4c269a60da1061b434b696c4a89293bea847b66bd8ba486a914d4209df651193ee8d454f8231840b7500fab6740620c7111d9a17d08b743133dc393ba2c0d4</sl:checksum>
    
    hv:imagelist/hv:images/hv:image/sl:arch
      comment)
        architecture of the image typically 'i386' or 'amd64'
      xml example)
        <sl:arch>amd64</sl:checksum>
    
    
    hv:imagelist/hv:images/hv:image/hv:uri
      comment)
        URI pointing to the latest version of this file.
      xml example)
        <hv:uri>https://example.org/imagelist.xml</hv:uri>
    
    hv:imagelist/hv:images/hv:image/dc:identifier
      comment)
        RFC 4122 UUID for image this allows updating the image, and uniqueness.
      xml example)
        <hv:uri>185c9f57-f838-4ac6-8246-85ccd6d6d3f4</hv:uri>
    
    hv:imagelist/hv:images/hv:image/sl:os
      comment)
        The operating system the image runs, examples include "Linux" "BSD" "FreeBSD"
      xml example)
        <sl:os>Linux</sl:os>
    
    
    hv:imagelist/hv:images/hv:image/sl:osversion
      comment)
        The operating system version the image runs, examples include "SL_6.0", maybe this
        tag should be further broken downs so easier to process for computers.
      xml example)
        <sl:osversion>SL_5.5</sl:osversion>
    
    hv:imagelist/hv:images/hv:image/sl:comments
      comment)
        Stratus lab suggest comments on how the image is to be deployed.
      xml example)
        <sl:comments></sl:comments>
    
    
    hv:imagelist/hv:images/hv:image/hv:hypervisor
      comment)
        Typically set to reflect the Virtualization technology values such as "xen", "kvm".
      xml example)
        <hv:hypervisor></hv:hypervisor>
    
    hv:imagelist/hv:images/hv:image/hv:version
      comment)
        The version of the image, so that updates can share the same metadata
        (with exception of dc:identifier) in case of update. Typically a numeric value.
      xml example)
        <hv:version></hv:version>
    
    
    hv:imagelist/dc:source
      comment)
        The URI where the latest version of this file can be found.
      xml example)
        <dc:source type="uri">http://example.org/example.img.gz</dc:source>
    
    hv:imagelist/hv:version
      comment)
        The version of the imagelist, so that updates can share the same metadata
        (with exception of dc:identifier) in case of update. Typically a numeric value.
      xml example)
        <hv:version></hv:version>
    
    hv:imagelist/hv:uri
      comment)
        The uri to retrive new versions of the imagelist, that updates can be handled.
      xml example)
        <hv:uri></hv:uri>


Example metadata files
-------------------------

A typical image list looks like such


    $cat imagelist.json 
    {
        "dc:date:created": "2011-03-10T17:09:12Z", 
        "dc:date:expires": "2011-04-07T17:09:12Z", 
        "dc:description": "a README example of an image list", 
        "dc:identifier": "4e186b44-2c64-40ea-97d5-e9e5c0bce059", 
        "dc:source": "example.org", 
        "dc:title": "README example", 
        "hv:endorser": {
            "hv:x509": {
                "dc:creator": "Owen Synge", 
                "hv:ca": "/C=DE/O=GermanGrid/CN=GridKa-CA", 
                "hv:dn": "/C=DE/O=GermanGrid/OU=DESY/CN=Owen Synge", 
                "hv:email": "owen.synge@desy.de"
            }
        }, 
        "hv:images": [
            {
                "hv:image": {
                    "dc:description": "This is an README example VM", 
                    "dc:identifier": "488dcdc4-9ab1-4fc8-a7ba-b7a5428ecb3d", 
                    "dc:title": "README example VM", 
                    "hv:hypervisor": "kvm", 
                    "hv:size": 2147483648, 
                    "hv:uri": "http://example.org/example-image.img", 
                    "hv:version": "1", 
                    "sl:arch": "x86_64", 
                    "sl:checksum:sha512": "8b4c269a60da1061b434b696c4a89293bea847b66bd8ba486a914d4209df651193ee8d454f8231840b7500fab6740620c7111d9a17d08b743133dc393ba2c0d4", 
                    "sl:comments": "Vanila install with contextulization scripts", 
                    "sl:os": "Linux", 
                    "sl:osversion": "SL 5.5"
                }
            }
        ], 
        "hv:uri": "http://example.org/example-image-list.image_list", 
        "hv:version": "1"
    }


and when signed like this.

    MIME-Version: 1.0
    Content-Type: multipart/signed; protocol="application/pkcs7-signature"; micalg="sha1"; boundary="----EAE3006C97F670EE450F46AC8DF4C070"
    
    This is an S/MIME signed message
    
    ------EAE3006C97F670EE450F46AC8DF4C070
    {
        "dc:date:created": "2011-03-10T17:09:12Z", 
        "dc:date:expires": "2011-04-07T17:09:12Z", 
        "dc:description": "a README example of an image list", 
        "dc:identifier": "4e186b44-2c64-40ea-97d5-e9e5c0bce059", 
        "dc:source": "example.org", 
        "dc:title": "README example", 
        "hv:endorser": {
            "hv:x509": {
                "dc:creator": "Owen Synge", 
                "hv:ca": "/C=DE/O=GermanGrid/CN=GridKa-CA", 
                "hv:dn": "/C=DE/O=GermanGrid/OU=DESY/CN=Owen Synge", 
                "hv:email": "owen.synge@desy.de"
            }
        }, 
        "hv:images": [
            {
                "hv:image": {
                    "dc:description": "This is an README example VM", 
                    "dc:identifier": "488dcdc4-9ab1-4fc8-a7ba-b7a5428ecb3d", 
                    "dc:title": "README example VM", 
                    "hv:hypervisor": "kvm", 
                    "hv:size": 2147483648, 
                    "hv:uri": "http://example.org/example-image.img", 
                    "hv:version": "1", 
                    "sl:arch": "x86_64", 
                    "sl:checksum:sha512": "8b4c269a60da1061b434b696c4a89293bea847b66bd8ba486a914d4209df651193ee8d454f8231840b7500fab6740620c7111d9a17d08b743133dc393ba2c0d4", 
                    "sl:comments": "Vanila install with contextulization scripts", 
                    "sl:os": "Linux", 
                    "sl:osversion": "SL 5.5"
                }
            }
        ], 
        "hv:uri": "http://example.org/example-image-list.image_list", 
        "hv:version": "1"
    }
    ------EAE3006C97F670EE450F46AC8DF4C070
    Content-Type: application/pkcs7-signature; name="smime.p7s"
    Content-Transfer-Encoding: base64
    Content-Disposition: attachment; filename="smime.p7s"
    
    MIIHdAYJKoZIhvcNAQcCoIIHZTCCB2ECAQExCzAJBgUrDgMCGgUAMAsGCSqGSIb3
    DQEHAaCCBSUwggUhMIIECaADAgECAgIz7DANBgkqhkiG9w0BAQUFADA2MQswCQYD
    VQQGEwJERTETMBEGA1UEChMKR2VybWFuR3JpZDESMBAGA1UEAxMJR3JpZEthLUNB
    MB4XDTExMDExMDE1MDMxN1oXDTEyMDIwOTE1MDMxN1owRjELMAkGA1UEBhMCREUx
    EzARBgNVBAoTCkdlcm1hbkdyaWQxDTALBgNVBAsTBERFU1kxEzARBgNVBAMTCk93
    ZW4gU3luZ2UwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCkgbPFZrVL
    pmwf7GKBBFkwTK5V7RmlupsU3Z3FqdfMnJGn2NrrnHIhthUTCTq4WbLIZTbOEh0n
    JqZgZBvYcwJV4V9pais4YlsEug+JLMbB9hZ6e2XgdjXWgLqz6vBSIf6KXi4KhCxe
    a4FylvIk7OtY+bg0mg5IFHib6uP7fXhFKdBEapoi+B05wpluBMA+2DBdSt+rjzA8
    SwiHUuan60VIyJAxammyOe3IKSpwyBXkQ10XjIhIpoSavqYXJboFOVzUcqxawdbX
    Con2W8QfiwFKYupohG/VTusDXFT2MP4k+KxG3/rTTPWUDJme7VUPv3+CTcEO+z4v
    X8/XhI44oAXlAgMBAAGjggInMIICIzAMBgNVHRMBAf8EAjAAMA4GA1UdDwEB/wQE
    AwIE8DAdBgNVHQ4EFgQUGakUy66kgvulNBIf18WBXjGolqYwXgYDVR0jBFcwVYAU
    xnXJKKzRC/w8/7m1HtNfO4BiEjShOqQ4MDYxCzAJBgNVBAYTAkRFMRMwEQYDVQQK
    EwpHZXJtYW5HcmlkMRIwEAYDVQQDEwlHcmlkS2EtQ0GCAQAwHQYDVR0RBBYwFIES
    b3dlbi5TeW5nZUBkZXN5LmRlMB8GA1UdEgQYMBaBFGdyaWRrYS1jYUBpd3IuZnpr
    LmRlMDUGA1UdHwQuMCwwKqAooCaGJGh0dHA6Ly9ncmlkLmZ6ay5kZS9jYS9ncmlk
    a2EtY3JsLmRlcjAaBgNVHSAEEzARMA8GDSsGAQQBlDarLAEBAQUwEQYJYIZIAYb4
    QgEBBAQDAgWgME4GCWCGSAGG+EIBDQRBFj9DZXJ0aWZpY2F0ZSBpc3N1ZWQgdW5k
    ZXIgQ1AvQ1BTIHYuIDEuNSBhdCBodHRwOi8vZ3JpZC5memsuZGUvY2EwJAYJYIZI
    AYb4QgECBBcWFWh0dHA6Ly9ncmlkLmZ6ay5kZS9jYTAzBglghkgBhvhCAQgEJhYk
    aHR0cDovL2dyaWQuZnprLmRlL2NhL2dyaWRrYS1jcHMucGRmMDMGCWCGSAGG+EIB
    AwQmFiRodHRwOi8vZ3JpZC5memsuZGUvY2EvZ3JpZGthLWNybC5kZXIwDQYJKoZI
    hvcNAQEFBQADggEBAMbn91TOQ6r4D/aKwgIFXiXe40B7iccz/P5pCFSi1R6IC3KH
    Ui4s/f9iAGl9rA21h8QAaRaJ/h1OQNlgMZbc9jDCWcqxr8wQTYAQDiBkspLT68ZO
    5xVFRiq3HjkkhwnFfFzsNSiLFYZTRjChPluclYG3TEvSg8dz9Lv/IEJxE5C5lZ2d
    e3CSu0vcD0DESiu/sVqPOOHi8NL/59U2ine3z23Y+piCabQCxjT0inT2MmR8UNDF
    ij2JJYxlt56U/SQCEe0304w3x1jIg8vcpm4dfh+L2IjJ9hVfEeLaCyhv9Wjbmu5O
    vk0yLjcEZ7b4RKeo7djVYh+5kCWJYCr/W6uGW44xggIXMIICEwIBATA8MDYxCzAJ
    BgNVBAYTAkRFMRMwEQYDVQQKEwpHZXJtYW5HcmlkMRIwEAYDVQQDEwlHcmlkS2Et
    Q0ECAjPsMAkGBSsOAwIaBQCggbEwGAYJKoZIhvcNAQkDMQsGCSqGSIb3DQEHATAc
    BgkqhkiG9w0BCQUxDxcNMTEwMzEwMTczMzU1WjAjBgkqhkiG9w0BCQQxFgQUd43y
    VT05Zk+7acFF+EeqExNI57cwUgYJKoZIhvcNAQkPMUUwQzAKBggqhkiG9w0DBzAO
    BggqhkiG9w0DAgICAIAwDQYIKoZIhvcNAwICAUAwBwYFKw4DAgcwDQYIKoZIhvcN
    AwICASgwDQYJKoZIhvcNAQEBBQAEggEAkA0RgB5AkGIYvFsFETzx7QHKWu9qas5k
    vlHn2a+EpRE9K1p+qrFNzS53E2BGqubyRcePfgG/WyGqYOK2h20d6GZH+ENUFkvM
    EAthbvQaHye6WEvF/0GUrr0QUBT1gQswkkryPHcqTVmJANQORakkNvCwynEBmfSC
    vb2TEppRuOCmxx3zqrzMr7zPNPY4w2+YaXQ1fHfmEmOrlf0ImP20TyTKIoQWqzbq
    WXwlRhZBUoD9zfiEM/jFvOvkuxLkQeiEcSzlLAGHXsHJ3anPMX9sobJFbJI0wYdN
    sUOInHRhksokh2ow68KZK4vXLI173v5yZE7FZZ1Gl9T+YpkmOIW4iQ==
    
    ------EAE3006C97F670EE450F46AC8DF4C070--


How to verify
---------------------------

To get the DN and CA of your signature.

    openssl smime -in your_signed.msg \
     -pk7out | openssl pkcs7 -print_certs
    
To Verfy the message agaisnt the CA certificate.

    openssl smime -in your_signed.msg \
     -CAfile /etc/grid-security/certificates/dd4b34ea.0 \
     -verify 1> /dev/null


For developers
---------------------------

For developers - minimal.py
---------------------------


This is an alternative implantation of an image list signer in python, 
using the m2crypto wrapper for openssl as does the CLI.

This version is very compact and simplistic.

It is intended for products like repoman, VMIC and Petrags release
system, to see an example of signing thier own image lists, using 
minimal code.

The image list should be placed on a web server.


For deployers
---------------------------


A note on web servers and security:
---------------------------

The security of a signed message with an expiry date is fine to ensure
that a message is not faked. An old image can be presented to 
subscribers blocking seeing the new image list. This is very unlikely 
given HEP name server setups, but to have confidance this is not occuring 
we need an authenticated connection with the web server and that means SSL. 

It is an image list producers desision if they provide http, or https 
authenticated or even X509 authenticated access, Since this tool is based 
on X509 trust I would suggest deploying on an X509 based web server and 
not requiring client have an identify if possible, http(s) maybe enough 
security for many this is a discussion point.


the signed message
from a web server we should know that it is the correct web server. I
am bias to adding restrictions such as only supporting X509 https to
some web servers. What do people think?
