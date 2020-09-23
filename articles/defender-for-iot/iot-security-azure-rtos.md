---
title: 適用于 Azure RTO 的安全性模組總覽
description: 深入瞭解 Azure RTO 支援和實施的安全性模組，作為 IoT 服務 Defender 的一部分
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 0cfd1e0ce16008c6f7fd128d561ad361bcc53b87
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934462"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>總覽：適用于 Azure RTO 的 Defender for IoT 安全性模組 (預覽版) 

適用于 RTO 的 Defender for IoT 安全性模組提供適用于 Azure RTO 裝置的全方位安全性解決方案。 Azure RTO 現在隨附于內建的 Azure IoT 安全性模組，並提供在即時作業系統裝置上常見威脅和潛在惡意活動的涵蓋範圍。 

![適用于 IoT 的 Defender Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


適用于 Azure RTO 的安全性模組提供下列功能： 
- 惡意網路活動偵測
- 自訂警示型、裝置行為基準
- 改善裝置安全性的防護

## <a name="detection-of-malicious-network-activities"></a>偵測惡意網路活動

每個裝置的輸入和輸出網路活動都會受到監視 (支援的通訊協定： TCP、UDP、IPv4 和 IPv6) 上的 ICMP。 Defender for IoT 會針對 Microsoft 威脅情報摘要檢查每個網路活動。 摘要會即時更新，並在世界各地收集數百萬個獨特的威脅指標。 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>根據自訂警示的裝置行為基準

基準化可將裝置叢集化至安全性群組，並定義每個群組的預期行為。 由於 IoT 裝置通常是設計成在定義完善且有限的案例中運作，因此您可以使用一組參數，輕鬆地建立基準來定義其預期的行為。 基準的任何偏差都會觸發警示。 

## <a name="improve-your-device-security-hygiene"></a>改善您的裝置安全性保健

藉由利用適用于 IoT 的建議基礎結構 Defender 提供，您可以在環境中取得影響並損害裝置安全性狀態的相關知識與見解。 弱式 IoT 裝置安全性狀態可能會在保持不變的情況下允許潛在攻擊成功，因為任何組織內的最弱連結都能以安全性來測量。 

## <a name="get-started-protecting-azure-rtos-devices"></a>開始保護 Azure RTO 裝置

Azure RTO 的安全性模組是以免費下載的方式提供給您的裝置。 每個 Azure 訂用帳戶30天試用版都提供適用于 IoT 雲端服務的 Defender。 下載 [適用于 AZURE rto 的安全性模組](https://github.com/azure-rtos/iot-security-module-preview) 以開始使用。 


## <a name="next-steps"></a>下一步

在本文中，您已瞭解 Azure RTO 服務的安全性模組。 若要深入瞭解安全性模組並開始使用，請參閱下列文章：

- [Azure RTO IoT 安全性模組概念](concept-rtos-security-module.md)
- [快速入門： Azure RTO IoT 安全性模組](quickstart-azure-rtos-security-module.md)
