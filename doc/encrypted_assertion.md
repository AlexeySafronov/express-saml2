# Encrypted Assertion

According to the guideline of SAML, our module leaves some security options for developers. If the assertion contains some sensitive information, Identity Provider may want to do encryption. In IdP's construction, add the following settings as follow:

```javascript
var saml = require('express-saml2');
var setting = {
    isAssertionEncrypted: true,
    encPrivateKeyFile: './key/idp/encryptKey.pem',
    encPrivateKeyFilePass: '9Ehf1bcRmp8We9g7hGPxT5QeP2qzTALN'
};
var idp = saml.IdentityProvider(setting, './metadata_idp.xml');
```
If you remember SP configuration for signing a request, there are two parameters in the setting object. They are `privateKeyFile` and `privateKeyFilePass`. **Warning:** If you are applying our solution instead of another 3rd party IdP, it's suggested not to use same key for both signing and encryption.

In IdP's metadata, the certificate must be included in order to decrypt the assertion.

```xml
<KeyDescriptor use="encryption">
    <KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <X509Data>
            <X509Certificate>MIID6TCCAtGg...</X509Certificate>
        </X509Data>
    </KeyInfo>
</KeyDescriptor>
```

**The sample encrypted response with signature:**
```xml
<samlp:Response xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_8e8dc5f69a98cc4c1ff3427e5ce34606fd672f91e6" Version="2.0" IssueInstant="2014-07-17T01:01:48Z" Destination="http://sp.example.com/demo1/index.php?acs" InResponseTo="_446395c4e751297f8900b53b066873d5fee011e5">
  <saml:Issuer>https://idp.example.com/metadata</saml:Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
  </samlp:Status>
  <saml:EncryptedAssertion><xenc:EncryptedData Type="http://www.w3.org/2001/04/xmlenc#Element" xmlns:xenc="http://www.w3.org/2001/04/xmlenc#">
  <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes256-cbc" />
  <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
  <e:EncryptedKey xmlns:e="http://www.w3.org/2001/04/xmlenc#">
    <e:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5">
      <DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
    </e:EncryptionMethod>
    <KeyInfo>
      <X509Data><X509Certificate>MIIDozCCAougAwIBAgIJAKNsmL8QbfpwMA0GCSqGSIb3DQEBCwUAMGgxCzAJBgNVBAYTAkhLMRIwEAYDVQQIDAlIb25nIEtvbmcxCzAJBgNVBAcMAkhLMRMwEQYDVQQKDApub2RlLXNhbWwyMSMwIQYJKoZIhvcNAQkBFhRub2RlLnNhbWwyQGdtYWlsLmNvbTAeFw0xNTA3MDUxNzU2NDdaFw0xODA3MDQxNzU2NDdaMGgxCzAJBgNVBAYTAkhLMRIwEAYDVQQIDAlIb25nIEtvbmcxCzAJBgNVBAcMAkhLMRMwEQYDVQQKDApub2RlLXNhbWwyMSMwIQYJKoZIhvcNAQkBFhRub2RlLnNhbWwyQGdtYWlsLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAMQJAB8JrsLQbUuJa8akzLqO1EZqClS0tQp+w+5wgufp07WwGn/shma8dcQNj1dbjszI5HBeVFjOKIxlfjmNB9ovhQPstBjP/UPQYp1Ip2IoHCYX9HDgMz3xyXKbHthUzZaECz+p+7WtgwhczRkBLDOm2k15qhPYGPw0vH2zbVRGWUBS9dy2Mp3tqlVbP0xZ9CDNkhCJkV9SMNfoCVW/VYPqK2QBo7ki4obm5x5ixFQSSHsKbVARVzyQH5iNjFe1TdAp3rDwrE5Lc1NQlQaxR5Gnb2NZApDORRZIVlNv2WUdi9QvM0yCzjQ90jP0OAogHhRYaxg0/vgNEye46h+PiY0CAwEAAaNQME4wHQYDVR0OBBYEFEVkjcLAITndky090Ay74QqCmQKIMB8GA1UdIwQYMBaAFEVkjcLAITndky090Ay74QqCmQKIMAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEBAG4lYX3KQXenez4LpDnZhcFBEZi9YstUKPF5EKd+WplpVbcTQc1A3/Z+uHRmyV8h+pQzeF6Liob37G87YpacPplJI66cf2Rj7j8hSBNbdr+66E2qpcEhAF1iJmzBNyhb/ydlEuVpn8/EsoP+HvBeiDl5gon3562MzZIgV/pLdTfxHyW6hzAQhjGq2UhcvR+gXNVJvHP2eS4jlHnJkB9bfo0kvf87Q+D6XKX3q5c3mO8tqW6UpqHSC+uLEpzZiNLeuFa4TUIhgBgjDjlRrNDKu8ndancSn3yBHYnqJ2t9cR+coFnnjYABQpNrvk4mtmXY8SXoBzYG9Y+lqeAun6+0YyE=</X509Certificate></X509Data>
    </KeyInfo>
    <e:CipherData>
      <e:CipherValue>gvOB36ehJ4C8O78xe+kmZpFrbtNWSRv4JZsCkD9QM3FVQyy1m0pgZ32m2p9M5Vvu5tr8r5nBl+syqGvCZeZFtvYg+LOwGlzcOKRFbfOlmmB6LwBcNsFvujeoEXVgJW/vF/yJcqBODyq5EpUbUmYpnUov6qnatHkAYhKfHS5UtNRY+mden3Sh89y0k3NFz1qH1LZPppQTti764c8+ML1WFa+jgdUba9t6uu1XBoHvzy9XeptdKgT2HGx/TGAmylSmzChlgwjopsCkK+3HoLsfy613/bAqTyxPU2JjlBUjy8RwK6tbofpTJpak+bbPxQBp+SOxAOetAesYuinG1uNpkA==</e:CipherValue>
    </e:CipherData>
  </e:EncryptedKey>
</KeyInfo>
  <xenc:CipherData>
    <xenc:CipherValue>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</xenc:CipherValue>
  </xenc:CipherData>
</xenc:EncryptedData></saml:EncryptedAssertion>
<Signature xmlns="http://www.w3.org/2000/09/xmldsig#"><SignedInfo><CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/><SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/><Reference URI="#_d71a3a8e9fcc45c9e9d248ef7049393fc8f04e5f75"><Transforms><Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/></Transforms><DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/><DigestValue>upUaQyNo8KYfaugAsM9I6e/HYNQ=</DigestValue></Reference></SignedInfo><SignatureValue>T3eM5SxwxP8wH+w1jxH/YezcsC03xU0N19uEggWbL//adCbdvPEWqnYDRlRi6HqdfLTAxqt0LqXzy4Sx2Sj/G3dKA1fTu8qZueqg1oTdKIHEO7BKQlWj+vhsxP4RJwOaooZ195Ez0DsqBjIYM8O+hrmGCpKoJNz7ZaASDhnBBqg4UyilA/VXwP2Wq/LwXdQQ+SfgbtmqxfrcqRQtN87aL3YdPFoS4oR6Q8d97g+YiSbsSvTrxfU5vDEo1tRKxvAQXDDxRBral9kELhqcd3Aumcr9zPF00KxkYIAKtyWd5RsWgOcfbiEQpCit0vh74Y1HplLhzeLhPI/RrLB5gSI4DA==</SignatureValue></Signature></samlp:Response>
```
Now all you need to do is to use `sp.parseLoginResponse` again to parse and verify the response.

```javascript
router.post('/acs', function(req, res, next) {
    sp.parseLoginResponse(idp, 'post', req, function(parseResult) {
        // Use the parseResult can do customized action
    });
});
```

Currently, we support the following encrpytion algorithms:

**Data encryption algorithms**
* http://www.w3.org/2001/04/xmlenc#tripledes-cbc
* http://www.w3.org/2001/04/xmlenc#aes128-cbc
* http://www.w3.org/2001/04/xmlenc#aes256-cbc

**Key encryption algorithms**
* http://www.w3.org/2001/04/xmlenc#rsa-1_5
* http://www.w3.org/2001/04/xmlenc#rsa-oaep-mgf1p

Credits to [auth0/node-xml-encryption](https://github.com/auth0/node-xml-encryption)