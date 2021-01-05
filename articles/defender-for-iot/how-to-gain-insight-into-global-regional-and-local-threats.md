---
title: 深入瞭解全球、區域和本機威脅
description: 使用內部部署管理主控台中的網站地圖，深入瞭解全域、區域和本機威脅。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cde55f3c2a875c593c07ea05427f8075d8599196
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839371"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>深入瞭解全球、區域和本機威脅

內部部署管理主控台中的網站地圖可協助您藉由將網路分割成可反映商務拓朴的地理和邏輯區段，來達成完整的安全性涵蓋範圍：

- **地理設備層級**：網站會根據地圖上呈現的地理位置來反映多個裝置群組。 根據預設，適用于 IoT 的 Azure Defender 會為您提供世界地圖。 您可以更新地圖以反映您的組織或商務結構。 例如，使用地圖來反映特定國家/地區、城市或產業校園內的網站。 當網站色彩在地圖上變更時，它會為 SOC 小組提供設備中重要系統狀態的指示。

  地圖是互動式的，可讓您開啟每個網站並探究到此網站的資訊。

- **全域邏輯層**：業務單位是根據特定產業將您的企業分成邏輯區段的方式。 當您這樣做時，您的商務拓朴會反映在地圖上。

  例如，包含玻璃工廠、塑膠工廠和汽車工廠的全球公司可以管理為三個不同的業務單位。 位於多倫多的實體網站包含三種不同的半透明生產線、塑膠生產線，以及貨車引擎生產線。 因此，此網站有三個業務單位的代表。

- **地理區域層級**：建立區域以將全球企業分成地理區域。 例如，我們所述的公司可能會使用區域北美洲、西歐和歐洲東部。 北美洲具有來自所有三個業務單位的工廠。 西歐有汽車工廠和玻璃工廠，而東歐版只有塑膠工廠。

- **本機邏輯區段層級**：區域是網站內的邏輯區段，可定義功能區域或生產線。 使用區域可強制執行與區域定義相關的安全性原則。 例如，包含五個生產線的網站可以分為五個區域。

- **本機視圖層級**：單一感應器安裝的本機視圖可讓您深入瞭解連線裝置的操作和安全性狀態。

## <a name="work-with-site-map-views"></a>使用網站地圖視圖

內部部署管理主控台可在內容相關的地圖中，提供產業網路的整體觀點。 [一般地圖視圖] 會顯示您組織的全域地圖，以及每個網站的地理位置。

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="企業地圖視圖的螢幕擷取畫面。":::

### <a name="color-coded-map-views"></a>色彩編碼的地圖視圖

**綠色**：安全性事件的數目低於適用于 IoT 的 Defender 為您的系統定義的閾值。 不需要採取任何動作。

**黃色**：安全性事件數目等於適用于 IoT 的 Defender 為您的系統定義的閾值。 請考慮調查事件。  

**紅色**：安全性事件數目超過 Defender for IoT 已為您的系統定義的閾值。 立即採取行動。

### <a name="risk-level-map-views"></a>風險層級的地圖視圖

**風險評估**：風險評估視圖顯示網站風險的資訊。 風險資訊可協助您設定緩和的優先順序，並建立藍圖來規劃安全性改進。

**事件回應**：取得跨企業的每個網站上所有未認可警示的集中式查看。 您可以向下切入並管理在特定網站中偵測到的警示。

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="具有事件回應的企業地圖視圖螢幕擷取畫面。":::

**惡意活動**：如果偵測到惡意程式碼，網站會以紅色顯示。 這表示您應該立即採取行動。

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="具有惡意活動的企業地圖視圖螢幕擷取畫面。":::

**操作警示**：適用于 ot 系統的這個地圖視圖可讓您更瞭解哪些系統可能會遇到作業事件，例如 PLC 停止、固件上傳和程式上傳。

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="具有操作警示的企業地圖視圖螢幕擷取畫面。":::

若要選擇地圖視圖：

1. 從地圖中選取 [ **預設視圖** ]。
2. 選取檢視。

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="網站地圖預設視圖的螢幕擷取畫面。":::

## <a name="update-the-site-map-image"></a>更新網站地圖影像

適用于 IoT 的 Defender 提供預設的世界地圖。 例如，您可以將它變更為反映您的組織：國家/地區地圖或城市地圖。 

若要取代對應：

1. 在左窗格中，選取 [ **系統設定**]。

2. 選取 [ **變更網站地圖** ] 並上傳圖形檔案，以取代預設對應。

## <a name="next-step"></a>後續步驟

[檢視警示](how-to-view-alerts.md)
