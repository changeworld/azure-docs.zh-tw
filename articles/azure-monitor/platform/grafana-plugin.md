---
title: 使用 Grafana 監視 Azure 服務與應用程式
description: 路由 Azure 監視器與 Application Insights 資料，以便使用 Grafana 檢視它們。
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672203"
---
# <a name="monitor-your-azure-services-in-grafana"></a>在 Grafana 中監視 Azure 服務
您現在可以從使用 [Azure 監視器資料來源外掛程式](https://grafana.com/)的 [Grafana](https://grafana.com/plugins/grafana-azure-monitor-datasource)，監視 Azure 服務和應用程式。 外掛程式會蒐集 Azure 監視器所收集的應用程式效能資料，包括各種記錄和計量。 然後，您就可以在 Grafana 儀表板上顯示此資料。

使用下列步驟設定 Grafana 伺服器，並為 Azure 監視器中的計量和記錄建置儀表板。

## <a name="set-up-a-grafana-server"></a>設定 Grafana 伺服器

### <a name="set-up-grafana-locally"></a>在本機設定 Grafana
若要設定本機的 Grafana 伺服器，請[在您的本機環境中下載並安裝 Grafana](https://grafana.com/grafana/download)。 若要使用外掛程式的 Azure 監視器整合，請安裝 Grafana 5.3 版或更高版本。

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>透過 Azure Marketplace 在 Azure 上設定 Grafana
1. 移至 Azure Marketplace，並依 Grafana 實驗室挑選 Grafana。

2. 填入名稱和詳細資料。 建立新的資源群組。 持續追蹤您選擇的 VM 使用者名稱、VM 密碼和 Grafana 伺服器系統管理員密碼的值。  

3. 選擇 VM 大小和儲存體帳戶。

4. 設定網路組態設定。

5. 檢視摘要，並在接受使用條款之後選取 [建立]。

6. 部署完成之後，請選取 [移至資源群組]。 您會看到新建的資源清單。

    ![Grafana 資源群組物件](media/grafana-plugin/grafana1.png)

    如果選取網路安全性群組 (本例中為 *grafana-nsg*)，您會看到使用連接埠 3000 存取 Grafana 伺服器。

7. 取得 Grafana 伺服器的公用 IP 位址 - 返回資源清單，然後選取 [公用 IP 位址]。

## <a name="sign-in-to-grafana"></a>登入 Grafana

1. 使用伺服器的 IP 位址，在您的瀏覽器中開啟登入頁面 (*http://\<IP 位址\>:3000* 或 *\<DNSName>\:3000*)。 雖然 3000 是預設連接埠，但請注意您在安裝過程中可能選取了不同的連接埠。 您應該會看到您建立的 Grafana 伺服器登入頁面。

    ![Grafana 登入畫面](./media/grafana-plugin/grafana-login-screen.png)

2. 使用您稍早建立的 [使用者名稱*管理員*] 和 [Grafana 伺服器管理員密碼] 登入。 如果您使用的是本機設定，則預設密碼為 *admin*，並且系統會要求您在首次登入時變更密碼。

## <a name="configure-data-source-plugin"></a>設定資料來源外掛程式

成功登入之後，您應該會看到已經包含 Azure 監視器資料來源外掛程式。

![Grafana 包括 Azure 監視器外掛程式](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. 選取 [加入資料來源] 以加入和設定 Azure 監視器資料來源。

2. 選取資料來源的名稱，然後從下拉式清單中選取 [Azure 監視器] 為類型。

3. 建立服務主體 - Grafana 使用 Azure Active Directory 服務主體連接到 Azure 監視器 API，並收集計量資料。 您必須建立或使用現有的服務主體來管理對 Azure 資源的存取。
    * 請參閱[這些指示](../../azure-resource-manager/resource-group-create-service-principal-portal.md)來建立服務主體。 複製並儲存您的租用戶識別碼 (目錄識別碼)、用戶端識別碼 (應用程式識別碼) 和用戶端祕密 (應用程式機碼值)。
    * 請參閱[將應用程式指派至角色](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)，以將「讀者」角色指派給要監視的訂用帳戶、資源組或資源上的 Azure Active Directory 應用程式。 
    Log Analytics API需要 [Log Analytics 讀者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader)，其中包含「讀者」角色的權限並加入到其中。

4. 提供您想要使用的 API 的連線詳細資料。 您可以連接到所有或其中一部分。 
    * 如果您連接到 Azure 監視器中的計量和記錄，您可以選取與**AZURE 監視器 API 相同的詳細資料**，以重複使用相同的認證。
    * 設定外掛程式時，您可以指示外掛程式要監視哪個 Azure 雲端 (公用、Azure 美國政府、Azure 德國或 Azure 中國)。
    * 如果您使用 Application Insights，您也可以包含 Application Insights API 和應用程式識別碼，收集 Application Insights 的基礎計量。 如需詳細資訊，請參閱 [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (取得 API 金鑰和應用程式識別碼)。

        > [!NOTE]
        > 某些資料來源欄位的命名方式，與其相互關聯的 Azure 設定不同：
        > * 租用戶識別碼是 Azure 目錄識別碼
        > * 用戶端識別碼是 Azure Active Directory 應用程式識別碼
        > * 用戶端密碼是 Azure Active Directory 應用程式機碼值

5. 如果您使用 Application Insights，您也可以包含 Application Insights API 和應用程式識別碼，收集 Application Insights 的基礎計量。 如需詳細資訊，請參閱 [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (取得 API 金鑰和應用程式識別碼)。

6. 選取 [儲存]，Grafana 將測試每個 API 的認證。 您應該會看見類似下方的訊息。  
    ![Grafana 資料來源設定已核准](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>建置 Grafana 儀表板

1. 移至 Grafana 首頁，然後選取 [新增儀表板]。

2. 在新的儀表板中選取 [圖形]。 您可以嘗試其他圖表選項，但本文使用 [圖形] 作為範例。

3. 空白的圖形會出現在儀表板中。 在面板標題上按一下，然後選取 [編輯] 輸入您想要在此圖表中繪製的資料詳細資料。
    ![Grafana 新增圖表](./media/grafana-plugin/grafana-new-graph-dark.png)

4. 選取已設定的 Azure 監視器資料來源。
   * 收集 Azure 監視器計量 - 在服務下拉式清單中選取 [Azure 監視器]。 這會顯示一個選取器清單，您可以在其中選取要在此圖表中監視的資源和計量。 若要從 VM 收集計量，請使用命名空間 **Microsoft.Compute/VirtualMachines**。 一旦您選取 VM 和計量後，您就可以開始在儀表板中檢視其資料。
     ![Azure 監視器的 Grafana 圖表設定](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * 收集 Azure 監視器記錄資料-在服務下拉式清單中選取 [ **Azure Log Analytics** ]。 選取您想要查詢的工作區並設定查詢文字。 您可以在此複製已有的任何記錄檔查詢，或建立一個新的。 當您輸入查詢時，IntelliSense 將顯示並建議自動完成選項。 選取 [視覺效果類型]、[**時間序列**]**資料表**，然後執行查詢。
    
     > [!NOTE]
     >
     > 外掛程式所提供的預設查詢會使用兩個巨集："$__timeFilter() 和 $__interval。 
     > 當您放大圖表的一部分時，這些巨集允許 Grafana 動態計算時間範圍和時間資料粒度。 您可以移除這些巨集並使用標準時間篩選條件，例如 *TimeGenerated > ago(1h)* ，但這表示圖形不支援縮放功能。
    
     ![Azure Log Analytics 的 Grafana 圖表設定](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. 以下是有兩份圖表的簡單儀表板。 左邊的這份顯示兩個 VM 的 CPU 百分比。 右側的圖表依交易 API 類型細分，顯示 Azure 儲存體帳戶中的交易。
    ![Grafana 雙圖表範例](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>選擇性：在相同的 Grafana 伺服器中監視自訂的計量

您也可以安裝 Telegraf 和 InfluxDB，收集自訂和代理程式型的計量，並繪製在相同的 Grafana 執行個體中。 有許多資料來源外掛程式可將這些計量結合在儀表板中。

您也可以重複使用這項設定，以包含來自 Prometheus 伺服器的計量。 使用 Grafana 外掛程式庫中的 Prometheus 資料來源外掛程式。

以下幾篇是非常好的 Telegraf、InfluxDB、Prometheus 和 Docker 使用方式參考文章
 - [How To Monitor System Metrics with the TICK Stack on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04) (如何在 Ubuntu 16.04 上使用刻度堆疊監視系統計量)

 - [A monitoring solution for Docker hosts, containers, and containerized services](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/) (Docker 主機、容器和容器化服務的監視解決方案)

以下是完整的 Grafana 儀表板映像，具有來自 Azure 監視器和 Application Insights 的計量。
![Grafana 範例計量](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Grafana 進階功能

### <a name="variables"></a>變數
可以透過 UI 下拉式清單選取某些查詢值，並在查詢中更新。 請考慮下列查詢作為範例：
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

您可以設定一個變數，列出所有可用的**解決方案**值，然後再更新您的查詢來使用它。
若要建立新的變數，請按一下右上方區域中儀表板的 [設定] 按鈕，選取 [變數]，然後 [新增]。
在 [變數] 頁面上，定義要執行的資料來源和查詢以取得值清單。
![Grafana 設定變數](./media/grafana-plugin/grafana-configure-variable-dark.png)

建立之後，請調整查詢以使用選取的值，您的圖表將相應地做出回應：
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana 使用變數](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>建立儀表板播放清單

Grafana 有許多實用功能，儀表板播放清單就是其中之一。 您可以建立多個儀表板，並將它們新增至播放清單，設定每個儀表板的顯示間隔。 選取 [播放] 查看循環播放的儀表板。 您可能想要在大型的牆面監視器中顯示它們，為群組提供「狀態面板」。

![Grafana 播放清單範例](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>清除資源

如果您在 Azure 上設定了 Grafana 環境，則無論您是否使用它們，都會在 VM 執行時向您收取費用。 為避免產生額外費用，請清除依本文建立的資源群組。

1. 從 Azure 入口網站的左側功能表中，依序按一下 [資源群組] 和 [Grafana]。
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中鍵入 **Grafana**，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟
* [Azure 監視器計量概觀](data-platform.md)

