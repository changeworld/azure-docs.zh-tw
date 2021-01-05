---
title: 安全性代理程式總覽
description: 在 IoT 裝置上開始瞭解、設定、部署及使用適用于 IoT 的 Azure Defender 安全性服務代理程式。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2019
ms.author: shhazam
ms.openlocfilehash: d3b50d909fb167ee123b548603628bae7d844c5b
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839430"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>開始使用適用于 IoT 的 Azure Defender 裝置安全性代理程式

適用于 IoT 的 Defender 安全性代理程式提供增強的安全性功能，例如監視遠端連線、使用中應用程式、登入事件，以及作業系統設定的最佳做法。 利用單一服務來掌控裝置欄位的威脅防護和安全性狀況。

您可以在 c # 和 C 中提供 Linux 和 Windows 安全性代理程式的參考架構。

適用于 IoT 的 Defender 安全性代理程式會處理來自裝置作業系統的原始事件收集、事件匯總以降低成本，並透過裝置模組對應項進行設定。 安全性訊息會透過 IoT 中樞傳送到 Defender for IoT analytics 服務。

使用下列工作流程來部署及測試適用于 IoT 安全性代理程式的 Defender：

1. [為 iot 中樞啟用適用于 IoT 的 Defender 服務](quickstart-onboard-iot-hub.md)
1. 如果您的 IoT 中樞沒有已註冊的裝置，請 [註冊新的裝置](../iot-accelerators/quickstart-device-simulation-deploy.md)。
1. 為您的裝置[建立 azureiotsecurity 安全性模組](quickstart-create-security-twin.md)。
1. 若要在 Azure 模擬裝置上安裝代理程式，而不是安裝在實際的裝置上，請在可用區域中 [啟動新的 Azure 虛擬機器 (VM) ](../virtual-machines/linux/quick-create-portal.md) 。
1. 在您的 IoT 裝置或新的 VM 上[部署適用于 iot 的 Defender 安全性代理程式](how-to-deploy-linux-cs.md)。
1. 遵循 [trigger_events](https://aka.ms/iot-security-github-trigger-events) 的指示，執行無害的攻擊模擬。
1. 驗證 Defender 的 IoT 警示，以回應上一個步驟中的模擬攻擊。 執行腳本後的五分鐘開始驗證。
1. 使用「IoT 中樞」探索[使用 Log Analytics](how-to-security-data-access.md)的[警示](concept-security-alerts.md)、[建議](concept-recommendations.md)和深入探討。

## <a name="next-steps"></a>後續步驟

- 設定您的 [解決方案](quickstart-configure-your-solution.md)
- [建立安全性模組](quickstart-create-security-twin.md)
- 設定 [自訂警示](quickstart-create-custom-alerts.md)
- [部署安全性代理程式](how-to-deploy-agent.md)