#X.509证书演变及证书格式

## X.509证书历史
### version1

- 1988, defined by [Telecommunication Standardization Sector of the International Telecommunications Union](https://en.wikipedia.org/wiki/ITU-T) (ITU-T)
- [ITU-T X.509 (formerly CCITT X.509) or ISO/IEC 9594-8](https://www.itu.int/ITU-T/recommendations/rec.aspx?rec=2999&lang=en)
- 描述了标准的证书格式



### version2

- 1993

- X.509 Internet Privacy Enhanced Mail (PEM)

- [RFC 1422: Privacy Enhancement for Internet Electronic Mail: Part II: Certificate-Based Key Management](https://tools.ietf.org/html/rfc1422)

- 与v1相比添加了2个field；每个证书只对一个主体生效，如果有多个主体，可以用相同公钥签发多个证书

  ```
  A user may possess multiple certificates which may embody the same or
     different public components.  For example, these certificates might
     represent  a current and a former organizational user identity and a
     residential user identity.  It is recommended that a PEM UA be
     capable of supporting a user who possess multiple certificates,
     irrespective of whether the certificates associated with the user
     contain the same or different DNs or public components.
  ```

  PEM体系下，证书的格式基本是基于x.509 v1标准，但是添加了一些严格的限制条件：比如必须是严格的单根（top-down）树状层次结构: 只有一个IPRA （level 1），PCAs （level 2）必须是已知的, CA (level 3)



### version3

- 2008, ISO/IEC, ITU-T, and ANSI X9
- X.509 or PKIX
- RFC 5280
- x.509 v3添加了extension字段，将RFC 1422的大部分规则通过extension实现了，这样就不用严格地约束CA的结构。
- x.509 v3证书包含: 
  - CA certificate：可以继续签发证书
    - cross-certificates: issuer and subject are different entities; describe a trust relationship between two CAs
    - self-issued certificates: issuer and subject are the same entity; are generated to support changes in policy or operations
    - self-signed certificates: the self-issued certificates where the digital signature may be verified by the public key bound to the certificate; are used to convay a public key for use to begin certificate paths
  - entity certificate
    - 签发给无权签发证书的subject


##X.509证书格式

#### X.509证书编码及存储格式

```
cer/crt是用于存放证书，它是2进制形式存放的，不含私钥。
pem跟crt/cer的区别是它以Ascii来表示，可以用于存放证书或私钥。
pfx/p12用于存放个人证书/私钥，他通常包含保护密码，2进制方式。
p10是证书请求。
p7r是CA对证书请求的回复，只用于导入
p7b以树状展示证书链(certificate chain)，同时也支持单个证书，不含私钥。
```



#### X.509证书数据结构

```
Certificate ::= SEQUENCE {
        tbsCertificate       TBSCertificate, -- 证书主体
        signatureAlgorithm   AlgorithmIdentifier, -- 证书签名算法标识
        signatureValue       BIT STRING --证书签名值,是使用signatureAlgorithm部分指定的签名算法对tbsCertificate证书主题部分签名后的值.
         }
   TBSCertificate ::= SEQUENCE {
        version         [0] EXPLICIT Version DEFAULT v1, -- 证书版本号
        serialNumber         CertificateSerialNumber, -- 证书序列号，对同一CA所颁发的证书，序列号唯一标识证书
        signature            AlgorithmIdentifier, --证书签名算法标识
        issuer               Name,                --证书发行者名称
        validity             Validity,            --证书有效期
        subject              Name,                --证书主体名称
        subjectPublicKeyInfo SubjectPublicKeyInfo,--证书公钥
        issuerUniqueID [1] IMPLICIT UniqueIdentifier OPTIONAL,
                             -- 证书发行者ID(可选)，只在证书版本2、3中才有
        subjectUniqueID [2] IMPLICIT UniqueIdentifier OPTIONAL,
                             -- 证书主体ID(可选)，只在证书版本2、3中才有
        extensions      [3] EXPLICIT Extensions OPTIONAL
                             -- 证书扩展段（可选），只在证书版本3中才有
        }
   Version ::= INTEGER { v1(0), v2(1), v3(2) }
   CertificateSerialNumber ::= INTEGER
   AlgorithmIdentifier ::= SEQUENCE {
        algorithm               OBJECT IDENTIFIER,
        parameters              ANY DEFINED BY algorithm OPTIONAL }
   parameters:
   Dss-Parms ::= SEQUENCE { -- parameters ，DSA(DSS)算法时的parameters,
RSA算法没有此参数
        p             INTEGER,
        q             INTEGER,
        g             INTEGER }
 
signatureValue：
Dss-Sig-Value ::= SEQUENCE { -- sha1DSA签名算法时,签名值
                   r       INTEGER,
                      s       INTEGER }
 
   Name ::= CHOICE {
     RDNSequence }
   RDNSequence ::= SEQUENCE OF RelativeDistinguishedName
   RelativeDistinguishedName ::=
     SET OF AttributeTypeAndValue
   AttributeTypeAndValue ::= SEQUENCE {
     type     AttributeType,
     value    AttributeValue }
   AttributeType ::= OBJECT IDENTIFIER
   AttributeValue ::= ANY DEFINED BY AttributeType
 
   Validity ::= SEQUENCE {
        notBefore      Time,  -- 证书有效期起始时间
        notAfter       Time  -- 证书有效期终止时间  }
   Time ::= CHOICE {
        utcTime        UTCTime,
        generalTime    GeneralizedTime }
   UniqueIdentifier ::= BIT STRING
   SubjectPublicKeyInfo ::= SEQUENCE {
        algorithm            AlgorithmIdentifier, -- 公钥算法
        subjectPublicKey     BIT STRING            -- 公钥值 }
   subjectPublicKey:
   RSAPublicKey ::= SEQUENCE { -- RSA算法时的公钥值
             modulus            INTEGER, -- n
             publicExponent     INTEGER -- e -- }
 
   Extensions ::= SEQUENCE SIZE (1..MAX) OF Extension
   Extension ::= SEQUENCE {
        extnID      OBJECT IDENTIFIER,
        critical    BOOLEAN DEFAULT FALSE,
        extnValue   OCTET STRING }

```



#### X.509证书各字段描述

##### X.509证书基本部分

1. version 版本
   - integer
   - 标识证书的X.509 标准的版本（版本1、版本2或是版本3）。版本1只支持简单的字段，版本2增加了两个标识符，而版本3增加了扩展功能。
2. serialNumber 序列号
   - positive integer
   - 标识证书的唯一整数，由证书颁发者分配的本证书的唯一标识符(unique in one CA, no longer than 20 octets)。发放证书的实体有责任为证书指定序列号，以使其区别于该实体发放的其它证书。此信息用途很多，例如，如果某一证书被撤消，其序列号将放到证书撤消清单 (CRL) 中。
   - 现在序列号要求是无序的，至少包括20位的熵。
3. signatureAlgorithm 签名算法
  - 用于签证书的算法标识，由对象标识符加上相关的参数组成，用于说明本证书所用的数字签名算法。
  - 例如，SHA-1和RSA的对象标识符就用来说明该数字签名是利用RSA对SHA-1杂凑加密。
4. validity
   - notBefore + notAfter: UTCTime (before2049), GeneralizedTime (after 2050)
   - 证书有效期的时间段。
5. subject/issuer

   - Subject: 证书拥有者的可识别名，这个字段必须是非空的，除非在证书扩展中有别名。证书可以识别其公钥的实体名，此名称使用 X.500 标准，因此在Internet中应是唯一的。它是实体的特征名 (DN)，例如，
     CN=Java Duke，OU=Java Software Division，O=Sun Microsystems Inc，C=US
   - Issuer: 证书颁发者的可识别名（DN）。签写证书的实体的 X.500 名称。它通常为一个 CA。 使用该证书意味着信任签写该证书的实体（注意：有些情况下（例如根或顶层 CA 证书），签发人会签写自己的证书）。
   - O/organizationName: organization 组织名称
   - OU/organizationalUnitName: (例如'(c) 2007 GeoTrust Inc. - For authorized use only')
   - C/countryName: country 国家代码
   - ST/stateOrProvinceName: state或province
   - emailAddress: xxx@xxx，不区分大小写
   - CN: commonName 字符串或域名都有
   - SN: surname
   - GN: givenName
   - L/localityName: locality 城市名
   - DC: domainComponent
   - pseudonym
   - title
   - initials
   - Jurisdictionc: "Washington" "Delaware"
   - Postalcode
   - Businesscategory
   - Jurisdictionst
   - Street
   - serialNumber：序列号 整数
   - businessCategory: 'Private Organization'
   - 其中CN=, C=, ST=, O=, OU= and serialNumber=是必选项，
6. Subject public key info
  - publicKey: bit string,  AlgorithmIdentifier
  - 主体的公钥（以及算法标识符）。这是被命名实体的公钥，同时包括指定该密钥所属公钥密码系统的算法标识符及所有相关的密钥参数。
7. 颁发者唯一标识符 unique identifier
  - 标识符—证书颁发者的唯一标识符，仅在版本2和版本3中有要求，属于可选项。
8. 主体唯一标识符
  - 证书拥有者的唯一标识符，仅在版本2和版本3中有要求，属于可选项。

##### X.509证书扩展部分
- 可选的标准和专用的扩展（仅在版本2和版本3中使用），扩展部分的元素都有这样的结构：
   Extension ::= SEQUENCE {
        extnID      OBJECT IDENTIFIER, -- 表示一个扩展元素的OID
        critical    BOOLEAN DEFAULT FALSE, -- 表示这个扩展元素是否极重要
        extnValue   OCTET STRING  -- 表示这个扩展元素的值，字符串类型。
   }
- 扩展部分不能有重复字段

###### 一、必须有的

1. authorityKeyIdentifier 
   - Octet string, id-ce 35
   - 当一个issuer有多个signing keys时，用来标示此证书的私钥对应的是哪一个pubkey（但self-signed证书可以没有）。
   - The keyIdentifier field of the AuthorityKeyIdentifier extension [MUST](https://ldapwiki.com/wiki/MUST) be included in all certificates generated by conforming CAs to facilitate certification path construction.
   - 这个字段可以包含keyid或issuer。keyid后携带的是父节点证书的subjectKeyIdentifier，如果带有"always"值，则该选项失败时会直接返回error。issuer选项携带issuer证书的issuer和serialNumber，如果keyid选项失败或不存在时才会用到issuer option。
   - 'keyid:CC:82:61:39:A4:DB:44:C6:91:D6:F8:37:5F:76:C3:13:02:C4:02:FF'
2. subjectKeyIdentifier
   - 这个字段可以是哈希值，也可以是十六进制字符串（ id-ce 14）
   - 'E5:6F:56:4A:E5:F0:61:6F:CD:C4:48:B5:74:1A:84:01:E2:1C:F7:38'
   - 证书所含密钥的唯一标识符，用来区分同一证书拥有者的多对密钥。
   - CA证书MUST包含这个字段，即basicconstraints字段是True的时候必须要有；end entity - certificate SHOULD be included.
   - 这个字段的value必须和“由该证书签发的其他证书”的authorityKeyIdentifier字段的value相同。
   - 在cert path验证的过程中不必须验证这个值

3. keyUsage

   -  multi valued (id-ce 15)  四字节

   - 一个比特串，包含（限定）证书的公钥可以完成的功能或服务列表，如：证书签名、数据加密等。

     具体如下：

     ```
     digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment, keyAgreement, keyCertSign, cRLSign, encipherOnly, decipherOnly
     ```

     digitalSignature: 用于验证an entity authentication service, a data origin authentication service, and/or an integrity service的数字签名
     nonRepudiation/contentCommitment: 用于保证不可抵赖性，防止签名主体否认一些行为
     keyEncipherment：表示公钥用于enciphering private or secret keys
     dataEncipherment：表示公钥用于直接加密user data，而不用其他对称密码
     keyAgreement：表示用于密钥协商
     keyCertSign：表示用于验证公钥证书的签名
     cRLSign：验证证书撤销列表的签名
     encipherOnly：与keyAgreement一起存在时表示在协商密钥的过程中加密数据
     decipherOnly：与keyAgreement一起存在时表示在协商密钥的过程中解密数据

   - 例子

     ```
     keyUsage=digitalSignature, nonRepudiation
     keyUsage=critical, keyCertSign
     
     ('Certificate Sign, CRL Sign', b'\x03\x02\x01\x06')
     ('Digital Signature, Key Encipherment', b'\x03\x02\x05\xa0')
     ('Digital Signature, Non Repudiation', b'\x03\x02\x06\xc0')
     ('Digital Signature', b'\x03\x02\x07\x80')
     ```

   - 如果证书的public key是用来验证其他pubkey cert或CRLs的，必须有这个字段

   - 如果某一证书将 KeyUsage 扩展标记为“极重要”，而且设置为“keyCertSign”，则在 SSL 通信期间该证书出现时将被拒绝，因为该证书扩展表示相关私钥应只用于签写证书，而不应该用于 SSL。

4. subjectAltName 使用者的可选名称

   - (id-ce 17))

   - 指出证书拥有者的别名，包括email(email address), URI, DNS(DNS domain name), RID (a registered ID: OBJECT IDENTIFIER), IP(ipv4 or ipv6), dirName(a distinguished name)和otherName。

   - 别名是和DN绑定在一起的。无subject时，SAN必须有.

   - 例子

     ```
     subjectAltName=email:copy,email:my@other.address,URI:http://my.url.here/
     subjectAltName=IP:192.168.7.1
     subjectAltName=IP:13::17
     subjectAltName=email:my@other.address,RID:1.2.3.4
     subjectAltName=otherName:1.2.3.4;UTF8:some other identifier
     subjectAltName=dirName:dir_sect
     [dir_sect]
      C=UK
      O=My Organization
      OU=My Unit
      CN=My Name
     ```

5. certificatePolicies

   - 'Policy: 2.16.840.1.113733.1.7.23.3,  CPS: https://d.symcb.com/cps,  User Notice:,    Explicit Text:   https://d.symcb.com/rpa'

   - 由对象标识符和限定符组成，这些对象标识符说明证书的颁发和使用策略有关

   - CPS Framework: RFC3647 https://tools.ietf.org/html/rfc3647

     ```
     - Policy: policy OID
     - CPS: an URL to a Certificate Practice Statement document that describes the policy under which the certificate in the subject was issued.
     - User Notice: is a small piece of text (RFC recommends to use no more than 200 characters) that describes particular policy.
     ```

     


######二、可以有的

1. crlDistributionPoints
   - multivalued
   
   - 指明CRL的分布点

   - 可以是name:value pair, 也可以只有一个value。
   
   - 如果name是“fullname”，value部分应该用SAN相同的格式包含分发点的full name; 如果name是“relatevename”，value部分应该包含代表DN fragment的section name；如果name是“CRLIssuer”，value部分应该和subjectAltName具有相同的格式；如果name是“reasons”，value部分应该包含逗号分隔的原因（有效的原因包括："keyCompromise", "CACompromise", "affiliationChanged", "superseded", "cessationOfOperation", "certificateHold", "privilegeWithdrawn" and "AACompromise".）。
   
   - 例如
   
     ```
     crlDistributionPoints=URI:http://myhost.com/myca.crl
     crlDistributionPoints=URI:http://my.com/my.crl,URI:http://oth.com/my.crl
     
     crlDistributionPoints=crldp1_section
     [crldp1_section]
      fullname=URI:http://myhost.com/myca.crl
      CRLissuer=dirName:issuer_sect
      reasons=keyCompromise, CACompromise
     [issuer_sect]
      C=UK
      O=Organisation
      CN=Some Name
     
     Full Name:,  URI:http://t.symcb.com/ThawtePCA-G2.crl'
     ```
   
2. 私钥的使用期

   - Privatekeyusageperiod
   - 'Not After: Aug 22 16:41:51 2018 GMT'

   - 指明证书中与公钥相联系的私钥的使用期限，它也有Not Before和Not After组成。若此项不存在时，公私钥的使用期是一样的。

3. 颁发者别名
   指出证书颁发者的别名，如电子邮件地址、IP地址等，但颁发者的DN必须出现在证书的颁发者字段。

4. basicConstraints (待确定)
   - multi valued
   - 该字段表明此证书是否为CA证书，值为CA:TRUE或CA:FALSE，前者后面可以增加一个pathlen。CA证书必须有此字段并且值为TRUE，end cert可以为FALSE也可以不包含此字段。pathlen表示自此证书向下证书链的最大长度，因此若其值为0表示此证书只能用来签发end certificate。
   - 理论上所有CA证书都必须包含这个扩展；而实际中又一些版本1协议的根证书还在使用中，它们不包含该扩展。
   - 'CA:TRUE, pathlen:0'
- CA:FALSE
  
5. 策略映射

   - policyMappings (id-ce 33)
   - '2.16.840.1.101.3.2.1.3.37:2.16.840.1.113733.1.7.23.3.1.8, 2.16.840.1.101.3.2.1.3.3:2.16.840.1.113733.1.7.23.3.1.6'
   - 表明两个CA之间的一个或多个策略OID之间的等价关系，仅在CA证书里存在。

6. policyConstraints

   - 'Require Explicit Policy:0, Inhibit Policy Mapping:1'
   - 只在CA证书中使用，用于表示遍历证书路径时的一些策略限制。
   - hibitPolicyMappings表示忽略路径中第N个中间证书之后的所有策略映射
   - requireExplicitPolicy表示如果该路径中的中间证书超过N个，则要求它们都具有X策略
   - 总之：basicConstraints表示是否是CA，如果是，则可以创建pathLen长度的信任路径。policyConstraints和信任路径的执行方式有关，它约束有多少个中间证书具有策略映射，或者从某一个证书开始忽略策略映射。

7. extendedKeyUsage
   - 此字段用于表明证书公钥的用途（代替或补充keyUsage扩展），只在end entity certificate中使用。

   - 这个字段可以包含一些object short name或者OID形式的点分数字，只有部分的OID有效。例如，'TLS Web Server Authentication, TLS Web Client Authentication, Code Signing, Netscape Server Gated Crypto, 2.16.840.1.113733.1.8.1'

   - ```
     id-kp OBJECT IDENTIFIER ::= { id-pkix 3 }
     
        id-kp-serverAuth             OBJECT IDENTIFIER ::= { id-kp 1 }
        -- TLS WWW server authentication
        -- Key usage bits that may be consistent: digitalSignature,
        -- keyEncipherment or keyAgreement
     
        id-kp-clientAuth             OBJECT IDENTIFIER ::= { id-kp 2 }
        -- TLS WWW client authentication
        -- Key usage bits that may be consistent: digitalSignature
        -- and/or keyAgreement
     
        id-kp-codeSigning             OBJECT IDENTIFIER ::= { id-kp 3 }
        -- Signing of downloadable executable code
        -- Key usage bits that may be consistent: digitalSignature
     
        id-kp-emailProtection         OBJECT IDENTIFIER ::= { id-kp 4 }
        -- Email protection
        -- Key usage bits that may be consistent: digitalSignature,
        -- nonRepudiation, and/or (keyEncipherment or keyAgreement)
     
        id-kp-timeStamping            OBJECT IDENTIFIER ::= { id-kp 8 }
        -- Binding the hash of an object to a time
        -- Key usage bits that may be consistent: digitalSignature
        -- and/or nonRepudiation
     
        id-kp-OCSPSigning            OBJECT IDENTIFIER ::= { id-kp 9 }
        -- Signing OCSP responses
        -- Key usage bits that may be consistent: digitalSignature
        -- and/or nonRepudiation
        
      Value                  Meaning
      -----                  -------
      serverAuth             SSL/TLS Web Server Authentication.
      clientAuth             SSL/TLS Web Client Authentication.
      codeSigning            Code signing.
      emailProtection        E-mail Protection (S/MIME).
      timeStamping           Trusted Timestamping
      OCSPSigning            OCSP Signing
      ipsecIKE               ipsec Internet Key Exchange
      msCodeInd              Microsoft Individual Code Signing (authenticode)
      msCodeCom              Microsoft Commercial Code Signing (authenticode)
      msCTLSign              Microsoft Trust List Signing
      msEFS                  Microsoft Encrypted File System
     ```

   - 例子

     ```
      extendedKeyUsage=critical,codeSigning,1.2.3.4
      extendedKeyUsage=serverAuth,clientAuth
     ```

8. authorityinfoaccess

   - 'OCSP - URI:http://gn.symcd.com,CA Issuers - URI:http://gn.symcb.com/gn.crt'
   - Provides one or more URLs from where an application or service can retrieve the issuing CA certificate. Used to validate the certificate of the CA that issued the certificate also referred to as the parent CA for revocation and validity.
   - 浏览器可以通过访问AIA的URI去获取intermediate certificate，并加入到本地证书哭

9. subjectInfoAccess

   - 'CA Repository - URI:http://pki.strac.org/bridge/certificates/STRACBridgeRootCA.p7c'

   - 表示去哪里访问此证书的subject信息


10. Ct_precert_poison
   - NULL
   
   - precertificate是一类用于CT中的特殊的SSL证书，它们不被用来认证server或connection中，只用于将CT数据（例如，提交CTlog的证明）嵌入一张证书中。
   
   - precertificate的使用场景
   
     - A Certificate Authority (CA) is going to sign and issue a certificate to a client. They need to make it compliant with browsers’ CT policies, so they need to submit the certificate to a CT Log. 
     - The CA has a few choices for how to deliver proof that a certificate has been logged. If they want to embed that proof directly in the certificate, they will need to use a precertificate. 
     - Before the CA signs the final certificate, they first create a precertificate, which contains the same data but is formatted a specific way so that it is not treated as a valid SSL certificate. 
     - The CA submits the precertificate to a CT log and receives an SCT (Signed Certificate Timestamp). This is a file signed by the log which can be used to cryptographically prove to clients that the certificate was logged. 
     - Now that the CA has the SCT it can put it into the final certificate and include it as part of the signed data. The CA issues the final certificate with the SCT embedded into it. 
     - The end-user can now deploy their certificate and have proof of their CT compliance contained within their certificate. This is the optimal way to include an SCT.
   
     Read more at: https://www.thesslstore.com/blog/ssl-precertificates/
11. Ct_precert_scts
  
   - precertificate的SCT，这个字段和ct_precert_poison一样都很迷，标准和博客里都没有详细的解释。根据理解，在新增CT log之前会签发precertificate，用于表示CT log中新增一条证书的记录？是谁签发的precertificate？precertificate和与之相关联的certificate同时存在于CT log中吗？为什么ct_precert_poison和Ct_precert_scts两个字段会不同时出现在precertificate中？为什么一条Ct_precert_scts记录中有很多时间戳？
   - 'Signed Certificate Timestamp:,    Version   : v1 (0x0),    Log ID    : DD:EB:1D:2B:7A:0D:4F:A6:20:8B:81:AD:81:68:70:7E:,                2E:8E:9D:01:D5:5C:88:8D:3D:11:C4:CD:B6:EC:BE:CC,    Timestamp : Feb 14 17:25:02.952 2017 GMT,    Extensions: none,    Signature : ecdsa-with-SHA256,                30:45:02:20:76:95:27:CC:57:B5:3B:BA:FB:5E:A9:63:,                83:E6:F2:1E:C8:69:91:84:B7:58:A8:60:8F:8D:10:6C:,                C9:83:67:E5:02:21:00:F7:BC:BE:8E:7A:05:16:CA:D7:,                26:C9:E1:AC:3A:6A:60:DB:24:96:0C:CD:CB:57:37:41:,                B7:8D:58:9A:D8:5F:DD,Signed Certificate Timestamp:,    Version   : v1 (0x0),    Log ID    : A4:B9:09:90:B4:18:58:14:87:BB:13:A2:CC:67:70:0A:,                3C:35:98:04:F9:1B:DF:B8:E3:77:CD:0E:C8:0D:DC:10,    Timestamp : Feb 14 17:25:03.004 2017 GMT,    Extensions: none,    Signature : ecdsa-with-SHA256,                30:45:02:21:00:83:EB:8A:4F:87:13:DB:2C:DF:3F:9C:,                6C:A2:79:89:B2:29:71:30:9C:F0:E5:7D:8D:79:86:A1:,                28:DB:97:88:F7:02:20:19:03:17:E3:A4:A9:FD:C3:30:,                B3:E2:FC:24:81:BA:C1:9C:39:31:E4:BC:C3:AC:C2:44:,                DE:C5:AA:02:7D:E3:FF'



## CT证书

RFC6962

特殊性

- 为了适应各种证书签发软件的quirks，CT log中允许添加过期的、被撤销的、未经过完整验证的证书，甚至因各种理由无效的证书，但是必须保证每个记录有完整的证书链、根证书属于可信CA。



## 参考资源

X.509证书结构和实例https://www.cnblogs.com/chnking/archive/2007/08/28/872104.html

OpenSSL: https://www.openssl.org/docs/manmaster/man5/x509v3_config.html

Certificate Transparency：https://tools.ietf.org/html/rfc6962#page-18

Survival guides - TLS/SSL and SSL (X.509) Certificates https://www.zytrax.com/tech/survival/ssl.html

《Bulletproof SSL and TLS: Understanding and Deploying SSL/TLS and PKI to Secure Servers and Web Applications》

《HTTPS权威指南：在服务器和Web应用上部署SSL/TLS和PKI》