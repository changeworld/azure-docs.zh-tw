---
title: IoT Edge 的安全性模組
description: 瞭解適用于 IoT Edge 的 IoT 安全性模組 Azure 資訊安全中心的架構和功能。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310634"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge 安全性模組

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)提供強大的功能，可在邊緣管理和執行商務工作流程。
IoT Edge 在 IoT 環境中所扮演的關鍵區段，使其對惡意執行者特別具吸引力。

適用于 IoT 安全性模組的 Azure 資訊安全中心可為您的 IoT Edge 裝置提供完整的安全性解決方案。
Azure 資訊安全中心 IoT 模組會收集、匯總及分析來自作業系統和容器系統的原始安全性資料，成為可操作的安全性建議和警示。

類似于 IoT 裝置的 IoT 安全性代理程式 Azure 資訊安全中心，IoT Edge 模組的 Azure 資訊安全中心可透過其模組對應項進行高度自訂。
若要深入瞭解，請參閱[設定您的代理程式](how-to-agent-configuration.md)。

適用于 IoT Edge 的 IoT 安全性模組 Azure 資訊安全中心提供下列功能：

- 從基礎作業系統（Linux）和 IoT Edge 容器系統收集原始安全性事件。

  若要深入瞭解可用的安全性資料收集器，請參閱[IoT 代理程式設定 Azure 資訊安全中心](how-to-agent-configuration.md)。

- IoT Edge 部署資訊清單的分析。

- 將原始安全性事件匯總到透過[IoT Edge 中樞](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)傳送的訊息。

- 透過使用安全性模組對應項來移除設定。

  若要深入瞭解，請參閱[設定 IoT 代理程式的 Azure 資訊安全中心](how-to-agent-configuration.md)。

適用于 IoT Edge 的 IoT 安全性模組 Azure 資訊安全中心會在 IoT Edge 底下的特殊許可權模式下執行。
需要有特殊許可權模式，才能讓模組監視作業系統和其他 IoT Edge 模組。

## <a name="module-supported-platforms"></a>模組支援的平臺

適用于 IoT Edge 的 IoT 安全性模組 Azure 資訊安全中心目前僅適用于 Linux。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解適用于 IoT Edge 的 IoT 安全性模組 Azure 資訊安全中心的架構和功能。

若要繼續開始使用 Azure 資訊安全中心進行 IoT 部署，請使用下列文章：

- [IoT Edge 的部署安全性模組](how-to-deploy-edge.md)
- 瞭解如何[設定您的安全性模組](how-to-agent-configuration.md)
- 審查 IoT[服務必要條件](service-prerequisites.md)的 Azure 資訊安全中心
- 瞭解如何[在您的 IoT 中樞中啟用 IoT 服務的 Azure 資訊安全中心](quickstart-onboard-iot-hub.md)
- 深入瞭解[適用于 IoT 的 Azure 資訊安全中心](resources-frequently-asked-questions.md)的服務常見問題
