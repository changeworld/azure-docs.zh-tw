---
title: 使用 Kibana 視覺化 Azure 資料資源管理器中的資料
description: 在本文中，您將瞭解如何將 Azure 資料資源管理器設置為 Kibana 的資料來源
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065600"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>使用 K2Bridge 開源連接器視覺化基巴納 Azure 資料資源管理器的資料

K2Bridge（基巴納-庫斯托橋）使您能夠使用 Azure 資料資源管理器作為資料來源，並在基巴納中視覺化該資料。 K2Bridge 是一個[開源](https://github.com/microsoft/K2Bridge)容器化應用程式，充當 Kibana 實例和 Azure 資料資源管理器群集之間的代理。 本文介紹如何使用 K2Bridge 創建該連接。

K2Bridge 將基巴納查詢轉換為 Kusto 查詢語言 （KQL），並將 Azure 資料資源管理器結果發送回基巴納。 

   ![圖表](media/k2bridge/k2bridge-chart.png)

K2Bridge 支援基巴納的"發現"選項卡，您可以在其中：
* 搜索和流覽資料
* 篩選結果
* 添加或刪除結果網格中的欄位
* 查看記錄內容
* 保存和共用搜索

下圖顯示了 K2Bridge 綁定到 Azure 資料資源管理器的 Kibana 實例。 基巴納的使用者體驗保持不變。

   [![基巴納頁面](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

在將來自 Kibana 中的 Azure 資料資源管理器的資料視覺化之前，準備好以下操作：

* [赫爾姆V3，](https://github.com/helm/helm#install)庫伯內斯包經理
* Azure 庫伯奈斯服務 （AKS） 群集或任何其他庫伯奈斯群集（版本 1.14 到版本 1.16 已過測試和驗證）。 如果需要 AKS 群集，請參閱[使用 Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)或使用[Azure 門戶](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)部署 AKS 群集
* [Azure 資料資源管理器群集](create-cluster-database-portal.md)， 包括：
    * Azure 資料資源管理器群集的 URL 
    * 資料庫名稱
    
* 受權在 Azure 資料資源管理器中查看資料的 Azure AD 服務主體，包括：
    * 用戶端 ID 
    * 用戶端密碼

    建議使用具有"檢視器"許可權的服務主體。 建議使用更高的許可權。

    * [為 Azure AD 服務主體設置群集的視圖許可權](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

    有關 Azure AD 服務主體的詳細資訊，請參閱[創建 Azure AD 服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>在 Azure 庫伯奈斯服務 （AKS） 上運行 K2Bridge

預設情況下，K2Bridges 的 Helm 圖表引用位於 Microsoft 容器註冊表 （MCR） 上的公開圖像。 MCR 不需要任何憑據，並且開箱即用。

1. 下載所需的頭盔圖表。

1. 將彈性搜索依賴項添加到 Helm。 
    彈性搜索依賴項的原因是 K2Bridge 使用內部小型彈性搜索實例來為中繼資料相關請求（如索引模式和保存的查詢）提供服務。 此內部實例中不保存任何業務資料，它可以被視為實現詳細資訊。 

    1. 要將彈性搜索依賴項添加到 Helm：

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. 要從 GitHub 存儲庫的圖表目錄下獲取 K2Bridge 圖表，請執行以下操作：
        1. 從[GitHub](https://github.com/microsoft/K2Bridge)克隆存儲庫 。
        1. 轉到 K2Bridges 根存儲庫目錄。
        1. 請執行：

            ```bash
            helm dependency update charts/k2bridge
            ```

1. 部署 K2Bridge：

    1. 使用環境的正確值設置變數：

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. （可選）啟用 Azure 應用程式見解遙測。 
        如果這是首次使用 Azure 應用程式見解，則應首先[創建應用程式見解資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。 您需要將[檢測金鑰複製到](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key)變數： 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>安裝 K2Bridge 圖表：

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        在["配置"](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)中，您可以找到完整的配置選項組。

    1. 命令輸出將建議運行下一個 Helm 命令以部署基巴納。 可以選擇運行：

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. 使用埠轉發訪問本地主機上的 Kibana： 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. 通過流覽連接到http://127.0.0.1:5601基巴納。

    1. 向最終使用者公開基巴納。 有多種方法可以執行此操作。 您使用的方法很大程度上取決於您的用例。

        例如：

        將服務公開為負載平衡器服務。 為此，請將`--set service.type=LoadBalancer`參數添加到 K2Bridge Helm 安裝命令 （[上）。](#install-k2bridge-chart)        
    
        然後執行：
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        輸出應會顯示如下： 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        然後，您可以使用顯示的生成的外部 IP，並通過打開瀏覽器訪問`<EXTERNAL-IP>:5601`基巴納來訪問 Kibana。

1. 配置索引模式以訪問資料：  
在新的 Kibana 實例中：
     1. 打開基巴納
     1. 導航到管理層。
     1. 選擇**索引模式**。 
     1. 創建索引模式。
索引的名稱必須與表名稱或函數名稱完全符合，而不帶星號。 可以從清單中複製相關行。

> [!Note]
> 要在其他 Kubernetes 提供程式上運行，更改 中的`values.yaml`彈性搜索存儲 ClassName 以適合提供程式建議的存儲。

## <a name="visualize-data"></a>顯現資料

當 Azure 資料資源管理器配置為 Kibana 的資料來源時，可以使用 Kibana 來流覽資料。 

1. 在左側功能表的 Kibana 中，選擇"**發現**"選項卡。

1. 從左側下拉清單中選擇索引模式（在本例中為 Azure 資料資源管理器表），該模式定義要流覽的資料來源。
    
   ![選擇索引模式](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 如果資料具有時間篩選器欄位，則可以指定時間範圍。 在頁面的右上角，設置時間篩選器。 預設情況下，"發現"顯示過去 15 分鐘的資料。

   ![時間篩選器](media/k2bridge/k2bridge-time-filter.png)
    
1. 結果表顯示前 500 條記錄。 您可以展開文檔以 JSON 或表格式檢查其欄位資料。

   ![展開記錄](media/k2bridge/k2bridge-expand-record.png)

1. 預設情況下，結果表包括文檔_source和時間欄位（如果存在）的列。 您可以通過選擇左側邊欄中欄位名稱旁邊的**添加**，選擇要添加到結果表的特定列。

   ![特定列](media/k2bridge/k2bridge-specific-columns.png)
    
1. 在查詢欄中，您可以通過以下條件搜索資料：
    * 輸入搜索詞
    * 使用 Lucene 查詢語法。 
    例如：
        * 搜索"錯誤"以查找包含此值的所有記錄。 
        * 搜索"狀態：200"，獲取狀態值為 200 的所有記錄。 
    * 使用邏輯運算子（AND、OR、不）
    * 使用萬用字元（星號""\*或問號"？"）例如：
        * 查詢`"destination_city: L*"`將匹配目標城市值以"l"開頭的記錄（K2Bridge 不區分大小寫）。

    ![執行查詢](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > 在[搜索](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)中，可以找到更多的搜索規則和邏輯。

1. 要篩選搜尋結果，請使用頁面右側欄上的**欄位清單**。 
    欄位清單是您可以看到的位置：
    * 欄位的前五個值
    * 包含欄位的記錄數
    * 包含每個值的記錄的百分比。 
    
    >[!Tip]
    > 使用 （+） 放大鏡圖示查找具有特定值的所有記錄。
    
    ![欄位清單](media/k2bridge/k2bridge-field-list.png)
   
    還可以使用結果表中每個記錄的結果表格式視圖中的 （+） 放大鏡圖示篩選結果。
    
     ![資料表清單](media/k2bridge/k2bridge-table-list.png)
    
1. 選擇"**保存**"或"**共用**搜索"。

     ![保存搜索](media/k2bridge/k2bridge-save-search.png)
