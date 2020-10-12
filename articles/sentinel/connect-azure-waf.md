---
title: 將 Azure Web 應用程式防火牆 (WAF) 資料連線至 Azure Sentinel
description: 瞭解如何將 Azure WAF 資料連線到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: 8f21b415ef36442d6ac1aac518cd1327f70b8927
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88263923"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>從 Azure Web 應用程式防火牆連接資料 (WAF) 

Web 應用程式已逐漸成為利用常見已知弱點進行惡意攻擊的目標。 Azure Web 應用程式防火牆 (WAF) 為您的 Web 應用程式提供集中式保護，使其免于遭受常見的惡意探索和威脅，例如程式碼插入和跨網站腳本。 Azure WAF 可以部署在 [Azure 應用程式閘道](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) 服務、 [Azure Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview) 服務，以及透過 [AZURE 內容傳遞網路 (CDN) ](https://docs.microsoft.com/azure/web-application-firewall/cdn/cdn-overview) WAF 原則 (目前處於公開預覽狀態的) 。
您可以將 Azure WAF 記錄連線到 Azure Sentinel，讓您可以在活頁簿中查看記錄資料、使用它來建立自訂警示，並加以合併以改善您的調查。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 Azure Sentinel 工作區的讀取和寫入權限。

## <a name="connect-to-azure-waf"></a>連接至 Azure WAF

### <a name="instructions-tab"></a>指示索引標籤

1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從資料連線器資源庫選取 [ **Azure web 應用程式防火牆] (WAF) ** ，然後選取 [預覽] 窗格上的 [ **開啟連接器] 頁面** 。

1. 選取您想要連線之記錄的 WAF 資源類型連結- **開啟應用程式閘道資源 >**、 **開啟 Front Door 資源 >**，或 **開啟 [內容傳遞網路 (CDN) WAF 原則 >** ]，然後在資源清單的畫面中，從清單中選擇 WAF 資源。

    1. 從 WAF 資源的導覽功能表中，選取 [ **診斷設定**]。

    1. 選取清單底部的 [ **+ 新增診斷設定** ]。

    1. 在 [ **診斷設定** ] 畫面的 [ **診斷設定名稱** ] 欄位中輸入名稱。

    1. 按一下 [ **傳送至 Log Analytics** ] 核取方塊。 其中會顯示兩個新的欄位。 選擇 Azure Sentinel 所在) 的相關 **訂** 用帳戶和 **Log Analytics 工作區** (。

    1. 按一下您想要內嵌其記錄檔之規則類型的核取方塊。 每個資源類型的建議：

        | 資源 | 要針對內嵌選取的記錄 |
        |----------|------------------------------|
        | 應用程式閘道 | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF 原則      | WebApplicationFirewallLogs |
        |

    1. 選取 [儲存]****。

### <a name="next-steps-tab"></a>後續步驟索引標籤

- 請參閱隨附于 **azure web 應用程式防火牆** 資料連線器的建議活頁簿、查詢範例和分析規則範本，以深入瞭解您的 azure WAF 記錄資料。

- 若要在 **記錄**中查詢 Azure WAF 資料，請在查詢視窗中輸入 **AzureDiagnostics** 。

> [!NOTE]
>
> 使用這個特定的資料連線器，線上狀態指標 (資料連線器資源庫中的色彩等量，以及資料類型名稱旁邊的連接圖示) 只會在過去兩周內的某個時間點內嵌資料時，才會顯示為 *已連線* (綠色) 。 一旦經過兩周後未內嵌資料，連接器將會顯示為已中斷連線。 資料越多， *線上狀態就* 會傳回。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure WAF 記錄連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
