---
title: 在 Azure Spring Cloud 中設定預備環境 | Microsoft Docs
description: 了解如何搭配 Azure Spring Cloud 使用藍色-綠色部署
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 72cf5553bec5985ba0310b4a347b0d2c60da6924
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090704"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>在 Azure 春季雲端中設定預備環境

**本文適用於：** ✔️ Java

本文討論如何使用 Azure 春季雲端中的藍綠部署模式來設定預備環境部署。 藍/綠部署是 Azure DevOps 的持續傳遞模式，此模式仰賴在部署新版本 (綠色) 時，仍讓現有版本 (藍色) 持續運作的方式。 本文說明如何將預備環境部署放到生產環境中，而不需要直接變更生產部署。

## <a name="prerequisites"></a>必要條件

本文假設您已在本教學課程中部署 PiggyMetrics 應用程式， [以啟動 Azure 春季 Cloud 應用程式](./spring-cloud-quickstart.md)。 PiggyMetrics 包含三個應用程式：「閘道」、「帳戶服務」和「驗證服務」。  

如果您想要在此範例中使用不同的應用程式，您必須在應用程式公開的部分中進行簡單的變更。  此變更會將您的預備部署與生產環境區分開來。

>[!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的指示。  它有常見的預先安裝 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果您已登入 Azure 訂用帳戶，請啟動您的 [Azure Cloud Shell](https://shell.azure.com)。  若要深入瞭解，請參閱 [Azure Cloud Shell 的總覽](../cloud-shell/overview.md)。

若要在 Azure 春季雲端中設定預備環境，請遵循下一節中的指示。

## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能：

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>檢視所有部署

移至 Azure 入口網站中的服務實例，然後選取 [ **部署管理** ] 以查看所有部署。 若要查看更多詳細資料，您可以選取每個部署。

## <a name="create-a-staging-deployment"></a>建立預備部署

1. 在您的本機開發環境中，對 PiggyMetrics 閘道應用程式進行較小的修改。 例如，變更 *gateway/src/main/resources/static/css/啟動 .css* 檔案中的色彩。 這麼做可讓您輕鬆區分這兩種部署。 若要建立 jar 封裝，請執行下列命令： 

    ```console
    mvn clean package
    ```

1. 在 Azure CLI 中，建立新的部署，並為其提供預備部署名稱「綠色」。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 順利完成部署之後，請從 **應用程式儀表板**存取 [閘道] 頁面，並在左側的 [ **應用程式實例** ] 索引標籤中，查看您的所有實例。
  
> [!NOTE]
> 探索狀態為 *OUT_OF_SERVICE* ，因此在驗證完成之前，不會將流量路由至此部署。

## <a name="verify-the-staging-deployment"></a>確認預備部署

1. 返回 [ **部署管理** ] 頁面，然後選取您的新部署。 部署狀態應該會顯示為 [正在執行]**。 [ **指派/取消指派網域** ] 按鈕應該會呈現灰色，因為環境是預備環境。

1. 在 [ **總覽** ] 窗格中，您應該會看到 **測試端點**。 將它複製並貼到新的瀏覽器視窗中，並顯示新的 PiggyMetrics 頁面。

>[!TIP]
> * 確認測試端點的結尾為斜線 (/) ，以確保正確載入 CSS 檔案。  
> * 如果您的瀏覽器需要您輸入登入認證來查看頁面，請使用 [URL decode](https://www.urldecoder.org/) \(英文\) 來將您的測試端點解碼。 Url 解碼會以 "HTTPs:// \<username> ： \<password> @ \<cluster-name> . test.azureapps.io/gateway/green" 格式傳回 url。  使用此表單來存取您的端點。

>[!NOTE]    
> Config server 設定適用于您的預備環境和生產環境。 例如，如果您將 `server.servlet.context-path` config server 中應用程式閘道的內容路徑 () 設定為*somepath*，則綠色部署的路徑會變更為 "HTTPs:// \<username> ： \<password> @ \<cluster-name> . test.azureapps.io/gateway/green/somepath/..."。
 
 如果您在此時造訪公眾面向的應用程式閘道，您應該會看到舊的頁面，而不會有新的變更。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>將綠色部署設定為生產環境

1. 在您驗證預備環境中的變更之後，您可以將它推送到生產環境。 返回 [ **部署管理**]，然後選取 [ **閘道** 應用程式] 核取方塊。

2. 選取 [ **設定部署**]。
3. 在 [**生產部署**] 清單**中選取 [****綠色**]，然後選取 [套用]。
4. 移至您閘道應用程式的 [概觀]**** 頁面。 如果您已經為閘道應用程式指派網域，則 URL 會出現在 [ **總覽** ] 窗格中。 若要查看修改過的 PiggyMetrics 頁面，請選取 URL，然後移至網站。

>[!NOTE]
> 將綠色部署設定為生產環境之後，先前的部署就會成為預備部署。

## <a name="modify-the-staging-deployment"></a>修改預備部署

如果您不滿意變更，您可以修改應用程式程式碼、建立新的 jar 套件，然後使用 Azure CLI 將它上傳到您的環保部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>刪除預備部署

若要從 Azure 埠刪除您的預備部署，請移至 [預備部署] 頁面，然後選取 [ **刪除** ] 按鈕。

或者，您可以執行下列命令，從 Azure CLI 中刪除您的預備部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>後續步驟

* [快速入門：部署您的第一個 Azure Spring Cloud 應用程式](spring-cloud-quickstart.md)