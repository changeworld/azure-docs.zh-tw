---
title: 管理警示事件
description: 管理在網路中偵測到的警示事件。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ad09bcb1ea4aa32bdd04af47d3503f74c850a10b
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98803510"
---
# <a name="manage-alert-events"></a>管理警示事件

下列選項可用於管理警示事件：

 | 動作 | 描述 |
 |--|--|
 | **學習** | 授權偵測到的事件。 如需詳細資訊，請參閱 [關於學習和 unlearning 事件](#about-learning-and-unlearning-events)。 |
 | **承認** | 針對偵測到的事件隱藏一次警示。 如果再次偵測到事件，則會再次觸發警示。 如需詳細資訊，請參閱 [關於確認和 unacknowledging 事件](#about-acknowledging-and-unacknowledging-events)。 |
 | **Mute** | 持續略過具有相同裝置和可比較流量的活動。 如需詳細資訊，請參閱 [關於靜音和靜音事件](#about-muting-and-unmuting-events)。 |

## <a name="about-learning-and-unlearning-events"></a>關於學習和 unlearning 事件

指出已學習網路偏差的事件可能會反映出有效的網路變更。 範例可能包括已加入網路或授權之固件更新的新授權裝置。

當您選取 [ **學習**] 時，感應器會將流量、設定或活動視為有效。 這表示將不會再觸發事件的警示。 這也表示當感應器產生風險評估、攻擊向量和其他報表時，不會計算事件。

例如，您會收到警示，指出網路掃描器先前未定義的裝置上的位址掃描活動。 如果將此裝置新增至網路以進行掃描，您可以指示感應器將裝置學習為掃描裝置。

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="偵測到的位址掃描視窗。":::

學習的事件可以是 unlearned。 當感應器 unlearns 事件時，它會重新觸發與此事件相關的警示。

## <a name="about-acknowledging-and-unacknowledging-events"></a>關於認可和 unacknowledging 事件

在某些情況下，您可能不想讓感應器學習偵測到的事件，或可能無法使用此選項。 相反地，事件可能需要緩和措施。 例如：

- **減緩網路設定或裝置**：您會收到警示，指出網路上偵測到新的裝置。 調查時，您發現裝置是未授權的網路裝置。 您可以透過中斷裝置與網路的連線來處理事件。
- **更新感應器** 設定：您會收到警示，指出伺服器起始的遠端連線數目過多。 觸發此警示的原因是定義了感應器異常閾值，以在一分鐘內觸發特定會話數目的警示。 您可以藉由更新閾值來處理事件。

完成緩和或調查之後，您可以選取 [ **認可**]，指示感應器隱藏警示。 如果再次偵測到事件，則會 retriggered 警示。

若要隱藏警示：

  - 選取 [ **認可**]。

若要再次查看警示：

  - 存取警示，然後選取 [ **Unacknowledge**]。

如果需要進一步調查，請 Unacknowledge 警示。

## <a name="about-muting-and-unmuting-events"></a>關於靜音和靜音事件

在某些情況下，您可能會想要指示感應器忽略您網路上的特定案例。 例如：

  - **異常** 引擎會在兩個裝置之間的頻寬尖峰產生警示，但尖峰對這些裝置而言是有效的。

  - **通訊協定違規** 引擎會針對在兩個裝置之間偵測到的通訊協定偏差觸發警示，但偏差在裝置之間是有效的。

在這些情況下，無法使用學習。 當您無法執行學習，而且您想要在計算風險和攻擊媒介時隱藏警示並移除裝置時，您可以改為將警示事件靜音。

> [!NOTE] 
> 您無法將網際網路裝置定義為來源或目的地的事件靜音。

### <a name="what-traffic-is-muted"></a>哪些流量會靜音？

靜音案例包括偵測到事件的網路裝置和流量。 警示標題描述正在靜音的流量。

已靜音的裝置將會顯示為警示中的影像。 如果顯示兩個裝置，則會將它們之間的流量靜音。

**範例 1**

當事件已靜音時，當主要 (來源) 傳送次要 (目的地) 不合法的函式程式碼時，就會忽略該事件（如廠商所定義）。

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="已收到次要裝置。":::

**範例 2**

當事件靜音時，無論目的地為何，只要來源傳送的 HTTP 標頭具有不合法的內容，系統就會忽略此事件。

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="不合法 HTTP 標頭內容的螢幕擷取畫面。":::

**當事件靜音之後：**

- 警示將可在 **認可** 的警示查看中存取，直到 unmuted 為止。

- 靜音動作將會出現在 **事件時間軸** 中。

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="偵測到事件並將其靜音。":::

- 感應器會在產生風險評估、攻擊向量和其他報表時重新計算裝置。 例如，如果您將偵測到裝置上有惡意流量的警示靜音，該裝置將不會在風險評估報告中計算。

**若要對警示進行靜音和取消靜音：**

- 選取警示上的 **靜音** 圖示。

**若要查看靜音的警示：**

1. 在 [**警示**] 主畫面中，選取 [已 **認可**] 選項。

2. 將滑鼠停留在警示上，以查看是否已將它靜音。  

## <a name="see-also"></a>另請參閱

[控制監視到的流量](how-to-control-what-traffic-is-monitored.md)
