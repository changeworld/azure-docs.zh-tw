---
title: 自動執行服務註冊表和發現
description: 瞭解如何使用 Spring 雲服務註冊表自動執行服務發現和註冊
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278848"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>發現並註冊您的春雲服務

服務發現是基於微服務的體系結構的關鍵要求。  手動設定每個用戶端需要時間，並引入了人為錯誤的可能性。  Azure 春季雲服務註冊表解決了此問題。  配置後，服務註冊表伺服器將控制應用程式的微服務的服務註冊和發現。 服務註冊表伺服器維護已部署微服務的註冊表，啟用用戶端負載平衡，並在不依賴于 DNS 的情況下將服務提供者與用戶端分離。

## <a name="register-your-application-using-spring-cloud-service-registry"></a>使用 Spring 雲服務註冊表註冊您的應用程式

在應用程式可以使用 Spring 雲服務註冊表管理服務註冊和發現之前，必須在應用程式的*pom.xml*檔中包含多個依賴項。
包括*彈簧雲啟動器-netflix-eureka 用戶端*和*彈簧雲啟動器-azure-彈簧雲-用戶端*對*pom.xml*的依賴關係

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>更新頂級類

最後，我們將注釋添加到應用程式的頂級類

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Spring 雲服務註冊表伺服器終結點將作為環境變數注入到應用程式中。  微服務現在將能夠在服務註冊表伺服器註冊自己併發現其他從屬微服務。

請注意，更改可能需要幾分鐘時間才能從伺服器傳播到所有微服務。
