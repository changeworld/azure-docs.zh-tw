---
title: 在與 Azure Cache for Redis 搭配使用時移除 TLS 1.0 和 1.1
description: 了解如何在與 Azure Cache for Redis 通訊時，從應用程式中移除 TLS 1.0 和 1.1
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: b7b3556896f2d8bb8fea7ffc4543356e248df60d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848816"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>在與 Azure Cache for Redis 搭配使用時移除 TLS 1.0 和 1.1

整個業界都在尋求專用的傳輸層安全性 (TLS) 1.2 版或更新版本。 TLS 版本 1.0 和 1.1 已知容易遭受攻擊 (例如 BEAST 與 POODLE)，並具有其他公共漏洞和暴露 (CVE) 弱點。 這兩個版本也不支援支付卡產業 (PCI) 合規性標準所建議的新式加密方法和密碼套件。 這個 [TLS 安全性部落格](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) \(英文\) 會更詳細地說明這其中一些弱點。

作為此工作的一部分，我們將對 Azure Cache for Redis 進行下列變更：

* **第 1 階段：** 我們會針對新建立的快取執行個體，將預設的最低 TLS 版本設定為 1.2 (以前是 TLS 1.0)。  此時將不會更新現有的快取執行個體。 系統允許您可以基於回溯相容性，視需要[變更最低 TLS 版本](cache-configure.md#access-ports)回到 1.0 或 1.1。 此變更可透過 Azure 入口網站或其他管理 API 來完成。
* **第 2 階段：** 我們將停止支援 TLS 版本 1.0 和 1.1。 在此變更之後，您的應用程式就必須使用 TLS 1.2 或更新版本來與您的快取通訊。

此外，作為此變更的一部分，我們將移除對較舊且不安全之密碼套件的支援。  當快取是透過最低 TLS 版本 1.2 來設定時，我們支援的密碼套件將受限於下列各項。

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

此文章提供有關如何偵測這些舊版 TLS 版本的相依性，並將其從您的應用程式中移除的一般指導方針。

這些變更的生效日期如下：

| Cloud                | 第 1 階段開始日期 | 第 2 階段開始日期      |
|----------------------|--------------------|-------------------------|
| Azure (全域)       |  2020 年 1 月 13 日  | 2020 年 5 月 11 日            |
| Azure Government     |  2020 年 3 月 13 日    | 2020 年 5 月 11 日            |
| Azure Germany        |  2020 年 3 月 13 日    | 2020 年 5 月 11 日            |
| Azure China 21Vianet |  2020 年 3 月 13 日    | 2020 年 5 月 11 日            |

## <a name="check-whether-your-application-is-already-compliant"></a>檢查您的應用程式是否已符合規範

找出您應用程式是否將與 TLS 1.2 搭配使用的最簡單方式，就是在其使用的測試或暫存快取上，將**最低 TLS 版本**值設定為 TLS 1.2。 **最低 TLS 版本**設定位於 Azure 入口網站中快取執行個體的 [[進階設定](cache-configure.md#advanced-settings)] 中。 如果應用程式在此變更之後仍繼續如預期般運作，則其可能符合規範。 您可能必須特別設定一些您應用程式所使用的 Redis 用戶端程式庫來啟用 TLS 1.2，讓其可透過該安全性通訊協定連線到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>設定應用程式以使用 TLS 1.2

大部分的應用程式都會使用 Redis 用戶端程式庫來處理與其快取的通訊。 以下指示說明如何在各種程式設計語言和架構中設定一些熱門的用戶端程式庫，以使用 TLS 1.2。

### <a name="net-framework"></a>.NET Framework

Redis .NET 用戶端預設會在 .NET Framework 4.5.2 或更早版本上使用最舊的 TLS 版本，並在 .NET Framework 4.6 或更新版本上使用最新的 TLS 版本。 如果您使用的是舊版的 .NET Framework，就可以手動啟用 TLS 1.2：

* **StackExchange.Redis：** 在連接字串中設定 `ssl=true` 和 `sslprotocols=tls12`。
* **ServiceStack.Redis：** 遵循 [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) \(英文\) 指示，且至少需要 ServiceStack.Redis v5.6。

### <a name="net-core"></a>.NET Core

Redis .NET Core 用戶端會預設為 OS 預設的 TLS 版本，其明顯取決於 OS 本身。 

根據 OS 的發行時間，以及是否有任何其他修補程式變更了預設的 TLS 版本，OS TLS 版本可能相當多樣。 儘管沒有關於這方面的完整資訊，但對於 Windows OS，您可以在[這裡](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) \(部分機器翻譯\) 找到詳細資訊。 

不過，如果您使用舊版 OS，或只想進行確認，則建議您透過用戶端手動設定慣用的 TLS 版本。


### <a name="java"></a>Java

Redis Java 用戶端會在 Java 版本 6 或更早版本上使用 TLS 1.0。 如果已在快取上停用 TLS 1.0，則 Jedis、Lettuce 及 Redisson 就無法連線至 Azure Cache for Redis。 升級您的 Java 架構以使用新的 TLS 版本。

針對 Java 7，Redis 用戶端預設不會使用 TLS 1.2，但可加以設定。 Jedis 可讓您使用下列程式碼片段來指定基礎 TLS 設定：

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Lettuce 和 Redisson 用戶端尚未支援指定 TLS 版本，因此，如果快取只接受 TLS 1.2 連線，這些用戶端就會中斷。 適用於這些用戶端的修正程式目前正在進行審核，因此，請檢查那些套件是否有具備此支援的更新版本。

在 Java 8 中，預設會使用 TLS 1.2，而且在大部分情況下都不需要更新您的用戶端設定。 為了安全起見，請測試您的應用程式。

### <a name="nodejs"></a>Node.js

Node Redis 和 IORedis 預設會使用 TLS 1.2。

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* PHP 7 之前的版本：Predis 僅支援 TLS 1.0。 這些版本無法搭配 TLS 1.2 使用；您必須升級才能使用 TLS 1.2。
 
* PHP 7.0 到 PHP 7.2.1：根據預設，Predis 只會使用 TLS 1.0 或 1.1。 您可以使用下列因應措施來使用 TLS 1.2。 當您建立用戶端執行個體時，指定 TLS 1.2：

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 和更新版本：Predis 會使用最新的 TLS 版本。

#### <a name="phpredis"></a>PhpRedis

PhpRedis 不支援任何 PHP 版本上的 TLS。

### <a name="python"></a>Python

Redis-py 預設會使用 TLS 1.2。

### <a name="go"></a>GO

Redigo 預設會使用 TLS 1.2。

## <a name="additional-information"></a>其他資訊

- [如何設定 Azure Cache for Redis](cache-configure.md)
