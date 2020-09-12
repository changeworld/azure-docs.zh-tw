---
title: 適用于 Azure RTO 的安全性模組總覽
description: 深入瞭解 Azure RTO 支援和實作為適用於 IoT 的 Azure 資訊安全中心服務一部分的安全性模組。
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514470"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>總覽：適用于 Azure RTO (preview) 的安全性模組

適用於 IoT 的 Azure 資訊安全中心 RTO 安全性模組為 Azure RTO 裝置提供了全方位的安全性解決方案。 Azure RTO 現在隨附于內建的 Azure IoT 安全性模組，並提供在即時作業系統裝置上常見威脅和潛在惡意活動的涵蓋範圍。 

![適用於 IoT 的 Azure 資訊安全中心 Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


適用于 Azure RTO 的安全性模組提供下列功能： 
- 惡意網路活動偵測
- 自訂警示型、裝置行為基準
- 改善裝置安全性的防護

### <a name="detection-of-malicious-network-activities"></a>偵測惡意網路活動

每個裝置的輸入和輸出網路活動都會受到監視 (支援的通訊協定： TCP、UDP、IPv4 和 IPv6) 上的 ICMP。 適用於 IoT 的 Azure 資訊安全中心會針對 Microsoft 威脅情報摘要檢查每個網路活動。 摘要會即時更新，並在世界各地收集數百萬個獨特的威脅指標。 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>根據自訂警示的裝置行為基準

基準化可將裝置叢集化至安全性群組，並定義每個群組的預期行為。 由於 IoT 裝置通常是設計成在定義完善且有限的案例中運作，因此您可以使用一組參數，輕鬆地建立基準來定義其預期的行為。 基準的任何偏差都會觸發警示。 

### <a name="improve-your-device-security-hygiene"></a>改善您的裝置安全性保健

藉由充分利用適用於 IoT 的 Azure 資訊安全中心提供的建議基礎結構，深入瞭解您環境中的問題，進而影響並損害裝置的安全性狀態。 弱式 IoT 裝置安全性狀態可能會在保持不變的情況下允許潛在攻擊成功，因為任何組織內的最弱連結都能以安全性來測量。 

## <a name="get-started-protecting-azure-rtos-devices"></a>開始保護 Azure RTO 裝置

Azure RTO 的安全性模組是以免費下載的方式提供給您的裝置。 每個 Azure 訂用帳戶30天試用版都提供適用於 IoT 的 Azure 資訊安全中心雲端服務。 下載 [適用于 AZURE rto 的安全性模組](https://github.com/azure-rtos/iot-security-module-preview) 以開始使用。 


## <a name="next-steps"></a>接下來的步驟

在本文中，您已瞭解 Azure RTO 服務的安全性模組。 若要深入瞭解安全性模組並開始使用，請參閱下列文章：

- [Azure RTO IoT 安全性模組概念](concept-rtos-security-module.md)
- [快速入門： Azure RTO IoT 安全性模組](quickstart-azure-rtos-security-module.md)


