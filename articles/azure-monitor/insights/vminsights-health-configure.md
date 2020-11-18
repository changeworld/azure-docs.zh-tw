---
title: '在適用於 VM 的 Azure 監視器來賓健康情況 (預覽版中設定監視) '
description: 說明如何使用 Azure 入口網站修改適用於 VM 的 Azure 監視器 guest health (preview) 的預設監視。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: f41a43e76993a03554d32fc7f3ce3149848989a9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686693"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>在適用於 VM 的 Azure 監視器來賓健康情況 (預覽版中設定監視) 
適用於 VM 的 Azure 監視器來賓健康狀態可讓您依定期取樣的一組效能測量所定義，來查看虛擬機器的健康情況。 本文說明如何使用 Azure 入口網站來修改預設監視。 它也會說明 [使用資料收集規則設定監視](vminsights-health-configure-dcr.md)所需的監視器基本概念。

## <a name="open-monitor-configuration"></a>開啟監視設定
依序 **選取 [監視] 和** [設定] 索引標籤，以開啟 [監視設定] Azure 入口網站。

[![監視詳細資料設定](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>啟用或停用監視
單位監視和匯總監視都有 **健全狀況監視器狀態** 設定，可讓您啟用或停用監視。 啟用監視時，會顯示其健康情況，並使用它來設定 VM 的健康情況。 停用監視時，不會計算其健康情況，也不會用來設定 VM 的健康情況。

| 設定 | 描述 |
|:---|:---|
| 啟用 | 不論其父系的設定為何，都會啟用監視器。 |
| Disabled | 無論其父系的設定為何，都會停用此監視器。 |
| 與父系相同 | 系統會根據其父系的設定來啟用或停用監視。 |

停用監視時，任何準則都會顯示為無法使用，如下列範例所示。

![停用監視](media/vminsights-health-configure/disabled-monitor.png)

## <a name="enable-or-disable-virtual-machine"></a>啟用或停用虛擬機器
您可以停用 VM 的監視，以暫時停止所有監視器。 您可以停用 VM 的監視，例如，當您在 VM 上執行維護。

| 設定 | 描述 |
|:---|:---|
| 啟用  | 電腦的健全狀況狀態隨即顯示。 |
| Disabled | 電腦的健全狀況狀態會顯示為 [ **已停用**]。 未建立警示。 |

## <a name="health-state-logic"></a>健全狀況狀態邏輯
單位監視的健全狀況狀態邏輯會定義設定其健康情況狀態的準則。 您可以指定監視有多少健全狀況狀態，以及每個健全狀況狀態的計算閾值。

![範例健全準則](media/vminsights-health-configure/sample-health-criteria.png)

匯總監視不會指定任何健全狀況狀態邏輯。 它們的健全狀況狀態是由其底下的單位監視根據其健康情況匯總來設定。

單位監視器各有兩個或三個健全狀況狀態。 每個都一律會有狀況良好的狀態，以及選擇性的警告狀態、重大狀態或兩者。 警告和重大狀態各需要唯一的準則，以定義何時將監視設定為此狀態。 狀況良好的狀態沒有準則，因為當不符合其他狀態的準則時，就會設定此狀態。

在下列範例中，CPU 使用率會設定為下列健全狀況狀態：

- 如果大於90%，則為重大。
- 如果大於或等於80%，則會出現警告。
- 狀況良好（如果其低於80%）。 這是隱含的，因為這是不適用任何其他條件的條件。

## <a name="next-steps"></a>後續步驟

- [使用資料收集規則大規模設定監視。](vminsights-health-configure-dcr.md)