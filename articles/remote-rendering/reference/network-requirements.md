---
title: 網路需求
description: 最佳體驗的網路需求和最佳網路做法
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fe684d15e2ce9b8d302db4eb6bd31dd1416abf21
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83196537"
---
# <a name="network-requirements"></a>網路需求

Azure 資料中心的穩定、低延遲網路連線，對於 Azure 遠端轉譯中的良好使用者體驗很重要。 不佳的網路狀況可能會導致中斷連接、不穩定、抖動或「跳躍」的全息影像，以及在補救伺服器端場景圖形時明顯的延遲。

## <a name="guidelines-for-network-connectivity"></a>網路連線的指導方針

確切的網路需求取決於您的特定使用案例，例如，對遠端場景圖形的修改次數和頻率，以及轉譯視圖的複雜度，但有一些指導方針可確保您的體驗盡可能有效：

* 您的網際網路連線能力必須針對 Azure 遠端轉譯的單一使用者會話，一致支援至少**40 Mbps 下游**和**5 Mbps 上游**，假設網路上沒有競爭的流量。 我們建議較高的成功率以獲得更好的體驗。 透過相同網路上的更多使用者，這些需求會相應增加。
* 使用**5 ghz 的 wi-fi 寬頻**，通常會產生比 2.4 ghz wi-fi 波段更好的結果，但兩者都應該可行。
* 如果附近有其他的 Wi-fi 網路，請避免使用這些其他網路所使用的 Wi-fi 通道。 您可以使用像是[WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html)的網路掃描工具來驗證您的 wi-fi 網路所使用的通道是否沒有競爭的流量。
* 嚴格**避免使用 wi-fi 中繼器**或 powerline 轉送。
* 避免在相同的 Wi-fi 網路上**爭用頻寬密集流量**（例如影片或遊戲串流）。
* 具備**良好的 wi-fi 信號強度**是不可或缺的。 可能的話，請靠近您的 Wi-fi 存取點，避免用戶端裝置與存取點之間的障礙。
* 請確定您一律為您的[區域](regions.md)連線到**最接近的 Azure 資料中心**。 資料中心愈接近，網路延遲越低，對全像全息的穩定性有很大的影響。

> [!NOTE]
> 下游頻寬主要是由影片串流取用，而此資料流程會依色彩和深度資訊（60 Hz、身歷聲）進行分割。

## <a name="network-performance-tests"></a>網路效能測試

如果您想要初步瞭解網路連線的品質是否足以執行 Azure 遠端轉譯，您可以使用現有的線上工具。 我們強烈建議您從連線到與您打算在其上執行 Azure 遠端轉譯用戶端應用程式的裝置相同的 Wi-fi 的強大膝上型電腦，執行這些線上工具。 從在行動電話或 HoloLens2 上執行測試所得到的結果通常較不實用，因為它們已經過證明，可以在低電源的端點裝置上顯示顯著的變化。 您放置膝上型電腦的位置應該大致與預期使用執行 Azure 遠端轉譯用戶端應用程式之裝置的相同位置。

以下是快速測試網路連線的幾個簡單步驟：

1. **執行網路測試控管（例如 www.speedtest.net），以取得網路連線的整體延遲和上游/下游頻寬的相關資料。**
選擇最靠近您的伺服器並執行測試。 雖然伺服器不會是 Azure 遠端轉譯將連接的 Azure 資料中心，但產生的資料仍然有助於瞭解網際網路連線和 Wi-fi 的效能。
   * Azure 遠端呈現的**最低需求**：大約 40 Mbps 下游和 5 Mbps 上游。
   * **建議**用於 Azure 遠端呈現：大約 100 Mbps 下游和 10 Mbps 上游。
我們建議您多次執行測試，並取得最差的結果。
1. **使用 www.azurespeed.com 這類工具來測量 Azure 資料中心的延遲**。 選取最接近您的 Azure 遠端轉譯所支援的 Azure 資料中心（請參閱[支援的區域](regions.md)）並執行**延遲測試**。 如果您看到的數位有變化，請提供結果一些時間來穩定。
   * Azure 遠端轉譯的**最低需求**：延遲應持續小於100毫秒。
   * **建議**用於 Azure 遠端轉譯：延遲應持續小於70毫秒。

雖然低延遲並不保證 Azure 遠端轉譯在您的網路上能正常運作，但我們通常會在成功通過這些測試的情況下看得到良好的效能。
如果您在執行 Azure 遠端轉譯時遇到不穩定、抖動或跳躍全息顯示的成品，請參閱[疑難排解指南](../resources/troubleshoot.md)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Unity 轉譯模型](../quickstarts/render-model.md)
