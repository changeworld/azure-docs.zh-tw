---
title: 高級威脅防護 - MariaDB 的 Azure 資料庫
description: 「進階威脅防護」會偵測出暗示對資料庫有潛在安全性威脅的異常資料庫活動。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532173"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>用於 MariaDB 高級威脅保護的 Azure 資料庫

MariaDB Azure 資料庫的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。

> [!IMPORTANT]
> 高級威脅防護處於公共預覽版。

「進階威脅防護」是進階資料安全性供應項目的一部分，該供應項目是進階安全性功能的整合套件。 高級威脅防護可以通過[Azure 門戶](https://portal.azure.com)進行訪問和管理。 此功能可用於通用和記憶體優化伺服器。

> [!NOTE]
> 下列 Azure Government 和主權雲端區域**無法**使用進階威脅防護功能：US Gov 德克薩斯州、US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 維吉尼亞州、US DoD 東部、US DoD 中部、德國中部、德國北部、中國東部、中國東部 2。 如需一般產品可用性，請瀏覽[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。


## <a name="what-is-advanced-threat-protection"></a>什麼是進階威脅防護？

MariaDB Azure 資料庫的高級威脅保護提供了新的安全層，使客戶能夠通過為異常活動提供安全警報來檢測和回應潛在威脅。 當有可疑的資料庫活動、潛在弱點，以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 MariaDB 的 Azure 資料庫高級威脅保護將警報與[Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包括可疑活動的詳細資訊，並建議就如何調查和緩解威脅採取行動。 MariaDB Azure 資料庫的高級威脅保護使解決資料庫的潛在威脅變得簡單，而無需成為安全專家或管理高級安全監控系統。 

![進階威脅防護概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>進階威脅防護警示 
MariaDB Azure 資料庫的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試，並可能引發以下警報：
- **從異常位置訪問**：當 MariaDB 伺服器的 Azure 資料庫訪問模式發生更改時觸發此警報，其中有人從異常地理位置登錄到 MariaDB 伺服器的 Azure 資料庫。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從異常 Azure 資料中心訪問**：當 MariaDB 伺服器對 Azure 資料庫的訪問模式發生更改時觸發此警報，其中有人從最近一段時間內在此伺服器上看到的異常 Azure 資料中心登錄到伺服器。 在某些情況下，警報會檢測合法操作（Azure 中的新應用程式 Power BI）。 在其他情況下，警示則是偵測來自 Azure 資源/服務的惡意動作 (離職員工、外部攻擊者)。
- **來自不熟悉主體的訪問**：當 MariaDB 伺服器的 Azure 資料庫訪問模式發生更改時觸發此警報，其中有人使用異常主體（MariaDB 使用者的 Azure 資料庫）登錄到伺服器。 在某些情況下，警示會偵測到合法的動作 (新的應用程式、開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從可能有害的應用程式存取**：使用可能有害的應用程式用存取資料庫時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測使用常見攻擊工具的攻擊。
- **MariaDB 憑據的強制 Azure 資料庫**：當具有不同憑據的異常大量失敗登錄時觸發此警報。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
* 有關定價的詳細資訊，請參閱[MariaDB 定價的 Azure 資料庫頁面](https://azure.microsoft.com/pricing/details/mariadb/) 
* 使用 Azure 門戶[為 MariaDB 高級威脅保護](howto-database-threat-protection-portal.md)配置 Azure 資料庫  
