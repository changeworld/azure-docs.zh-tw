---
title: 網路需求
description: 最佳體驗的網路需求和最佳網路作法
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fe684d15e2ce9b8d302db4eb6bd31dd1416abf21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83196537"
---
# <a name="network-requirements"></a>網路需求

與 Azure 資料中心的穩定、低延遲網路連線，對於 Azure 遠端轉譯中的良好使用者體驗而言很重要。 網路狀況不佳可能會導致中斷連接、不穩定、抖動或「跳躍」的全像是補救伺服器端場景圖形時所造成的延遲。

## <a name="guidelines-for-network-connectivity"></a>網路連接的指導方針

確切的網路需求取決於您的特定使用案例，例如，對遠端場景圖形的修改次數和頻率，以及轉譯視圖的複雜度，但有一些指導方針可確保您的體驗能正常運作：

* 假設網路上沒有競爭流量，則您的網際網路連線必須針對 Azure 遠端轉譯的單一使用者會話，一致地支援至少 **40 Mbps 下游** 和 **5 Mbps 上游** 。 建議較高的速率，以獲得更好的體驗。 在相同網路上有更多使用者時，這些需求也會相應增加。
* 使用 **5 ghz Wi-Fi 頻外** ，通常會產生比 2.4-ghz Wi-Fi 頻外更好的結果，但兩者都應該可以運作。
* 如果附近有其他 Wi-Fi 網路，請避免使用這些其他網路使用 Wi-Fi 通道。 您可以使用網路掃描工具（例如 [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) ）來確認 Wi-Fi 網路使用的通道是否沒有競爭的流量。
* 嚴格 **避免使用 Wi-Fi 中繼器** 或 powerline 間的轉送。
* 避免在相同的 Wi-Fi 網路上**競爭頻寬密集的流量**（例如影片或遊戲串流）。
* 有 **良好的 Wi-Fi 信號強度** 是不可或缺的。 可能的話，請盡可能靠近您的 Wi-Fi 存取點，並避免用戶端裝置與存取點之間的障礙。
* 請確定您永遠連接到您[區域](regions.md)**最接近的 Azure 資料中心**。 資料中心越接近，網路延遲越低，就會對全像全像

> [!NOTE]
> 下游頻寬大多是由影片串流取用，而這會在色彩和深度資訊之間分割， (60 Hz、身歷聲) 。

## <a name="network-performance-tests"></a>網路效能測試

如果您想要初步瞭解您的網路連線品質是否足以執行 Azure 遠端轉譯，則有現有的線上工具可供您使用。 我們強烈建議您從與您打算執行 Azure 遠端轉譯用戶端應用程式的裝置相同的 Wi-Fi，執行這些線上工具。 從在行動電話或 HoloLens2 上執行測試所得到的結果通常較不實用，因為它們已證明在低電源的端點裝置上顯示明顯變化。 放置膝上型電腦的位置，應該大約在您預期使用執行 Azure 遠端轉譯用戶端應用程式之裝置的相同位置。

以下是一些簡單的步驟，讓您快速測試網路連線能力：

1. **執行網路測試控管（例如 www.speedtest.net），以取得網路連線整體延遲和上游/下游頻寬的資料。**
挑選最接近您的伺服器，然後執行測試。 雖然伺服器不會是 Azure 遠端轉譯將連接的 Azure 資料中心，但產生的資料仍有助於瞭解網際網路連線和 Wi-fi 的效能。
   * Azure 遠端轉譯的**最低需求**：大約是 40 Mbps 下游和 5 Mbps 上游。
   * **建議** Azure 遠端轉譯：大約是 100 Mbps 下游和 10 Mbps 上游。
建議您多次執行測試，並取得最差的結果。
1. **使用 www.azurespeed.com 這類工具來測量 Azure 資料中心的延遲**。 選取最接近您的 Azure 遠端轉譯所支援的 Azure 資料中心 (查看 [支援的區域](regions.md)) 並執行 **延遲測試**。 如果您看到的數位有變化，請讓結果有一些時間穩定。
   * Azure 遠端轉譯的**最低需求**：延遲應持續小於100毫秒。
   * **建議** Azure 遠端轉譯：延遲應持續小於70毫秒。

雖然低延遲不保證 Azure 遠端轉譯可在您的網路上正常運作，但我們通常會在成功通過這些測試的情況下，才會有很好的表現。
如果您在執行 Azure 遠端轉譯時遇到不穩定、抖動或跳躍的構件，請參閱 [疑難排解指南](../resources/troubleshoot.md)。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Unity 轉譯模型](../quickstarts/render-model.md)
