---
title: 快速入門 - 範例應用程式簡介 - Azure Spring Cloud
description: 說明此系列快速入門中用於部署至 Azure Spring Cloud 的範例應用程式。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903592"
---
# <a name="introduction-to-the-sample-app"></a>範例應用程式簡介

::: zone pivot="programming-language-csharp"
此系列的快速入門會使用由兩個微服務組成的範例應用程式，來示範如何將 .NET Core Steeltoe 應用程式部署至 Azure Spring Cloud 服務。 您將使用 Azure Spring Cloud 功能，例如服務探索、設定伺服器、記錄、計量和分散式追蹤。

## <a name="functional-services"></a>功能服務

範例應用程式由兩個微服務組成：

* `planet-weather-provider` 服務會傳回氣象文字，以回應指定了行星名稱的 HTTP 要求。 例如，其可能會針對水星傳回「非常溫暖」。 其會從設定伺服器取得氣象資料。 設定伺服器會從 Git 存放庫中的 YAML 檔案取得氣象資料，例如：

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* `solar-system-weather` 服務會傳回四個行星的資料，以回應 HTTP 要求。 其會藉由對 `planet-weather-provider` 提出四個 HTTP 要求來取得資料。 其會使用 Eureka 伺服器探索服務來呼叫 `planet-weather-provider`。 其會傳回 JSON，例如：

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

下圖說明範例應用程式的架構：

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="範例應用程式圖表":::

## <a name="code-repository"></a>程式碼存放庫

範例應用程式位於 GitHub 上 Azure-Samples/Azure-Spring-Cloud-Samples 存放庫的 [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) 資料夾中。

下列快速入門中的指示會視需要參考原始程式碼。

::: zone-end

::: zone pivot="programming-language-java"
在本快速入門中，我們會使用名為 PiggyMetrics 的個人財務範例，向您示範如何將應用程式部署至 Azure Spring Cloud 服務。 PiggyMetrics 會示範微服務架構模式，並醒目提示服務細項。 您將了解如何使用功能強大的 Azure Spring Cloud 功能將其部署至 Azure，包括服務探索、Config Server、記錄、計量和分散式追蹤。

若要遵循 Azure Spring Cloud 部署範例，您只需要原始程式碼的位置 (視需要提供)。

## <a name="functional-services"></a>功能服務

PiggyMetrics 會分解成三個核心微服務。 這些全都是由商務網域組織的獨立部署應用程式。

* **帳戶服務 (要部署的項目)** ：包含一般使用者輸入邏輯和驗證：收入/支出項目、存款和帳戶設定。
* **統計資料服務 (本快速入門中未使用)** ：執行主要統計資料參數的計算，並擷取每個帳戶的時間序列。 資料點包含值，已正規化為基底貨幣和時間週期。 此資料可用來追蹤帳戶存留期內的現金流動態。
* **通知服務 (本快速入門中未使用)** ：儲存使用者連絡資訊和通知設定，例如提醒和備份頻率。 排定的背景工作角色會收集其他服務的必要資訊，並將電子郵件訊息傳送給已訂閱的客戶。

## <a name="infrastructure-services"></a>基礎結構服務

分散式系統中有數個常見的模式，可協助核心服務正常執行。 Azure Spring Cloud 提供強大的工具，可強化執行這些模式的 Spring Boot 應用程式行為： 

* **Config 服務 (由 Azure Spring Cloud 託管)** ：Azure Spring Cloud Config 是適用於分散式系統且可水平擴充的集中式設定服務。 其會使用目前支援本機儲存體、Git 和 Subversion 的可插入存放庫。
* **服務探索 (由 Azure Spring Cloud 託管)** ：其允許自動偵測服務執行個體的網路位置 (可能會因為自動調整、失敗和升級而動態指派位址)。
* **驗證服務 (要部署的項目)** 授權責任會完全落在個別伺服器上，以授與後端資源服務的 OAuth2 權杖。 Auth Server 會在周邊內執行使用者授權和安全的機器對機器通訊。
* **API 閘道 (要部署的項目)** ：這三個核心服務會向用戶端公開外部 API。 在真實世界的系統中，功能數目可能會因為系統複雜度而快速成長。 轉譯一個複雜的網頁可能涉及數百項服務。 API 閘道是系統的單一進入點，用來處理要求並將其路由傳送至適當的後端服務，或叫用多個後端服務並彙總結果。 

## <a name="sample-usage-of-piggymetrics"></a>PiggyMetrics 的使用範例

如需完整的實作詳細資料，請參閱 [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics)。 範例會視需要參考原始程式碼。
::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [佈建 Azure Spring Cloud 執行個體](spring-cloud-quickstart-provision-service-instance.md)
