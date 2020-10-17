---
title: 收集 & 分析資源記錄
description: 瞭解如何從 Azure 容器實例中的容器群組將資源記錄和事件資料傳送至 Azure 監視器記錄
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: b110ba46bdcf2741e5f16845f28fe8305bcee1a1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148648"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>使用 Azure 監視器記錄的容器群組和實例記錄

Log Analytics 工作區提供集中式位置，不僅能儲存和查詢來自 Azure 資源的記錄資料，還可提供內部部署資源和其他雲端中的資源。 Azure 容器執行個體包含將記錄和事件資料傳送至 Azure 監視器記錄的內建支援。

若要將容器群組記錄和事件資料傳送至 Azure 監視器記錄，請在設定容器群組時，指定現有的 Log Analytics 工作區識別碼和工作區金鑰。 

下列各節說明如何建立啟用記錄的容器群組，以及如何查詢記錄。 您也可以使用工作區識別碼和工作區金鑰來 [更新容器群組](container-instances-update.md) ，以啟用記錄功能。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> 目前，您只能將來自 Linux 容器執行個體的事件資料傳送至 Log Analytics。

## <a name="prerequisites"></a>必要條件

若要在您的容器執行個體中啟用記錄，您必須具備下列項目：

* [Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (或 [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>取得 Log Analytics 認證

Azure 容器執行個體必須具備將資料傳送至 Log Analytics 工作區的權限。 若要授與此權限並啟用記錄，您必須在建立容器群組時提供 Log Analytics 工作區識別碼，以及它的其中一個金鑰 (主要或次要)。

若要取得記錄分析工作區識別碼和主要金鑰：

1. 在 Azure 入口網站中瀏覽至您的 Log Analytics 工作區
1. 在 [**設定**] 底下，選取 [**代理程式管理**]
1. 記下：
   * **Workspace ID** \(工作區識別碼\)
   * **主要金鑰**

## <a name="create-container-group"></a>建立容器群組

現在您已有記錄分析工作區識別碼和主要金鑰，接下來即可建立已啟用記錄的容器群組。

下列範例示範兩種建立容器群組的方式，其中包含單一 [fluentd][fluentd] 容器： Azure CLI，以及 YAML 範本的 Azure CLI。 Fluentd 容器會在其預設設定中產生數行的輸出。 此輸出會傳送到您的 Log Analytics 工作區，因此很適合用來示範記錄的檢視和查詢。

### <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

若要使用 Azure CLI 進行部署，請在 [az container create][az-container-create] 命令中指定 `--log-analytics-workspace` 和 `--log-analytics-workspace-key` 參數。 在執行下列命令之前，請先將兩個工作區值取代為您在先前的步驟中取得的值 (並更新資源群組名稱)。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>使用 YAML 進行部署

如果您想要使用 YAML 部署容器群組，請使用此方法。 下列 YAML 會定義具有單一容器的容器群組。 請將 YAML 複製到新檔案中，然後將 `LOG_ANALYTICS_WORKSPACE_ID` 和 `LOG_ANALYTICS_WORKSPACE_KEY` 取代為您在先前的步驟中取得的值。 將檔案儲存為 **deploy-aci.yaml**。

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

接下來，執行下列命令以部署容器群組。 請將 `myResourceGroup` 取代為您訂用帳戶中的資源群組 (或先建立名為 "myResourceGroup" 的資源群組)：

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

在發出命令不久後，您應該就會收到 Azure 的回應，其中包含部署詳細資料。

## <a name="view-logs"></a>檢視記錄

在您部署容器群組後，可能需要幾分鐘的時間 (最多 10 分鐘)，第一個記錄項目才會出現在 Azure 入口網站中。 

若要在 `ContainerInstanceLog_CL` 資料表中檢視容器群組的記錄：

1. 在 Azure 入口網站中瀏覽至您的 Log Analytics 工作區
1. 在 **[一般**] 底下，選取 [**記錄**]  
1. 執行下列查詢：`ContainerInstanceLog_CL | limit 50`
1. 選取 **執行**

您應該會看到查詢所顯示的數個結果。 如果您在第一次沒有看到任何結果，請等候幾分鐘，然後選取 [ **執行** ] 按鈕以再次執行查詢。 根據預設，記錄項目會以 [資料表]**** 格式顯示。 接著，您可以展開資料列來查看個別記錄項目的內容。

![Azure 入口網站中的記錄搜尋結果][log-search-01]

## <a name="view-events"></a>檢視事件

您也可以在 Azure 入口網站中，檢視容器執行個體的事件。 事件包括執行個體的建立時間和啟動時間。 若要在 `ContainerEvent_CL` 資料表中檢視事件資料：

1. 在 Azure 入口網站中瀏覽至您的 Log Analytics 工作區
1. 在 **[一般**] 底下，選取 [**記錄**]  
1. 執行下列查詢：`ContainerEvent_CL | limit 50`
1. 選取 **執行**

您應該會看到查詢所顯示的數個結果。 如果您在第一次沒有看到任何結果，請等候幾分鐘，然後選取 [ **執行** ] 按鈕以再次執行查詢。 根據預設，項目會以 [資料表]**** 格式顯示。 接著，您可以展開資料列來查看個別項目的內容。

![Azure 入口網站中的事件搜尋結果][log-search-02]

## <a name="query-container-logs"></a>查詢容器記錄

Azure 監視器記錄包含涵蓋範圍廣大的[查詢語言][query_lang]，可從可能高數千行的記錄輸出中提取資訊。

查詢的基本結構是一個來源資料表 (在本文為 `ContainerInstanceLog_CL` 或 `ContainerEvent_CL`)，後面接著一系列由管道字元 (`|`) 隔開的運算子。 您可以鏈結數個運算子，以找出更精確的結果及執行進階函式。

若要查看範例查詢結果，請將下列查詢貼入 [查詢] 文字方塊中，然後選取 **[執行] 按鈕以** 執行查詢。 此查詢會顯示 [訊息] 欄位中包含「警告」一詞的所有記錄項目：

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

此外也支援更複雜的查詢。 例如，下列查詢只會顯示過去一小時內為 "mycontainergroup001" 容器群組產生的記錄項目：

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>後續步驟

### <a name="azure-monitor-logs"></a>Azure 監視器記錄

如需關於在 Azure 監視器記錄中查詢記錄和設定警示的詳細資訊，請參閱：

* [瞭解 Azure 監視器記錄檔中的記錄搜尋](../azure-monitor/log-query/log-query-overview.md)
* [Azure 監視器中的整合警示](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>監視容器 CPU 和記憶體

如需監視容器執行個體 CPU 和記憶體資源的相關資訊，請參閱：

* [在 Azure 容器執行個體中監視容器資源](container-instances-monitor.md)。

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create