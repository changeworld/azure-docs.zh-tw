---
title: 使用 Azure Spring Boot Actuator 來管理和監視應用程式
description: 瞭解如何使用春季開機傳動程式來管理和監視應用程式。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5cfc13700decf0176b283f5f1f9bd81503c1b10a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037588"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>使用 Azure Spring Boot Actuator 來管理和監視應用程式

將新的二進位檔部署至您的應用程式之後，您可能會想要檢查功能，並查看執行中應用程式的相關資訊。 本文說明如何從 Azure 春季雲端所提供的測試端點存取 API，並為您的應用程式公開已準備好用於生產環境的功能。

## <a name="prerequisites"></a>先決條件
本文假設您有一個春季 Boot 2.x 應用程式，可以在 Azure 春季雲端服務上成功部署和啟動。  請參閱[快速入門：使用 Azure 入口網站啟動現有的 Azure 春季雲端應用程式](spring-cloud-quickstart-launch-app-portal.md)

## <a name="verify-app-through-test-endpoint"></a>透過測試端點驗證應用程式
1. 移至**應用程式儀表板**，然後按一下您的應用程式以輸入應用程式的 [總覽] 頁面。

1. 在 [**總覽**] 窗格中，您應該會看到 [**測試端點**]。  從命令列或瀏覽器存取此端點，並觀察 API 回應。

1. 請注意將在接下來的區段中使用的**測試端點**URI。

>[!TIP]
> * 如果應用程式傳回前端頁面，並透過相對路徑參考其他檔案，請確認您的測試端點結尾為斜線（/）。 這可確保 CSS 檔案已正確載入。
> * 如果您從瀏覽器中查看您的 API，且瀏覽器要求您輸入登入認證來查看頁面，請使用[URL](https://www.urldecoder.org/)解碼來解碼您的測試端點。 Url 解碼會傳回格式為 "HTTPs:// \<username> ： \<password> @ \<cluster-name> . test.azureapps.io/ \<app-name> / \<deployment-name> " 的 url。  使用此表單來存取您的端點。

## <a name="add-actuator-dependency"></a>新增傳動裝置相關性

若要將傳動項新增至以 Maven 為基礎的專案，請新增 ' Starter ' 相依性：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

編譯新的二進位檔，並將它部署至您的應用程式。

## <a name="enable-production-ready-features"></a>啟用已準備好用於生產環境的功能
傳動者端點可讓您監視應用程式並與其互動。 根據預設，春季開機應用程式會公開 `health` 和 `info` 端點，以顯示任意的應用程式資訊和健康情況資訊。

若要觀察設定和可設定的環境，我們也需要啟用 `env` 和 `configgrops` 端點。

1. 前往 [應用程式**總覽**] 窗格 **，按一下 [** 設定] 功能表中的 [設定]，移至 [**環境變數**] 設定頁面。
1. 新增下列屬性，如同「金鑰：值」表單中的一樣。 此環境會開啟彈簧傳動端點「env」、「健康情況」、「資訊」。

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. 按一下 [**儲存**] 按鈕，您的應用程式會自動重新開機並載入新的環境變數。

您現在可以回到 [應用程式總覽] 窗格，等待布建狀態變更為 [成功]。  將會有一個以上的執行中實例。

> [!Note] 
> 一旦將應用程式公開至公用，這些制動器端點也會公開至公用。 您可以藉由刪除環境變數來隱藏所有端點 `management.endpoints.web.exposure.include` ，並設定`management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>查看傳動裝置端點以查看應用程式資訊
1. 您現在可以存取 url `"<test-endpoint>/actuator/"` ，以查看春季開機傳動器所公開的所有端點。
1. 存取 url `"<test-endpoint>/actuator/env"` ，您可以看到應用程式使用的作用中設定檔，以及所有已載入的環境變數。
1. 如果您想要搜尋特定的環境，您可以存取 url `"<test-endpoint>/actuator/env/{toMatch}"` 來進行查看。

若要查看內建的所有端點，請參閱[公開端點](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>接下來的步驟
* [瞭解 Azure 春季雲端的計量](spring-cloud-concept-metrics.md)
* [瞭解 Azure 春季雲端中的應用程式狀態](spring-cloud-concept-app-status.md)