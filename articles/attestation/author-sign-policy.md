---
title: 如何撰寫和簽署 Azure 證明原則
description: 如何撰寫和簽署證明原則的說明。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a3afb12ac831d87b03d0bb16d1b7ef553f1bb906
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006814"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>如何撰寫和簽署證明原則

證明原則是上傳至 Microsoft Azure 證明的檔案。 Azure 證明會提供以證明特定原則格式上傳原則的彈性。 或者，也可以上傳原則的編碼版本 (JSON Web 簽章格式)。 原則管理員負責撰寫證明原則。 在大部分的證明案例中，信賴憑證者扮演原則管理員。 進行證明呼叫的用戶端會傳送證明辨識項，服務會加以剖析並轉換成傳入宣告 (屬性集合，值)。 然後，服務會根據原則中定義的內容來處理宣告，並傳回計算的結果。

此原則包含的規則會決定授權準則、屬性及證明權杖的內容。 範例原則檔案如下所示：

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
原則檔案有三個區段，如上所示：

- **版本**：版本是所遵循文法的版本號碼。 

    ```
    version=MajorVersion.MinorVersion   
    ```

    目前唯一支援的版本為 1.0。

- **authorizationrules**：將要先檢查的宣告規則集合，以判斷 Azure 證明是否應該繼續處理 **issuancerules**。 宣告規則會依照其定義的順序套用。

- **issuancerules**：將要評估的宣告規則集合，以將其他資訊新增至原則中定義的證明結果。 宣告規則會依照其定義的順序套用，而且也是選擇性的。

如需詳細資訊，請參閱[宣告和宣告規則](claim-rule-grammar.md)。
   
## <a name="drafting-the-policy-file"></a>草擬原則檔案

1. 建立新檔案。
1. 新增檔案的版本。
1. 新增 **authorizationrules** 和 **issuancerules** 的區段。

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  授權規則包含 deny() 動作，但不含任何條件，以確保不會處理任何發行規則。 或者，授權規則也可以包含 permit() 動作，以允許處理發行規則。
  
4. 將宣告規則新增至授權規則

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  如果傳入宣告集包含符合類型、值和簽發者的宣告，則 permit() 動作會告訴原則引擎處理 **issuancerules**。
  
5. 將宣告規則新增至 **issuancerules**。

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  傳出宣告集將包含具有下列內容的宣告：

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  複雜的原則可以用類似的方式製作。 如需詳細資訊，請參閱[證明原則範例](policy-examples.md)。
  
6. 儲存檔案。

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>以 JSON Web 簽章格式建立原則檔案

建立原則檔案之後，若要上傳 JWS 格式的原則，請遵循下列步驟。

1. 使用原則 (utf-8 編碼) 產生 JWS、RFC 7515 作為承載
     - Base64Url 編碼原則的承載識別碼應為 "AttestationPolicy"。
     
     範例 JWT：
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (選擇性) 簽署原則。 Azure 證明支援下列演算法：
     - **無**：請勿簽署原則承載。
     - **RS256**：支援用來簽署原則承載的演算法

3. 上傳 JWS 並驗證原則。
     - 如果原則檔案沒有語法錯誤，服務就會接受原則檔案。
     - 如果原則檔案包含語法錯誤，服務就會拒絕原則檔案。

## <a name="signing-the-policy"></a>簽署原則

以下是如何執行原則簽署作業的範例 Python 指令碼

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>後續步驟
- [使用 PowerShell 設定 Azure 證明](quickstart-powershell.md)
- [使用程式碼範例證明 SGX 記憶體保護區](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
