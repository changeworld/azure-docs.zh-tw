---
title: 設定 advanced 威脅防護
titleSuffix: Azure Storage
description: 設定 Azure 儲存體的「先進的威脅防護」來偵測帳戶活動中的異常狀況，並收到可能有害的帳戶存取嘗試的通知。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: f69301bdbc0af9334858940fbfd3d7d0a0a63153
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807635"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>設定 Azure 儲存體的先進威脅防護

Azure 儲存體的先進威脅防護提供一層額外的安全性情報，可偵測不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 這一層保護可讓您在不是安全性專家或管理安全性監視系統的情況下處理威脅。

出現異常活動時，就會觸發安全性警示。 這些安全性警示會與[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合，並透過電子郵件傳送給訂用帳戶系統管理員，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。

服務會內嵌讀取、寫入及刪除要求 Blob 儲存體的資源記錄，以進行威脅偵測。 若要調查 [advanced 威脅防護] 中的警示，您可以使用儲存體分析記錄來查看相關的儲存體活動。 如需詳細資訊，請參閱在[Azure 入口網站中設定監視儲存體帳戶中](storage-monitor-storage-account.md#configure-logging)的**記錄**。

## <a name="availability"></a>可用性

Azure 儲存體的進階威脅防護目前僅適用於 [Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)。 支援「先進的威脅防護」的帳戶類型包括一般用途 v2、區塊 blob 和 Blob 儲存體帳戶。 先進的威脅防護功能適用于所有公用雲端和美國政府雲端，但不適用於其他主權或 Azure 政府機構地區。

如需定價詳細資料，包括免費的30天試用版，請參閱[Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="set-up-advanced-threat-protection"></a>設定先進的威脅防護

您可以透過下列各節所述的任一種方式設定進階威脅防護。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 啟動[Azure 入口網站](https://portal.azure.com/)。
1. 流覽至您的 Azure 儲存體帳戶。 在 [**設定**] 底下，選取 [ **Advanced security**]。
1. 選取 [advanced security 設定] 頁面上的 [**設定**] 連結。
1. 將 [**高級安全性**] 設定為 [**開啟**]。
1. 按一下 [**儲存**] 以儲存新的或更新的原則。

    ![開啟 Azure 儲存體進階威脅防護](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure 資訊安全中心](#tab/azure-security-center)

當您訂閱 Azure 資訊安全中心中的標準層時，系統會自動在您所有的儲存體帳戶上設定 advanced 威脅防護。 您可以在特定訂用帳戶下，針對儲存體帳戶啟用或停用「先進的威脅防護」，如下所示：

1. 啟動[Azure 入口網站](https://portal.azure.com)中的**Azure 資訊安全中心**。
1. 從主功能表中，按一下 [定價] [ **& 設定**]。
1. 按一下您要啟用或停用其儲存體帳戶的 [威脅防護] 的訂用帳戶。

    ![選取訂閱](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. 按一下 [**定價層**]。
1. 在 [**依資源類型選取定價層**] 區段的 [**儲存體帳戶**] 資料列中，按一下 [**已啟用**] 或 [**已停用**]。

    ![在資訊安全中心中啟用 ATP](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. 按一下 [檔案] 。

### <a name="template"></a>[範本](#tab/template)

使用 Azure Resource Manager 範本來部署已啟用「先進的威脅防護」的 Azure 儲存體帳戶。 如需詳細資訊，請參閱[具有 advanced 威脅防護的儲存體帳戶](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="azure-policy"></a>[Azure 原則](#tab/azure-policy)

使用 Azure 原則，在特定訂用帳戶或資源群組下的儲存體帳戶之間啟用先進的威脅防護。

1. 啟動 [Azure**原則-定義**] 頁面。

1. 搜尋在**儲存體帳戶上部署先進的威脅防護**原則。

     ![搜尋原則](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. 選取 Azure 訂用帳戶或資源群組。

    ![選取 [訂閱] 或 [群組]](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 指派原則。

    ![原則定義頁面](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令來建立、更新或取得特定儲存體帳戶的「先進的威脅防護」設定。

* [先進的威脅防護-建立](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [先進的威脅防護-取得](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列 PowerShell Cmdlet：

* [啟用先進的威脅防護](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [取得先進的威脅防護](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [停用先進的威脅防護](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>探索安全性異常

發生儲存體活動異常狀況時，您會收到電子郵件通知，其中包含可疑安全性事件的相關資訊。 事件的詳細資料包括：

* 異常的本質
* 儲存體帳戶名稱
* 事件時間
* 儲存類型
* 可能的原因
* 調查步驟
* 補救步驟

該電子郵件還會提供關於可能原因和建議動作的詳細資料，以協助您調查並減輕潛在威脅。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

您可以從 Azure 資訊安全中心的 [[安全性警示] 磚](../../security-center/security-center-managing-and-responding-alerts.md)查看和管理目前的安全性警示。 按一下特定警示，即會提供適合用來調查目前威脅及解決未來威脅的詳細資料與動作。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>安全性警示

警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索儲存體帳戶而產生的。 如需 Azure 儲存體的警示清單，請參閱[Azure 資訊安全中心中資料服務的威脅防護](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)中的**儲存體**一節。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 儲存體帳戶中的記錄](/rest/api/storageservices/About-Storage-Analytics-Logging)
* 深入了解 [Azure 資訊安全中心](../../security-center/security-center-intro.md)
