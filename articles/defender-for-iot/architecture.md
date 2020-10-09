---
title: 方案架構
description: 瞭解 Azure Defender for IoT 服務中的資訊流程。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 3fc695770350e5a60ae3da9ab1796da5cac99370
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843408"
---
# <a name="azure-defender-for-iot-architecture"></a>適用于 IoT 的 Azure Defender 架構

本文說明 Defender for IoT 解決方案的功能性系統架構。

## <a name="defender-for-iot-components"></a>適用于 IoT 的 Defender 元件

適用于 IoT 的 Defender 是由下列元件所組成：

- IoT 中樞整合
- 裝置代理程式 (選用) 
- 傳送安全性訊息 SDK
- 分析管線

### <a name="defender-for-iot-workflows"></a>適用于 IoT 的 Defender 工作流程

Defender for IoT 適用于下列兩個功能工作流程的其中一個：內建和增強

### <a name="built-in"></a>內建

在內 **建** 模式中，當您選擇在 iot 中樞中開啟 **安全性** 選項時，會啟用 Defender for IoT。 內建模式提供即時監視、建議和警示，提供單一步驟的裝置可見度和無與倫比的安全性。 內建模式不需要在任何裝置上安裝代理程式，而且會在已記錄的活動上使用 advanced analytics 來分析及保護您的欄位裝置。

### <a name="enhanced"></a>增強

在 **增強** 模式中，開啟 iot 中樞的 [ **安全性** ] 選項，並在裝置上安裝適用于 IoT 裝置代理程式的 Defender 之後，代理程式會從您的裝置收集、匯總及分析原始安全性事件。 原始安全性事件可以包含 IP 連線、進程建立、使用者登入，以及其他安全性相關資訊。 IoT 裝置代理程式的防禦者也會處理事件匯總，以協助避免高的網路輸送量。 代理程式具有高度自訂性，可讓您將它們用於特定的工作，例如只傳送最快速 SLA 的重要資訊，或是將大量的安全性資訊和內容匯總到較大的區段，以避免產生更高的服務成本。

![適用于 IoT 的 Defender 架構](./media/architecture/azure-iot-security-architecture.png)

裝置代理程式和其他應用程式會使用 **Azure 傳送安全性訊息 SDK** 將安全性資訊傳送至 Azure IoT 中樞。 IoT 中樞會取得此資訊，並將其轉送至適用于 IoT 的 Defender 服務。

一旦啟用 Defender for IoT 服務，除了轉送的資料之外，IoT 中樞也會傳送其內部資料，以供 Defender 針對 IoT 進行分析。 此資料包含裝置雲端作業記錄、裝置身分識別和中樞設定。 所有這些資訊都有助於建立適用于 IoT 分析管線的 Defender。

適用于 IoT 分析管線的 Defender 也會從 Microsoft 和 Microsoft 合作夥伴內的各種來源接收額外的威脅情報串流。 適用于 IoT 的 Defender 整個分析管線適用于服務 (的每個客戶設定，例如自訂警示和傳送安全性訊息 SDK) 的使用。

流量分析管線，適用于 IoT 的 Defender 會合並所有資訊串流，以產生可採取動作的建議和警示。 此管線包含安全性研究人員和專家所建立的自訂規則，以及搜尋標準裝置行為和風險分析偏差的機器學習模型。

適用于 IoT 的 Defender 建議和警示 (分析管線輸出) 會寫入至每個客戶的 Log Analytics 工作區。 在工作區中包含未經處理的事件以及警示和建議，可讓您使用所偵測到的可疑活動的確切詳細資料，深入探討調查和查詢。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 Defender for IoT 解決方案的基本架構和工作流程。 若要深入瞭解必要條件、如何在 IoT 中樞開始使用並啟用您的安全性解決方案，請參閱下列文章：

- [服務必要條件](service-prerequisites.md)
- [快速入門](getting-started.md)
- [設定您的解決方案](quickstart-configure-your-solution.md)
- [啟用 IoT 中樞的安全性](quickstart-onboard-iot-hub.md)
- [適用于 IoT 的 Defender 常見問題](resources-frequently-asked-questions.md)
- [適用于 IoT 的 Defender 安全性警示](concept-security-alerts.md)
