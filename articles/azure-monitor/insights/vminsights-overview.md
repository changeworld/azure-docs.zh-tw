---
title: 適用於 VM 的 Azure 監視器是什麼？
description: VM 的 Azure 監視器概述，除了自動探索和映射應用程式元件及其依賴項外，還監視 Azure VM 的運行狀況和性能。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480482"
---
# <a name="what-is-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器是什麼？

適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。 它包括對本地或其他雲供應商中託管的 VM 的監視性能和應用程式依賴關係的支援。 以下關鍵功能提供深入洞察：

- **預定義的趨勢性能圖表**：顯示來賓 VM 作業系統的核心性能指標。

- **依賴項映射**：顯示來自各種資源組和訂閱的 VM 的互連元件。  

>[!NOTE]
>我們最近宣佈[，](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)我們將根據從公共預覽客戶收到的回饋對"健康"功能進行更改。 鑒於我們將進行的數量變化，我們將停止為新客戶提供健康功能。 現有客戶可以繼續使用運行狀況功能。 有關詳細資訊，請參閱我們的[通用常見問題解答](vminsights-ga-release-faq.md)。  

與 Azure 監視器日誌集成可提供強大的聚合和篩選，使 VM 的 Azure 監視器能夠分析隨時間變化的資料趨勢。 可以直接從虛擬機器在單個 VM 中查看此資料，也可以使用 Azure 監視器提供 VM 的彙總檢視，其中視圖支援 Azure 資源上下文或工作區上下文模式。 有關詳細資訊，請參閱[訪問模式概述](../platform/design-logs-deployment.md#access-mode)。

![Azure 入口網站中的虛擬機器深入解析檢視方塊](media/vminsights-overview/vminsights-azmon-directvm.png)

適用於 VM 的 Azure 監視器可以提供重要應用程式的可預測效能與可用性。 它可識別性能瓶頸和網路問題，還可以説明您瞭解問題是否與其他依賴關係相關。  

## <a name="data-usage"></a>資料使用量

當您部署適用於 VM 的 Azure 監視器時，您 VM 所收集的資料會內嵌並儲存於 Azure 監視器中。 收集的性能和依賴項資料存儲在日誌分析工作區中。 根據 [Azure 監視器價格頁面](https://azure.microsoft.com/pricing/details/monitor/)上所發佈的價格，適用於 VM 的 Azure 監視器會針對下列項目進行計費：

- 擷取並儲存的資料。
- 已建立的警示規則。
- 已傳送的通知。 

日誌大小因效能計數器的字串長度而異，並且隨著分配給 VM 的邏輯磁片和網路介面卡的數量而增加。 如果您已經擁有工作區，並且正在收集這些計數器，則不會收取任何重複費用。 如果您已在使用服務映射，您將看到的唯一更改是發送到 Azure 監視器的其他連接資料。

## <a name="next-steps"></a>後續步驟

若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。
