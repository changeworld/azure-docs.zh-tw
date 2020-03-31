---
title: 禁用 Azure 監視器中 VM 的監視
description: 本文介紹如何停止監視虛擬機器在 Azure 監視器中的 VM。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480516"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>禁用對 VM Azure 監視器中的 VM 的監視

啟用虛擬機器 （VM） 的監視後，以後可以選擇在 Azure 監視器中禁用 VM 監視。 本文演示如何禁用一個或多個 VM 的監視。  

目前，VM 的 Azure 監視器不支援選擇性禁用 VM 監視。 日誌分析工作區可能支援針對 VM 的 Azure 監視器和其他解決方案。 它還可能收集其他監視資料。 如果日誌分析工作區提供這些服務，則需要在開始之前瞭解禁用監視的效果和方法。

「適用於 VM 的 Azure 監視器」倚賴下列元件來提供其體驗：

* 日誌分析工作區，用於存儲來自 VM 和其他源的監視資料。
* 工作區中配置的效能計數器的集合。 集合更新連接到工作區的所有 VM 上的監視配置。
* `VMInsights`，這是在工作區中配置的監視解決方案。 此解決方案更新連接到工作區的所有 VM 上的監視配置。
* `MicrosoftMonitoringAgent`和`DependencyAgent`，它們是 Azure VM 擴展。 這些擴展收集資料並將資料發送到工作區。

在準備禁用 VM 監視時，請記住以下注意事項：

* 如果使用單個 VM 進行評估，並且使用了預先選擇的預設日誌分析工作區，則可以通過從 VM 卸載依賴項代理並斷開日誌分析代理從此工作區斷開連接來禁用監視。 如果您打算將 VM 用於其他目的，並在以後決定將其重新連接到其他工作區，則此方法是合適的。
* 如果選擇了支援其他監視解決方案和從其他源進行資料收集的預先存在的日誌分析工作區，則可以從工作區中刪除解決方案元件，而不會中斷或影響工作區。  

>[!NOTE]
> 從工作區中刪除解決方案元件後，可能會繼續查看 Azure VM 的性能和映射資料。 資料最終將停止顯示在**性能和****地圖**視圖中。 "**啟用**"選項將從選定的 Azure VM 中可用，以便以後可以重新啟用監視。  

## <a name="remove-azure-monitor-for-vms-completely"></a>完全刪除 VM 的 Azure 監視器

如果仍然需要日誌分析工作區，請按照以下步驟完全刪除 VM 的 Azure 監視器。 您將從工作區中刪除`VMInsights`解決方案。  

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 Azure 門戶中，選擇 **"所有服務**"。 在資源清單中，鍵入**日誌分析**。 開始鍵入時，清單會根據您的輸入篩選建議。 選擇**日誌分析**。
3. 在日誌分析工作區清單中，選擇啟用 VM Azure 監視器時選擇的工作區。
4. 在左側，選擇 **"解決方案**"。  
5. 在解決方案清單中，選擇**VMInsights（工作區名稱）。** 在解決方案的 **"概述"** 頁上，選擇 **"刪除**"。 當提示確認時，選擇 **"是**"。

## <a name="disable-monitoring-and-keep-the-workspace"></a>禁用監視並保留工作區  

如果日誌分析工作區仍需要支援來自其他源的監視，請按照以下步驟禁用用於評估 VM 的監視。 對於 Azure VM，您將直接從 VM 中刪除依賴代理 VM 擴展和 Windows 或 Linux 的日誌分析代理 VM 擴展。 

>[!NOTE]
>如果以下情況：則不要刪除日誌分析代理： 
>
> * Azure 自動化管理 VM 以協調進程或管理配置或更新。 
> * Azure 安全中心管理用於安全和威脅檢測的 VM。 
>
> 如果刪除日誌分析代理，將阻止這些服務和解決方案主動管理 VM。 

1. 登錄到 Azure[門戶](https://portal.azure.com)。 
2. 在 Azure 入口網站中，選取 [虛擬機器]****。 
3. 從清單中選取 VM。 
4. 在左側，選擇 **"擴展**"。 在 **"擴展"** 頁上，選擇**依賴項代理**。
5. 在擴充屬性頁上，選擇 **"卸載**"。
6. 在 **"擴展版"** 頁上，選擇**Microsoft 監控代理**。 在擴充屬性頁上，選擇 **"卸載**"。  
