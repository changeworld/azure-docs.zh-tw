---
title: 將威脅情報資料連線到 Azure Sentinel |Microsoft Docs
description: 深入瞭解如何將威脅情報資料連線到 Azure Sentinel。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 8036203e33fd63a25ecfa7c4ea720e01259be04a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84769868"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>從威脅情報提供者連接資料

> [!IMPORTANT]
> Azure Sentinel 中的威脅情報資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可讓您匯入組織所使用的威脅指標，這可以增強您的安全性分析師偵測已知威脅的能力，並排定其優先順序。 Azure Sentinel 中的幾項功能會變成可用或已增強：

- **分析**包含一組已排程的規則範本，您可以根據威脅指示器中的記錄事件的相符專案，啟用此範本來產生警示和事件。

- 活頁**簿**提供匯入 Azure Sentinel 的威脅指標的摘要資訊，以及與您的威脅指標相符的分析規則所產生的任何警示。

- **搜尋**查詢可讓安全性調查人員在常見的搜尋案例內容中，使用威脅指示器。

- 當您調查異常並尋找惡意行為時，**筆記本**可以使用威脅指標。

您可以使用下一節所列的其中一個整合式威脅情報平臺（TIP）產品，將威脅指標串流至 Azure Sentinel，方法是連接到 TAXII 伺服器，或使用[Microsoft Graph Security TIINDICATORS API](https://aka.ms/graphsecuritytiindicators)的直接整合。

## <a name="integrated-threat-intelligence-platform-products"></a>整合式威脅情報平台產品

- [MISP 開放原始碼威脅情報平臺](https://www.misp-project.org/)
    
    如需為用戶端提供 MISP 實例以將威脅指標遷移至 Microsoft Graph 安全性 API 的範例腳本，請參閱[MISP to Microsoft Graph Security 腳本](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)。

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    若要下載 ThreatStream 整合器和延伸模組，以及將 ThreatStream 情報連接到 Microsoft Graph 安全性 API 的指示，請參閱[ThreatStream 下載](https://ui.threatstream.com/downloads)頁面。

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    如需引導式指示，請參閱[使用 MineMeld 將 ioc 傳送至 Microsoft Graph 安全性 API](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)。

- [ThreatConnect 平臺](https://threatconnect.com/solution/)

    如需相關資訊，請參閱[ThreatConnect](https://threatconnect.com/integrations/)整合和尋找頁面上的 MICROSOFT GRAPH 安全性 API。


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>將 Azure Sentinel 連接到您的威脅情報平臺

## <a name="prerequisites"></a>必要條件  

- 全域管理員或安全性系統管理員的 Azure AD 角色，以授與許可權給您的 TIP 產品或自訂應用程式，其使用與 Microsoft Graph Security tiIndicators API 的直接整合。

- [Azure Sentinel] 工作區的 [讀取] 和 [寫入] 許可權，以儲存您的威脅指示器。

## <a name="instructions"></a>Instructions

1. 在 Azure Active Directory 中[註冊應用程式](/graph/auth-v2-service#1-register-your-app)，以取得應用程式識別碼、應用程式密碼，以及 Azure Active Directory 的租使用者識別碼。 當您設定整合式秘訣產品或使用與 Microsoft Graph Security tiIndicators API 直接整合的應用程式時，您需要這些值。

2. 設定已註冊應用程式的[API 許可權](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph)：將 Microsoft Graph 應用程式許可權**OwnedBy**新增至已註冊的應用程式。

3. 要求您的 Azure Active Directory 租使用者系統管理員將管理員同意授與貴組織已註冊的應用程式。 從 Azure 入口網站： **Azure Active Directory**  >  **應用程式註冊**  >  **\<_app name_>**  >  **View API 許可權**授與系統  >  **管理員同意 \<_tenant name_> **。

4. 設定您的 TIP 產品或應用程式，使用與 Microsoft Graph Security tiIndicators API 的直接整合，藉由指定下列各項，將指標傳送給 Azure Sentinel：
    
    a. 已註冊應用程式的識別碼、密碼和租使用者識別碼的值。
    
    b. 針對目標產品，指定 Azure Sentinel。
    
    c. 針對 [動作]，指定 [警示]。

5. 在 [Azure 入口網站中，流覽至 [ **Azure Sentinel**  >  **資料連線器**]，然後選取 [**威脅情報平臺（預覽）** ] 連接器。

6. 選取 [**開啟連接器] 頁面**，然後按一下 **[連接]**。

7. 若要查看匯入 Azure Sentinel 的威脅指標，請流覽至 [ **Azure Sentinel 記錄**]  >  **SecurityInsights**，然後展開 [ **ThreatIntelligenceIndicator**]。

## <a name="connect-azure-sentinel-to-taxii-servers"></a>將 Azure Sentinel 連線至 TAXII 伺服器

## <a name="prerequisites"></a>必要條件  

- [Azure Sentinel] 工作區的 [讀取] 和 [寫入] 許可權，以儲存您的威脅指示器。

- TAXII 2.0 伺服器 URI 和集合識別碼。

## <a name="instructions"></a>Instructions

1. 在 [Azure 入口網站中，流覽至 [ **Azure Sentinel**  >  **資料連線器**]，然後選取 [**威脅情報-TAXII （預覽）** ] 連接器。

2. 選取 [**開啟連接器] 頁面**。

3. 在文字方塊中指定必要和選擇性的資訊。

4. 選取 [**新增**] 以啟用與 TAXII 2.0 伺服器的連接。

5. 如果您有其他 TAXII 2.0 伺服器：請重複步驟3和4。

6. 若要查看匯入 Azure Sentinel 的威脅指標，請流覽至 [ **Azure Sentinel 記錄**]  >  **SecurityInsights**，然後展開 [ **ThreatIntelligenceIndicator**]。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將威脅情報提供者連接到 Azure Sentinel。 若要深入瞭解 Azure Sentinel，請參閱下列文章。

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
