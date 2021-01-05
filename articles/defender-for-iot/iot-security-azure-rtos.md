---
title: 適用于 Azure RTO 的安全性模組總覽
description: 深入瞭解適用于 IoT 的 Azure Defender 支援和實施的安全性模組。
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
ms.openlocfilehash: 9950f3727aac365205e979d9590edacebd32f1fc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832738"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>總覽：適用于 Azure RTO 的 Defender for IoT 安全性模組 (預覽版) 

適用于 IoT 的 Azure Defender 安全性模組可為使用 Azure RTO 的裝置提供全方位的安全性解決方案。 它可提供在即時作業系統 (RTO) 裝置上常見威脅和潛在惡意活動的涵蓋範圍。 Azure RTO 現在隨附內建的 Azure IoT 安全性模組。

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="適用于 IoT Azure RTO 的 Defender 視覺效果。":::


適用于 Azure RTO 的安全性模組提供下列功能：

- 惡意網路活動偵測
- 自訂警示型裝置行為基準
- 改進裝置安全性的防護

## <a name="detect-malicious-network-activities"></a>偵測惡意網路活動

每個裝置的輸入和輸出網路活動都會受到監視。 支援的通訊協定為 IPv4 和 IPv6 上的 TCP、UDP 和 ICMP。 Defender for IoT 會針對 Microsoft 威脅情報摘要檢查每個網路活動。 摘要會即時更新，並在全球收集數百萬個獨特的威脅指標。

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>根據自訂警示的裝置行為基準

基準化可將裝置叢集化至安全性群組，並定義每個群組的預期行為。 由於 IoT 裝置通常是設計成可在定義完善且有限的案例中運作，因此您可以使用一組參數，輕鬆地建立基準來定義其預期的行為。 基準的任何偏差都會觸發警示。

## <a name="improve-your-device-security-hygiene"></a>改善您的裝置安全性保健

藉由使用適用于 IoT 的建議基礎結構 Defender 提供，您可以取得環境中影響和損害裝置安全性狀態之問題的相關知識與見解。 弱式 IoT 裝置安全性狀態可能會讓潛在攻擊成功（如果保持不變）。 安全性一律是以任何組織內的最弱連結來測量。

## <a name="get-started-protecting-azure-rtos-devices"></a>開始保護 Azure RTO 裝置

Azure RTO 的安全性模組是以免費下載的方式提供給您的裝置。 每個 Azure 訂用帳戶30天的試用版都有提供適用于 IoT 的 Defender 雲端服務。 若要開始使用，請下載 [適用于 AZURE rto 的安全性模組](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md)。 

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 Azure RTO 的安全性模組。 若要深入瞭解安全性模組並開始使用，請參閱下列文章：

- [Azure RTO IoT 安全性模組概念](concept-rtos-security-module.md)
- [快速入門： Azure RTO IoT 安全性模組](quickstart-azure-rtos-security-module.md)
