---
title: 將威脅情報資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將威脅智慧資料連線到 Azure 哨兵。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588037"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>連接來自威脅情報供應商的資料

> [!IMPORTANT]
> Azure Sentinel 中的威脅智慧資料連線器當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 允許您導入組織正在使用的威脅指示器，這可以增強安全分析人員檢測已知威脅和確定已知威脅優先順序的能力。 然後，Azure Sentinel 的多個功能變為可用或增強：

- **分析**包括一組計畫規則範本，您可以啟用這些範本，以便根據威脅指示器中的日誌事件的匹配項生成警報和事件。

- **活頁簿**提供有關導入到 Azure Sentinel 的威脅指標以及從分析規則生成的與威脅指示器匹配的任何警報的匯總資訊。

- **尋獵**查詢允許安全調查員在常見狩獵方案的上下文中使用威脅指示器。

- 當您調查異常並搜索惡意行為時 **，筆記本**可以使用威脅指示器。

您可以使用下一節中列出的集成威脅智慧平臺 （TIP） 產品之一、連接到 TAXII 伺服器或使用直接集成 Microsoft[圖形安全 tiIndicators API](https://aka.ms/graphsecuritytiindicators)將威脅指示器資料流到 Azure Sentinel。

## <a name="integrated-threat-intelligence-platform-products"></a>集成威脅情報平台產品

- [MISP 開源威脅情報平臺](https://www.misp-project.org/)
    
    有關向用戶端提供 MISP 實例將威脅指示器遷移到 Microsoft 圖形安全 API 的示例腳本，請參閱[MISP 到 Microsoft 圖形安全腳本](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)。

- [帕洛阿爾托網路礦梅爾德](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    有關指導性說明，請參閱[使用 MineMeld 將 IOC 發送到 Microsoft 圖形安全 API。](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)

- [威脅連接平臺](https://threatconnect.com/solution/)

    有關詳細資訊，請參閱[威脅連接集成，](https://threatconnect.com/integrations/)並在頁面上查找 Microsoft 圖形安全 API。


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>將 Azure 哨兵連接到威脅情報平臺

## <a name="prerequisites"></a>Prerequisites  

- 全域管理員或安全管理員的 Azure AD 角色，用於向使用與 Microsoft 圖形安全 tiIndicators API 直接集成的 TIP 產品或自訂應用程式授予許可權。

- 讀取和寫入 Azure Sentinel 工作區的許可權以存儲威脅指示器。

## <a name="instructions"></a>Instructions

1. 在 Azure 活動目錄中[註冊應用程式](/graph/auth-v2-service#1-register-your-app)以獲取應用程式 ID、應用程式機密和 Azure 活動目錄租戶 ID。 當您組態集成的 TIP 產品或應用時，您需要這些值，這些產品或應用使用與 Microsoft 圖形安全 tiIndicators API 的直接集成。

2. [為](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph)註冊應用程式佈建 API 許可權：將 Microsoft 圖形應用程式許可權**威脅指標.ReadWrite.自擁有添加到**已註冊的應用程式。

3. 請 Azure 活動目錄租戶管理員向組織的註冊應用程式授予管理員同意。 從 Azure 門戶 **：Azure 活動目錄** > **應用註冊** > **\<_應用名稱_>** > **視圖 API 許可權** > **授予\<_管理員同意租戶名稱_>**。

4. 配置使用與 Microsoft 圖形安全 tiIndicators API 直接集成的 TIP 產品或應用，通過指定以下內容將指標發送到 Azure Sentinel：
    
    a. 已註冊應用程式的 ID、機密和租戶 ID 的值。
    
    b. 對於目標產品，請指定 Azure 哨兵。
    
    c. 對於操作，請指定警報。

5. 在 Azure 門戶中，導航到**Azure 哨兵** > **資料連線器**，然後選擇**威脅智慧平臺（預覽）** 連接器。

6. 選擇 **"打開連接器"頁**，然後 **"連接**"。

7. 要查看導入到 Azure Sentinel 的威脅指示器，請查看**Azure 哨兵 - 日誌** > **安全見解**，然後展開**威脅智慧指示器**。

## <a name="connect-azure-sentinel-to-taxii-servers"></a>將 Azure 哨兵連接到 TAXII 伺服器

## <a name="prerequisites"></a>Prerequisites  

- 讀取和寫入 Azure Sentinel 工作區的許可權以存儲威脅指示器。

- TAXII 2.0 伺服器 URI 和集合 ID。

## <a name="instructions"></a>Instructions

1. 在 Azure 門戶中，導航到**Azure 哨兵** > **資料連線器**，然後選擇**威脅智慧 - TAXII（預覽）** 連接器。

2. 選擇 **"打開連接器"頁**。

3. 在文字方塊中指定所需的和可選的資訊。

4. 選擇 **"添加**"以啟用與 TAXII 2.0 伺服器的連接。

5. 如果您有其他 TAXII 2.0 伺服器：重複步驟 3 和 4。

6. 要查看導入到 Azure Sentinel 的威脅指示器，請查看**Azure 哨兵 - 日誌** > **安全見解**，然後展開**威脅智慧指示器**。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何將威脅智慧提供程式連接到 Azure Sentinel。 要瞭解有關 Azure 哨兵的更多內容，請參閱以下文章。

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
