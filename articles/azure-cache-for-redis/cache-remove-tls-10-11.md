---
title: 刪除 TLS 1.0 和 1.1 與用於 Redis 的 Azure 緩存
description: 瞭解如何在與 Redis 的 Azure 緩存通信時從應用程式中刪除 TLS 1.0 和 1.1
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 0d28469820f63f63089d9b91d57ccd7fe75c8b95
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348647"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>刪除 TLS 1.0 和 1.1 與用於 Redis 的 Azure 緩存

業界普遍推動獨家使用傳輸層安全 （TLS） 版本 1.2 或更高版本。 TLS 版本 1.0 和 1.1 已知容易受到諸如 BEAST 和 POODLE 等攻擊，並且存在其他常見漏洞和暴露 （CVE） 弱點。 它們還不支援支付卡行業 （PCI） 合規性標準推薦的現代加密方法和密碼套件。 此[TLS 安全博客](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)更詳細地解釋了其中一些漏洞。

作為這項工作的一部分，我們將對 Redis 的 Azure 緩存進行以下更改：

* **第 1 階段：** 我們將新創建的緩存實例的預設最小 TLS 版本配置為 1.2。 （這以前是 TLS 1.0。此時不會更新現有緩存實例。 如果需要，將允許您[將最小 TLS 版本更改](cache-configure.md#access-ports)回 1.0 或 1.1，以便向後相容。 此更改可以通過 Azure 門戶或其他管理 API 進行。
* **第 2 階段：** 我們將停止支援 TLS 版本 1.0 和 1.1。 此更改後，應用程式將需要使用 TLS 1.2 或更高版本與緩存通信。

此外，作為此更改的一部分，我們將刪除對較舊的、不安全的迴圈套件的支援。  當緩存配置為最小 TLS 版本為 1.2 時，我們支援的 cypher 套件將限制為以下內容。

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

本文提供有關如何檢測這些早期 TLS 版本的依賴項並從應用程式中刪除它們的一般指南。

這些更改生效的日期為：

| Cloud               | 階段 1 開始日期 | 階段 2 開始日期      |
|---------------------|--------------------|-------------------------|
| Azure (全域)      |  2020 年 1 月 13 日  | 2020年5月11日（擴展） |
| Azure Government    |  2020年3月13日    | 2020年5月11日            |
| Azure Germany       |  2020年3月13日    | 2020年5月11日            |
| Azure 中國         |  2020年3月13日    | 2020年5月11日            |

## <a name="check-whether-your-application-is-already-compliant"></a>檢查應用程式是否已合規

瞭解應用程式是否適用于 TLS 1.2 的最簡單方法是在其使用的測試或暫存緩存上將**最小 TLS 版本**值設置為 TLS 1.2。 **"最小 TLS"版本**設置位於 Azure 門戶中緩存實例[的高級設置](cache-configure.md#advanced-settings)中。 如果應用程式在此更改後繼續按預期運行，則可能符合要求。 您可能需要配置應用程式專門用於啟用 TLS 1.2 的某些 Redis 用戶端庫，以便它們可以通過該安全協定連接到 Redis 的 Azure 緩存。

## <a name="configure-your-application-to-use-tls-12"></a>將應用程式佈建為使用 TLS 1.2

大多數應用程式使用 Redis 用戶端庫來處理與其緩存的通信。 以下是用於在各種程式設計語言和框架中配置一些常用用戶端庫以使用 TLS 1.2 的說明。

### <a name="net-framework"></a>.NET Framework

Redis .NET 用戶端預設在 .NET 框架 4.5.2 或更早版本上使用最早的 TLS 版本，並在 .NET 框架 4.6 或更高版本上使用最新的 TLS 版本。 如果您使用的是舊版本的 .NET 框架，則可以手動啟用 TLS 1.2：

* **堆疊交換.Redis：** 在`ssl=true`連接`sslprotocols=tls12`字串中設置和。
* **服務堆疊.Redis：** 按照[服務堆疊.Redis 說明](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)操作。

### <a name="net-core"></a>.NET Core

預設情況下，Redis .NET 核心用戶端使用最新的 TLS 版本。

### <a name="java"></a>Java

Redis JAVA 用戶端在 JAVA 版本 6 或更早版本上使用 TLS 1.0。 如果緩存上禁用 TLS 1.0，絕地、萊圖斯和雷迪森無法連接到 Redis 的 Azure 緩存。 升級 JAVA 框架以使用新的 TLS 版本。

對於 JAVA 7，Redis 用戶端預設不使用 TLS 1.2，但可以為其配置。 Jedis 允許您使用以下程式碼片段指定基礎 TLS 設置：

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

Lettuce 和 Redisson 用戶端還不支援指定 TLS 版本，因此如果緩存僅接受 TLS 1.2 連接，它們就會中斷。 正在審核這些用戶端的修補程式，因此請使用此支援查看這些包的更新版本。

在 JAVA 8 中，TLS 1.2 預設使用，在大多數情況下不需要更新用戶端配置。 為了安全起見，請測試您的應用程式。

### <a name="nodejs"></a>Node.js

預設情況下，節點 Redis 和 IORedis 使用 TLS 1.2。

### <a name="php"></a>PHP

#### <a name="predis"></a>普雷迪斯
 
* 比 PHP 7 早的版本：Predis 僅支援 TLS 1.0。 這些版本不工作與 TLS 1.2;您必須升級才能使用 TLS 1.2。
 
* PHP 7.0 到 PHP 7.2.1：預設情況下，Predis 僅使用 TLS 1.0 或 1.1。 您可以使用以下解決方法使用 TLS 1.2。 創建用戶端實例時指定 TLS 1.2：

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

* PHP 7.3 及更高版本：Predis 使用最新的 TLS 版本。

#### <a name="phpredis"></a>PhpRedis

PhpRedis 不支援任何 PHP 版本的 TLS。

### <a name="python"></a>Python

預設情況下，Redis-py 使用 TLS 1.2。

### <a name="go"></a>GO

預設情況下，Redigo 使用 TLS 1.2。

## <a name="additional-information"></a>其他資訊

- [如何設定 Azure Redis 快取](cache-configure.md)
