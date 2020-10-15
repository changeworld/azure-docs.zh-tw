---
title: 教學課程 - 使用適用於 VM 的 Azure 監視器監視混合式機器
description: 了解如何在 Azure 監視器中收集和分析資料。
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 97ab390570f434295a5aa836ef994640f6dc14f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335410"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>教學課程：使用適用於 VM 的 Azure 監視器監視混合式機器

[Azure 監視器](../overview.md)可將混合式虛擬機器的資料直接收集到 Log Analytics 工作區，以供詳細分析和相互關聯。 通常這需要使用指令碼、手動或遵循組態管理標準的自動化方法，在電腦上安裝 [Log Analytics 代理程式](../../../azure-monitor/platform/agents-overview.md#log-analytics-agent)。 已啟用 Arc 的伺服器近期導入了安裝 Log Analytics 和 Dependency 代理程式 [VM 擴充功能](../manage-vm-extensions.md)的支援 (包括 Windows 和 Linux)，讓 Azure 監視器能夠從您的非 Azure VM 中收集資料。

本教學課程說明如何透過簡化的步驟啟用適用於 VM 的 Azure 監視器，以設定和收集來自 Linux 或 Windows VM 的資料，讓體驗更加流暢並節省時間。  

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 只有[支援的區域](../overview.md#supported-regions)清單中列出的區域可使用 VM 擴充功能。

* 請參閱[支援的作業系統](../../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)，以確保您要啟用的 VM 作業系統受到適用於 VM 的 Azure 監視器支援。

* 請檢閱 [Log Analytics 代理程式概觀](../../../azure-monitor/platform/log-analytics-agent.md#network-requirements)中提供的 Log Analytics 代理程式防火牆需求。 適用於 VM 的 Azure 監視器對應 Dependency Agent 本身不會傳輸任何資料，因此不需變更防火牆或連接埠。

## <a name="sign-in-to-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-azure-monitor-for-vms"></a>啟用適用於 VM 的 Azure 監視器

1. 藉由按一下 [所有服務] 然後搜尋並選取 [電腦 - Azure Arc]，在 Azure 入口網站中啟動 Azure Arc 服務。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

1. 在**電腦 - Azure Arc** 頁面上，選取您在[快速入門](quick-enable-hybrid-vm.md)文章中建立的連線電腦。

1. 從**監視**區段下的左側窗格中，選取 [深入解析]，然後選取 [啟用]。

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

1. 在 Azure 監視器**深入解析上線**頁面中，系統會提示您建立工作區。 在本教學課程中，我們不建議您選取現有的 Log Analytics 工作區 (如果已經有的話)。 選取預設值，也就是和已註冊並連線電腦位於相同區域，且具有唯一名稱的工作區。 系統會為您建立並設定此工作區。

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

1. 執行組態時，您會收到狀態訊息。 此程序需要幾分鐘的時間，因為已連線電腦上已安裝擴充功能。

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

    完成時，您會收到一則訊息，指出電腦已成功上線，而且已成功部署深入解析。

## <a name="view-data-collected"></a>檢視收集的資料

完成部署和設定之後，請選取 [深入解析]，然後選取 [效能] 索引標籤。在效能索引標籤中，會顯示從 VM 的客體作業系統所收集到的一組選定效能計數器群組。 向下捲動以檢視更多計數器，並將滑鼠移至圖表上，檢視從在電腦上安裝 Log Analytics VM 擴充功能起的平均值和百分位數。

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

選取 [對應] 可開啟對應功能，以顯示虛擬機器上正在執行的流程及其相依性。 選取 [屬性] 以開啟 [屬性] 窗格 (如果尚未開啟)。

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="搜尋所有服務中已啟用 Arc 的伺服器" border="false":::

展開虛擬機器的流程。 選取其中一個流程來檢視其詳細資料，並醒目提示其相依性。

再次選取您的虛擬機器，然後選取 [記錄事件]。 您會看到適用於虛擬機器而儲存在 Log Analytics 工作區中的資料表清單。 這份清單會因為您使用的是 Windows 或 Linux 虛擬機器而有所不同。 選取 [事件] 資料表。 **事件**資料表包括 Windows 事件記錄檔中的所有事件。 Log Analytics 隨即會開啟，您可使用簡單查詢功能擷取已收集的事件記錄項目。

## <a name="next-steps"></a>後續步驟

查看下列文卓，以深入了解 Azure 監視器：

> [!div class="nextstepaction"]
> [Azure 監視器概觀](../../../azure-monitor/overview.md)