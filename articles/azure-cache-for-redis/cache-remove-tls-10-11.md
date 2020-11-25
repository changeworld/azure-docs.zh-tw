---
title: 在與 Azure Cache for Redis 搭配使用時移除 TLS 1.0 和 1.1
description: 了解如何在與 Azure Cache for Redis 通訊時，從應用程式中移除 TLS 1.0 和 1.1
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: fd0e6f893d152259c46ff06e9ec20af54395c5e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994378"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>在與 Azure Cache for Redis 搭配使用時移除 TLS 1.0 和 1.1

整個業界都在尋求專用的傳輸層安全性 (TLS) 1.2 版或更新版本。 TLS 版本 1.0 和 1.1 已知容易遭受攻擊 (例如 BEAST 與 POODLE)，並具有其他公共漏洞和暴露 (CVE) 弱點。 這兩個版本也不支援支付卡產業 (PCI) 合規性標準所建議的新式加密方法和密碼套件。 這個 [TLS 安全性部落格](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) \(英文\) 會更詳細地說明這其中一些弱點。

作為此工作的一部分，我們將對 Azure Cache for Redis 進行下列變更：

* **第1階段：** 針對新建立的快取實例，我們會將預設的最小 TLS 版本設定為1.2， (之前是 TLS 1.0) 。 此時將不會更新現有的快取執行個體。 如有需要，您仍然可以使用 Azure 入口網站或其他管理 Api，將 [最低的 TLS 版本變更](cache-configure.md#access-ports) 為1.0 或1.1，以提供回溯相容性。
* **第2階段：** 我們將停止支援 TLS 1.1 和 TLS 1.0。 在此變更之後，您的應用程式必須使用 TLS 1.2 或更新版本來與您的快取通訊。 當我們將 Azure Cache for Redis 服務遷移為僅支援 TLS 1.2 或更新版本時，就會提供此服務。

  > [!NOTE]
  > 階段2的計畫將于2020年12月31日之前開始。 不過，我們強烈建議您立即開始規劃此變更，並主動更新用戶端以支援 TLS 1.2 或更新版本。 
  >

作為這項變更的一部分，我們也會移除對較不安全的舊版 cypher 套件的支援。 當您以最少的 TLS 1.2 設定快取時，支援的 cypher 套件將會限制為下列套件：

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

此文章提供有關如何偵測這些舊版 TLS 版本的相依性，並將其從您的應用程式中移除的一般指導方針。

這些變更的生效日期如下：

| Cloud                | 第 1 階段開始日期 | 第 2 階段開始日期         |
|----------------------|--------------------|----------------------------|
| Azure (全域)       |  2020 年 1 月 13 日  | 因為 COVID-19-19 而延後  |
| Azure Government     |  2020 年 3 月 13 日    | 因為 COVID-19-19 而延後  |
| Azure Germany        |  2020 年 3 月 13 日    | 因為 COVID-19-19 而延後  |
| Azure China 21Vianet |  2020 年 3 月 13 日    | 因為 COVID-19-19 而延後  |

> [!NOTE]
> 階段2的計畫將于2020年12月31日之前開始。 當設定特定日期時，將會更新本文。
>

## <a name="check-whether-your-application-is-already-compliant"></a>檢查您的應用程式是否已符合規範

若要瞭解您的應用程式是否能使用 TLS 1.2，最簡單的方式就是在測試或暫存快取上將 **最小的 tls 版本** 值設定為 tls 1.2，然後執行測試。 **最低 TLS 版本** 設定位於 Azure 入口網站中快取執行個體的 [[進階設定](cache-configure.md#advanced-settings)] 中。  如果應用程式在此變更之後仍繼續如預期般運作，則其可能符合規範。 您可能需要設定應用程式所使用的 Redis 用戶端程式庫，以啟用 TLS 1.2，以便連線到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>設定應用程式以使用 TLS 1.2

大部分的應用程式都會使用 Redis 用戶端程式庫來處理與其快取的通訊。 以下指示說明如何在各種程式設計語言和架構中設定一些熱門的用戶端程式庫，以使用 TLS 1.2。

### <a name="net-framework"></a>.NET Framework

Redis .NET 用戶端預設會在 .NET Framework 4.5.2 或更早版本上使用最舊的 TLS 版本，並在 .NET Framework 4.6 或更新版本上使用最新的 TLS 版本。 如果您使用的是舊版的 .NET Framework，就可以手動啟用 TLS 1.2：

* **StackExchange.Redis：** 在連接字串中設定 `ssl=true` 和 `sslprotocols=tls12`。
* **ServiceStack.Redis：** 遵循 [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) \(英文\) 指示，且至少需要 ServiceStack.Redis v5.6。

### <a name="net-core"></a>.NET Core

Redis .NET Core 用戶端會預設為 OS 預設的 TLS 版本，其明顯取決於 OS 本身。 

根據作業系統版本和已套用的任何修補程式，有效的預設 TLS 版本可能會有所不同。 雖然有一項資訊來源， [以下](/dotnet/framework/network-programming/tls#support-for-tls-12) 是 Windows 的文章。 

但是，如果您使用舊的作業系統，或只想要確定，建議您透過用戶端手動設定慣用的 TLS 版本。


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