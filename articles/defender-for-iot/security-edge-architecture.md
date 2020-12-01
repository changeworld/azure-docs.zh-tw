---
title: 適用于 IoT Edge 的 Defender for IoT 安全性模組
description: 瞭解適用于 IoT 的 Azure Defender for IoT 安全性模組的架構和功能 IoT Edge。
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
ms.openlocfilehash: 4f6d9f670a1b85e55ccc8f6cb18645b92927221a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351634"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>適用于 IoT Edge 安全性的 Azure Defender 模組

[Azure IoT Edge](../iot-edge/index.yml) 提供強大的功能，可在邊緣管理及執行商務工作流程。
IoT Edge 在 IoT 環境中扮演的關鍵區段，可讓惡意執行者特別吸引人。

Defender for IoT security 模組為您的 IoT Edge 裝置提供了全方位的安全性解決方案。
適用于 IoT 的 Defender 模組會收集、匯總和分析來自您作業系統和容器系統的原始安全性資料，並將其匯總成可採取動作的安全性建議和警示。

與適用于 IoT 裝置的 Defender for IoT 安全性代理程式類似，Defender for IoT Edge 模組可透過其模組對應項高度自訂。
若要深入瞭解，請參閱 [設定您的代理程式](how-to-agent-configuration.md) 。

適用于 IoT Edge 的 Defender for IoT 安全性模組提供下列功能：

- 從基礎作業系統收集 (Linux) 和 IoT Edge 容器系統的原始安全性事件。

  若要深入瞭解可用的安全性資料收集器，請參閱 [Defender For IoT 代理程式](how-to-agent-configuration.md) 設定。

- IoT Edge 部署資訊清單的分析。

- 將原始安全性事件匯總至透過 [IoT Edge 中樞](../iot-edge/iot-edge-runtime.md#iot-edge-hub)傳送的訊息。

- 使用安全性模組對應項來移除設定。

  若要深入瞭解，請參閱 [設定適用于 IoT 的 Defender 代理程式](how-to-agent-configuration.md) 。

適用于 IoT Edge 的 Defender for IoT 安全性模組會在 IoT Edge 下以特殊許可權模式執行。
需要有特殊許可權模式，才能讓模組監視作業系統和其他 IoT Edge 模組。

## <a name="module-supported-platforms"></a>模組支援的平臺

適用于 IoT Edge 的 Defender for IoT 安全性模組目前僅適用于 Linux。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解適用于 IoT Edge 的 Defender for IoT 安全性模組的架構和功能。

若要繼續開始使用 Defender 進行 IoT 部署，請使用下列文章：

- 部署 [IoT Edge 的安全性模組](how-to-deploy-edge.md)
- 瞭解如何 [設定您的安全性模組](how-to-agent-configuration.md)
- 查看 Defender for IoT [服務必要條件](service-prerequisites.md)
- 瞭解如何 [在 Iot 中樞啟用 Defender For iot 服務](quickstart-onboard-iot-hub.md)
- 深入瞭解[Defender For IoT](resources-frequently-asked-questions.md)的服務常見問題