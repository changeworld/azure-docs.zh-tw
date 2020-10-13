---
title: Defender for IoT 的常見問題
description: 尋找適用于 IoT 的 Azure Defender 功能和服務最常見問題的解答。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934563"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>適用于 IoT 的 Azure Defender 常見問題

本文提供關於 Defender for IoT 的常見問題和解答清單。

## <a name="does-azure-provide-support-for-iot-security"></a>Azure 是否提供對 IoT 安全性的支援？

Azure 可透過 Azure 資訊安全中心，提供用來監視及管理 IoT 安全性的整合式檢視，作為整體安全性解決方案的一部分。 如果您是應用程式開發人員，則可使用 IoT 中樞檢視來管理 IoT 應用程式安全性。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>何謂 Azure 的 IoT 安全性獨特價值主張？

適用于 IoT 的 Defender 可讓企業將其現有的網路安全性視圖擴充至整個 IoT 解決方案。 Azure 提供商務解決方案的端對端檢視，其可供根據企業安全性狀態和收集的資料，採取商務相關動作與決策。 使用 Azure IoT、Azure IoT Edge 和 Azure 資訊安全中心的合併安全性，其可供使用所需安全性來建立想要的解決方案。

## <a name="who-is-defender-for-iot-made-for"></a>什麼是適用于 IoT 的 Defender？

Defender for IoT 已整合 Azure IoT 中樞的安全性，並提供日常商務解決方案安全性作業的管理。 適用于 IoT 的 Defender 也整合至 Azure 資訊安全中心的功能，並提供整合式的監視和管理您的 IoT 安全性解決方案，作為整體安全性解決方案的一部分。

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>適用于 IoT 的 Defender 與競賽有何不同？

雖然其他解決方案提供了一組功能，可讓客戶建立自己的解決方案，但 Defender for IoT 提供獨特的端對端 IoT 安全性解決方案，可讓您全面瞭解所有相關 Azure 資源的安全性。 Azure 可供快速部署並與 IoT 中樞模組對應項整合，以便與現有的裝置管理工具輕鬆整合。

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>必須是 Azure 資訊安全中心的客戶才能使用此服務嗎？

不必，但這是建議。 若沒有 Azure 資訊安全中心，適用于 IoT 的 Defender 會接收有限的連線資源資料，並對您的潛在攻擊面、威脅和潛在攻擊提供有限的分析。

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>必須是 Azure IoT 的客戶嗎？

是。 適用于 IoT 的 Defender 依賴 Azure IoT 連線能力和基礎結構。

## <a name="do-i-have-to-install-an-agent"></a>必須安裝代理程式嗎？

您的 IoT 裝置上的代理程式安裝並不是啟用 Defender for IoT 的必要項。 您可從下列三個選項進行選擇，根據選擇取得不同層級的安全性監視及管理功能：

1. 安裝適用于 IoT 安全性代理程式的 Defender，不論是否有修改。 此選項可為裝置行為和存取提供最高層級的增強式安全性見解。

1. 建立您自己的代理程式，並執行 Defender for IoT 安全性訊息架構。 此選項可讓您在裝置安全性代理程式上使用 Defender for IoT 分析工具。

1. IoT 裝置上不會安裝任何安全性代理程式。 此選項可供啟用 IoT 中樞通訊監視，並縮減安全性監視及管理功能。

## <a name="what-does-the-defender-for-iot-agent-do"></a>Defender for IoT 代理程式有什麼功能？

適用于 IoT 的 Defender 代理程式會藉由掃描設定) 、處理 & 連線，為裝置設定、行為和存取 (提供裝置層級威脅涵蓋範圍。 適用于 IoT 的 Defender 安全性代理程式不會掃描商務相關資料或活動。

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>哪裡可以取得 IoT 安全性代理程式的 Defender？

適用于 IoT 的 Defender 安全性代理程式是開放原始碼，並可在32位和64位 Windows 和 Linux 版本的 GitHub 上取得： https://github.com/Azure/Azure-IoT-Security 。

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>如何安裝適用于 IoT 代理程式的 Defender？

您可在 GitHub 中找到詳細的安裝和代理程式部署資訊： https://github.com/Azure/Azure-IoT-Security 。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理程式的相依性和先決條件為何？

適用于 IoT 的 Defender 支援各種不同的平臺。 請參閱[支援的裝置平台](how-to-deploy-agent.md)以確認支援特定裝置。

## <a name="which-data-is-collected-by-the-agent"></a>代理程式會收集哪些資料？

代理程式會收集連線、存取、防火牆設定、處理序清單與 OS 基準。

## <a name="how-much-data-will-the-agent-generate"></a>代理程式會產生多少資料？

代理程式資料的產生是根據裝置、應用程式、連線類型和客戶代理程式設定而定。 由於裝置與 IoT 解決方案之間的高度變化性，因此建議先在實驗或測試設定中部署代理程式，以觀察、學習和設定符合需求的特定設定，同時測量所產生的資料量。 啟動服務之後，適用于 IoT 的 Defender 代理程式會提供將代理程式輸送量優化的操作建議，以協助您進行設定和自訂程式。

## <a name="how-can-i-control-my-billing"></a>如何控制帳單？

適用于 IoT 的 Defender 提供可設定的代理程式掃描、資料緩衝區，以及建立自訂警示的能力，以增加或減少代理程式所產生的資料量。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理程式訊息是否會耗盡 IoT 中樞的配額？

是。 代理程式傳輸的資料會計入 IoT 中樞配額。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下來呢？ 我已安裝代理程式，但看不到任何活動或記錄...

1. 檢查[代理程式類型是否符合裝置所指定的 OS 平台](how-to-deploy-agent.md)

1. 確認[代理程式正在裝置上執行](how-to-agent-configuration.md)。

1. 檢查是否已在 IoT 中樞針對**安全性**[成功啟用服務](quickstart-onboard-iot-hub.md)。

1. 檢查是否已 [在 Iot 中樞中使用 Defender For IoT 模組設定](quickstart-create-security-twin.md)裝置。

如果活動或記錄仍無法使用，請洽詢您的 Defender for IoT 合作夥伴以取得其他協助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>當網際網路連線停止運作時，會發生什麼情況？

只要裝置正在執行，代理程式就會繼續執行並儲存資料。 資料會根據大小設定儲存在安全性訊息快取中。 當裝置重新取得連線後，安全性訊息便會繼續傳送。

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>如果裝置重新開機，則安全性代理程式會自行復原嗎？

安全性代理程式的設計是要在每次裝置重新開機時自動重新執行。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理程式可能會影響裝置或其他已安裝軟體的效能嗎？

代理程式會像任何其他應用程式/處理序一樣取用機器資源，且不應中斷正常的裝置活動。 代理程式執行所在裝置上的資源耗用量與其安裝和設定有關。 建議在嘗試於生產環境中部署之前，先在內含的環境中測試代理程式設定，以及與其他 IoT 應用程式和功能的互通性。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我正在對裝置進行一些維護。 可以關閉代理程式嗎？

無法關閉代理程式。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>有方法可測試代理程式是否正常運作嗎？

如果代理程式停止通訊或無法傳送安全性訊息，則會產生**裝置無訊息**警示。

## <a name="can-i-create-my-own-alerts"></a>是否可建立自己的警示？

是。 您可對一組預先決定的行為 (例如 IP 位址和開放連接埠) 設定自訂警示。 若要深入了解自訂警示及其建立方式，請參閱[建立自訂警示](quickstart-create-custom-alerts.md)。

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>哪裡可看到記錄？ 是否可自訂記錄？

- 使用已連線的 Log Analytics 工作區來檢視警示和建議。 設定工作區中的儲存體大小和持續期間。

- 安全性代理程式中的原始資料也可以儲存在 Log Analytics 帳戶中。 在變更此選項的設定之前，請考慮大小、持續期間、儲存體需求和相關成本。

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>為什麼我應該將 Defender for IoT 新增至模組身分識別？ 用途為何？

適用于 IoT 的 Defender 模組可用於代理程式設定和管理。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何開始使用 Defender for IoT，請參閱下列文章：

- 閱讀適用于 IoT 的 Defender [總覽](overview.md)
- 確認[服務先決條件](service-prerequisites.md)
- 深入了解如何[開始使用](getting-started.md)
- 瞭解 [適用于 IoT 的 Defender 安全性警示](concept-security-alerts.md)
