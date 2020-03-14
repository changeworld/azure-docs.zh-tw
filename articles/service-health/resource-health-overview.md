---
title: Azure 資源健康狀態概觀
description: Azure 資源健康狀態的概觀
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: f22b41e66f15dd12d973c5744870969fae795bc1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258196"
---
# <a name="resource-health-overview"></a>資源健康狀態總覽
 
Azure 資源健康狀態可協助您診斷並取得影響 Azure 資源的服務問題支援。 它會報告您的資源目前和過去的健全狀況。

[Azure 狀態](https://status.azure.com)會報告影響一系列 Azure 客戶的服務問題。 資源健康狀態可為您提供資源健康狀態的個人化儀表板。 資源健康狀態會顯示您的資源因 Azure 服務問題而無法使用的所有時間。 此資料可讓您輕鬆查看是否違反 SLA。

## <a name="resource-definition-and-health-assessment"></a>資源定義和健康狀態評估

*資源*是 Azure 服務的特定實例，例如虛擬機器、web 應用程式或 SQL database。 資源健康狀態會依賴來自不同 Azure 服務的信號來評估資源是否狀況良好。 如果資源健康狀態不佳，資源健康狀態會分析其他資訊來判斷問題的來源。 此外，它也會報告 Microsoft 為了修正問題而採取的動作，並識別您可以處理的動作。

如需健康情況評估方式的詳細資訊，請參閱[Azure 資源健康狀態](resource-health-checks-resource-types.md)的資源類型和健康狀態檢查清單。

## <a name="health-status"></a>健康狀態

資源的健康會顯示為下列其中一種狀態。

### <a name="available"></a>可用

[*可用*] 表示沒有偵測到會影響資源健康情況的事件。 如果資源在過去24小時內從未規劃的停機時間復原，您會看到 [最近已解決] 通知。

![具有「最近已解決」通知之虛擬機器的 [可用 *] 狀態](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>無法使用

[*無法使用*] 表示服務偵測到會影響資源健康情況的進行中平臺或非平臺事件。

#### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基礎結構的多個元件觸發。 其中包括排程的動作（例如，規劃的維護）和非預期的事件（例如，在指定的時間範圍之後預測為失敗的未計畫主機重新開機或降級主機硬體）。

資源健康狀態提供有關事件和復原程式的其他詳細資料。 即使您沒有有效的支援合約，它也可讓您與 Microsoft 支援服務聯繫。

![因平臺事件而導致虛擬機器的 [無法使用] 狀態](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>非平台事件

非平臺事件是由使用者動作所觸發。 範例包括停止虛擬機器或達到 Azure Cache for Redis 的最大連線數目。

![因非平臺事件而導致虛擬機器的「無法使用」狀態](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown

「*不明*」表示資源健康狀態未收到超過10分鐘的資源相關資訊。 雖然此狀態並非資源狀態的明確指示，但對於疑難排解而言，這是很重要的資料點。

如果資源如預期般執行，幾分鐘後資源的狀態會變更為 [可用]。

如果您遇到資源的問題，*未知*的健全狀況狀態可能表示平臺中的事件影響了資源。

![虛擬機器的「未知」狀態](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>已降級

[已*降級*] 表示您的資源偵測到效能降低，但仍可供使用。

不同的資源在報告其已降級時，有自己的準則。

![虛擬機器的狀態為 [已降級]](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>報告不正確的狀態

如果您認為目前的健全狀況狀態不正確，您可以選取 [回報**不正確的健全狀況狀態**] 來告訴我們。 在 Azure 問題影響您的情況下，建議您洽詢資源健康狀態的支援。

![提交錯誤狀態之相關資訊的表單](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>歷程記錄資訊

您可以在資源健康狀態的 [健康情況歷程**記錄**] 區段中，存取14天的歷程記錄。

![過去兩週的資源健康狀態事件清單](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>開始使用

若要開啟一個資源的資源健康狀態：

1. 登入 Azure 入口網站。
2. 瀏覽至您的資源。
3. 在左窗格中的資源功能表上，選取 [資源健康狀態]。

![從 [資源] 檢視開啟 [資源健康狀態]](./media/resource-health-overview/from-resource-blade.png)

選取 [所有服務]，然後在篩選文字方塊中輸入**資源健康狀態**，也可以存取資源健康狀態。 在 [說明 + 支援] 窗格中，選取[資源健康狀態](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)。

![從 [所有服務] 開啟 [資源健康狀態]](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>後續步驟

若要深入瞭解資源健康狀態，請參閱這些參考：
-  [Azure 資源健康狀態中的資源類型和健康狀態檢查](resource-health-checks-resource-types.md)
-  [關於 Azure 資源健康狀態的常見問題集](resource-health-faq.md)
