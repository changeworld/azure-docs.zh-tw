---
title: 高級威脅防護 - Azure 門戶 - MariaDB 的 Azure 資料庫
description: 「威脅保護」會偵測異常資料庫活動，指出資料庫有潛在的安全性威脅。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: de056f61337311642589ba937c6d9842d8354d36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530133"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>MariaDB Azure 資料庫的高級威脅保護

MariaDB Azure 資料庫的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。

「進階威脅防護」是進階資料安全性供應項目的一部分，該供應項目是進階安全性功能的整合套件。 高級威脅防護可以通過[Azure 門戶](https://portal.azure.com)進行訪問和管理。

> [!IMPORTANT]
> 高級威脅防護處於公共預覽版。 如果適用於 MariaDB 的 Azure 資料庫是針對「一般用途」和「記憶體最佳化」伺服器來部署的，則此功能可在所有 Azure 區域中使用。

> [!NOTE]
> 下列 Azure Government 和主權雲端區域**無法**使用進階威脅防護功能：US Gov 德克薩斯州、US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 維吉尼亞州、US DoD 東部、US DoD 中部、德國中部、德國北部、中國東部、中國東部 2。 如需一般產品可用性，請瀏覽[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。

## <a name="set-up-threat-detection"></a>設定威脅偵測
1. 在 啟動 Azure[https://portal.azure.com](https://portal.azure.com)門戶。
2. 導航到要保護的 MariaDB 伺服器 Azure 資料庫的配置頁。 在 [安全性] 設定中，選取 [進階威脅防護 (預覽)]****。
3. 在 [進階威脅防護 (預覽)]**** 設定頁面上：

   - 在伺服器上啟用進階威脅防護。
   - 在 [進階威脅防護設定]**** 的 [傳送警示給]**** 文字方塊中，提供要在偵測到異常資料庫活動時收到安全性警示的電子郵件清單。
  
   ![設定威脅偵測](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>探索異常資料庫活動

偵測到異常資料庫活動時，您會收到電子郵件通知。 電子郵件會提供可疑安全性事件的相關資訊，包括異常活動的性質、資料庫名稱、伺服器名稱、應用程式名稱和事件時間。 此外，該電子郵件還會提供可能原因和建議動作的相關資訊，以協助您調查和減輕資料庫的潛在威脅。
 
1. 按一下電子郵件中的 [檢視最近的警示]**** 連結來啟動 Azure 入口網站，並顯示 Azure 資訊安全中心警示頁面，其中會概述在 SQL 資料庫上偵測到的作用中威脅。
    
    ![異常活動報告](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    檢視作用中的威脅：

    ![作用中的威脅](./media/howto-database-threat-protection-portal/active-threats.png)

2. 按一下特定警示可取得其他詳細資料和調查此威脅的建議，並對未來的威脅採取補救措施。
    
    ![特定警示](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>探索威脅偵測警示

SQL Database 威脅偵測將自有的警示與 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合。 在 Azure 入口網站中，資料庫和 SQL ATP 刀鋒視窗內的 SQL 動態威脅偵測圖格會追蹤作用中威脅的狀態。

按一下 [威脅偵測警示]**** 會啟動 Azure 資訊安全中心的警示頁面，並獲得在資料庫中偵測到的作用中 SQL 威脅概觀。

   ![威脅偵測警示](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
* 有關定價的詳細資訊，請參閱[MariaDB 定價的 Azure 資料庫頁面](https://azure.microsoft.com/pricing/details/mariadb/)  
