---
title: 防止對容器和 blob 的匿名公用讀取權限
titleSuffix: Azure Storage
description: 瞭解如何針對儲存體帳戶分析匿名要求，以及如何防止整個儲存體帳戶或個別容器的匿名存取。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: f46a7927c149009eaf5baddbad2758732d4da758
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534259"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>防止對容器和 blob 的匿名公用讀取權限

Azure 儲存體中容器和 blob 的匿名公用讀取權限，是共用資料的便利方式，但也可能會帶來安全性風險。 請務必謹慎管理匿名存取，並瞭解如何評估匿名存取您的資料。 對於可公開存取的資料，操作複雜度、人為錯誤或惡意攻擊可能會導致成本高昂的資料違規。 Microsoft 建議您只有在應用程式案例需要時，才啟用匿名存取。

根據預設，一律禁止對 blob 資料進行公用存取。 不過，儲存體帳戶的預設設定允許具有適當許可權的使用者，在儲存體帳戶中設定容器和 blob 的公用存取權。 為了加強安全性，不論個別容器的公用存取設定為何，您都可以不允許所有對儲存體帳戶的公用存取。 不允許對儲存體帳戶進行公用存取，以防止使用者啟用帳戶中容器的公用存取權。 除非您的案例需要儲存體帳戶的公用存取權，否則 Microsoft 建議您不要對其進行公開存取。 不允許公用存取有助於防止不想要的匿名存取所造成的資料缺口。

當您不允許儲存體帳戶的公用 blob 存取時，Azure 儲存體會拒絕該帳戶的所有匿名要求。 當帳戶不允許公用存取之後，該帳戶中的容器將無法後續設定為公開存取。 任何已設定為公開存取的容器將不再接受匿名要求。 如需詳細資訊，請參閱[設定容器和 blob 的匿名公用讀取權限](anonymous-read-access-configure.md)。

本文說明如何使用拖曳（偵測-補救-Audit-治理）架構，持續管理儲存體帳戶的公用存取。

## <a name="detect-anonymous-requests-from-client-applications"></a>從用戶端應用程式偵測匿名要求

當您不允許儲存體帳戶的公用讀取權限時，您會拒絕對目前設定為公用存取的容器和 blob 提出要求的風險。 不允許儲存體帳戶的公用存取會覆寫該儲存體帳戶中個別容器的公用存取設定。 當儲存體帳戶不允許公用存取時，任何未來對該帳戶的匿名要求都會失敗。

若要瞭解不允許公用存取如何影響用戶端應用程式，Microsoft 建議您啟用該帳戶的記錄和計量，並在一段時間內分析匿名要求的模式。 使用計量來判斷對儲存體帳戶的匿名要求數目，並使用記錄來判斷以匿名方式存取的容器。

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>使用計量瀏覽器監視匿名要求

若要追蹤對儲存體帳戶的匿名要求，請使用 Azure 入口網站中的 Azure 計量瀏覽器。 如需計量瀏覽器的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)。

請遵循下列步驟來建立可追蹤匿名要求的度量：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。 在 [**監視**] 區段下，選取 [**計量**]。
1. 選取 [新增計量]。 在 [**度量**] 對話方塊中，指定下列值：
    1. 將 [範圍] 欄位保持設定為儲存體帳戶的名稱。
    1. 將 [計量**命名空間**] 設定為 [ *Blob*]。 此度量只會針對 Blob 儲存體報告要求。
    1. 將 [**度量**] 欄位設定為 [*交易*]。
    1. 將 [**匯總**] 欄位設定為 [*總和*]。

    新的計量會顯示在指定的時間間隔內，針對 Blob 儲存體的交易數總和。 產生的度量會顯示如下圖所示：

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="顯示如何設定計量以加總 blob 交易的螢幕擷取畫面":::

1. 接下來，選取 [**新增篩選**] 按鈕，以針對匿名要求的度量建立篩選準則。
1. 在 [**篩選準則**] 對話方塊中，指定下列值：
    1. 將**屬性**值設定為 [*驗證*]。
    1. 將 [**運算子**] 欄位設定為等號（=）。
    1. 將 [**值**] 欄位設為 [*匿名*]。
1. 在右上角，選取您要查看其度量的時間間隔。 您也可以指定從1分鐘到1個月的任何時間間隔，來指出要求匯總的細微程度。

設定度量之後，匿名要求就會開始出現在圖形上。 下圖顯示在過去三十分鐘內匯總的匿名要求。

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="顯示針對 Blob 儲存體的匯總匿名要求的螢幕擷取畫面":::

您也可以設定警示規則，以在對儲存體帳戶進行特定數目的匿名要求時通知您。 如需詳細資訊，請參閱[使用 Azure 監視器建立、檢視及管理計量警示](../../azure-monitor/platform/alerts-metric.md)。

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>分析記錄以識別接收匿名要求的容器

Azure 儲存體記錄檔會取得對儲存體帳戶提出之要求的詳細資料，包括要求的授權方式。 您可以分析記錄，以判斷哪些容器正在接收匿名要求。

若要將要求記錄到您的 Azure 儲存體帳戶，以便評估匿名要求，您可以在 Azure 監視器（預覽）中使用 Azure 儲存體記錄。 如需詳細資訊，請參閱[Monitor Azure 儲存體](../common/monitor-storage.md)。

Azure 監視器中的 Azure 儲存體記錄支援使用記錄查詢來分析記錄資料。 若要查詢記錄，您可以使用 Azure Log Analytics 工作區。 若要深入瞭解記錄查詢，請參閱[教學課程：開始使用 Log Analytics 查詢](../../azure-monitor/log-query/get-started-portal.md)。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 入口網站中建立診斷設定

若要使用 Azure 監視器記錄 Azure 儲存體資料，並使用 Azure Log Analytics 加以分析，您必須先建立診斷設定，以指出您想要記錄資料的要求類型和儲存體服務。 若要在 Azure 入口網站中建立診斷設定，請遵循下列步驟：

1. 在[Azure 監視器 preview 中註冊 Azure 儲存體記錄](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在訂用帳戶中建立新的 Log Analytics 工作區，其中包含您的 Azure 儲存體帳戶。 在您設定儲存體帳戶的記錄之後，記錄將會在 Log Analytics 工作區中提供。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [監視] 區段中，選取 [**診斷設定（預覽）**]。
1. 選取 [ **blob** ] 以記錄對 Blob 儲存體提出的要求。
1. 選取 [**新增診斷設定**]。
1. 提供診斷設定的名稱。
1. 在 [**類別細節**] 底下的 [**記錄**檔] 區段中，選擇要記錄的要求類型。 所有的匿名要求都會是讀取要求，因此請選取 [ **StorageRead** ] 來捕捉匿名要求。
1. 在 [**目的地詳細資料**] 底下，選取 [**傳送至 Log Analytics**]。 選取您稍早建立的訂用帳戶和 Log Analytics 工作區，如下圖所示。

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="顯示如何建立記錄要求之診斷設定的螢幕擷取畫面":::

建立診斷設定之後，系統會根據該設定來記錄對儲存體帳戶的要求。 如需詳細資訊，請參閱[建立診斷設定以收集 Azure 中的資源記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)。

如需 Azure 監視器中 Azure 儲存體記錄可用欄位的參考，請參閱[資源記錄（預覽）](../common/monitor-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-anonymous-requests"></a>匿名要求的查詢記錄

Azure 監視器中的 Azure 儲存體記錄包含用來對儲存體帳戶提出要求的授權類型。 在您的記錄查詢中，篩選**AuthenticationType**屬性以查看匿名要求。

若要針對 Blob 儲存體的匿名要求，取得過去7天的記錄，請開啟您的 Log Analytics 工作區。 接下來，將下列查詢貼入新的記錄查詢並加以執行：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

您也可以根據此查詢來設定警示規則，以通知您有關匿名要求的資訊。 如需詳細資訊，請參閱[使用 Azure 監視器建立、查看和記錄管理警示](../../azure-monitor/platform/alerts-log.md)。

## <a name="remediate-anonymous-public-access"></a>補救匿名公用存取

在您對儲存體帳戶中的容器和 blob 評估匿名要求之後，您可以採取動作來限制或防止公用存取。 如果您的儲存體帳戶中的某些容器可能需要可供公用存取，則您可以在儲存體帳戶中設定每個容器的公用存取設定。 此選項可讓您更精確地控制公用存取。 如需詳細資訊，請參閱[設定容器的公用存取層級](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)。

為了加強安全性，您可以禁止整個儲存體帳戶的公用存取。 儲存體帳戶的公用存取設定會覆寫該帳戶中容器的個別設定。 當您不允許儲存體帳戶的公用存取權時，任何設定為允許公用存取的容器都將無法再以匿名方式存取。 如需詳細資訊，請參閱[允許或禁止儲存體帳戶的公用讀取權限](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)。

如果您的案例要求某些容器必須可供公用存取，建議您將這些容器和其 blob 移至保留供公用存取的儲存體帳戶。 然後，您就可以禁止任何其他儲存體帳戶的公用存取。

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>確認不允許對 blob 的公用存取

若要確認不允許對特定 blob 的公用存取，您可以嘗試透過其 URL 下載 blob。 如果下載成功，則 blob 仍可公開使用。 如果因為儲存體帳戶不允許公用存取而無法公開存取 blob，則您會看到錯誤訊息，指出此儲存體帳戶上不允許公用存取。

下列範例示範如何使用 PowerShell，透過其 URL 嘗試下載 blob。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>確認不允許修改容器的公用存取設定

若要確認在不允許儲存體帳戶公開存取之後，無法修改容器的公用存取設定，您可以嘗試修改設定。 如果儲存體帳戶不允許公用存取，變更容器的公用存取設定將會失敗。

下列範例顯示如何使用 PowerShell 來嘗試變更容器的公用存取設定。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>確認不允許建立啟用公用存取的容器

如果儲存體帳戶不允許公用存取，您將無法建立啟用公用存取的新容器。 若要確認，您可以嘗試建立啟用公用存取的容器。

下列範例顯示如何使用 PowerShell 來嘗試建立啟用公用存取的容器。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>檢查多個帳戶的公用存取設定

若要在具有最佳效能的一組儲存體帳戶上檢查公用存取設定，您可以使用 Azure 入口網站中的 [Azure Resource Graph Explorer]。 若要深入瞭解如何使用 Resource Graph Explorer，請參閱[快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](/azure/governance/resource-graph/first-query-portal)。

在 Resource Graph Explorer 中執行下列查詢，會傳回儲存體帳戶的清單，並顯示每個帳戶的公用存取設定：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure 原則來審查合規性

如果您有大量的儲存體帳戶，您可能會想要執行審核，以確保這些帳戶已設定為防止公用存取。 若要針對其合規性來審核一組儲存體帳戶，請使用 Azure 原則。 Azure 原則是一種服務，可讓您用來建立、指派和管理將規則套用至 Azure 資源的原則。 Azure 原則可協助您保留符合公司標準和服務等級協定規範的資源。 如需詳細資訊，請參閱 [Azure 原則概觀](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>建立具有 Audit 效果的原則

Azure 原則支援決定針對資源評估原則規則時所發生狀況的效果。 當資源不符合規範時，此審核效果會建立警告，但不會停止要求。 如需效果的詳細資訊，請參閱[瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)。

若要針對具有 Azure 入口網站之儲存體帳戶的 [公用存取] 設定，建立具有 Audit 效果的原則，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至 [Azure 原則] 服務。
1. 在 [**撰寫**中] 區段底下，選取 [**定義**]。
1. 選取 [**新增原則定義**] 以建立新的原則定義。
1. 在 [**定義位置**] 欄位中，選取 [**更多**] 按鈕以指定稽核原則資源的所在位置。
1. 指定原則的名稱。 您可以選擇性地指定 [描述] 和 [類別]。
1. 在 [**原則規則**] 底下，將下列原則定義新增至 [ **policyRule** ] 區段。

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

接下來，將原則指派給資源。 原則的範圍會對應到該資源和其下的所有資源。 如需原則指派的詳細資訊，請參閱[Azure 原則指派結構](../../governance/policy/concepts/assignment-structure.md)。

若要使用 Azure 入口網站指派原則，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至 [Azure 原則] 服務。
1. 在 [**撰寫**中] 區段下，選取 [**指派**]。
1. 選取 [**指派原則**] 以建立新的原則指派。
1. 在 [**範圍**] 欄位中，選取原則指派的範圍。
1. 針對 [**原則定義**] 欄位，選取 [**更多**] 按鈕，然後從清單中選取您在上一節中定義的原則。
1. 提供原則指派的名稱。 描述是選擇性的。
1. 將 [**強制執行原則**] 設為 [*已啟用*]。 此設定不會影響稽核原則。
1. 選取 [**審核] + [建立**] 以建立指派。

### <a name="view-compliance-report"></a>查看合規性報告

指派原則之後，您就可以查看合規性報告。 稽核原則的合規性報告提供有關哪些儲存體帳戶不符合原則的資訊。 如需詳細資訊，請參閱[取得原則合規性資料](../../governance/policy/how-to/get-compliance-data.md)。

建立原則指派之後，可能需要幾分鐘的時間才能讓合規性報告變成可用狀態。

若要在 Azure 入口網站中查看合規性報告，請遵循下列步驟：

1. 在 [Azure 入口網站中，流覽至 [Azure 原則] 服務。
1. 選取 [**相容性**]。
1. 針對您在上一個步驟中建立的原則指派名稱，篩選其結果。 此報告會顯示有多少資源不符合原則。
1. 您可以向下切入報表以取得其他詳細資料，包括不符合規範的儲存體帳戶清單。

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="顯示 blob 公用存取的稽核原則合規性報告的螢幕擷取畫面":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>使用 Azure 原則強制執行授權的存取

Azure 原則藉由確保 Azure 資源符合需求和標準，來支援雲端治理。 若要確保貴組織中的儲存體帳戶只允許已獲得授權的要求，您可以建立原則，以防止使用允許匿名要求的公用存取設定來建立新的儲存體帳戶。 如果該帳戶的公用存取設定與原則不相容，此原則也會防止現有帳戶的所有設定變更。

強制原則會使用拒絕效果，以防止會建立或修改儲存體帳戶以允許公用存取的要求。 如需效果的詳細資訊，請參閱[瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)。

若要針對允許匿名要求的公用存取設定，建立具有拒絕效果的原則，請遵循[使用 Azure 原則來審查合規性](#use-azure-policy-to-audit-for-compliance)中所述的相同步驟，但在原則定義的**policyRule**區段中提供下列 JSON：

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

建立具有 [拒絕] 效果的原則並將它指派給範圍之後，使用者將無法建立允許公用存取的儲存體帳戶。 使用者也不能對目前允許公用存取的現有儲存體帳戶進行任何設定變更。 嘗試這麼做會導致錯誤。 儲存體帳戶的公用存取設定必須設為**false** ，才能繼續建立或設定帳戶。

下圖顯示當您嘗試在具有拒絕效果的原則需要公用存取權時，建立允許公用存取（新帳戶的預設值）的儲存體帳戶時所發生的錯誤。

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="螢幕擷取畫面，顯示在違反原則時建立儲存體帳戶時所發生的錯誤":::

## <a name="next-steps"></a>後續步驟

- [設定容器和 blob 的匿名公用讀取權限](anonymous-read-access-configure.md)
- [以匿名方式使用 .NET 存取公用容器和 blob](anonymous-read-access-client.md)
