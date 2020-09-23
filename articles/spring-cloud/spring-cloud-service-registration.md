---
title: 自動化服務登錄和探索
description: 瞭解如何使用春季 Cloud Service Registry 將服務探索和註冊自動化
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 1e60799878cc30b729344c03df36a4c5e4f4a199
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904212"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>探索並註冊您的春季雲端服務

服務探索是以微服務為基礎的架構的重要需求。  手動設定每個用戶端需要一些時間，並引進人為錯誤的可能性。  Azure 春季 Cloud Service Registry 可解決此問題。  一旦設定之後，服務登錄伺服器將會控制應用程式微服務的服務註冊和探索。 服務登錄伺服器會維護已部署之微服務的登錄、啟用用戶端負載平衡，並將服務提供者從用戶端分離，而不需要依賴 DNS。

::: zone pivot="programming-language-csharp"
如需有關如何設定 Steeltoe 應用程式之服務註冊的詳細資訊，請參閱 [準備 JAVA 春季應用程式以在 Azure 春季雲端中進行部署](spring-cloud-tutorial-prepare-app-deployment.md)。
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>使用春季 Cloud Service Registry 註冊您的應用程式

應用程式的 *pom.xml* 檔中必須包含數個相依性，您的應用程式才能使用春季 Cloud service Registry 來管理服務註冊和探索。
包含 netflix 的相依性-eureka-用戶端和春季- *-客戶*端和春季- *pom.xml* *雲端*

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

## <a name="update-the-top-level-class"></a>更新最上層類別

最後，我們會將注釋新增至您應用程式的最上層類別

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

春季 Cloud Service 登錄伺服器端點將會在您的應用程式中插入為環境變數。  微服務現在可以向 Service Registry 伺服器註冊自己，並探索其他相依的微服務。

請注意，變更可能需要幾分鐘的時間才會從伺服器傳播到所有微服務。
::: zone-end
