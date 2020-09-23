---
title: 設定適用于儲存體的 Azure Defender
titleSuffix: Azure Storage
description: 設定適用于儲存體的 Azure Defender 來偵測帳戶活動中的異常狀況，並收到可能有害的存取帳戶嘗試通知。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: d2ad302042b277cf29b3a7b22af88b662686b3fd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994393"
---
# <a name="configure-azure-defender-for-storage"></a>設定適用于儲存體的 Azure Defender

適用于儲存體的 Azure Defender 提供額外一層的安全性情報，可偵測到不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 這一層保護可讓您在不是安全性專家或管理安全性監視系統的情況下，解決威脅。

出現異常活動時，就會觸發安全性警示。 這些安全性警示會與 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合，也會透過電子郵件傳送給訂用帳戶系統管理員，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。

服務會內嵌對 Blob 儲存體的讀取、寫入和刪除要求的資源記錄，以及 Azure 檔案儲存體 (預覽) 以進行威脅偵測。 若要調查 Azure Defender 的警示，您可以使用儲存體分析記錄來查看相關的儲存體活動。 如需詳細資訊，請參閱 Azure 入口網站中的**設定記錄**[監視儲存體帳戶](storage-monitor-storage-account.md#configure-logging)。

## <a name="availability"></a>可用性

適用于儲存體的 Azure Defender 目前適用于 Blob 儲存體、Azure 檔案儲存體 (preview) ，以及 Azure Data Lake Storage Gen2 (preview) 。 支援 Azure Defender 的帳戶類型包括一般用途 v2、區塊 blob 和 Blob 儲存體帳戶。 適用于儲存體的 Azure Defender 適用于所有公用雲端和美國政府雲端，但不適用於其他主權或 Azure Government 雲端區域。

啟用階層命名空間的帳戶，Data Lake Storage 使用 Azure Blob 儲存體 Api 和 Data Lake Storage Api 來支援交易。 Azure 檔案共用支援透過 SMB 的交易。

如需定價詳細資料，包括30天免費試用版，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

下列清單摘要說明適用于儲存體的 Azure Defender 可用性：

- 釋放狀態：
  - [Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/) (公開上市) 
  - [Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (PREVIEW 支援 SMB 和 REST 交易) 
  - Azure Data Lake Storage Gen2 (預覽)
- 雲端：<br>
    ✔ 商用雲端<br>
    ✔ US Gov<br>
    ✘中國 Gov，其他 Gov

## <a name="set-up-azure-defender"></a>設定 Azure Defender

您可以透過數種方式來設定 Azure Defender 以進行儲存，如下列各節所述。

### <a name="azure-security-center"></a>[Azure 資訊安全中心](#tab/azure-security-center)

當您在 Azure 資訊安全中心中訂閱標準層時，Azure Defender 會自動在您的所有儲存體帳戶上設定。 您可以針對特定訂用帳戶下的儲存體帳戶啟用或停用 Azure Defender，如下所示：

1. 在[Azure 入口網站](https://portal.azure.com)啟動**Azure 資訊安全中心**。
1. 從主功能表的 [ **管理**] 底下，選取 [ **定價 & 設定**]。
1. 選取您要啟用或停用 Azure Defender 的訂用帳戶。
1. 選取 [ **Azure defender 開啟** ] 以啟用訂用帳戶的 azure defender。
1. 在 [**依資源類型選取 Azure Defender 方案**] 底下，找出 [**儲存體**] 資料列，然後在 [**方案**] 資料行中選取 [**啟用**]
1. 儲存您的變更。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="顯示如何在安全中心啟用 Azure Defender 儲存的螢幕擷取畫面":::

此訂用帳戶中的所有儲存體帳戶現在都會啟用 Azure Defender。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 啟動 [Azure 入口網站](https://portal.azure.com/)。
1. 瀏覽至儲存體帳戶。 在 [ **設定**] 底下，選取 [ **Advanced security**]。
1. 選取 [ **啟用適用于儲存體的 Azure Defender**]。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="顯示如何為 Azure 儲存體帳戶啟用 Azure Defender 的螢幕擷取畫面":::

現在已為此儲存體帳戶啟用 Azure Defender。

### <a name="template"></a>[範本](#tab/template)

使用 Azure Resource Manager 範本來部署已啟用 Azure Defender 的 Azure 儲存體帳戶。 如需詳細資訊，請參閱 [具有 advanced 威脅防護的儲存體帳戶](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="azure-policy"></a>[Azure 原則](#tab/azure-policy)

使用 Azure 原則，在特定訂用帳戶或資源群組下的儲存體帳戶之間啟用 Azure Defender。

1. 啟動 [Azure **原則定義** ] 頁面。
1. 搜尋在 **儲存體帳戶上部署 Azure Defender** 原則。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="套用原則以啟用適用于儲存體帳戶的 Azure Defender":::

1. 選取 Azure 訂用帳戶或資源群組。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="選取原則範圍的訂用帳戶或資源群組 ":::

1. 指派原則。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="指派原則以啟用 Azure Defender 的儲存體":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令來建立、更新或取得特定儲存體帳戶的 Azure Defender 設定。

- [Advanced 威脅防護-建立](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
- [先進的威脅防護-取得](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列 PowerShell Cmdlet：

- [啟用先進的威脅防護](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [取得 advanced 威脅防護](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [停用 advanced 威脅防護](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>探索安全性異常

發生儲存體活動異常狀況時，您會收到電子郵件通知，其中包含可疑安全性事件的相關資訊。 事件的詳細資料包括：

- 異常的本質
- 儲存體帳戶名稱
- 事件時間
- 儲存體類型
- 可能的原因
- 調查步驟
- 補救步驟

該電子郵件還會提供關於可能原因和建議動作的詳細資料，以協助您調查並減輕潛在威脅。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="適用于儲存體的 Azure Defender 警示電子郵件":::

您可以從 Azure 資訊安全中心的 [ [安全性警示] 磚](../../security-center/security-center-managing-and-responding-alerts.md)，檢查並管理目前的安全性警示。 按一下特定警示，即會提供適合用來調查目前威脅及解決未來威脅的詳細資料與動作。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="適用于儲存體的 Azure Defender 警示":::

## <a name="security-alerts"></a>安全性警示

警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索儲存體帳戶而產生的。 如需 Azure 儲存體的警示清單，請參閱 [Azure 儲存體的警示](../../security-center/alerts-reference.md#alerts-azurestorage)。

## <a name="next-steps"></a>下一步

- 深入瞭解 [Azure 儲存體帳戶中的記錄](/rest/api/storageservices/About-Storage-Analytics-Logging)
- 深入了解 [Azure 資訊安全中心](../../security-center/security-center-intro.md)
