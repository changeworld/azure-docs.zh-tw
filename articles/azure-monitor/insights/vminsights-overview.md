---
title: 什麼是適用於 VM 的 Azure 監視器 (預覽)？ | Microsoft Docs
description: 概述除了自動探索和對應應用程式元件及其相依性之外，還會監視 Azure Vm 的健康情況和效能的適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 1dcce3ab9f975fcf5910c382df3489d5d4ed425a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670658"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>什麼是適用於 VM 的 Azure 監視器 (預覽)？

適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。 

它包含監視內部部署或其他雲端提供者中裝載之 Vm 的效能和應用程式相依性的支援。 下列主要功能提供深入的深入解析：

- **預先定義的趨勢效能圖表**：顯示來自來賓 VM 作業系統的核心效能計量。

- 相依性**對應**：顯示來自各種資源群組和訂用帳戶的互連元件與 VM。  

>[!NOTE]
>我們最近[宣佈](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)根據我們從公開預覽客戶收到的意見反應，對健全狀況功能進行的變更。 由於我們將進行的變更數目，我們將停止為新客戶提供健康情況功能。 現有的客戶可以繼續使用健全狀況功能。 如需詳細資訊，請參閱我們的[一般可用性常見問題](vminsights-ga-release-faq.md)。  

與 Azure 監視器記錄整合，可提供強大的彙總與篩選，而且它可以隨著時間執行趨勢分析。 單獨使用 Azure 監視器或服務對應，無法達到這類全面性工作負載監視。  

您可以直接從虛擬機器在單一 VM 中查看這項資料，或者您可以使用 Azure 監視器來傳遞 Vm 的匯總視圖，其中此視圖支援 Azure 資源內容或工作區內容模式。 如需詳細資訊，請參閱[存取模式總覽](../platform/design-logs-deployment.md#access-mode)。

![Azure 入口網站中的虛擬機器深入解析檢視方塊](./media/vminsights-overview/vminsights-azmon-directvm.png)

適用於 VM 的 Azure 監視器可以提供重要應用程式的可預測效能與可用性。 它會找出效能瓶頸和網路問題。 適用於 VM 的 Azure 監視器也可協助您了解問題是否與其他相依性有關。  

## <a name="data-usage"></a>資料使用量

當您部署適用於 VM 的 Azure 監視器時，您 VM 所收集的資料會內嵌並儲存於 Azure 監視器中。 收集的效能和相依性資料會儲存在 Log Analytics 工作區中。 根據 [Azure 監視器價格頁面](https://azure.microsoft.com/pricing/details/monitor/)上所發佈的價格，適用於 VM 的 Azure 監視器會針對下列項目進行計費：

- 擷取並儲存的資料。
- 已建立的警示規則。
- 已傳送的通知。 

記錄檔大小會因效能計數器的字串長度而異，而且可能會隨著配置給 VM 的邏輯磁片和網路介面卡數目增加。 如果您已經擁有工作區，並且正在收集這些計數器，則不會收取任何重複費用。 如果您已經使用服務對應，您將看到的唯一變更會是傳送到 Azure 監視器的額外連線資料。

## <a name="next-steps"></a>後續步驟

若要了解有助於您監視虛擬機器的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。
