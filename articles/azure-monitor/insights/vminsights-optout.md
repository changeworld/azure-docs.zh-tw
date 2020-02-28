---
title: 在適用於 VM 的 Azure 監視器中停用監視（預覽） |Microsoft Docs
description: 本文說明如何在適用於 VM 的 Azure 監視器中停止監視您的虛擬機器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/05/2018
ms.openlocfilehash: fb4347e610920380792a17bb620e6d97a7d72505
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669500"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>在適用於 VM 的 Azure 監視器中停用 Vm 的監視（預覽）

啟用虛擬機器（Vm）的監視之後，您可以稍後選擇停用適用於 VM 的 Azure 監視器中的監視功能。 本文說明如何停用一或多個 Vm 的監視。  

目前，適用於 VM 的 Azure 監視器不支援選擇性停用 VM 監視。 您的 Log Analytics 工作區可能支援適用於 VM 的 Azure 監視器和其他解決方案。 它也可能會收集其他監視資料。 如果您的 Log Analytics 工作區提供這些服務，您必須先瞭解停用監視的效果和方法，再開始進行。

「適用於 VM 的 Azure 監視器」倚賴下列元件來提供其體驗：

* Log Analytics 工作區，它會儲存來自 Vm 和其他來源的監視資料。
* 在工作區中設定的效能計數器集合。 集合會更新連線至工作區之所有 Vm 上的監視設定。
* `InfrastructureInsights` 和 `ServiceMap`，這是在工作區中設定的監視解決方案。 這些解決方案會更新連線至工作區之所有 Vm 上的監視設定。
* `MicrosoftMonitoringAgent` 和 `DependencyAgent`，也就是 Azure VM 擴充功能。 這些延伸模組會收集資料，並將其傳送至工作區。

當您準備停用 Vm 的監視功能時，請記住下列考慮：

* 如果您使用單一 VM 進行評估，並使用預先選取的預設 Log Analytics 工作區，您可以從 VM 卸載 Dependency agent，並從這個工作區中斷連接 Log Analytics 代理程式，以停用監視。 如果您想要將 VM 用於其他用途，並決定稍後將其重新連接至不同的工作區，則適合使用此方法。
* 如果您選取的預先存在 Log Analytics 工作區支援其他來源的其他監視解決方案和資料收集，您可以從工作區移除解決方案元件，而不會中斷或影響您的工作區。  

>[!NOTE]
> 從您的工作區移除解決方案元件之後，您可能會繼續查看 Azure Vm 的健全狀況狀態。具體來說，當您移至入口網站中的任一視圖時，您會看到效能和對應資料。 資料最終會停止出現在 [**效能**] 和 [**對應**] 視圖中。 但**健全狀況**視圖會繼續顯示 vm 的健康狀態。 選取的 Azure VM 將會提供 [**立即試用**] 選項，讓您可以在未來重新啟用監視。  

## <a name="remove-azure-monitor-for-vms-completely"></a>完全移除適用於 VM 的 Azure 監視器

如果您仍然需要 Log Analytics 工作區，請遵循下列步驟來完全移除適用於 VM 的 Azure 監視器。 您將從工作區中移除 `InfrastructureInsights` 和 `ServiceMap` 解決方案。  

>[!NOTE]
>如果您在啟用適用於 VM 的 Azure 監視器之前使用了服務對應監視解決方案，而您仍然依賴它，請勿移除該解決方案，如下列程式的最後一個步驟中所述。  
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您的輸入來篩選建議。 選取 [Log Analytics]。
3. 在您的 Log Analytics 工作區清單中，選取當您啟用適用於 VM 的 Azure 監視器時所選擇的工作區。
4. 在左側選取 [**方案**]。  
5. 在解決方案清單中，選取 [ **InfrastructureInsights （工作區名稱）** ]。 在解決方案的 [**總覽**] 頁面上，選取 [**刪除**]。 當系統提示您確認時，請選取 **[是]** 。  
6. 在解決方案清單中，選取 [ **ServiceMap （工作區名稱）** ]。 在解決方案的 [**總覽**] 頁面上，選取 [**刪除**]。 當系統提示您確認時，請選取 **[是]** 。  

啟用適用於 VM 的 Azure 監視器之前，如果您未在工作區中收集以 Windows 或 Linux 為基礎的 Vm 的[效能計數器](vminsights-enable-overview.md#performance-counters-enabled)，請[停](../platform/data-sources-performance-counters.md#configuring-performance-counters)用 windows 和 linux 的規則。

## <a name="disable-monitoring-and-keep-the-workspace"></a>停用監視並保留工作區  

如果您的 Log Analytics 工作區仍需要支援來自其他來源的監視，請遵循下列步驟，在您用來評估適用於 VM 的 Azure 監視器的 VM 上停用監視。 針對 Azure Vm，您將直接從 VM 移除相依性代理程式 VM 延伸模組和適用于 Windows 或 Linux 的 Log Analytics 代理程式 VM 延伸模組。 

>[!NOTE]
>若下列情況，請勿移除 Log Analytics 代理程式： 
>
> * Azure 自動化管理 VM 以協調進程或管理設定或更新。 
> * Azure 資訊安全中心會管理 VM 以進行安全性和威脅偵測。 
>
> 如果您移除 Log Analytics 代理程式，您將會防止這些服務和解決方案主動管理您的 VM。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在 Azure 入口網站中，選取 [虛擬機器]。 
3. 從清單中選取 VM。 
4. 在左側選取 [**擴充**功能]。 在 [**延伸**模組] 頁面上，選取 [ **DependencyAgent**]。
5. 在 [擴充功能屬性] 頁面上，選取 [**卸載**]。
6. 在 [**延伸**模組] 頁面上，選取 [ **MicrosoftMonitoringAgent**]。 在 [擴充功能屬性] 頁面上，選取 [**卸載**]。  
