# A4:2017 XML External Entities (XXE)

| Agen ancaman / vektor serangan | Kelemahan Keamanan          | Dampak            |
| -- | -- | -- |
| Access Lvl : Eksploitasi 2 | Prevalensi 3: Deteksi 2 | Teknis 3: Bisnis |
| Penyerang dapat mengeksploitasi prosesor XML yang rentan jika mereka dapat mengunggah XML atau menyertakan konten yang tidak bersahabat dalam dokumen XML, mengeksploitasi kode yang rentan, ketergantungan, atau integrasi. | Secara default, banyak pemroses XML yang lebih lama mengizinkan spesifikasi entitas eksternal, URI yang direferensikan dan dievaluasi selama pemrosesan XML. [SAST](https://www.owasp.org/index.php/Source_Code_Analysis_Tools) tool apat menemukan masalah ini dengan memeriksa dependensi dan konfigurasi. [DAST](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) tool memerlukan langkah manual tambahan untuk mendeteksi dan memanfaatkan masalah ini. Manual tester perlu untuk dilatih tentang cara menguji XXE, karena tidak umum diuji pada 2017. | Kelemahan ini dapat digunakan untuk mengekstrak data, menjalankan permintaan jarak jauh dari server, scan sistem internal, melakukan denial-of-service attack,serta melakukan serangan lainnya. |

## Apakah Aplikasi itu Rentan?

Aplikasi dan layanan web berbasis XML tertentu atau integrasi downstream mungkin rentan terhadap serangan jika:

* Aplikasi menerima XML secara langsung atau unggahan XML, terutama dari sumber yang tidak tepercaya, atau menyisipkan data yang tidak tepercaya ke dalam dokumen XML, yang kemudian diurai oleh pemroses XML.
* Setiap prosesor XML dalam aplikasi atau layanan web berbasis SOAP memiliki [_document type definitions (DTDs_)](https://en.wikipedia.org/wiki/Document_type_definition) yang diijinkan. Karena mekanisme yang tepat untuk menonaktifkan pemrosesan DTD bervariasi berdasarkan prosesor, praktik yang baik untuk berkonsultasi dengan referensi seperti [OWASP Cheat Sheet 'XXE Prevention'](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Prevention_Cheat_Sheet). 
* If the application uses SAML for identity processing within federated security or single sign on (SSO) purposes. SAML uses XML for identity assertions, and may be vulnerable.
* If the application uses SOAP prior to version 1.2, it is likely susceptible to XXE attacks if XML entities are being passed to the SOAP framework.
* Being vulnerable to XXE attacks likely means that the application is vulnerable to denial of service attacks including the Billion Laughs attack

## How To Prevent

Developer training is essential to identify and mitigate XXE. Besides that, preventing XXE requires:

* Whenever possible, use less complex data formats such as JSON, and avoiding serialization of sensitive data.
* Patch or upgrade all XML processors and libraries in use by the application or on the underlying operating system. Use dependency checkers. Update SOAP to SOAP 1.2 or higher.
* Disable XML external entity and DTD processing in all XML parsers in the application, as per the [OWASP Cheat Sheet 'XXE Prevention'](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Prevention_Cheat_Sheet). 
* Implement positive ("whitelisting") server-side input validation, filtering, or sanitization to prevent hostile data within XML documents, headers, or nodes.
* Verify that XML or XSL file upload functionality validates incoming XML using XSD validation or similar.
* SAST tools can help detect XXE in source code, although manual code review is the best alternative in large, complex applications with many integrations.

If these controls are not possible, consider using virtual patching, API security gateways, or Web Application Firewalls (WAFs) to detect, monitor, and block XXE attacks.

## Example Attack Scenarios

Numerous public XXE issues have been discovered, including attacking embedded devices. XXE occurs in a lot of unexpected places, including deeply nested dependencies. The easiest way is to upload a malicious XML file, if accepted:

**Scenario #1**: The attacker attempts to extract data from the server:

```
  <?xml version="1.0" encoding="ISO-8859-1"?>
    <!DOCTYPE foo [
    <!ELEMENT foo ANY >
    <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
    <foo>&xxe;</foo>
```

**Scenario #2**: An attacker probes the server's private network by changing the above ENTITY line to:
```
   <!ENTITY xxe SYSTEM "https://192.168.1.1/private" >]>
```

**Scenario #3**: An attacker attempts a denial-of-service attack by including a potentially endless file:

```
   <!ENTITY xxe SYSTEM "file:///dev/random" >]>
```

## References

### OWASP

* [OWASP Application Security Verification Standard](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project#tab=Home)
* [OWASP Testing Guide: Testing for XML Injection](https://www.owasp.org/index.php/Testing_for_XML_Injection_(OTG-INPVAL-008))
* [OWASP XXE Vulnerability](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Processing)
* [OWASP Cheat Sheet: XXE Prevention](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Prevention_Cheat_Sheet)
* [OWASP Cheat Sheet: XML Security](https://www.owasp.org/index.php/XML_Security_Cheat_Sheet)

### External

* [CWE-611: Improper Restriction of XXE](https://cwe.mitre.org/data/definitions/611.html)
* [Billion Laughs Attack](https://en.wikipedia.org/wiki/Billion_laughs_attack)
* [SAML Security XML External Entity Attack](https://secretsofappsecurity.blogspot.tw/2017/01/saml-security-xml-external-entity-attack.html)
* [Detecting and exploiting XXE in SAML Interfaces](https://web-in-security.blogspot.tw/2014/11/detecting-and-exploiting-xxe-in-saml.html)
