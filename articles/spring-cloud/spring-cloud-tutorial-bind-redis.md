---
title: 將 Azure Cache for Redis 繫結至 Azure Spring Cloud 應用程式
description: 了解如何將 Azure Cache for Redis 繫結至您的 Azure Spring Cloud 應用程式
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ef77ba6e34f2a699c8c4f06fde8cb602ae98c728
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885666"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>將 Azure Cache for Redis 繫結至 Azure Spring Cloud 應用程式 

**本文適用於：** ✔️ Java

您可以使用 Azure Spring Cloud 將精選的 Azure 服務自動繫結至應用程式，而不必手動設定 Spring Boot 應用程式。 本文會示範如何將您的應用程式繫結至 Azure Cache for Redis。

## <a name="prerequisites"></a>必要條件

* 已部署的 Azure Spring Cloud 執行個體
* Azure Cache for Redis 服務執行個體
* 適用於 Azure CLI 的 Azure Spring Cloud 擴充功能

如果您沒有已部署好的 Azure Spring Cloud 執行個體，請遵循[關於如何部署 Azure Spring Cloud 應用程式的快速入門](spring-cloud-quickstart.md)中的步驟。

## <a name="bind-azure-cache-for-redis"></a>繫結 Azure Cache for Redis

1. 在專案的 pom.xml 檔案中新增下列相依性：

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. 從 `application.properties` 檔案移除任何 `spring.redis.*` 屬性

1. 使用 `az spring-cloud app update` 來更新目前的部署，或是使用 `az spring-cloud app deployment create` 來建立新部署。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務頁面。 移至 [應用程式儀表板]，然後選取要繫結至 Azure Cache for Redis 的應用程式。 這是您在上一個步驟中更新或部署的同一個應用程式。

1. 選取 [服務繫結]，然後選取 [建立服務繫結]。 填寫表單，並務必選取 [繫結類型] 值 [Azure Cache for Redis]、您的 Azure Cache for Redis 伺服器和 [主要] 金鑰選項。

1. 重新啟動應用程式。 繫結現在應可正常運作。

1. 為了確定服務繫結的正確性，請選取繫結名稱並確認其詳細資料。 `property` 欄位看起來應該如下所示：
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何將 Azure Spring Cloud 應用程式繫結至 Azure Cache for Redis。 若要深入了解如何將服務繫結至您的應用程式，請參閱[繫結至適用於 MySQL 的 Azure 資料庫執行個體](spring-cloud-tutorial-bind-mysql.md)。
