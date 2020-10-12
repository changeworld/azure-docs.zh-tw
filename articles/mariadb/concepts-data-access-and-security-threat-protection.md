---
title: Advanced 威脅防護-適用於 MariaDB 的 Azure 資料庫
description: 「進階威脅防護」會偵測出暗示對資料庫有潛在安全性威脅的異常資料庫活動。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79532173"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>適用於 MariaDB 的 Azure 資料庫 Advanced 威脅防護

適用於 MariaDB 的 Azure 資料庫的 Advanced 威脅防護會偵測異常活動，指出有不尋常且可能有害的嘗試存取或惡意探索資料庫。

> [!IMPORTANT]
> Advanced 威脅防護目前處於公開預覽狀態。

「進階威脅防護」是進階資料安全性供應項目的一部分，該供應項目是進階安全性功能的整合套件。 您可以透過 [Azure 入口網站](https://portal.azure.com)來存取和管理 Advanced 威脅防護。 這項功能適用于一般用途和記憶體優化伺服器。

> [!NOTE]
> 下列 Azure Government 和主權雲端區域**無法**使用進階威脅防護功能：US Gov 德克薩斯州、US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 維吉尼亞州、US DoD 東部、US DoD 中部、德國中部、德國北部、中國東部、中國東部 2。 如需一般產品可用性，請瀏覽[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。


## <a name="what-is-advanced-threat-protection"></a>什麼是進階威脅防護？

適用於 MariaDB 的 Azure 資料庫的 Advanced 威脅防護提供新的安全性層級，可讓客戶藉由提供異常活動的安全性警示，偵測及回應潛在威脅。 當有可疑的資料庫活動、潛在弱點，以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 適用於 MariaDB 的 Azure 資料庫的 Advanced 威脅防護會將警示與 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合，包括可疑活動的詳細資料，以及如何調查和緩和威脅的建議動作。 適用於 MariaDB 的 Azure 資料庫的 Advanced 威脅防護可讓您輕鬆地解決資料庫的潛在威脅，而不需要是安全性專家或管理安全性監視系統。 

![進階威脅防護概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>進階威脅防護警示 
適用於 MariaDB 的 Azure 資料庫的 Advanced 威脅防護會偵測異常活動，指出有不尋常且可能有害的存取或惡意探索資料庫，而且可能會觸發下列警示：
- **從不尋常的位置存取**：當適用於 MariaDB 的 Azure 資料庫伺服器的存取模式發生變更時，就會觸發此警示，其中有人從不尋常的地理位置登入適用於 MariaDB 的 Azure 資料庫伺服器。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從不尋常的 Azure 資料中心存取**：當適用於 MariaDB 的 Azure 資料庫伺服器的存取模式發生變更時，就會觸發此警示，而在最近的期間內，有人從這台伺服器上看到的不尋常 Azure 資料中心登入伺服器。 在某些情況下，警示會偵測到您在 Azure 中的新應用程式 (合法的動作，Power BI) 。 在其他情況下，警示則是偵測來自 Azure 資源/服務的惡意動作 (離職員工、外部攻擊者)。
- **從不熟悉的主體存取**：當適用於 MariaDB 的 Azure 資料庫伺服器的存取模式發生變更時，就會觸發此警示，其中有人使用不尋常的主體 (適用於 MariaDB 的 Azure 資料庫使用者) 來登入伺服器。 在某些情況下，警示會偵測到合法的動作 (新的應用程式、開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從可能有害的應用程式存取**：使用可能有害的應用程式用存取資料庫時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測使用常見攻擊工具的攻擊。
- **暴力密碼破解適用於 MariaDB 的 Azure 資料庫認證**：當有異常大量的失敗登入具有不同的認證時，就會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
* 如需定價的詳細資訊，請參閱 [適用於 MariaDB 的 Azure 資料庫定價頁面](https://azure.microsoft.com/pricing/details/mariadb/) 
* 使用 Azure 入口網站設定[適用於 MariaDB 的 Azure 資料庫 Advanced 威脅防護](howto-database-threat-protection-portal.md)  
