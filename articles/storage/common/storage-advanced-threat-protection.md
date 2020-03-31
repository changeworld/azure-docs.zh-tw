---
title: 配置高級威脅防護
titleSuffix: Azure Storage
description: 為 Azure 存儲配置高級威脅保護，以檢測帳戶活動中的異常，並通知存取帳戶的潛在有害嘗試。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 27860b8761c565c45a604253efdff5f77606606e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061304"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>為 Azure 存儲配置高級威脅保護

Azure 存儲的高級威脅保護提供了額外的安全智慧層，用於檢測訪問或利用存儲帳戶的異常和潛在有害嘗試。 此保護層允許您解決威脅，而無需成為安全專家或管理安全監控系統。

當活動異常發生時，將觸發安全警報。 這些安全警報與 Azure[安全中心](https://azure.microsoft.com/services/security-center/)集成，並且也通過電子郵件發送給訂閱管理員，其中詳細介紹了可疑活動，並就如何調查和修復威脅提出了建議。

> [!NOTE]
> Azure 存儲的高級威脅保護目前僅適用于 Blob 存儲。 它在 Azure 政府和主權雲區域中不可用。 有關定價詳細資訊（包括 30 天免費試用版），請參閱[Azure 安全中心定價頁面]( https://azure.microsoft.com/pricing/details/security-center/)。

Azure 存儲的高級威脅保護可引入讀取、寫入和刪除到 Blob 存儲的請求以檢測威脅的診斷日誌。 要調查來自高級威脅防護的警報，可以使用存儲分析日誌記錄查看相關的存儲活動。 有關詳細資訊，請參閱在[Azure 門戶中](storage-monitor-storage-account.md#configure-logging)**配置日誌記錄**。

## <a name="set-up-advanced-threat-protection"></a>設置高級威脅防護

您可以通過以下幾節仲介紹的幾種方式配置高級威脅防護。

### <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 啟動[Azure 門戶](https://portal.azure.com/)。
1. 導航到 Azure 存儲帳戶。 在 **"設置"** 下，選擇 **"高級安全**"。
1. 選擇高級安全配置頁面上的 **"設置"** 連結。
1. 將**高級安全性**設置為**ON**。
1. 按一下 **"保存**"以保存新策略或更新的策略。

    ![開啟 Azure 儲存體進階威脅防護](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure 安全中心](#tab/azure-security-center)

訂閱 Azure 安全中心中的標準層時，所有存儲帳戶上會自動設置高級威脅保護。 您可以在特定訂閱下為存儲帳戶啟用或禁用高級威脅保護，如下所示：

1. 在[Azure 門戶](https://portal.azure.com)中啟動**Azure 安全中心**。
1. 在主功能表中，按一下 **"定價&設置**。
1. 按一下要為其存儲帳戶啟用或禁用威脅保護的訂閱。

    ![選取訂閱](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. 按一下**定價層**。
1. 在"**按資源類型選擇定價層"** 部分中，在 **"存儲帳戶**"行中，按一下"**已啟用**"或 **"已禁用**"。

    ![在安全中心啟用 ATP](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. 按一下 [儲存]****。

### <a name="template"></a>[範本](#tab/template)

使用 Azure 資源管理器範本部署啟用了高級威脅保護的 Azure 存儲帳戶。 有關詳細資訊，請參閱[具有高級威脅保護的存儲帳戶](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="azure-policy"></a>[Azure 策略](#tab/azure-policy)

使用 Azure 策略在特定訂閱或資源組下跨存儲帳戶啟用高級威脅保護。

1. 啟動 Azure**策略 - 定義**頁。

1. 搜索**存儲帳戶原則上的"部署高級威脅保護**"。

     ![搜索策略](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. 選擇 Azure 訂閱或資源組。

    ![選擇訂閱或組](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 分配策略。

    ![策略定義頁面](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令為特定存儲帳戶創建、更新或獲取高級威脅保護設置。

* [高級威脅防護 - 創建](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [高級威脅防護 - 獲取](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用以下 PowerShell Cmdlet：

* [啟用高級威脅防護](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [獲得高級威脅防護](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [禁用高級威脅防護](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>探索安全異常

發生儲存體活動異常狀況時，您會收到電子郵件通知，其中包含可疑安全性事件的相關資訊。 事件的詳細資料包括：

* 異常的性質
* 儲存體帳戶名稱
* 啟用時間
* 存儲類型
* 潛在原因
* 調查步驟
* 補救步驟

該電子郵件還會提供關於可能原因和建議動作的詳細資料，以協助您調查並減輕潛在威脅。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

可以從 Azure 安全中心[的安全警報磁貼](../../security-center/security-center-managing-and-responding-alerts.md)查看和管理當前安全警報。 按一下特定警示，即會提供適合用來調查目前威脅及解決未來威脅的詳細資料與動作。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>安全性警示

警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索儲存體帳戶而產生的。 有關 Azure 存儲的警報清單，請參閱[Azure 安全中心中資料服務的威脅保護中的](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)**存儲**部分。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 存儲帳戶中的日誌](/rest/api/storageservices/About-Storage-Analytics-Logging)詳細資訊
* 瞭解有關[Azure 安全中心](../../security-center/security-center-intro.md)
