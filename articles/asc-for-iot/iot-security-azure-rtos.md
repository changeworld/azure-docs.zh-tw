---
title: Azure RTO 支援
description: 瞭解 IoT 服務的 Azure 資訊安全中心中的 Azure RTO 支援。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096149"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>適用于 Azure RTO 的 IoT 安全性解決方案 Azure 資訊安全中心 

IoT 安全性模組的 Azure 資訊安全中心為 Azure RTO 裝置提供了全方位的安全性解決方案。 Azure RTO 隨附內建的安全性模組，涵蓋即時作業系統裝置上的常見威脅。 

![IoT Azure RTO 的 Azure 資訊安全中心](./media/architecture/azure-rtos-security-monitoring.png)


具有 Azure RTO 支援的 IoT 安全性模組 Azure 資訊安全中心提供下列功能： 
- 惡意的網路活動偵測
- 以自訂警示為基礎，裝置行為基準
- 改善裝置安全性防護

### <a name="detection-of-malicious-network-activities"></a>偵測惡意的網路活動

每個裝置的輸入和輸出網路活動都會受到監視（支援的通訊協定： TCP、UDP、IPv4 和 IPv6 上的 ICMP）。 適用于 IoT 的 Azure 資訊安全中心會針對 Microsoft 威脅情報摘要檢查每個網路活動。 此摘要會即時更新，並以數百萬個唯一的威脅指標在全球各地收集。 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>根據自訂警示的裝置行為基準

基準允許將裝置叢集化到安全性群組，並定義每個群組的預期行為。 IoT 裝置通常是設計成在定義良好和有限的案例中運作，因此使用一組參數來建立基準來定義其預期行為是很容易的。 任何與基準的偏差都會觸發警示。 

### <a name="improve-your-device-security-hygiene"></a>改善您的裝置安全性防護

藉由運用 IoT 的建議基礎結構 Azure 資訊安全中心提供的相關知識和深入解析，以瞭解您的環境中會影響和損害裝置安全性狀態的問題。 不佳的 IoT 裝置安全性狀態可能會讓潛在的攻擊在不變的情況下成功，因為安全性一律是由任何組織內的最弱連結來測量。 

## <a name="get-started-protecting-azure-rtos-devices"></a>開始保護 Azure RTO 裝置

- 適用于 Azure RTO 的 IoT 安全性模組 Azure 資訊安全中心是以免費下載的方式提供給您的裝置。 IoT 雲端服務的 Azure 資訊安全中心提供每個 Azure 訂用帳戶30天的試用版。 下載[適用于 AZURE rto 的 IoT 安全性模組 Azure 資訊安全中心](https://github.com/azure-rtos/iot-security-module-preview)以開始使用。 


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 IoT Azure RTO 支援的 Azure 資訊安全中心。 若要瞭解如何開始使用並在 IoT 中樞中啟用您的安全性解決方案，請參閱下列文章：

- [服務必要條件](service-prerequisites.md)
- [快速入門](getting-started.md)
- [設定您的解決方案](quickstart-configure-your-solution.md)
- [在 IoT 中樞中啟用安全性](quickstart-onboard-iot-hub.md)
- [IoT 的 Azure 資訊安全中心常見問題](resources-frequently-asked-questions.md)
- [適用於 IoT 的 Azure 資訊安全中心的安全性警示](concept-security-alerts.md)
