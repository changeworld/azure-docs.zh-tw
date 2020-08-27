---
title: '使用共用金鑰 (預覽版來防止授權) '
titleSuffix: Azure Storage
description: 若要要求用戶端使用 Azure AD 來授與要求，您可以不允許使用共用金鑰 (preview) 授權之儲存體帳戶的要求。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 3d46df8847a5865c42438ea36245ead0f1e6e528
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951115"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>防止 Azure 儲存體帳戶的共用金鑰授權 (預覽) 

Azure 儲存體帳戶的每個安全要求都必須獲得授權。 根據預設，您可以使用 Azure Active Directory (Azure AD) 認證或使用共用金鑰授權的帳戶存取金鑰來授權要求。 在這兩種類型的授權中，Azure AD 可提供優於共用金鑰的更高安全性和使用方式，而且 Microsoft 建議使用此方法。 若要要求用戶端使用 Azure AD 來授與要求，您可以不允許使用共用金鑰 (preview) 授權之儲存體帳戶的要求。

如果您不允許儲存體帳戶的共用金鑰授權，Azure 儲存體會拒絕對該帳戶的所有後續要求，該帳戶已獲授權帳戶存取金鑰。 只有獲授權 Azure AD 的安全要求才會成功。 如需使用 Azure AD 的詳細資訊，請參閱 [使用 Azure Active Directory 授權存取 blob 和佇列](storage-auth-aad.md)。

> [!WARNING]
> Azure 儲存體僅支援對 Blob 和佇列儲存體的要求 Azure AD 授權。 如果您不允許針對儲存體帳戶使用共用金鑰進行授權，則要求使用共用金鑰授權的 Azure 檔案儲存體或資料表儲存體將會失敗。
>
> 在預覽期間，不允許共用金鑰授權時，對使用共用存取簽章的 Azure 檔案儲存體或資料表儲存體的要求 (使用帳戶存取金鑰產生的 SAS) 權杖將會成功。 如需詳細資訊，請參閱 [關於預覽](#about-the-preview)。
>
> 若不允許儲存體帳戶的共用金鑰存取，就不會影響 Azure 檔案儲存體的 SMB 連接。
>
> Microsoft 建議您先將任何 Azure 檔案儲存體或資料表儲存體資料移轉至個別的儲存體帳戶，再透過共用金鑰禁止存取帳戶，或者您不將此設定套用至支援 Azure 檔案儲存體或資料表儲存體工作負載的儲存體帳戶。

本文說明如何偵測以共用金鑰授權傳送的要求，以及如何補救儲存體帳戶的共用金鑰授權。 若要瞭解如何註冊預覽版，請參閱 [關於預覽](#about-the-preview)。

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>偵測用戶端應用程式所使用的授權類型

當您不允許儲存體帳戶的共用金鑰授權時，使用帳戶存取金鑰進行共用金鑰授權之用戶端的要求將會失敗。 若要瞭解在您進行這項變更之前，不允許共用金鑰授權如何影響用戶端應用程式，請啟用儲存體帳戶的記錄和計量。 然後，您可以在一段時間內分析帳戶要求的模式，以判斷要求的授權方式。

使用計量來判斷儲存體帳戶接收的要求數目，這些要求是以共用金鑰或共用存取簽章 (SAS) 來授權。 使用記錄來判斷哪些用戶端正在傳送這些要求。

如需有關在預覽期間解讀以共用存取簽章提出之要求的詳細資訊，請參閱 [關於預覽](#about-the-preview)。

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>監視有多少要求有共用金鑰授權

若要追蹤如何授權儲存體帳戶的要求，請使用 Azure 入口網站中的 Azure 計量瀏覽器。 如需計量瀏覽器的詳細資訊，請參閱 [開始使用 Azure 計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)。

遵循下列步驟來建立計量，以追蹤以共用金鑰或 SAS 所提出的要求：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。 在 [ **監視** ] 區段下，選取 [ **計量**]。
1. 選取 [新增計量]。 在 [ **度量** ] 對話方塊中，指定下列值：
    1. 將 [ **領域** ] 欄位設定為儲存體帳戶的名稱。
    1. 將計量 **命名空間** 設定為 [ *帳戶*]。 此計量會報告對儲存體帳戶的所有要求。
    1. 將 [ **度量** ] 欄位設定為 [ *交易*]。
    1. 將 **匯總** 欄位設定為 *Sum*。

    新計量會在指定的時間間隔內，顯示針對儲存體帳戶的交易數目總和。 產生的計量會顯示如下圖所示：

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="螢幕擷取畫面，顯示如何設定計量來加總以共用金鑰或 SAS 進行的交易":::

1. 接下來，選取 [ **新增篩選** ] 按鈕，以針對授權類型的度量建立篩選準則。
1. 在 [ **篩選** ] 對話方塊中，指定下列值：
    1. 將 **屬性** 值設定為 [ *驗證*]。
    1. 將 [ **運算子** ] 欄位設定為等號 (=) 。
    1. 在 [ **值** ] 欄位中，選取 [ *帳戶金鑰* 和 *SAS*]。
1. 在右上角，選取您要查看其度量的時間範圍。 您也可以指定從1分鐘到1個月之間的間隔，以指定要求匯總的細微程度。 例如，將 **時間範圍** 設定為30天，並將 **時間細微性** 設定為1天，以查看過去30天內依日匯總的要求。

設定計量之後，對您儲存體帳戶的要求就會開始出現在圖形上。 下圖顯示以共用金鑰授權或使用 SAS 權杖進行授權的要求。 過去三十天內的要求是每天匯總的。

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="顯示以共用金鑰授權之匯總要求的螢幕擷取畫面":::

您也可以設定警示規則，以在針對您的儲存體帳戶進行共用金鑰授權的特定要求數目時通知您。 如需詳細資訊，請參閱[使用 Azure 監視器建立、檢視及管理計量警示](../../azure-monitor/platform/alerts-metric.md)。

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>分析記錄以識別以共用金鑰或 SAS 授權要求的用戶端

Azure 儲存體記錄取得對儲存體帳戶提出之要求的詳細資料，包括要求的授權方式。 您可以分析記錄，以判斷哪些用戶端是以共用金鑰或 SAS 權杖來授權要求。

若要將要求記錄到您的 Azure 儲存體帳戶以評估其授權方式，您可以使用 Azure 監視器 (preview) 中的 Azure 儲存體記錄。 如需詳細資訊，請參閱 [監視 Azure 儲存體](../common/monitor-storage.md)。

Azure 監視器中的 Azure 儲存體記錄支援使用記錄查詢來分析記錄資料。 若要查詢記錄，您可以使用 Azure Log Analytics 工作區。 若要深入瞭解記錄查詢，請參閱 [教學課程：開始使用 Log Analytics 查詢](../../azure-monitor/log-query/get-started-portal.md)。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 入口網站中建立診斷設定

若要使用 Azure 監視器來記錄 Azure 儲存體資料，並使用 Azure Log Analytics 進行分析，您必須先建立診斷設定，以指出哪些類型的要求以及您想要用來記錄資料的儲存體服務。 若要在 Azure 入口網站中建立診斷設定，請遵循下列步驟：

1. [在 Azure 監視器 preview 中註冊 Azure 儲存體記錄](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在訂用帳戶中建立新的 Log Analytics 工作區，其中包含您的 Azure 儲存體帳戶，或使用現有的 Log Analytics 工作區。 設定儲存體帳戶的記錄之後，記錄將會出現在 Log Analytics 工作區中。 如需詳細資訊，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [監視] 區段中，選取 [ **診斷設定 (預覽]) **。
1. 選取您要記錄要求的 Azure 儲存體服務。 例如，選擇 [ **blob** ] 將要求記錄至 blob 儲存體。
1. 選取 [ **新增診斷設定**]。
1. 提供診斷設定的名稱。
1. 在 [ **類別細節**] 下的 [ **記錄** ] 區段中，選擇 [ **StorageRead**]、[ **StorageWrite**] 和 [ **StorageDelete** ]，將所有資料要求記錄到選取的服務。
1. 在 [ **目的地詳細資料**] 底下，選取 [ **傳送至 Log Analytics**]。 選取您的訂用帳戶和您稍早建立的 Log Analytics 工作區，如下列影像所示。

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="顯示如何建立記錄要求之診斷設定的螢幕擷取畫面":::

您可以針對儲存體帳戶中的每個 Azure 儲存體資源類型建立診斷設定。

建立診斷設定之後，系統會根據該設定，對儲存體帳戶的要求進行記錄。 如需詳細資訊，請參閱 [建立診斷設定以收集 Azure 中的資源記錄和計量](../../azure-monitor/platform/diagnostic-settings.md)。

如需 Azure 監視器中 Azure 儲存體記錄檔中可用欄位的參考，請參閱 [ (preview 的資源記錄) ](../common/monitor-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>使用共用金鑰或 SAS 提出要求的查詢記錄

Azure 監視器中的 Azure 儲存體記錄包含用來對儲存體帳戶提出要求的授權類型。 若要取得過去七天內以共用金鑰或 SAS 授權之要求的記錄，請開啟您的 Log Analytics 工作區。 接下來，將下列查詢貼入新的記錄查詢中，然後執行它。 此查詢會顯示以共用金鑰或 SAS 授權之最常傳送要求的10個 IP 位址：

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

您也可以根據此查詢來設定警示規則，以通知您以共用金鑰或 SAS 授權的要求。 如需詳細資訊，請參閱 [使用 Azure 監視器建立、查看和記錄管理警示](../../azure-monitor/platform/alerts-log.md)。

## <a name="remediate-authorization-via-shared-key"></a>透過共用金鑰補救授權

分析儲存體帳戶的要求獲得授權之後，您可以採取動作來防止透過共用金鑰存取。 但首先，您必須更新任何使用共用金鑰授權的應用程式，以改用 Azure AD。 您可以依照「偵測 [用戶端應用程式所用的授權類型](#detect-the-type-of-authorization-used-by-client-applications) 來追蹤轉換」中所述，監視記錄和計量。 如需搭配使用 Azure AD 與 blob 和佇列資料的詳細資訊，請參閱 [使用 Azure Active Directory 授權存取 blob 和佇列](storage-auth-aad.md)。

當您確信可以安全地拒絕以共用金鑰授權的要求時，可以將儲存體帳戶的 [ **AllowSharedKeyAccess** ] 屬性設定為 [ **false**]。

預設不會設定 **AllowSharedKeyAccess** 屬性，而且在您明確設定之後，才會傳回值。 當屬性值為 **null** 或為 **true**時，儲存體帳戶允許以共用金鑰授權的要求。

> [!WARNING]
> 如果有任何用戶端目前正在以共用金鑰存取儲存體帳戶中的資料，則 Microsoft 建議您先將這些用戶端遷移至 Azure AD，然後才允許共用金鑰存取儲存體帳戶。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要在 Azure 入口網站中不允許儲存體帳戶的共用金鑰授權，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [**設定**] 下找**出設定。**
1. 將 [ **允許共用金鑰存取** ] 設定為 **停用**。

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="顯示如何禁止帳戶共用金鑰存取的螢幕擷取畫面":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若不允許具有 Azure CLI 之儲存體帳戶的共用金鑰授權，請安裝 Azure CLI 2.9.1 版或更新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。 接下來，為新的或現有的儲存體帳戶設定 **allowSharedKeyAccess** 屬性。

下列範例顯示如何使用 Azure CLI 設定 **allowSharedKeyAccess** 屬性。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

不允許共用金鑰授權之後，對具有共用金鑰授權的儲存體帳戶提出要求將會失敗，並出現錯誤碼 403 (禁止) 。 Azure 儲存體會傳回錯誤，表示儲存體帳戶上不允許以金鑰為基礎的授權。

### <a name="verify-that-shared-key-access-is-not-allowed"></a>確認不允許共用金鑰存取

若要確認不再允許共用金鑰授權，您可以嘗試使用帳戶存取金鑰來呼叫資料作業。 下列範例會嘗試使用存取金鑰建立容器。 當儲存體帳戶不允許共用金鑰授權時，此呼叫會失敗。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> 如果您已設定匿名公用讀取權限的儲存體帳戶和容器，則不會授權匿名要求，且會繼續進行。 如需詳細資訊，請參閱 [設定容器和 blob 的匿名公用讀取權限](../blobs/anonymous-read-access-configure.md)。

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>檢查多個帳戶的共用金鑰存取設定

若要在一組具有最佳效能的儲存體帳戶間檢查共用金鑰存取設定，您可以在 Azure 入口網站中使用 Azure Resource Graph Explorer。 若要深入瞭解如何使用 Resource Graph Explorer，請參閱 [快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](/azure/governance/resource-graph/first-query-portal)。

在 Resource Graph Explorer 中執行下列查詢，會傳回儲存體帳戶的清單，並顯示每個帳戶的共用金鑰存取設定：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>瞭解禁止共用金鑰如何影響 SAS 權杖

當儲存體帳戶不允許共用金鑰時，Azure 儲存體會根據 SAS 的類型以及要求的目標服務來處理 SAS 權杖。 下表說明每種類型的 SAS 如何獲得授權，以及當儲存體帳戶的 **AllowSharedKeyAccess** 屬性為 **false**時，Azure 儲存體將如何處理該 sas。

| SAS 類型 | 授權類型 | AllowSharedKeyAccess 為 false 時的行為 |
|-|-|-|
| 僅限 (Blob 儲存體的使用者委派 SAS)  | Azure AD | 允許要求。 Microsoft 建議您盡可能使用使用者委派 SAS 來取得較佳的安全性。 |
| 服務 SAS | 共用金鑰 | Blob 儲存體的要求遭到拒絕。 佇列和資料表儲存體以及 Azure 檔案儲存體都允許要求。 如需詳細資訊，請參閱**關於預覽**一節中的[AllowSharedKeyAccess 為 false 時，在佇列、資料表和檔案中允許使用 SAS 權杖的要求](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false)。 |
| 帳戶 SAS | 共用金鑰 | Blob 儲存體的要求遭到拒絕。 佇列和資料表儲存體以及 Azure 檔案儲存體都允許要求。 如需詳細資訊，請參閱**關於預覽**一節中的[AllowSharedKeyAccess 為 false 時，在佇列、資料表和檔案中允許使用 SAS 權杖的要求](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false)。 |

如需共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](storage-sas-overview.md)。

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>考慮與其他 Azure 工具和服務的相容性

許多 Azure 服務會使用共用金鑰授權來與 Azure 儲存體進行通訊。 如果您不允許針對儲存體帳戶使用共用金鑰授權，這些服務將無法存取該帳戶中的資料，而且您的應用程式可能會受到負面影響。

某些 Azure 工具提供使用 Azure AD 授權來存取 Azure 儲存體的選項。 下表列出一些熱門的 Azure 工具，並說明是否可以使用 Azure AD 來授權 Azure 儲存體的要求。

| Azure 工具 | Azure AD Azure 儲存體的授權 |
|-|-|
| Azure 入口網站 | 支援。 如需從 Azure 入口網站授權 Azure AD 帳戶的相關資訊，請參閱在 [Azure 入口網站中選擇如何授權存取 blob 或佇列資料](storage-access-blobs-queues-portal.md)。 |
| AzCopy | 支援 Blob 儲存體。 如需授權 AzCopy 作業的相關資訊，請參閱 AzCopy 檔中的 [選擇如何提供授權認證](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) 。 |
| Azure 儲存體總管 | 僅支援 Blob 儲存體及 Azure Data Lake Storage Gen2。 不支援對佇列儲存體 Azure AD 存取。 請務必選取正確的 Azure AD 租使用者。 如需詳細資訊，請參閱 [開始使用儲存體總管](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | 支援。 如需如何授權 PowerShell 命令搭配 Azure AD 存取 blob 和佇列資料的資訊，請參閱 [使用 Azure AD 認證執行 powershell 命令以存取 blob 或佇列資料](authorize-active-directory-powershell.md)。 |
| Azure CLI | 支援。 如需如何使用 Azure AD 存取 blob 和佇列資料的授權 Azure CLI 命令的詳細資訊，請參閱 [使用 Azure AD 認證執行 Azure CLI 命令以存取 blob 或佇列資料](authorize-data-operations-cli.md)。 |
| Azure IoT 中樞 | 支援。 如需詳細資訊，請參閱 [虛擬網路的 IoT 中樞支援](../../iot-hub/virtual-network-support.md)。 |
| Azure Cloud Shell | Azure Cloud Shell 是 Azure 入口網站中的整合式 Shell。 Azure Cloud Shell 會在儲存體帳戶中裝載 Azure 檔案共用的持續性檔案。 如果該儲存體帳戶不允許共用金鑰授權，這些檔案將變成無法存取。 如需詳細資訊，請參閱 [連接您的 Microsoft Azure 檔案儲存體](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage)。 <br /><br /> 若要在 Azure Cloud Shell 中執行命令以管理不允許共用金鑰存取的儲存體帳戶，請先確定您已透過角色型存取控制，將這些帳戶的必要許可權授與 (RBAC) 。 如需詳細資訊，請參閱 [什麼是 AZURE RBAC)  (的 azure 角色型存取控制？](../../role-based-access-control/overview.md)。 |

## <a name="about-the-preview"></a>關於預覽

Azure 公用雲端中提供不允許共用金鑰授權的預覽。 只有使用 Azure Resource Manager 部署模型的儲存體帳戶才支援此功能。 如需哪些儲存體帳戶使用 Azure Resource Manager 部署模型的相關資訊，請參閱 [儲存體帳戶類型](storage-account-overview.md#types-of-storage-accounts)。

若要註冊預覽，請參閱 [Azure 儲存體允許共用金鑰存取有限的公開預覽](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u)。

> [!IMPORTANT]
> 此預覽僅適用於非生產環境。

預覽版包含下列各節所述的限制。

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>計量和記錄會報告透過 SAS 所提出的所有要求，而不論其授權方式為何

Azure 監視器中的 Azure 計量和記錄不會在預覽中區分不同類型的共用存取簽章。 Azure 計量瀏覽器中的 **sas** 篩選器，以及 Azure 儲存體記錄的 **sas** 欄位 Azure 監視器以任何類型的 SAS 授權的報表要求。 不過，不同類型的共用存取簽章會以不同的方式進行授權，而且不允許共用金鑰存取時，會有不同的行為：

- 服務 SAS 權杖或帳戶 SAS 權杖是以共用金鑰授權，而且當 **AllowSharedKeyAccess** 屬性設定為 **false**時，將不允許在對 Blob 儲存體的要求中使用。
- 使用者委派 SAS 的授權為 Azure AD，當 **AllowSharedKeyAccess** 屬性設定為 **false**時，將允許在對 Blob 儲存體的要求上進行。

當您評估儲存體帳戶的流量時，請記住，如「偵測 [用戶端應用程式所使用的授權類型](#detect-the-type-of-authorization-used-by-client-applications) 」中所述的計量和記錄，可能會包含使用者委派 SAS 所提出的要求。 如需 **AllowSharedKeyAccess** 屬性設定為 **false**時，Azure 儲存體如何回應 sas 的詳細資訊，請參閱瞭解不允許 [共用金鑰如何影響 sas 權杖](#understand-how-disallowing-shared-key-affects-sas-tokens)。

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>當 AllowSharedKeyAccess 為 false 時，允許使用 SAS 權杖的要求提供佇列、資料表和檔案

當儲存體帳戶在預覽期間不允許共用金鑰存取時，會繼續允許以佇列、資料表或 Azure 檔案儲存體資源為目標的共用存取簽章。 這項限制適用于服務 SAS 權杖和帳戶 SAS 權杖。 這兩種類型的 SAS 都具有共用金鑰的授權。

## <a name="next-steps"></a>接下來的步驟

- [授權存取 Azure 儲存體中的資料](storage-auth.md)
- [使用 Azure Active Directory 授權存取 blob 和佇列](storage-auth-aad.md)
- [使用共用金鑰進行授權](/rest/api/storageservices/authorize-with-shared-key)