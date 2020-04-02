---
title: 使用分享存取簽名對 Azure 事件中心進行身份驗證存取
description: 本文介紹如何使用共用訪問簽名對事件中心資源的訪問進行身份驗證。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: 82773ab6decfe15ee1a9d839a1f10a158ae72c42
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521326"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>使用分享存取簽章 (SAS) 對事件中心資源進行身份驗證存取
共用存取簽名 (SAS) 使您能夠精細地控制向具有共用存取簽名的用戶端授予的存取類型。 以下是您可以在 SAS 中設定的一些控制項: 

- SAS 有效的間隔,包括開始時間和到期時間。
- SAS 所授與的權限。 例如,事件中心命名空間的 SAS 可能會授予偵聽許可權,但不能授予發送許可權。
- 只有出示有效認證的用戶端才能將資料傳送到事件中樞。
- 用戶端無法類比其他用戶端。
- 可以阻止 rouge 客戶端將數據發送到事件中心。

本文介紹使用 SAS 對事件中心資源的訪問進行身份驗證。 要瞭解如何使用 SAS**授權**存取事件中心資源,請參閱[本文](authorize-access-shared-access-signature.md)。 

> [!NOTE]
> Microsoft 建議您盡可能使用 Azure AD 認證作為安全最佳做法,而不是使用共享存取簽名,這些簽名更易於破壞。 雖然您可以繼續使用共享訪問簽名 (SAS) 來授予對事件中心資源的細粒度訪問,但 Azure AD 提供了類似的功能,而無需管理 SAS 令牌或擔心撤銷受攻擊的 SAS。
> 
> 有關 Azure 事件中心中的 Azure AD 整合的詳細資訊,請參閱[使用 Azure AD 授權存取事件中心](authorize-access-azure-active-directory.md)。 


## <a name="configuring-for-sas-authentication"></a>設定 SAS 認證
您可以在事件中心命名空間或實體(事件中心中心實例或事件中心中的 Kafka 主題)設定事件中心共用存取授權規則。 當前不支援在消費者組上配置共用存取授權規則,但您可以使用在命名空間或實體上配置的規則來保護對消費者組的訪問。 

下圖顯示了授權規則如何應用於範例實體。 

![設定授權規則](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

在此示例中,範例事件中心命名空間(範例命名空間)有兩個實體:eh1 和主題 1。 授權規則既在實體級別,也在命名空間級別定義。  

管理規則、發送RuleNS和偵聽RuleNS授權規則適用於事件中心實例 eh1 和主題 t1。 偵聽規則-eh 和 sendRule-eh 授權規則僅適用於事件中心實例 eh1,並且發送RuleT授權規則僅適用於主題 1。 

使用 sendRuleNS 授權規則時,用戶端應用程式可以同時發送到 eh1 和主題 1。 使用 sendRuleT 授權規則時,它僅強制對主題 1 進行精細訪問,因此現在使用此規則進行訪問的用戶端應用程式不能發送到 eh1,而只能發送到主題 1。

## <a name="generate-a-shared-access-signature-token"></a>產生共用存取簽章權杖 
任何有權存取授權規則名稱及其簽署金鑰之一的用戶端，都可以產生 SAS 權杖。 權杖是以下列格式編寫字串而產生的：

- `se`• 令牌到期瞬間。 自 1970 年 1 月 1 日紀元 00:00:00 UTC (UNIX 紀元)以來反映秒數的整數,當令牌過期時
- `skn`• 授權規則的名稱,即 SAS 密鑰名稱。
- `sr`• 正在訪問的資源的 URI。
- `sig`• 簽名。

簽名字符串是通過資源 URI(如上一節所述的範圍)計算的 SHA-256 哈希和權杖過期即時的字串表示形式,由 CRLF 分隔。

雜湊計算看起來類似下列虛擬程式碼，會傳回 256 位元/32 位元組的雜湊值。 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

權杖會包含非雜湊值，因此接收者可以用相同的參數重新計算雜湊，驗證簽發者擁有有效的簽署金鑰。

資源 URI 是宣告其存取權之服務匯流排資源的完整 URI。 例如,HTTP://<namespace><entityPath>`sb://<namespace>.servicebus.windows.net/<entityPath>;`或 servicebus.windows.net/或`http://contoso.servicebus.windows.net/eventhubs/eh1`即 。

URI 必須是百分比編碼。

用於簽署的共用存取授權規則必須設定於此 URI 或其中一個階層式上層所指定的實體。 例如，先前範例中的 `http://contoso.servicebus.windows.net/eventhubs/eh1` 或 `http://contoso.servicebus.windows.net`。

SAS 令牌對於在簽名字串串中使用的所有<resourceURI>資源 都有效。

> [!NOTE]
> 使用共用訪問策略為事件中心生成訪問權杖。 有關詳細資訊,請參閱[共享存取授權原則](authorize-access-shared-access-signature.md#shared-access-authorization-policies)。

### <a name="generating-a-signaturetoken-from-a-policy"></a>從策略產生簽名(權杖) 
以下部分顯示使用共享訪問簽名策略生成 SAS 權杖,

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>使用 SAS 對事件中心發佈商進行身分驗證 
事件發行者會定義事件中樞的虛擬端點。 發行者只能用於向事件中心發送消息,而不能接收消息。

一般而言，事件中樞會針對每個用戶端採用一個發行者。 系統會將傳送到事件中樞之任何發行者的所有訊息，排入該事件中樞內的佇列中。 發行者啟用細粒度訪問控制。

系統會為每個「事件中樞」用戶端指派一個唯一權杖，該權杖會上傳到用戶端。 生成權杖,以便每個唯一令牌授予對不同唯一發佈者的訪問。 持有權杖的用戶端只能發送給一個發行者,而不能發送到其他發行者。 如果多個用戶端共用同一令牌,則每個用戶端共用發行者。

所有權杖都使用 SAS 金鑰分配。 一般而言，所有權杖都會經過同一個金鑰簽署。 用戶端不知道密鑰,這可以防止用戶端製造權杖。 用戶端使用相同的權杖操作,直到過期。

例如,要定義範圍為僅發送到事件中心的授權規則,需要定義發送授權規則。 這可以在命名空間級別完成,也可以為特定實體(事件中心實例或主題)提供更精細的範圍。 調用具有此類粒度訪問許可權範圍的用戶端或應用程式,事件中心發行者。 若要這樣做，請依照下列步驟執行：

1. 在要發佈的實體上創建 SAS 密鑰,以在實體上分配**發送**範圍。 有關詳細資訊,請參閱[共享存取授權原則](authorize-access-shared-access-signature.md#shared-access-authorization-policies)。
2. 使用步驟 1 中生成的密鑰,生成特定發行者到期時間的 SAS 令牌。

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. 向發行者用戶端提供權杖,該用戶端只能發送到權杖授予存取許可權的實體和發行者。

    令牌過期后,用戶端將失去向實體發送/發佈訪問許可權。 


> [!NOTE]
> 儘管不建議這樣做,但可以為設備配備授予對事件中心或命名空間訪問許可權的權杖。 任何保存此令牌的設備都可以將消息直接發送到該事件中心。 此外，您也無法將這類裝置加入封鎖清單，以禁止其傳送到該事件中樞。
> 
> 始終建議提供特定和粒度的範圍。

> [!IMPORTANT]
> 建立權杖之後，系統就會為每個用戶端佈建其自己的唯一權杖。
>
> 當用戶端將數據發送到事件中心時,它將使用令牌標記其請求。 為了防止攻擊者竊聽及竊取權杖，用戶端與事件中樞之間的通訊必須透過已加密的通道進行。
> 
> 如果權杖遭攻擊者竊取，攻擊者便可以模擬權杖遭竊的用戶端。 將發佈者列入黑名單,使該用戶端不可用,直到它收到使用其他發佈者的新令牌。


## <a name="authenticating-event-hubs-consumers-with-sas"></a>使用 SAS 驗證事件中心消費者 
要驗證從事件中心生成器生成的數據使用的後端應用程式,事件中心令牌身份驗證要求其用戶端具有分配給其事件中心命名空間或事件中心實例或主題**的管理**許可權或**偵聽**許可權。 使用消費者組從事件中心使用數據。 雖然 SAS 策略為您提供了粒度範圍,但此作用域僅在實體級別定義,而不是在使用者級別定義。 這意味著在命名空間級別或事件中心實例或主題級別定義的許可權將應用於該實體的消費者組。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [使用 SAS 授權](authenticate-shared-access-signature.md)
- [使用角色庫存取控制 (RBAC) 授權](authenticate-shared-access-signature.md)
- [瞭解有關事件中心數](event-hubs-about.md)

請參考以下的文章:

- [使用 Azure 活動目錄對應用程式對 Azure 事件中心的要求進行身份驗證](authenticate-application.md)
- [使用 Azure 活動目錄對託管識別進行身分驗證以存取事件中心資源](authenticate-managed-identity.md)
- [使用 Azure 活動目錄授權存取事件中心資源](authorize-access-azure-active-directory.md)
- [使用分享存取簽署授權存取事件中心資源](authorize-access-shared-access-signature.md)