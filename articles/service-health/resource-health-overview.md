---
title: Azure 資源健康狀態概觀
description: Azure 資源健康狀態的概觀
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159246"
---
# <a name="resource-health-overview"></a>資源運行狀況概述
 
Azure 資源運行狀況可説明您診斷和獲取有關影響 Azure 資源的服務問題的支援。 它報告您的資源的當前和過去運行狀況。

[Azure 狀態](https://status.azure.com)報告影響大量 Azure 客戶的服務問題。 資源運行狀況為您提供了資源運行狀況的個人化儀表板。 資源運行狀況顯示資源由於 Azure 服務問題而不可用的所有時間。 通過此資料，您可以輕鬆查看是否違反了 SLA。

## <a name="resource-definition-and-health-assessment"></a>資源定義和健康狀態評估

*資源*是 Azure 服務的特定實例，如虛擬機器、Web 應用或 SQL 資料庫。 資源運行狀況依賴于來自不同 Azure 服務的信號來評估資源是否正常運行。 如果資源健康狀態不佳，資源健康狀態會分析其他資訊來判斷問題的來源。 它還報告 Microsoft 正在採取哪些措施來解決問題，並確定您可以採取哪些措施來解決問題。

有關如何評估運行狀況的詳細資訊，請參閱[Azure 資源運行狀況](resource-health-checks-resource-types.md)中的資源類型和運行狀況檢查清單。

## <a name="health-status"></a>健康狀態

資源的健康會顯示為下列其中一種狀態。

### <a name="available"></a>可用

*可用*意味著未檢測到影響資源運行狀況的事件。 如果資源在過去 24 小時內從計畫外停機中恢復，您將看到"最近解決"通知。

![具有"最近解決"通知的虛擬機器的狀態 [可用]](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>無法使用

*不可用*意味著服務檢測到影響資源運行狀況的持續平臺或非平臺事件。

#### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基礎結構的多個元件觸發。 它們包括計畫操作（例如，計畫維護）和意外事件（例如，計畫外主機重新開機或降級的主機硬體，預計在指定的時間視窗後將失敗）。

資源運行狀況提供有關事件和恢復過程的其他詳細資訊。 它還使您能夠聯繫 Microsoft 支援部門，即使您沒有有效的支援協定。

![由於平臺事件，虛擬機器的 [不可用] 的狀態](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>非平台事件

非平臺事件由使用者操作觸發。 示例包括停止虛擬機器或達到到 Redis Azure 緩存的最大連接數。

![由於非平臺事件，虛擬機器的"不可用"狀態](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown

*未知*表示資源運行狀況在超過 10 分鐘中未收到有關資源的資訊。 儘管此狀態不是資源狀態的明確指示，但它是故障排除的重要資料點。

如果資源如預期般執行，幾分鐘後資源的狀態會變更為 [可用]**。

如果遇到資源問題，*未知*運行狀況狀態可能意味著平臺中的事件正在影響資源。

![虛擬機器的狀態 [未知]](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>已降級

*降級*意味著您的資源檢測到性能損失，儘管它仍可供使用。

不同的資源在報告降級時有自己的標準。

![虛擬機器的 [已降級] 狀態](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>報告不正確的狀態

如果您認為當前運行狀況不正確，您可以通過選擇 **"報告不正確的運行狀況"** 來告訴我們。 如果 Azure 問題影響您，我們鼓勵您聯繫資源運行狀況的支援。

![提交有關錯誤狀態的資訊的表單](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>歷史資訊

您可以在"資源運行狀況**的運行狀況歷史記錄**"部分訪問最多 30 天的歷史記錄。

![過去兩週的資源健康狀態事件清單](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>開始使用

若要開啟一個資源的資源健康狀態：

1. 登入 Azure 入口網站。
2. 瀏覽至您的資源。
3. 在左窗格中的資源功能表上，選取 [資源健康狀態]****。

![從 [資源] 檢視開啟 [資源健康狀態]](./media/resource-health-overview/from-resource-blade.png)

選取 [所有服務]****，然後在篩選文字方塊中輸入**資源健康狀態**，也可以存取資源健康狀態。 在 [說明 + 支援]**** 窗格中，選取[資源健康狀態](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)。

![從 [所有服務] 開啟 [資源健康狀態]](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>後續步驟

請查看這些引用以瞭解有關資源運行狀況的更多內容：
-  [Azure 資源運行狀況中的資源類型和運行狀況檢查](resource-health-checks-resource-types.md)
-  [關於 Azure 資源健康狀態的常見問題集](resource-health-faq.md)
