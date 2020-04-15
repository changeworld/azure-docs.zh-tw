---
title: IoT 邊緣的安全模組
description: 瞭解 IoT 邊緣 IoT 安全模組的 Azure 安全中心的體系結構和功能。
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310634"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT 邊緣安全模組

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)提供了強大的功能,可在邊緣管理和執行業務工作流。
IoT Edge 在 IoT 環境中扮演的關鍵角色使其對惡意參與者特別有吸引力。

面向 IoT 安全的 Azure 安全中心模組為您的 IoT 邊緣設備提供了全面的安全解決方案。
IoT 模組的 Azure 安全中心會收集、聚合和分析來自作業系統和容器系統的原始安全數據,並將其轉化為可操作的安全建議和警報。

與適用於 IoT 裝置的 IoT 安全代理的 Azure 安全中心類似,IoT 邊緣 Azure 安全中心模組透過其模組孿生高度可自定義。
請參閱[配置代理](how-to-agent-configuration.md)以瞭解更多資訊。

適用於 IoT 邊緣的 IoT 安全模組的 Azure 安全中心提供以下功能:

- 從基礎作業系統 (Linux) 和 IoT 邊緣容器系統收集原始安全事件。

  請參閱[Azure 安全中心進行 IoT 代理設定](how-to-agent-configuration.md),瞭解有關可用安全數據收集器的更多資訊。

- IoT 邊緣部署清單的分析。

- 將原始安全事件聚合到通過[IoT 邊緣中心](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)發送的消息中。

- 使用安全模組孿生來刪除配置。

  請參閱[為 IoT 代理配置 Azure 安全中心](how-to-agent-configuration.md)以瞭解更多資訊。

IoT 邊緣 IoT 安全模組的 Azure 安全中心在 IoT 邊緣下的特權模式下運行。
需要特權模式才能允許模組監視操作系統和其他 IoT 邊緣模組。

## <a name="module-supported-platforms"></a>模組支援的平臺

IoT 邊緣 IoT 安全模組的 Azure 安全中心目前僅適用於 Linux。

## <a name="next-steps"></a>後續步驟

在本文中,您瞭解了IoT邊緣IoT安全模組的Azure安全中心的體系結構和功能。

要繼續使用用於 IoT 部署的 Azure 安全中心,請使用以下文章:

- [為 IoT 邊緣部署安全模組](how-to-deploy-edge.md)
- 瞭解如何[設定安全模組](how-to-agent-configuration.md)
- 檢視 Azure 安全中心,查看 IoT[服務先決條件](service-prerequisites.md)
- 瞭解如何在[IoT 中心啟用 IoT 服務的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 從[IoT 常見問題解答的 Azure 安全中心](resources-frequently-asked-questions.md)瞭解有關該服務
