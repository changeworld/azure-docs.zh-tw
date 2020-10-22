---
title: 監視虛擬機器上的流失模式
description: 瞭解如何在使用 Azure Site Recovery 保護的虛擬機器上監視流失模式
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359861"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>監視虛擬機器上的流失模式

本文概述各種工具，可用來監視虛擬機器上的流失模式。 藉由使用適當的工具，您就可以輕鬆地找出造成高變換的應用程式，然後再採取進一步的動作來執行該應用程式。

## <a name="for-azure-virtual-machines-windows-or-linux"></a>針對 (Windows 或 Linux) 的 Azure 虛擬機器

如果您的電腦裝載在 Azure 中，並使用受控或非受控磁片來儲存，您可以藉由追蹤磁片計量來輕鬆地追蹤效能。 這可讓您密切監視並做出正確的磁片選擇，以符合您的應用程式使用模式。 您也可以使用它來建立警示、診斷和組建自動化。 [深入了解](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

當您使用 Azure Site Recovery 保護電腦之後，您可以使用 Azure 監視器記錄和記錄分析來監視您的機器。 [深入了解](./monitor-log-analytics.md)。

您也可以使用一些作業系統特定的工具。

## <a name="for-windows-machines"></a>Windows 電腦

如果您的電腦是在內部部署環境中執行，而不是執行 Windows 作業系統，則有幾個工具可供使用。

除了檢查工作管理員上的磁片使用量，您隨時都可以參考 **資源監視器** 和 **效能監視器**。 這些工具已經存在於 Windows 電腦上。

### <a name="resource-monitor"></a>資源監視器

**資源監視器** 顯示即時使用硬體和軟體資源的相關資訊。 若要在 Windows 電腦上執行資源監視器，請遵循下列步驟：

1. 按 Win + R 並輸入 _resmon_。
1. 一旦 resmon （即資源監視器）視窗開啟時，就會切換至 [磁片] 索引標籤。它提供下列視圖-

    ![資源監視器磁片] 索引標籤](./media/monitoring-high-churn/resmon-disk-tab.png)

1. 此索引標籤必須持續監視一段時間，才能取得清楚的圖片。 在上述範例中，我們看到 _wmiprv.exe_ 流失高。

一旦識別出在電腦上造成高變換的應用程式，您就可以採取必要的動作來處理與這些應用程式相關的流失。

### <a name="performance-monitor"></a>效能監視器

**效能監視器** 會監視電腦上的各種活動，例如 CPU 或記憶體使用量。 若要在 Windows 電腦上執行效能監視器，請遵循下列步驟：

1. 按 Win + R 並輸入 _perfmon_。
1. 一旦 perfmon （即效能監視器）視窗開啟後，就會提供下列視圖：

    ![效能監視器步驟1](./media/monitoring-high-churn/perfmon-step1.png)

1. 展開右側的 [ **監視工具** ] 資料夾，然後按一下 [效能監視器]。 這會開啟下列視圖，提供您目前效能的即時資訊-

    ![效能監視器步驟2](./media/monitoring-high-churn/perfmon-step1.png)

1. 此圖形目前只會監視一個監視器，也就是 [% Processor Time （處理器時間）]，如圖形下方的表格所示。 您可以按一下工具頂端的 [ **+** ]，加入更多專案以進行監視。
1. 以下是我們在新增更多計數器之後，效能監視器看起來的樣子-

    ![效能監視器步驟3](./media/monitoring-high-churn/perfmon-step3.png)

若要深入瞭解 [效能監視器，](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data)請參閱。

## <a name="for-linux-machines"></a>Linux 電腦

如果您的電腦是在內部部署環境中執行，而不是執行 Linux 作業系統，則有一些工具可用來監視流失模式。

### <a name="iotop"></a>Iotop

其中一個最常使用的工具是 _iotop_。 它是用來顯示即時磁片活動的公用程式。 它可以列出正在執行 i/o 的進程，以及它們所使用的磁片頻寬。

開啟命令提示字元，然後執行命令 `iotop` 。

### <a name="iostat"></a>Iostat

IoStat 是簡單的工具，會收集並顯示系統輸入和輸出儲存裝置統計資料。 此工具通常用來追蹤存放裝置的效能問題，包括裝置、本機磁片、遠端磁片。

開啟命令提示字元，然後執行命令 `iostat` 。

## <a name="next-steps"></a>後續步驟

瞭解如何使用 [Azure 監視器](monitor-log-analytics.md)進行監視。