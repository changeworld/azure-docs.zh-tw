---
title: 防止對容器和 blob 進行匿名公用讀取存取
titleSuffix: Azure Storage
description: 瞭解如何針對儲存體帳戶分析匿名要求，以及如何防止整個儲存體帳戶或個別容器的匿名存取。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 3d843440adc61b315616a05f223c5a13ebe271ed
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930827"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>防止對容器和 blob 進行匿名公用讀取存取

以匿名的公開讀取權限來存取 Azure 儲存體中的容器和 Blob，是共用資料的便利方式，但也可能會帶來安全性風險。 請務必謹慎地管理匿名存取，以及瞭解如何評估匿名存取您的資料。 對可公開存取的資料進行作業複雜性、人為錯誤或惡意攻擊可能會導致成本高昂的資料缺口。 Microsoft 建議您只在應用程式案例需要時，才啟用匿名存取。

依預設，一律禁止公開存取 blob 資料。 不過，儲存體帳戶的預設設定可讓具有適當許可權的使用者，在儲存體帳戶中設定容器和 blob 的公用存取權。 為了加強安全性，不論個別容器的公用存取設定為何，您都可以不允許所有對儲存體帳戶的公用存取。 不允許公開存取儲存體帳戶可防止使用者啟用帳戶中容器的公用存取權。 除非您的案例需要對儲存體帳戶使用公開存取，否則 Microsoft 建議您不要對帳戶使用公開存取。 不允許公開存取有助於防止非預期匿名存取所造成的資料洩漏。

如果您不允許儲存體帳戶的公用 blob 存取，Azure 儲存體會拒絕對該帳戶的所有匿名要求。 帳戶不允許公用存取之後，該帳戶中的容器就無法接著設定為公開存取。 任何已針對公用存取設定的容器將不再接受匿名要求。 如需詳細資訊，請參閱 [設定容器和 blob 的匿名公用讀取權限](anonymous-read-access-configure.md)。

本文說明如何使用拖曳 (偵測-補救-Audit-Audit) framework 來持續管理儲存體帳戶的公用存取。

## <a name="detect-anonymous-requests-from-client-applications"></a>從用戶端應用程式偵測匿名要求

如果您不允許儲存體帳戶的公用讀取權限，則會拒絕對目前設定為公用存取權的容器和 blob 發出要求的風險。 不允許儲存體帳戶的公用存取會覆寫該儲存體帳戶中個別容器的公用存取設定。 當儲存體帳戶不允許公用存取時，對該帳戶的任何未來匿名要求都將會失敗。

若要瞭解不允許公用存取如何影響用戶端應用程式，Microsoft 建議您啟用該帳戶的記錄和計量，並在一段時間間隔內分析匿名要求的模式。 使用計量來決定儲存體帳戶的匿名要求數目，並使用記錄來判斷要以匿名方式存取的容器。

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>使用計量瀏覽器監視匿名要求

若要追蹤對儲存體帳戶的匿名要求，請使用 Azure 入口網站中的 Azure 計量瀏覽器。 如需計量瀏覽器的詳細資訊，請參閱 [開始使用 Azure 計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)。

遵循下列步驟來建立追蹤匿名要求的度量：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。 在 [ **監視** ] 區段下，選取 [ **計量**]。
1. 選取 [新增計量]。 在 [ **度量** ] 對話方塊中，指定下列值：
    1. 將 [領域] 欄位設定為儲存體帳戶的名稱。
    1. 將計量 **命名空間** 設定為 *Blob*。 此計量只會報告對 Blob 儲存體的要求。
    1. 將 [ **度量** ] 欄位設定為 [ *交易*]。
    1. 將 **匯總** 欄位設定為 *Sum*。

    新計量會在指定的時間間隔內，針對 Blob 儲存體顯示交易數目的總和。 產生的計量會顯示如下圖所示：

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="顯示如何設定計量以將 blob 交易加總的螢幕擷取畫面":::

1. 接下來，選取 [ **新增篩選** ] 按鈕，以建立匿名要求的度量篩選。
1. 在 [ **篩選** ] 對話方塊中，指定下列值：
    1. 將 **屬性** 值設定為 [ *驗證*]。
    1. 將 [ **運算子** ] 欄位設定為等號 (=) 。
    1. 將 [ **值** ] 欄位設定為 [ *匿名*]。
1. 在右上角，選取您要用來查看度量的時間間隔。 您也可以指定從1分鐘到1個月之間的間隔，以指定要求匯總的細微程度。

設定計量之後，匿名要求會開始出現在圖形上。 下圖顯示過去三十分鐘內匯總的匿名要求。

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="顯示如何設定計量以將 blob 交易加總的螢幕擷取畫面":::

您也可以設定警示規則，在對您的儲存體帳戶進行特定數目的匿名要求時通知您。 如需詳細資訊，請參閱[使用 Azure 監視器建立、檢視及管理計量警示](../../azure-monitor/platform/alerts-metric.md)。

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>分析記錄以識別接收匿名要求的容器

Azure 儲存體記錄取得對儲存體帳戶提出之要求的詳細資料，包括要求的授權方式。 您可以分析記錄檔，以判斷哪些容器正在接收匿名要求。

若要將要求記錄到您的 Azure 儲存體帳戶以評估匿名要求，您可以使用 Azure 監視器 (preview) 中的 Azure 儲存體記錄。 如需詳細資訊，請參閱 [監視 Azure 儲存體](../common/monitor-storage.md)。

Azure 監視器中的 Azure 儲存體記錄支援使用記錄查詢來分析記錄資料。 若要查詢記錄，您可以使用 Azure Log Analytics 工作區。 若要深入瞭解記錄查詢，請參閱 [教學課程：開始使用 Log Analytics 查詢](../../azure-monitor/log-query/get-started-portal.md)。

> [!NOTE]
> 只有在 Azure 公用雲端中才支援 Azure 監視器中 Azure 儲存體記錄的預覽。 政府雲端不支援記錄 Azure 監視器的 Azure 儲存體。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 入口網站中建立診斷設定

若要使用 Azure 監視器來記錄 Azure 儲存體資料，並使用 Azure Log Analytics 進行分析，您必須先建立診斷設定，以指出哪些類型的要求以及您想要用來記錄資料的儲存體服務。 若要在 Azure 入口網站中建立診斷設定，請遵循下列步驟：

1. [在 Azure 監視器 preview 中註冊 Azure 儲存體記錄](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在訂用帳戶中建立新的 Log Analytics 工作區，其中包含您的 Azure 儲存體帳戶。 設定儲存體帳戶的記錄之後，記錄將會出現在 Log Analytics 工作區中。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [監視] 區段中，選取 [ **診斷設定 (預覽]) **。
1. 選取 **blob** 來記錄對 blob 儲存體提出的要求。
1. 選取 [ **新增診斷設定**]。
1. 提供診斷設定的名稱。
1. 在 [ **類別細節**] 下的 [ **記錄** ] 區段中，選擇要記錄的要求類型。 所有匿名要求都會被讀取要求，所以請選取 **StorageRead** 來捕捉匿名要求。
1. 在 [ **目的地詳細資料**] 底下，選取 [ **傳送至 Log Analytics**]。 選取您的訂用帳戶和您稍早建立的 Log Analytics 工作區，如下列影像所示。

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="顯示如何設定計量以將 blob 交易加總的螢幕擷取畫面":::

建立診斷設定之後，系統會根據該設定，對儲存體帳戶的要求進行記錄。 如需詳細資訊，請參閱 [建立診斷設定以收集 Azure 中的資源記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)。

如需 Azure 監視器中 Azure 儲存體記錄檔中可用欄位的參考，請參閱 [ (preview 的資源記錄) ](../common/monitor-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-anonymous-requests"></a>匿名要求的查詢記錄

Azure 監視器中的 Azure 儲存體記錄包含用來對儲存體帳戶提出要求的授權類型。 在記錄查詢中，篩選 **AuthenticationType** 屬性以查看匿名要求。

若要針對 Blob 儲存體取得過去7天的匿名要求記錄，請開啟您的 Log Analytics 工作區。 接下來，將下列查詢貼入新的記錄查詢中，然後執行它：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

您也可以根據此查詢來設定警示規則，以通知您匿名要求。 如需詳細資訊，請參閱 [使用 Azure 監視器建立、查看和記錄管理警示](../../azure-monitor/platform/alerts-log.md)。

## <a name="remediate-anonymous-public-access"></a>補救匿名公用存取

在您對儲存體帳戶中的容器和 blob 評估匿名要求之後，您可以採取動作來限制或防止公用存取。 如果您的儲存體帳戶中的某些容器可能必須可供公開存取，您可以為儲存體帳戶中的每個容器設定公用存取設定。 此選項可讓您對公用存取提供最細微的控制。 如需詳細資訊，請參閱 [設定容器的公用存取層級](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)。

為了加強安全性，您可以禁止整個儲存體帳戶的公用存取。 儲存體帳戶的公用存取設定會覆寫該帳戶中容器的個別設定。 如果您不允許儲存體帳戶的公用存取，則會無法以匿名方式存取設定為允許公用存取的任何容器。 如需詳細資訊，請參閱 [允許或不允許儲存體帳戶的公用讀取權限](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)。

如果您的案例需要某些容器必須可供公開存取，建議您將這些容器及其 blob 移至保留給公用存取的儲存體帳戶。 然後，您可以不允許任何其他儲存體帳戶進行公用存取。

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>確認不允許對 blob 的公用存取

若要確認不允許對特定 blob 的公用存取，您可以嘗試透過其 URL 下載 blob。 如果下載成功，則 blob 仍為公開可用。 如果 blob 因為儲存體帳戶不允許公用存取而無法公開存取，您將會看到錯誤訊息，指出此儲存體帳戶上不允許公用存取。

下列範例示範如何使用 PowerShell 來嘗試透過其 URL 下載 blob。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>確認不允許修改容器的公用存取設定

若要在儲存體帳戶不允許公用存取權之後，確認容器的公用存取設定無法修改，您可以嘗試修改設定。 如果儲存體帳戶不允許公用存取，則變更容器的公用存取設定將會失敗。

下列範例顯示如何使用 PowerShell 來嘗試變更容器的公用存取設定。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>確認不允許建立已啟用公用存取的容器

如果儲存體帳戶不允許公用存取，則您將無法建立已啟用公用存取的新容器。 若要確認，您可以嘗試建立已啟用公用存取的容器。

下列範例顯示如何使用 PowerShell 來嘗試建立已啟用公用存取的容器。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>檢查多個帳戶的公用存取設定

若要在一組儲存體帳戶間檢查公用存取設定，並獲得最佳效能，您可以使用 Azure 入口網站中的 Azure Resource Graph Explorer。 若要深入瞭解如何使用 Resource Graph Explorer，請參閱 [快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](/azure/governance/resource-graph/first-query-portal)。

在 Resource Graph Explorer 中執行下列查詢，會傳回儲存體帳戶的清單，並顯示每個帳戶的公用存取設定：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure 原則來針對合規性進行審核

如果您有大量的儲存體帳戶，您可能會想要執行審核，以確定這些帳戶已設定為防止公用存取。 若要針對合規性審核一組儲存體帳戶，請使用 Azure 原則。 Azure 原則是一項服務，可讓您用來建立、指派和管理將規則套用至 Azure 資源的原則。 Azure 原則可協助您讓這些資源符合公司標準和服務等級協定的規範。 如需詳細資訊，請參閱 [Azure 原則概觀](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>建立具有審核效果的原則

Azure 原則支援的效果，可決定針對資源評估原則規則時所發生的情況。 當資源不符合規範時，此審核效果會建立一則警告，但不會停止要求。 如需效果的詳細資訊，請參閱 [瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)。

若要針對具有 Azure 入口網站之儲存體帳戶的公用存取設定建立具有審核效果的原則，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至 Azure 原則服務。
1. 在 [ **撰寫** 中] 區段下，選取 [ **定義**]。
1. 選取 [ **新增原則定義** ] 以建立新的原則定義。
1. 在 [ **定義位置** ] 欄位中，選取 [ **更多** ] 按鈕以指定稽核原則資源所在的位置。
1. 指定原則的名稱。 您可以選擇性地指定描述和分類。
1. 在 [ **原則規則**] 下，將下列原則定義新增至 [ **>policyrule.if** ] 區段。

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 儲存原則。

### <a name="assign-the-policy"></a>指派原則

接下來，將原則指派給資源。 原則的範圍會對應至該資源及其下的任何資源。 如需原則指派的詳細資訊，請參閱 [Azure 原則指派結構](../../governance/policy/concepts/assignment-structure.md)。

若要使用 Azure 入口網站指派原則，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至 Azure 原則服務。
1. 在 [ **撰寫** 中] 區段下，選取 [ **指派**]。
1. 選取 [ **指派原則** ] 以建立新的原則指派。
1. 在 [ **領域** ] 欄位中，選取原則指派的範圍。
1. 在 [ **原則定義** ] 欄位中，選取 [ **更多** ] 按鈕，然後從清單中選取您在上一節中定義的原則。
1. 提供原則指派的名稱。 描述是選擇性的。
1. 將 [ **強制執行原則** ] 設為 [ *啟用*]。 此設定對稽核原則沒有任何作用。
1. 選取 [ **審核 + 建立** ] 以建立指派。

### <a name="view-compliance-report"></a>查看合規性報告

指派原則之後，您可以查看合規性報告。 稽核原則的合規性報告提供有關哪些儲存體帳戶不符合原則的資訊。 如需詳細資訊，請參閱 [取得原則合規性資料](../../governance/policy/how-to/get-compliance-data.md)。

在建立原則指派之後，可能需要幾分鐘的時間，合規性報告才會變成可用狀態。

若要在 Azure 入口網站中查看合規性報告，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至 Azure 原則服務。
1. 選取 [ **符合**]。
1. 篩選您在上一個步驟中建立的原則指派名稱結果。 報表會顯示有多少資源不符合原則。
1. 您可以向下切入報表以取得其他詳細資料，包括不符合規範的儲存體帳戶清單。

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="顯示如何設定計量以將 blob 交易加總的螢幕擷取畫面":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>使用 Azure 原則強制執行授權存取

Azure 原則藉由確保 Azure 資源遵守需求和標準，來支援雲端治理。 為確保您組織中的儲存體帳戶只允許授權的要求，您可以建立原則，以防止使用允許匿名要求的公用存取設定來建立新的儲存體帳戶。 如果該帳戶的公用存取設定與原則不相容，此原則也會防止對現有帳戶進行所有設定變更。

強制原則會使用 Deny 效果來防止要求建立或修改儲存體帳戶，以允許公用存取。 如需效果的詳細資訊，請參閱 [瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)。

若要針對允許匿名要求的公用存取設定建立具有拒絕效果的原則，請遵循使用 Azure 原則中所述的相同步驟 [來進行合規性的審核](#use-azure-policy-to-audit-for-compliance)，但在原則定義的 **>policyrule.if** 區段中提供下列 JSON：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

當您建立具有 Deny 效果的原則，並將其指派給某個範圍之後，使用者將無法建立允許公用存取的儲存體帳戶。 使用者也不能對目前允許公用存取的現有儲存體帳戶進行任何設定變更。 嘗試這麼做會導致錯誤。 儲存體帳戶的公用存取設定必須設定為 **false** ，才能繼續建立或設定帳戶。

下圖顯示當您嘗試建立儲存體帳戶以允許公用存取 (新帳戶的預設值時，所發生的錯誤) 當具有 Deny 效果的原則需要不允許公用存取時。

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="顯示如何設定計量以將 blob 交易加總的螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

- [設定容器和 blob 的匿名公用讀取權限](anonymous-read-access-configure.md)
- [使用 .NET 以匿名方式存取公用容器和 blob](anonymous-read-access-client.md)
