---
title: 開啟 Azure IoT 中樞的預覽模式
description: 瞭解如何開啟 IoT 中樞的預覽模式、您想要的原因，以及一些警告
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621700"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>開啟 IoT 中樞的預覽模式以嘗試選取新功能

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

IoT 中樞的新功能處於公開預覽狀態，包括：

- MQTT 5
- ECC 伺服器憑證
- TLS 片段長度的協商
- 適用于 HTTPS/WebSocket 的 X509 CA 鏈支援

這些功能是 IoT 中樞通訊協定和驗證層的增強功能，因此目前僅適用于 **新** 的 IoT 中樞。 現有的 IoT 中樞 *尚未提供這些* 功能。 若要預覽這些功能，必須在開啟預覽模式的情況下建立 IoT 中樞。

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>開啟新 IoT 中樞的預覽模式

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從 Azure 首頁中選擇 [+建立資源] 按鈕，然後在 [搜尋 Marketplace] 欄位中輸入「IoT 中樞」。

1. 從搜尋結果中選取 [IoT 中樞]，然後選取 [建立]。

1. 在 [**基本**] 索引標籤上，如同 **區域** 以外的 [一般方式](iot-hub-create-through-portal.md)完成欄位。 選取下列其中一個區域：
    
    - 美國中部
    - 西歐
    - 東南亞

1. 選取 [ **管理** ] 索引標籤，然後展開 [ **Advanced settings** ] 區段。

1. 在 [ **預覽] 模式** 旁邊，選取 [ **開啟**]。 仔細檢查警告文字。

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="顯示建立新 IoT 中樞時，要在哪裡選取 [預覽] 模式選項的影像":::

1. 選取 **[下一步]：檢查 + 建立**，然後 **建立**。

一旦建立，處於預覽模式的 IoT 中樞一律會顯示此橫幅，讓您知道使用此 IoT 中樞僅供預覽之用： 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="顯示預覽模式 IoT 中樞橫幅的影像":::

## <a name="using-an-iot-hub-in-preview-mode"></a>在預覽模式中使用 IoT 中樞

請勿在生產環境 *中使用處于* 預覽模式的 IoT 中樞。 預覽模式 *的目的是* 要預覽此頁面上方所列的選取功能。 IoT 中樞預覽模式的其他限制為

- 某些現有的 IoT 中樞功能，例如 IP 篩選、私人連結、受控識別、裝置串流和容錯移轉，可能會意外運作或根本無法運作。
- 處於預覽模式的 IoT 中樞無法變更或升級為一般 IoT 中樞。
- 我們無法保證標準的 [IoT 中樞 SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) -請勿用於生產環境。

> [!TIP]
> [裝置串流](iot-hub-device-streams-overview.md)和[分散式追蹤](iot-hub-distributed-tracing.md)不需要預覽模式。 若要使用這些舊版的預覽功能，請依照一般方式遵循其檔。 

## <a name="next-steps"></a>後續步驟

- 若要預覽 MQTT 5 支援，請參閱 [IoT 中樞 MQTT 5 支援總覽 (預覽) ](iot-hub-mqtt-5.md)
- 若要預覽 ECC 伺服器憑證，請參閱 [ (ecc) SERVER TLS 憑證 (preview 的橢圓曲線密碼編譯) ](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- 若要預覽 TLS 片段大小的協商，請參閱 [tls 最大片段長度的協商 (預覽) ](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)