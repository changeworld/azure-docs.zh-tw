---
title: Azure RTO 安全性模組的基本概念說明
description: 瞭解 Azure RTO 概念和工作流程安全性模組的基本概念。
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
ms.openlocfilehash: 8f521bd593369509fd520831f90ce9c601227f09
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340045"
---
# <a name="security-module-for-azure-rtos-preview"></a>適用于 Azure RTO (預覽版的安全性模組) 

使用本文來深入瞭解 Azure RTO 的安全性模組，包括功能和優點，以及相關設定和參考資源的連結。 

## <a name="azure-rtos-iot-security-module"></a>Azure RTO IoT 安全性模組

適用于 Azure RTO 的安全性模組提供適用于 Azure RTO 裝置的全方位安全性解決方案，作為 NetX 系列供應專案的一部分。 在 NetX 的基礎供應專案中，Azure RTO 隨附內建的 Azure IoT 安全性模組，可在您啟用的情況下，為您的即時作業系統裝置提供一般威脅的涵蓋範圍。 

適用于 Azure RTO 的安全性模組會在背景執行，並提供順暢的使用者體驗，同時使用每個客戶唯一的 IoT 中樞連線來傳送安全性訊息。 預設會啟用 Azure RTO 的安全性模組。  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX 雙核處理器是先進的工業級 TCP/IP 網路堆疊，專為深度內嵌的即時和 IoT 應用程式所設計。 Azure RTO NetX 雙核是雙重的 IPv4 和 IPv6 網路堆疊，提供一組豐富的通訊協定，包括安全性和雲端。 深入瞭解 [AZURE Rto NetX 雙核](/azure/rtos/netx-duo/) 解決方案。

此課程模組提供下列功能：

- **偵測惡意網路活動**
- **根據自訂警示的裝置行為基準**
- **改善裝置安全性的防護**

## <a name="security-module-for-azure-rtos-architecture"></a>適用于 Azure RTO 架構的安全性模組

Azure RTO 的安全性模組會由 Azure IoT 中介軟體平臺初始化，並使用 IoT 中樞用戶端將安全性遙測傳送至中樞。

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT 安全性模組狀態圖表和資訊流程":::

適用于 Azure RTO 的安全性模組會使用三個收集器來監視下列裝置活動和資訊：
- 裝置網路活動 **TCP**、 **UDP** 和 **ICM**
- **Threadx** 和 **NetX 雙核** 版本的系統資訊
- 活動訊號事件

每個收集器都會連結到優先順序群組，且每個優先權群組都有自己的間隔，其可能值為 **Low**、 **Medium** 和 **High**。 間隔會影響收集和傳送資料的時間間隔。

每個時間間隔都是可設定的，而且可以啟用和停用 IoT 連接器，以便進一步 [自訂您的解決方案](how-to-azure-rtos-security-module.md)。 

## <a name="supported-security-alerts-and-recommendations"></a>支援的安全性警示和建議

適用于 Azure RTO 的安全性模組支援特定的安全性警示和建議。 完成初始設定之後，請務必 [檢查並自訂服務的相關警示和建議值](concept-rtos-security-alerts-recommendations.md) 。

## <a name="ready-to-begin"></a>準備開始了嗎？

Azure RTO 的安全性模組是以免費下載的方式提供給您的 IoT 裝置。 每個 Azure 訂用帳戶30天的試用版都有提供適用于 IoT 的 Defender 雲端服務。 [立即下載安全性模組](https://github.com/azure-rtos/azure-iot-preview/releases) ，讓我們開始吧。 

## <a name="next-steps"></a>後續步驟

- 開始使用適用于 Azure RTO [必要條件和設定](quickstart-azure-rtos-security-module.md)的安全性模組。
- 深入瞭解 Azure RTO [安全性警示和建議支援](concept-rtos-security-alerts-recommendations.md)的安全性模組。 
- 使用 Azure RTO [參考 API](azure-rtos-security-module-api.md)的安全性模組。