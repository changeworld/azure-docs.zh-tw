---
title: 適用于 IoT 的 Azure Defender 代理程式常見問題
description: 尋找適用于 IoT 的 Azure Defender 代理程式最常見問題的解答。
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
ms.openlocfilehash: 9862519a2003eb373c43fef1b660986a8d830327
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094334"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>適用于 IoT 的 Azure Defender 代理程式常見問題

本文提供 Defender for IoT 代理程式的常見問題和解答清單。


## <a name="do-i-have-to-install-an-embedded-security-agent"></a>我是否需要安裝內嵌的安全性代理程式？

您的 IoT 裝置上的代理程式安裝並不是啟用 Defender for IoT 的必要項。 您可從下列三個選項進行選擇，根據選擇取得不同層級的安全性監視及管理功能：

- 被動式、非侵入式 (無代理程式) 部署使用 NTA (網路流量分析) 感應器來監視和提供 IoT/OT 風險的深度可見度，對網路和裝置的效能不會有任何影響
- 安裝適用于 IoT embedded 安全性代理程式的 Defender （不論有沒有修改）。 此選項可為裝置行為和存取提供最高層級的增強式安全性見解。

- 建立您自己的代理程式，並執行 Defender for IoT 安全性訊息架構。 此選項可讓您在裝置安全性代理程式上使用 Defender for IoT 分析工具。

- IoT 裝置上不會安裝任何安全性代理程式。 此選項可供啟用 IoT 中樞通訊監視，並縮減安全性監視及管理功能。

## <a name="what-does-the-defender-for-iot-agent-do"></a>Defender for IoT 代理程式有什麼功能？

適用于 IoT 的 Defender 代理程式會藉由掃描設定) 、處理 & 連線，為裝置設定、行為和存取 (提供裝置層級威脅涵蓋範圍。 適用于 IoT 的 Defender 安全性代理程式不會掃描商務相關資料或活動。

適用于 IoT 的 Defender 安全性代理程式是開放原始碼，並可在32位和64位 Windows 和 Linux 版本的 GitHub 上取得： https://github.com/Azure/Azure-IoT-Security 。


## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理程式的相依性和先決條件為何？

適用于 IoT 的 Defender 支援各種不同的平臺。 請參閱[支援的裝置平台](how-to-deploy-agent.md)以確認支援特定裝置。

## <a name="which-data-is-collected-by-the-agent"></a>代理程式會收集哪些資料？

代理程式會收集連線、存取、防火牆設定、處理序清單與 OS 基準。

## <a name="how-much-data-will-the-agent-generate"></a>代理程式會產生多少資料？

代理程式資料的產生是根據裝置、應用程式、連線類型和客戶代理程式設定而定。 由於裝置與 IoT 解決方案之間的高度變化性，因此建議先在實驗或測試設定中部署代理程式，以觀察、學習和設定符合需求的特定設定，同時測量所產生的資料量。 啟動服務之後，適用于 IoT 的 Defender 代理程式會提供將代理程式輸送量優化的操作建議，以協助您進行設定和自訂程式。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理程式訊息是否會耗盡 IoT 中樞的配額？

是。 代理程式傳輸的資料會計入 IoT 中樞配額。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下來呢？ 我已安裝代理程式，但看不到任何活動或記錄...

1. 檢查[代理程式類型是否符合裝置所指定的 OS 平台](how-to-deploy-agent.md)

1. 確認[代理程式正在裝置上執行](how-to-agent-configuration.md)。

1. 檢查是否已在 IoT 中樞針對**安全性**[成功啟用服務](quickstart-onboard-iot-hub.md)。

1. 檢查是否已 [在 Iot 中樞中使用 Defender For IoT 模組設定](quickstart-create-security-twin.md)裝置。

如果活動或記錄仍無法使用，請洽詢您的 Defender for IoT 合作夥伴以取得其他協助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>當網際網路連線停止運作時，會發生什麼情況？

只要裝置正在執行，感應器和代理程式就會繼續執行和儲存資料。 資料會根據大小設定儲存在安全性訊息快取中。 當裝置重新取得連線後，安全性訊息便會繼續傳送。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理程式可能會影響裝置或其他已安裝軟體的效能嗎？

代理程式會像任何其他應用程式/處理序一樣取用機器資源，且不應中斷正常的裝置活動。 代理程式執行所在裝置上的資源耗用量與其安裝和設定有關。 建議在嘗試於生產環境中部署之前，先在內含的環境中測試代理程式設定，以及與其他 IoT 應用程式和功能的互通性。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我正在對裝置進行一些維護。 可以關閉代理程式嗎？

無法關閉代理程式。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>有方法可測試代理程式是否正常運作嗎？

如果代理程式停止通訊或無法傳送安全性訊息，則會產生**裝置無訊息**警示。



## <a name="next-steps"></a>後續步驟

若要深入瞭解如何開始使用 Defender for IoT，請參閱下列文章：

- 閱讀適用于 IoT 的 Defender [總覽](overview.md)
- 確認[服務先決條件](service-prerequisites.md)
- 深入了解如何[開始使用](getting-started.md)
- 瞭解 [適用于 IoT 的 Defender 安全性警示](concept-security-alerts.md)
