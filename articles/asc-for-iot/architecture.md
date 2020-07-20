---
title: 方案架構
description: 瞭解 IoT 服務的 Azure 資訊安全中心中的資訊流程。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311758"
---
# <a name="azure-security-center-for-iot-architecture"></a>適用於 IoT 的 Azure 資訊安全中心架構

本文說明 IoT 解決方案之 Azure 資訊安全中心的功能系統架構。

## <a name="azure-security-center-for-iot-components"></a>IoT 元件的 Azure 資訊安全中心

IoT 的 Azure 資訊安全中心是由下列元件所組成：

- IoT 中樞整合
- 裝置代理程式（選擇性）
- 傳送安全性訊息 SDK
- 分析管線

### <a name="azure-security-center-for-iot-workflows"></a>IoT 工作流程的 Azure 資訊安全中心

IoT 的 Azure 資訊安全中心適用于下列兩種功能工作流程的其中一種：內建和增強式

### <a name="built-in"></a>內建

在內**建**模式中，當您選擇在 IoT 中樞中開啟 [**安全性**] 選項時，就會啟用 Azure 資訊安全中心 for IoT。 內建模式可提供即時監視、建議和警示，提供單一步驟的裝置可見度和無與倫比的安全性。 內建模式不需要在任何裝置上安裝代理程式，而且會在記錄的活動上使用 advanced analytics 來分析和保護您的欄位裝置。

### <a name="enhanced"></a>增強

在 [**增強**] 模式中，在您的 IoT 中樞中開啟 [**安全性**] 選項，並在裝置上安裝 IoT 裝置代理程式的 Azure 資訊安全中心之後，代理程式會從您的裝置收集、匯總和分析原始安全性事件。 原始安全性事件可能包括 IP 連線、進程建立、使用者登入，以及其他安全性相關資訊。 IoT 裝置代理程式的 Azure 資訊安全中心也會處理事件匯總，以協助避免高網路輸送量。 代理程式可高度自訂，可讓您將其用於特定的工作，例如只以最快速的 SLA 傳送重要資訊，或將大量安全性資訊和內容匯總到較大的區段，以避免服務成本更高。

![適用於 IoT 的 Azure 資訊安全中心架構](./media/architecture/azure-iot-security-architecture.png)

裝置代理程式和其他應用程式會使用**Azure 傳送安全性訊息 SDK** ，將安全性資訊傳送至 Azure IoT 中樞。 IoT 中樞會收取此資訊，並將其轉送至 IoT 服務的 Azure 資訊安全中心。

啟用 IoT 服務的 Azure 資訊安全中心之後，除了轉送的資料以外，IoT 中樞也會送出其所有內部資料，以供 IoT Azure 資訊安全中心進行分析。 此資料包含裝置雲端作業記錄、裝置身分識別和中樞設定。 這些資訊都有助於建立 IoT 分析管線的 Azure 資訊安全中心。

IoT 分析管線的 Azure 資訊安全中心也會從 Microsoft 和 Microsoft 合作夥伴內的各種來源接收其他威脅情報串流。 IoT 整個分析管線的 Azure 資訊安全中心適用于服務上的每個客戶設定（例如自訂警示和傳送安全性訊息 SDK 的使用）。

流量分析管線，適用于 IoT 的 Azure 資訊安全中心結合所有的資訊串流，以產生可採取動作的建議和警示。 管線包含安全性研究人員和專家所建立的自訂規則，以及從標準裝置行為和風險分析搜尋偏差的機器學習服務模型。

IoT 建議和警示的 Azure 資訊安全中心（分析管線輸出）會寫入每個客戶的 Log Analytics 工作區。 在工作區中包含未經處理的事件以及警示和建議，可讓您使用所偵測到之可疑活動的確切詳細資料，深入探索調查和查詢。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 IoT 解決方案 Azure 資訊安全中心的基本架構和工作流程。 若要深入瞭解必要條件、如何開始使用，以及如何在 IoT 中樞中啟用您的安全性解決方案，請參閱下列文章：

- [服務必要條件](service-prerequisites.md)
- [快速入門](getting-started.md)
- [設定您的解決方案](quickstart-configure-your-solution.md)
- [在 IoT 中樞中啟用安全性](quickstart-onboard-iot-hub.md)
- [IoT 的 Azure 資訊安全中心常見問題](resources-frequently-asked-questions.md)
- [適用於 IoT 的 Azure 資訊安全中心的安全性警示](concept-security-alerts.md)
