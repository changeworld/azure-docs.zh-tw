---
title: 設定進階威脅防護
titleSuffix: Azure Storage
description: 為 Azure 儲存配置高級威脅保護,以檢測帳戶活動中的異常,並通知訪問帳戶的潛在有害嘗試。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: f390aececdbf9a20f191279892c1856332f2aa8e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482102"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>為 Azure 儲存設定進階威脅保護

Azure 儲存的先進威脅保護提供額外的安全智慧式,用於偵測存取或利用儲存帳戶的異常和潛在有害嘗試。 此保護層允許您解決威脅,而無需成為安全專家或管理安全監控系統。

當活動異常發生時,將觸發安全警報。 這些安全警報與 Azure[安全中心](https://azure.microsoft.com/services/security-center/)集成,並且也通過電子郵件發送給訂閱管理員,其中詳細介紹了可疑活動,並就如何調查和修復威脅提出了建議。

服務引入讀取、寫入和刪除到 Blob 儲存的請求以進行威脅檢測的診斷日誌。 要調查來自高級威脅防護的警報,可以使用存儲分析日誌記錄查看相關的存儲活動。 有關詳細資訊,請參閱在[Azure 門戶中](storage-monitor-storage-account.md#configure-logging)**設定紀錄紀錄**。

## <a name="availability"></a>可用性

Azure 儲存的進階威脅保護目前僅適用於 Blob[儲存](https://azure.microsoft.com/services/storage/blobs/)。 支援進階威脅保護的帳戶類型包括通用 v2、塊 Blob 和 Blob 儲存帳戶。 高級威脅防護在所有公共雲和美國政府雲中都可用,但在其他主權或 Azure 政府雲區域中不可用。

有關定價詳細資訊(包括 30 天免費試用版),請參閱[Azure 安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="set-up-advanced-threat-protection"></a>設置高級威脅防護

您可以通過以下幾節中介紹的幾種方式配置高級威脅防護。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 啟動[Azure 門戶](https://portal.azure.com/)。
1. 導航到 Azure 儲存帳戶。 在 **「設定」** 下,選擇 **「高級安全**」。。
1. 選擇進階安全配置頁面上的 **「設定」** 連結。
1. 將**進階安全性**設定為**ON**。
1. 按下 **「保存**」以儲存新策略或更新的策略。

    ![開啟 Azure 儲存體進階威脅防護](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure 資訊安全中心](#tab/azure-security-center)

訂閱 Azure 安全中心中的標準層時,所有存儲帳戶上會自動設置高級威脅保護。 您可以在特定訂閱下為儲存帳戶啟用或禁用進階威脅保護,如下所示:

1. 在[Azure 門戶](https://portal.azure.com)中啟動**Azure 安全中心**。
1. 在主選單中,按下 **「定價&設定**。
1. 單擊要為其存儲帳戶啟用或禁用威脅保護的訂閱。

    ![選取訂閱](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. 點選 單擊**定價層**。
1. 在「**按資源類型選擇定價層」** 部分中,在 **「存儲帳戶**」 行中,按一下「**已啟用**」 或 **「已禁用**」 。

    ![在安全中心啟用 ATP](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. 按一下 [檔案]  。

### <a name="template"></a>[範本](#tab/template)

使用 Azure 資源管理器樣本部署啟用了進階威脅保護的 Azure 儲存帳戶。 有關詳細資訊,請參閱[具有進階威脅保護的儲存帳戶](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="azure-policy"></a>[Azure 原則](#tab/azure-policy)

使用 Azure 策略在特定訂閱或資源組下跨存儲帳戶啟用進階威脅保護。

1. 啟動 Azure**策略 - 定義**頁。

1. 搜索**存儲帳戶策略上的「部署進階威脅保護**」。

     ![搜尋原則](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. 選擇 Azure 訂閱或資源組。

    ![選擇訂閱或群組](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 分配策略。

    ![原則定義頁面](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令為特定儲存帳戶創建、更新或獲取進階威脅保護設定。

* [進階威脅防護 - 建立](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [進階威脅防護 - 取得](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下 PowerShell cmdlet:

* [啟用進階威脅防護](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [獲得高級威脅防護](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [禁用高級威脅防護](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>探索安全異常

發生儲存體活動異常狀況時，您會收到電子郵件通知，其中包含可疑安全性事件的相關資訊。 事件的詳細資料包括：

* 異常的性質
* 儲存體帳戶名稱
* 啟用時間
* 儲存類型
* 潛在原因
* 調查步驟
* 補救步驟

該電子郵件還會提供關於可能原因和建議動作的詳細資料，以協助您調查並減輕潛在威脅。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

可以從 Azure 安全中心[的安全警報磁貼](../../security-center/security-center-managing-and-responding-alerts.md)查看和管理當前安全警報。 按一下特定警示，即會提供適合用來調查目前威脅及解決未來威脅的詳細資料與動作。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>安全性警示

警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索儲存體帳戶而產生的。 有關 Azure 儲存的警報清單,請參閱[Azure 安全中心中數據服務的威脅保護中的](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)**存儲**部分。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 儲存帳戶中的紀錄](/rest/api/storageservices/About-Storage-Analytics-Logging)詳細資訊
* 瞭解有關[Azure 安全中心](../../security-center/security-center-intro.md)
