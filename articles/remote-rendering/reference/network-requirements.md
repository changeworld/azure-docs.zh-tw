---
title: 網路需求
description: 網路要求和最佳網路實踐,實現最佳體驗
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 73bbfad4c0535fa00b1aa53764eb52acb83124f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680371"
---
# <a name="network-requirements"></a>網路需求

與 Azure 資料中心的穩定、低延遲網路連接對於 Azure 遠端呈現中良好的用戶體驗至關重要。 網路條件不佳可能導致連接斷開、不穩定、抖動或"跳躍"全息圖,並在更新伺服器端場景圖時出現明顯的延遲。

## <a name="guidelines-for-network-connectivity"></a>網路連線指南

確切的網路要求取決於您的特定用例,例如對遠端場景圖的修改次數和頻率以及渲染視圖的複雜性,但有許多準則可確保您的體驗盡可能好:

* 假定網路上沒有競爭流量,您的互聯網連接需要始終如一地支援**下游 50 Mbps**和**10 Mbps 的上行**Azure 遠程呈現單個使用者會話。 我們建議提高費率以提供更好的體驗。 隨著同一網路上的用戶越來越多,這些要求相應地擴展。
* 使用**5-GHz Wi-Fi 頻段**通常比 2.4 GHz Wi-Fi 頻段產生更好的效果,儘管兩者都應正常工作。
* 如果附近還有其他 Wi-Fi 網路,請避免使用這些其他網路使用的 Wi-Fi 通道。 您可以使用[WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html)等網路掃描工具來驗證您的 Wi-Fi 網路使用的頻道是否沒有競爭流量。
* 嚴禁**使用 Wi-Fi 中繼器**或 LAN 過電源線轉發。
* 避免在同一 Wi-Fi 網路上**出現爭用頻寬密集流量**(如視頻或遊戲流)。
* **良好的 Wi-Fi 信號強度**至關重要。 如果可能,請靠近 Wi-Fi 接入點,避免用戶端設備和接入點之間的障礙物。
* 請確定永遠連接到[區域](regions.md)最近的 Azure**資料中心**。 數據中心越近,網路延遲越低,對全息圖穩定性的影響很大。

## <a name="network-performance-tests"></a>網路效能測試

如果要初步瞭解網路連接的品質是否足以運行 Azure 遠端呈現,則可以使用現有的連線工具。 我們強烈建議使用與計劃運行 Azure 遠端呈現客戶端應用程式的設備相同的強大便攜式電腦運行這些連線工具。 在行動電話或 HoloLens2 上運行測試的結果通常不太有用,因為它們已證明在低功率端點設備上表現出顯著的變化。 放置便攜式計算機的位置應大致位於使用運行 Azure 遠端呈現用戶端應用程式的設備時所期望的位置。

以下是快速測試網路連線的幾個簡單步驟:

1. **運行網路測試工具,如www.speedtest.net,獲取網路連接的總體延遲和上下游頻寬數據。**
選擇離您最近的伺服器並運行測試。 雖然伺服器不是 Azure 遠端呈現將連接到的 Azure 資料中心,但生成的數據對於瞭解 Internet 連接和 Wi-Fi 的性能仍然很有用。
   * Azure 遠端呈現**的最低要求**:下游約 40 Mbps 和 5 Mbps 上行。
   * **建議**用於 Azure 遠端呈現:下游約 100 Mbps 和上行 10 Mbps。
我們建議多次運行測試並獲取最差結果。
1. **使用www.azurespeed.com等工具測量 Azure 資料中心的延遲**。 選擇 Azure 遠端呈現支援的 Azure 資料中心(請參閱[支援的區域](regions.md)),然後執行**延遲測試**。 如果看到的數位有變化,給結果一些時間來穩定。
   * Azure 遠端呈現**的最低要求**:延遲應始終小於 100 毫秒。
   * **建議**用於 Azure 遠端呈現:延遲應始終小於 70 毫秒。

雖然低延遲不能保證 Azure 遠端呈現在您的網路上運行良好,但我們通常看到,在這些測試成功通過的情況下,它運行良好。
如果在執行 Azure 遠端渲染時遇到不穩定、抖動或跳躍全息圖等偽影,請參閱[故障排除指南](../resources/troubleshoot.md)。

## <a name="next-steps"></a>後續步驟

* [快速入門:使用 Unity 渲染模型](../quickstarts/render-model.md)
