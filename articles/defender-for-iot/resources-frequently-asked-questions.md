---
title: Defender for IoT 的常見問題
description: 尋找適用于 IoT 的 Azure Defender 功能和服務最常見問題的解答。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 0c303adb4ee7c7551c6b389ec169d92a661a63bc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835018"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>適用于 IoT 的 Azure Defender 常見問題

本文提供關於 Defender for IoT 的常見問題和解答清單。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>何謂 Azure 的 IoT 安全性獨特價值主張？

適用于 IoT 的 Defender 可讓企業將其現有的網路安全性視圖擴充至整個 IoT 解決方案。 Azure 提供商務解決方案的端對端檢視，其可供根據企業安全性狀態和收集的資料，採取商務相關動作與決策。 使用 Azure IoT、Azure IoT Edge 和 Azure 資訊安全中心的合併安全性，其可供使用所需安全性來建立想要的解決方案。

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>我們的組織使用專屬的非標準產業通訊協定。 個人模式支援它們嗎？ 

適用于 IoT 的 Azure Defender 提供完整的通訊協定支援。 除了內嵌的通訊協定支援，您還可以保護 IoT 和執行專屬和自訂通訊協定的裝置，或與任何標準的通訊協定。 使用 (NODE.JS) SDK 的水準開放開發環境，開發人員可以建立 dissector 外掛程式，以根據定義的通訊協定來解碼網路流量。 服務會分析流量，以提供完整的監視、警示和報告。 使用範圍：
- 展開 [可見度] 和 [控制]，而不需要升級至新版本。
- 以外部外掛程式的形式開發，以保護專屬資訊。 
- 將警示、事件和通訊協定參數的文字當地語系化。

此獨特的解決方案可用於開發作為外掛程式的通訊協定，不需要專用的開發人員小組或版本發行，就能支援新的通訊協定。 開發人員、合作夥伴和客戶可以安全地開發通訊協定，並使用範圍共用見解和知識。 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>我必須向 Microsoft 合作夥伴購買硬體設備嗎？
適用于 IoT 的 Azure Defender 感應器會依照 [硬體規格指南](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)中所述，在特定硬體規格上執行，客戶可以向 Microsoft 合作夥伴購買經認證的硬體，或使用所提供的物料清單 (BOM) 並自行購買。 

經認證的硬體已在我們的實驗室中測試，以進行驅動程式穩定性、封包捨棄和網路調整大小。


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>法規不允許我們將我們的系統連接到網際網路。 我們仍然可以使用適用于 IoT 的 Defender 嗎？

沒錯，您可以！ 適用于 IoT 的 Azure Defender 平臺內部部署解決方案會部署為實體或虛擬感應器設備，以被動方式內嵌網路流量 (透過 SPAN、RSPAN 或點擊) 來分析、探索和持續監視 IT、OT 和 IoT 網路。 針對較大型的企業，多個感應器可將其資料匯總至內部部署管理主控台。

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>我應該將監視埠連線到網路的哪個位置？

適用于 IoT 的 Azure Defender 感應器會連線至 SPAN 埠或網路功能，並立即開始透過被動 (無代理程式) 監視收集 ICS 網路流量。 它對對等網路沒有任何影響，因為它不是放在資料路徑中，而且不會主動掃描 OT 裝置。

例如：
- 單一設備 (實體) 的虛擬，可位於店面 DMZ 層，讓所有的店面資料格流量都路由傳送到此層。
- 或者，您也可以在每個店面儲存格中，找出位於店面 DMZ 層的雲端或本機管理的小型迷你感應器。  (虛擬或實體) 的另一個應用裝置可以監視 SCADA、Historian 或 MES) 之車間 DMZ 層 (的流量。

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>適用于 IoT 的 Defender 與競賽有何不同？

雖然其他解決方案提供了一組功能，可讓客戶建立自己的解決方案，但 Defender for IoT 提供獨特的端對端 IoT 安全性解決方案，可讓您全面瞭解所有相關 Azure 資源的安全性。 Azure 可供快速部署並與 IoT 中樞模組對應項整合，以便與現有的裝置管理工具輕鬆整合。


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>必須是 Azure IoT 的客戶嗎？

是。 針對雲端連線部署，適用于 IoT 的 Azure Defender 會依賴 Azure IoT 連線能力和基礎結構。
## <a name="can-i-create-my-own-alerts"></a>是否可建立自己的警示？

是。 您可對一組預先決定的行為 (例如 IP 位址和開放連接埠) 設定自訂警示。 若要深入了解自訂警示及其建立方式，請參閱[建立自訂警示](quickstart-create-custom-alerts.md)。

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>哪裡可看到記錄？ 是否可自訂記錄？

- 使用已連線的 Log Analytics 工作區來檢視警示和建議。 設定工作區中的儲存體大小和持續期間。

- 安全性代理程式中的原始資料也可以儲存在 Log Analytics 帳戶中。 在變更此選項的設定之前，請考慮大小、持續期間、儲存體需求和相關成本。



## <a name="what-happens-when-the-internet-connection-stops-working"></a>當網際網路連線停止運作時，會發生什麼情況？

只要裝置正在執行，感應器和代理程式就會繼續執行和儲存資料。 資料會根據大小設定儲存在安全性訊息快取中。 當裝置重新取得連線後，安全性訊息便會繼續傳送。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何開始使用 Defender for IoT，請參閱下列文章：

- 閱讀適用于 IoT 的 Defender [總覽](overview.md)
- 確認 [系統必要條件](quickstart-system-prerequisites.md)
- 深入瞭解如何 [開始使用 Defender For IoT](getting-started.md)
- 瞭解 [適用于 IoT 的 Defender 安全性警示](concept-security-alerts.md)
