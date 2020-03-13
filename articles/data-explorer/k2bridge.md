---
title: 使用 Kibana 從 Azure 資料總管將資料視覺化
description: 在本文中，您將瞭解如何設定 Azure 資料總管做為 Kibana 的資料來源
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164809"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>使用 K2Bridge 開放原始碼連接器將 Kibana 中 Azure 資料總管的資料視覺化

K2Bridge （Kibana-Kusto Bridge）可讓您使用 Azure 資料總管做為資料來源，並將 Kibana 中的資料視覺化。 K2Bridge 是[開放原始](https://github.com/microsoft/K2Bridge)碼容器化應用程式，可作為 Kibana 實例與 Azure 資料總管叢集之間的 proxy。 本文說明如何使用 K2Bridge 來建立該連接。

K2Bridge 會將 Kibana 查詢轉譯為 Kusto 查詢語言（KQL），並將 Azure 資料總管結果傳送回 Kibana。 

   ![圖表](media/k2bridge/k2bridge-chart.png)

K2Bridge 支援 Kibana 的 [探索] 索引標籤，您可以在其中：
* 搜尋和流覽資料
* 篩選結果
* 在結果方格中新增或移除欄位
* 查看記錄內容
* 儲存和共用搜尋

下圖顯示由 K2Bridge 系結至 Azure 資料總管的 Kibana 實例。 Kibana 中的使用者體驗不會變更。

   [![Kibana 頁面](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

在您可以將 Kibana 中的 Azure 資料總管的資料視覺化之前，請備妥下列內容：

* [Helm V3](https://github.com/helm/helm#install)，Kubernetes 套件管理員
* Azure Kubernetes Service （AKS）叢集或任何其他 Kubernetes 叢集（版本1.14 至1.16 版已經過測試和驗證）。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)部署 AKS 叢集或[使用 Azure 入口網站](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Azure 資料總管](create-cluster-database-portal.md)叢集，包括：
    * Azure 資料總管叢集的 URL 
    * 資料庫名稱
    
* 授權用來在 Azure 資料總管中查看資料的 Azure AD 服務主體，包括：
    * 用戶端識別碼 
    * 用戶端密碼

    建議使用具有「檢視器」許可權的服務主體。 不建議使用較高的許可權。

    * [設定 Azure AD 服務主體的叢集 [view] 許可權](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

    如需 Azure AD 服務主體的詳細資訊，請參閱[建立 Azure AD 服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 上執行 K2Bridge （AKS）

根據預設，K2Bridges's Helm 圖表會參考位於 Microsoft Container Registry （MCR）上的可公開取得影像。 MCR 不需要任何認證，而是現成適用的。

1. 下載必要的 Helm 圖表。

1. 將 Elasticsearch 相依性新增至 Helm。 
    Elasticsearch 相依性的原因是 K2Bridge 會使用內部的小型 Elasticsearch 實例來服務與中繼資料相關的要求（例如索引模式和已儲存的查詢）。 此內部實例中不會儲存任何商務資料，而且可以將其視為執行詳細資料。 

    1. 若要將 Elasticsearch 相依性新增至 Helm：

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. 若要從 GitHub 存放庫的圖表目錄底下取得 K2Bridge 圖：
        1. 從[GitHub](https://github.com/microsoft/K2Bridge)複製存放庫。
        1. 移至 K2Bridges 根存放庫目錄。
        1. 請執行：

            ```bash
            helm dependency update charts/k2bridge
            ```

1. 部署 K2Bridge：

    1. 使用適用于您環境的正確值來設定變數：

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. 選擇性啟用 Azure 應用程式 Insights 遙測。 
        如果這是您第一次使用 Azure 應用程式深入解析，您應該先[建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。 您必須將[檢測金鑰複製](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key)到變數： 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>安裝 K2Bridge 圖表：

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        在[設定中，您可以找到](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)一組完整的設定選項。

    1. 命令輸出將會建議要執行的下一個 Helm 命令，以部署 Kibana。 （選擇性）執行：

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. 使用埠轉送來存取 localhost 上的 Kibana： 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. 流覽至 http://127.0.0.1:5601以連接到 Kibana。

    1. 向終端使用者公開 Kibana。 有多種方法可以這麼做。 您所使用的方法主要取決於您的使用案例。

        例如：

        以 LoadBalancer 服務的形式公開服務。 若要這麼做，請將下列參數新增至 K2Bridge Helm install 命令（[上方](#install-k2bridge-chart)）：

        `--set service.type=LoadBalancer`
    
        然後執行：

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        輸出應會顯示如下： 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        接著，您可以使用顯示的產生的外部 IP，並藉由開啟瀏覽器來存取 Kibana： `\<EXTERNAL-IP>:5601`。

1. 設定索引模式來存取您的資料：  
在新的 Kibana 實例中：
     1. 開啟 Kibana。
     1. 流覽至 [管理]。
     1. 選取 [**索引模式**]。 
     1. 建立索引模式。
索引的名稱必須完全符合資料表名稱或函數名稱，但不含星號。 您可以從清單中複製相關的行。

> [!Note]
> 若要在其他 Kubernetes 提供者上執行，請將 `values.yaml` 中的 Elasticsearch storageClassName 變更為符合提供者建議的。

## <a name="visualize-data"></a>顯現資料

當 Azure 資料總管設定為 Kibana 的資料來源時，您可以使用 Kibana 來流覽資料。 

1. 在 [Kibana] 中，選取左側功能表上的 [**探索**] 索引標籤。

1. 從左側下拉式清單中，選取索引模式（在此案例中為 Azure 資料總管資料表），這會定義您想要探索的資料來源。
    
   ![選取索引模式](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 如果您的資料有時間篩選欄位，您可以指定時間範圍。 在頁面的右上方，設定時間篩選準則。 根據預設，探索會顯示過去15分鐘的資料。

   ![時間篩選器](media/k2bridge/k2bridge-time-filter.png)
    
1. 結果資料表會顯示前500筆記錄。 您可以展開檔，以 JSON 或資料表格式來檢查其欄位資料。

   ![展開記錄](media/k2bridge/k2bridge-expand-record.png)

1. 根據預設，[結果] 資料表會包含檔 _source 的資料行，以及 [時間] 欄位（如果有的話）。 您可以選取左側提要欄位中功能變數名稱旁的 [**新增**]，來選擇要加入至結果資料表的特定資料行。

   ![特定資料行](media/k2bridge/k2bridge-specific-columns.png)
    
1. 在查詢列中，您可以藉由下列方式搜尋資料：
    * 輸入搜尋字詞
    * 使用 Lucene 查詢語法。 
    例如：
        * 搜尋「錯誤」以尋找包含此值的所有記錄。 
        * 搜尋 "status： 200"，取得 status 值為200的所有記錄。 
    * 使用邏輯運算子（AND、OR、NOT）
    * 使用萬用字元（星號 "\*" 或問號 "？"）例如：
        * 查詢 `"destination_city: L*"` 會符合目的地城市值開頭為 "l" （K2Bridge 不區分大小寫）的記錄。

    ![執行查詢](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > 在[搜尋](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)中，您可以找到更多搜尋規則和邏輯。

1. 若要篩選您的搜尋結果，請使用頁面右邊欄上的 [**欄位清單**]。 
    您可以在 [欄位清單] 中看到：
    * 欄位的前五個值
    * 包含欄位的記錄數目
    * 包含每個值的記錄百分比。 
    
    >[!Tip]
    > 使用（+）放大鏡圖示來尋找具有特定值的所有記錄。
    
    ![欄位清單](media/k2bridge/k2bridge-field-list.png)
   
    您也可以在結果資料表中每筆記錄的 [結果] 資料表格式視圖中，使用（+）放大鏡圖示來篩選結果。
    
     ![資料表清單](media/k2bridge/k2bridge-table-list.png)
    
1. 選取以**儲存**或**分享**您的搜尋。

     ![儲存搜尋](media/k2bridge/k2bridge-save-search.png)
