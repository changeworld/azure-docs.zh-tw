---
title: 在 Spring Boot 應用程式中使用功能旗標的教學課程 - Azure 應用程式組態 | Microsoft Docs
description: 在本教學課程中，您將了解如何在 Spring Boot 應用程式中實作功能旗標。
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 83c437cb613e3dad04dee17f0f67040532066c3b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326591"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>教學課程：在 Spring Boot 應用程式中使用功能旗標

Spring Boot Core 功能管理程式庫可讓您在 Spring Boot 應用程式中實作功能旗標。 這些程式庫可讓您以宣告方式在程式碼中新增功能旗標。

功能管理程式庫也可在幕後管理功能旗標的生命週期。 例如，這些程式庫會重新整理及快取旗標狀態，或保證旗標的狀態在要求呼叫期間不會變化。 此外，Spring Boot 程式庫還提供整合，包括 MVC 控制器動作、路由和中介軟體。

[將功能旗標新增至 Spring Boot 應用程式的快速入門](./quickstart-feature-flag-spring-boot.md)會示範數種方法，讓您了解如何在 Spring Boot 應用程式中新增功能旗標。 本教學課程會詳細說明這些方法。

在本教學課程中，您將學會如何：

> [!div class="checklist"]
> * 在應用程式的重要部分新增功能旗標來控制功能的可用性。
> * 在您使用應用程式組態來管理功能旗標時與其進行整合。

## <a name="set-up-feature-management"></a>設定功能管理

Spring Boot 功能管理員 `FeatureManager` 會從架構的原生組態系統取得功能旗標。 因此，您可以使用 Spring Boot 所支援的組態來源 (包括本機 bootstrap.yml  檔或環境變數)，來定義應用程式的功能旗標。 `FeatureManager` 會仰賴相依性插入。 您可以使用標準慣例來註冊功能管理服務：

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

建議您將功能旗標置於應用程式外，並個別加以管理。 這樣做可讓您隨時修改旗標狀態，並讓那些變更在應用程式中立即生效。 應用程式組態可讓您透過專用的入口網站 UI 在集中的位置組織和控制所有功能旗標。 應用程式組態也可透過其 Spring Boot 用戶端程式庫，直接將旗標傳遞到您的應用程式。

若要將 Spring Boot 應用程式連線至應用程式組態，最簡單的方式是透過組態提供者：

### <a name="spring-cloud-11x"></a>Spring Cloud 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Spring Cloud 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>功能旗標宣告

每個功能旗標都有兩個部分：名稱以及一份清單，此清單內含可用來評估功能狀態是否為「開啟」  (也就是當其值為 `True` 時) 的一或多個篩選條件。 篩選條件會定義應開啟功能的使用案例。

如果功能旗標有多個篩選條件，則會依序周遊篩選條件清單，直到其中一個篩選條件確定應啟用功能。 屆時，功能旗標即會「開啟」  ，並略過剩餘的篩選結果。 如果沒有篩選條件指出應啟用功能，則功能旗標會「關閉」  。

功能管理員支援以 application.yml  作為功能旗標的組態來源。 下列範例說明如何在 YAML 檔案中設定功能旗標：

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

依照慣例，我們會將此 YML 文件的 `feature-management` 區段用於功能旗標設定。 上述範例顯示了三個功能旗標，及其在 `EnabledFor` 屬性中定義的篩選條件：

* `feature-a` 為「開啟」  。
* `feature-b` 為「關閉」  。
* `feature-c` 會使用 `parameters` 屬性指定名為 `Percentage` 的篩選條件。 `Percentage` 是可設定的篩選條件。 在此範例中，`Percentage` 會指定 `feature-c` 旗標有 50% 的機率會「開啟」  。

## <a name="feature-flag-checks"></a>功能旗標檢查

功能管理的基本模式是先檢查功能旗標是否設定為「開啟」  。 如果是，則功能管理員會功能所包含的動作。 例如：

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>相依性插入

在 Spring Boot 中，您可以透過相依性插入來存取功能管理員 `FeatureManager`：

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>控制器動作

在 MVC 控制器中，您可以使用 `@FeatureGate` 屬性來控制是否要啟用特定動作。 下列 `Index` 動作必須在 `feature-a`「開啟」  時才能執行：

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

當 MVC 控制器或動作因為控制功能旗標「關閉」  而遭到封鎖時，系統便會呼叫已註冊的 `IDisabledFeaturesHandler` 介面。 預設的 `IDisabledFeaturesHandler` 介面會對用戶端傳回沒有回應本文的 404 狀態碼。

## <a name="mvc-filters"></a>MVC 篩選條件

您可以將 MVC 篩選條件設定為根據功能旗標的狀態來啟用。 下列程式碼會新增名為 `FeatureFlagFilter` 的 MVC 篩選條件。 只有在 `feature-a` 已啟用時，才會在 MVC 管線內觸發此篩選條件。

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>路由

您可以使用功能旗標來重新導向路由。 下列程式碼會將已啟用 `feature-a` 的使用者重新導向：

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何藉由使用 `spring-cloud-azure-feature-management-web` 程式庫在 Spring Boot 應用程式中實作功能旗標。 若要進一步了解 Spring Boot 和應用程式組態中的功能管理支援，請參閱下列資源：

* [Spring Boot 功能旗標範例程式碼](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [編輯功能旗標](./manage-feature-flags.md)
