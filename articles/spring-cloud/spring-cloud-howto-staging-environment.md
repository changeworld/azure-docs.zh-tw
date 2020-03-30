---
title: 在 Azure Spring Cloud 中設定預備環境 | Microsoft Docs
description: 了解如何搭配 Azure Spring Cloud 使用藍色-綠色部署
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471025"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>在 Azure 春雲中設置暫存環境

本文討論如何使用 Azure Spring Cloud 中的藍綠色部署模式設置暫存部署。 藍/綠部署是 Azure DevOps 的持續傳遞模式，此模式仰賴在部署新版本 (綠色) 時，仍讓現有版本 (藍色) 持續運作的方式。 本文介紹如何在不直接更改生產部署的情況下將暫存部署投入生產。

## <a name="prerequisites"></a>Prerequisites

本文假定您已經部署了有關[啟動 Azure 春雲應用程式的教程](spring-cloud-quickstart-launch-app-portal.md)中的 PiggyMetrics 應用程式。 PiggyMetrics 包括三個應用程式："閘道"、"帳戶服務"和"服務"。  

如果要對此示例使用不同的應用程式，則需要在應用程式的面向公共的部分進行簡單的更改。  此變更會將您的預備部署與生產環境區分開來。

>[!TIP]
> Azure 雲外殼是一個免費的互動式外殼，可用於運行本文中的說明。  它有常見的預先安裝 Azure 工具，包括最新版本的 Git、JDK、Maven 和 Azure CLI。 如果已登錄到 Azure 訂閱，請啟動 Azure[雲外殼](https://shell.azure.com)。  要瞭解更多資訊，請參閱[Azure 雲外殼概述](../cloud-shell/overview.md)。

要在 Azure Spring Cloud 中設置暫存環境，請按照以下各節中的說明操作。

## <a name="install-the-azure-cli-extension"></a>安裝 Azure CLI 擴充功能

使用下列命令安裝適用於 Azure CLI 的 Azure Spring Cloud 擴充功能：

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>檢視所有部署

轉到 Azure 門戶中的服務實例，然後選擇 **"部署管理"** 以查看所有部署。 要查看更多詳細資訊，可以選擇每個部署。

## <a name="create-a-staging-deployment"></a>建立預備部署

1. 在本地開發環境中，對 PiggyMetrics 閘道應用程式進行小修改。 例如，更改*閘道/src/主/資源/靜態/css/開機檔案*中的顏色。 這樣，您就可以輕鬆區分兩個部署。 要生成 jar 包，運行以下命令： 

    ```console
    mvn clean package
    ```

1. 在 Azure CLI 中，創建新部署，並將其暫存部署名稱命名為"綠色"。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. 成功完成部署後，從**應用程式儀表板**訪問閘道頁，並在左側的 **"應用實例"** 選項卡中查看所有實例。
  
> [!NOTE]
> 發現狀態*OUT_OF_SERVICE*以便在驗證完成之前不會將流量路由到此部署。

## <a name="verify-the-staging-deployment"></a>確認預備部署

1. 返回到 **"部署管理**"頁，然後選擇新部署。 部署狀態應該會顯示為 [正在執行]**。 "**分配/取消分配域"** 按鈕應顯示為灰色，因為環境是暫存環境。

1. 在 **"概述"** 窗格中，應看到**測試終結點**。 複製並粘貼到新的瀏覽器視窗中，並應顯示新的 PiggyMetrics 頁面。

>[!TIP]
> * 確認測試終結點以斜杠 （/） 結尾，以確保 CSS 檔載入正確。  
> * 如果您的瀏覽器需要您輸入登入認證來查看頁面，請使用 [URL decode](https://www.urldecoder.org/) \(英文\) 來將您的測試端點解碼。 URL decode 會以 "https://\<使用者名稱>:\<密碼>@\<叢集名稱>.test.azureapps.io/gateway/green" 的格式傳回 URL。  使用此表單可以訪問終結點。

>[!NOTE]    
> 佈建服務器設置適用于過渡環境和生產。 例如，如果將佈建服務器中的應用閘道的上下文路徑`server.servlet.context-path`（） 設置為*某個路徑*，則綠色部署的路徑將更改為"HTTPs://\<使用者名>：\<密碼>=\<群集名稱>.test.azureapps.io/閘道/綠色/某些路徑/..."。
 
 如果此時訪問面向公共的應用閘道，您應該會看到舊頁面，而不會進行新的更改。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>將綠色部署設置為生產環境

1. 驗證暫存環境中的更改後，可以將其推送到生產環境。 返回到**部署管理**，然後選擇**閘道**應用程式核取方塊。

2. 選擇 **"設置部署**"。
3. 在 **"生產部署"** 清單中，選擇 **"綠色**"，然後選擇 **"應用**"。
4. 移至您閘道應用程式的 [概觀]**** 頁面。 如果您已經為閘道應用程式分配了域，則 URL 將顯示在 **"概述"** 窗格中。 要查看修改後的 PiggyMetrics 頁面，請選擇 URL 並轉到網站。

>[!NOTE]
> 將綠色部署設置為生產環境後，上一個部署將成為臨時部署。

## <a name="modify-the-staging-deployment"></a>修改預備部署

如果您對更改不滿意，則可以修改應用程式代碼、構建新的 jar 包，並使用 Azure CLI 將其上載到綠色部署。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>刪除暫存部署

要從 Azure 埠中刪除暫存部署，請轉到暫存部署頁，然後選擇"**刪除**"按鈕。

或者，通過運行以下命令從 Azure CLI 中刪除臨時部署：

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
