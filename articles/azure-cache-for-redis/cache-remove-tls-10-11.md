---
title: 移除與 Azure Cache for Redis 搭配使用的 TLS 1.0 和1。1
description: 瞭解如何在與 Azure Cache for Redis 通訊時，從應用程式移除 TLS 1.0 和1。1
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 809fbe85a9783777d5dbef86357bd5a386bd6f81
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261221"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>移除與 Azure Cache for Redis 搭配使用的 TLS 1.0 和1。1

針對傳輸層安全性（TLS）1.2 版或更新版本的專屬使用，有業界廣泛的推播。 TLS 版本1.0 和1.1 已知容易遭受攻擊，例如 BEAST 和貴賓，並具有其他常見的弱點和暴露（CVE）弱點。 它們也不支援支付卡產業（PCI）合規性標準所建議的新式加密方法和加密套件。 此[TLS 安全性 blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)會更詳細地說明其中一些弱點。

作為這項工作的一部分，我們將對 Azure Cache for Redis 進行下列變更：

* **第1階段：** 我們會針對新建立的快取實例，將預設的最低 TLS 版本設定為1.2。 （這是用來做為 TLS 1.0）。此時不會更新現有的快取實例。 如有需要，您可以將[最小的 TLS 版本變更](cache-configure.md#access-ports)回1.0 或1.1 以提供回溯相容性。 這種變更可以透過 Azure 入口網站或其他管理 Api 來完成。
* **第2階段：** 我們將停止支援 TLS 版本1.0 和1.1。 在這種變更之後，您的應用程式必須使用 TLS 1.2 或更新版本來與您的快取通訊。

此外，做為這項變更的一部分，我們將會移除對舊版、不安全的 cypher 套件的支援。  當快取設定為最低的 TLS 版本1.2 時，我們支援的 cypher 套件將受限於下列各項。

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

本文提供有關如何偵測這些舊版 TLS 版本的相依性，並將其從您的應用程式中移除的一般指引。

這些變更生效的日期如下：

| 雲端               | 第1階段開始日期 | 第2階段開始日期      |
|---------------------|--------------------|-------------------------|
| Azure (全域)      |  2020 年 1 月 13 日  | 2020 5 月11日（擴充） |
| Azure Government    |  2020年3月13日    | 2020 5 月11日            |
| Azure Germany       |  2020年3月13日    | 2020 5 月11日            |
| Azure 中國         |  2020年3月13日    | 2020 5 月11日            |

## <a name="check-whether-your-application-is-already-compliant"></a>檢查您的應用程式是否已經符合規範

找出您的應用程式是否能與 TLS 1.2 搭配使用的最簡單方式，就是在其所使用的測試或暫存快取上，將**最小的 tls 版本**值設定為 tls 1.2。 **最低的 TLS 版本**設定位於 Azure 入口網站中快取實例的[Advanced 設定](cache-configure.md#advanced-settings)中。 如果應用程式在這種變更之後仍繼續如預期般運作，可能是相容的。 您可能需要特別設定一些應用程式所使用的 Redis 用戶端程式庫，以啟用 TLS 1.2，讓他們可以透過該安全性通訊協定連接到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>將您的應用程式設定為使用 TLS 1。2

大部分的應用程式會使用 Redis 用戶端程式庫來處理與其快取的通訊。 以下指示說明如何在各種程式設計語言和架構中設定一些熱門的用戶端程式庫，以使用 TLS 1.2。

### <a name="net-framework"></a>.NET Framework

Redis .NET 用戶端預設會在 .NET Framework 4.5.2 或更早版本上使用最舊的 TLS 版本，並使用 .NET Framework 4.6 或更新版本上的最新 TLS 版本。 如果您使用較舊版本的 .NET Framework，您可以手動啟用 TLS 1.2：

* **Stackexchange.redis. Redis：** 在`ssl=true`連接`sslprotocols=tls12`字串中設定和。
* **Servicestack.redis 時. Redis：** 請遵循[servicestack.redis 時. Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support)指示，並至少需要 Servicestack.redis 時. Redis v 5.6。

### <a name="net-core"></a>.NET Core

Redis .NET Core 用戶端預設會使用最新的 TLS 版本。

### <a name="java"></a>Java

Redis JAVA 用戶端在 JAVA 第6版或更早版本上使用 TLS 1.0。 如果快取上已停用 TLS 1.0，Jedis、萵苣和 Redisson 將無法連線至 Azure Cache for Redis。 升級您的 JAVA framework 以使用新的 TLS 版本。

針對 JAVA 7，Redis 用戶端預設不會使用 TLS 1.2，但可以加以設定。 Jedis 可讓您使用下列程式碼片段來指定基礎 TLS 設定：

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

萵苣和 Redisson 用戶端尚不支援指定 TLS 版本，因此如果快取只接受 TLS 1.2 連線，它們就會中斷。 這些用戶端的修正正在進行審核，因此請使用這項支援來檢查是否有更新版本的套件。

在 JAVA 8 中，預設會使用 TLS 1.2，而且在大部分情況下都不需要更新您的用戶端設定。 為安全起見，請測試您的應用程式。

### <a name="nodejs"></a>Node.js

Node Redis 和 IORedis 預設會使用 TLS 1.2。

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* PHP 7 之前的版本： Predis 僅支援 TLS 1.0。 這些版本無法與 TLS 1.2 搭配使用;您必須升級才能使用 TLS 1.2。
 
* PHP 7.0 到 PHP 7.2.1： Predis 預設僅使用 TLS 1.0 或1.1。 您可以使用下列因應措施來使用 TLS 1.2。 當您建立用戶端實例時，請指定 TLS 1.2：

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

* PHP 7.3 和更新版本： Predis 會使用最新的 TLS 版本。

#### <a name="phpredis"></a>對 phpredis

對 phpredis 不支援任何 PHP 版本上的 TLS。

### <a name="python"></a>Python

Redis-.py 預設會使用 TLS 1.2。

### <a name="go"></a>GO

Redigo 預設會使用 TLS 1.2。

## <a name="additional-information"></a>其他資訊

- [如何設定 Azure Redis 快取](cache-configure.md)
