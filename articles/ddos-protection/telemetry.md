---
title: 查看並設定 Azure DDoS 保護 Standard 的 DDoS 保護遙測
description: 瞭解如何針對 Azure DDoS 保護 Standard 來查看和設定 DDoS 保護遙測。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 86e4a111be7585373ecf4c2fe9508f2bfca7de16
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915074"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>檢視和設定 DDoS 保護遙測

Azure DDoS 保護標準透過 DDoS 攻擊分析，提供詳細的攻擊見解和視覺效果。 保護其虛擬網路避免遭受 DDoS 攻擊的客戶可以詳細了解攻擊流量，並透過攻擊風險降低報告和風險降低流程記錄來緩解攻擊。 豐富的遙測會透過 Azure 監視器公開，包括在 DDoS 攻擊期間的詳細計量。 警示可以針對 DDoS 保護所公開的任何 Azure 監視器計量進行設定。 您可以透過 Azure 儲存體診斷介面，進一步整合 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中樞) 、OMS Log Analytics 和 Azure 監視器的記錄，以進行 advanced analysis。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 查看 DDoS 保護遙測
> * 檢視 DDoS 風險降低原則
> * 驗證並測試 DDoS 保護遙測

### <a name="metrics"></a>計量

> [!NOTE]
> 雖然 Azure 入口網站上會顯示多個 **匯總** 選項，但是每個度量只支援下表列出的匯總類型。 這種混淆很抱歉，我們正努力解決此問題。

以下是適用于 Azure DDoS 保護標準的 [計量](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetworkpublicipaddresses) 。 這些計量也可透過診斷設定匯出 (請參閱 [查看和設定 DDoS 診斷記錄](diagnostic-logging.md)) 。


| 計量 | 計量顯示名稱 | 單位 | 彙總類型 | 描述 |
| --- | --- | --- | --- | --- |
| ByteCount | 位元組計數 | Count | 總計 | 在期間內傳輸的位元組總數 |
| BytesDroppedDDoS | 傳入位元組數捨棄 DDoS | 每秒位元組 | 最大值 | 傳入位元組數捨棄 DDoS| 
| BytesForwardedDDoS | 傳入位元組數轉送 DDoS | 每秒位元組 | 最大值 | 傳入位元組數轉送 DDoS |
| BytesInDDoS | 傳入位元組數 DDoS | 每秒位元組 | 最大值 | 傳入位元組數 DDoS |
| DDoSTriggerSYNPackets | 用以觸發 DDoS 風險降低措施的輸入 SYN 封包數 | 每秒計數 | 最大值 | 用以觸發 DDoS 風險降低措施的輸入 SYN 封包數 |
| DDoSTriggerTCPPackets | 傳入 TCP 封包數以觸發 DDoS 緩和 | 每秒計數 | 最大值 | 傳入 TCP 封包數以觸發 DDoS 緩和 |
| DDoSTriggerUDPPackets | 傳入 UDP 封包數以觸發 DDoS 緩和 | 每秒計數 | 最大值 | 傳入 UDP 封包數以觸發 DDoS 緩和 |
| IfUnderDDoSAttack | 是否正遭受 DDoS 攻擊 | Count | 最大值 | 是否正遭受 DDoS 攻擊 |
| PacketCount | 封包計數 | Count | 總計 | 在期間內傳輸的封包總數 |
| PacketsDroppedDDoS | 傳入封包捨棄 DDoS | 每秒計數 | 最大值 | 傳入封包捨棄 DDoS |
| PacketsForwardedDDoS | 傳入封包轉寄 DDoS | 每秒計數 | 最大值 | 傳入封包轉寄 DDoS |
| PacketsInDDoS | 傳入封包 DDoS | 每秒計數 | 最大值 | 傳入封包 DDoS |
| SynCount | SYN 計數 | Count | 總計 | 在期間內傳輸的 SYN 封包總數 |
| TCPBytesDroppedDDoS | 傳入 TCP 位元組數捨棄 DDoS | 每秒位元組 | 最大值 | 傳入 TCP 位元組數捨棄 DDoS |
| TCPBytesForwardedDDoS | 傳入 TCP 位元組數轉送 DDoS | 每秒位元組 | 最大值 | 傳入 TCP 位元組數轉送 DDoS |
| TCPBytesInDDoS | 傳入 TCP 位元組數 DDoS | 每秒位元組 | 最大值 | 傳入 TCP 位元組數 DDoS |
| TCPPacketsDroppedDDoS | 傳入 TCP 封包捨棄 DDoS | 每秒計數 | 最大值 | 傳入 TCP 封包捨棄 DDoS |
| TCPPacketsForwardedDDoS | 傳入 TCP 封包轉送 DDoS | 每秒計數 | 最大值 | 傳入 TCP 封包轉送 DDoS |
| TCPPacketsInDDoS | 傳入 TCP 封包 DDoS | 每秒計數 | 最大值 | 傳入 TCP 封包 DDoS |
| UDPBytesDroppedDDoS | 傳入 UDP 位元組數捨棄 DDoS | 每秒位元組 | 最大值 | 傳入 UDP 位元組數捨棄 DDoS |
| UDPBytesForwardedDDoS | 傳入 UDP 位元組數轉送 DDoS | 每秒位元組 | 最大值 | 傳入 UDP 位元組數轉送 DDoS |
| UDPBytesInDDoS | 傳入 UDP 位元組數 DDoS | 每秒位元組 | 最大值 | 傳入 UDP 位元組數 DDoS |
| UDPPacketsDroppedDDoS | 傳入 UDP 封包捨棄 DDoS | 每秒計數 | 最大值 | 傳入 UDP 封包捨棄 DDoS |
| UDPPacketsForwardedDDoS | 傳入 UDP 封包轉送 DDoS | 每秒計數 | 最大值 | 傳入 UDP 封包轉送 DDoS |
| UDPPacketsInDDoS | 傳入 UDP 封包 DDoS | 每秒計數 | 最大值 | 傳入 UDP 封包 DDoS |
| VipAvailability | 資料路徑可用性 | Count | Average | 每個持續時間 IP 位址可用性的平均值 |

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在您可以完成本教學課程中的步驟之前，您必須先建立 [Azure DDoS 標準保護方案](manage-ddos-protection.md) ，而且必須在虛擬網路上啟用 Ddos 保護標準。
- DDoS 會監視指派給虛擬網路內資源的公用 IP 位址。 如果您在虛擬網路中沒有任何具有公用 IP 位址的資源，就必須先建立一個具有公用 IP 位址的資源。 您可以透過 [azure 服務的虛擬網路](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) 中所列出的 Resource Manager (非傳統) ，監視所有部署資源的公用 IP 位址 (包括) 環境以外的後端虛擬機器位於虛擬網路 Azure App Service 的 Azure 負載平衡器。 若要繼續進行本教學課程，您可以快速建立一個 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器。  

## <a name="view-ddos-protection-telemetry"></a>查看 DDoS 保護遙測

攻擊的遙測可透過 Azure 監視器即時提供。 遙測只適用於公用 IP 位址在安全防護之下的期間。 在攻擊風險降低之前或之後，您都不會看到遙測。

1. 登入 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至您的 DDoS 保護計劃。
2. 在 [監視] 下，選取 [計量]。
3. 選取 [範圍]。 選取包含您要記錄之公用 IP 位址的 **訂** 用帳戶，選取 [**資源類型**] 的 [**公用 ip 位址**]，**然後選取您** 要記錄計量的特定公用 ip 位址，然後選取 [套用]。
4. 選取 [**最大值**]**匯總** 類型。

計量名稱呈現不同的套件類型和位元組及封包，包含每個計量的標籤名稱基本結構，如下所示：

- **捨棄的標籤名稱** (例如 **捨棄的輸入封包 DDoS**)：DDoS 保護系統所捨棄/清除的封包數目。
- **轉送的標籤名稱** (例如 **轉送的輸入封包 DDoS**)：DDoS 系統轉送到目的地 VIP 的封包數目 – 未篩選的流量。
- **沒有標籤名稱** (例如 **輸入封包 DDoS**)：進入清除系統的封包總數 – 代表所捨棄和轉送的封包總和。

## <a name="view-ddos-mitigation-policies"></a>檢視 DDoS 風險降低原則

「標準 DDoS 保護」會在啟用 DDoS 的虛擬網路中，針對受保護資源的每個公用 IP 位址套用三個自動調整的風險降低措施 (TCP SYN、TCP 及 UDP)。 您可以藉由選取  **輸入 TCP 封包來觸發 ddos 緩和** 和 **輸入 UDP 封包，以觸發** 以 **匯總** 類型為「最大值」的 ddos 風險降低計量（如下圖所示）來查看原則閾值：

![檢視風險降低原則](./media/manage-ddos-protection/view-mitigation-policies.png)

原則閾值會透過以 Azure Machine Learning 為基礎的網路流量分析進行自動設定。 只有在超出原則閾值時，才會針對遭受攻擊的 IP 位址進行 DDoS 風險降低措施。

## <a name="validate-and-test"></a>驗證和測試

若要模擬 DDoS 攻擊來驗證 DDoS 保護遙測，請參閱 [驗證 ddos 偵測](test-through-simulations.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

- 設定 DDoS 保護計量的警示
- 查看 DDoS 保護遙測
- 檢視 DDoS 風險降低原則
- 驗證並測試 DDoS 保護遙測

若要瞭解如何設定攻擊風險降低報告和流程記錄，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [檢視和設定 DDoS 診斷記錄](diagnostic-logging.md)
