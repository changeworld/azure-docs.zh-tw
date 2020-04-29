---
title: 安全性代理程式總覽
description: 開始瞭解、設定、部署和使用 IoT 裝置上的 IoT 安全性服務代理程式 Azure 資訊安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310656"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>開始使用適用于 IoT 裝置安全性代理程式的 Azure 資訊安全中心

IoT 安全性代理程式的 Azure 資訊安全中心可提供增強的安全性功能，例如監視遠端連線、作用中應用程式、登入事件，以及作業系統設定的最佳作法。 利用單一服務來控制您的裝置欄位威脅防護和安全性狀態。

同時提供 Linux 和 Windows 安全性代理程式的參考架構（c # 和 C）。

IoT 安全性代理程式的 Azure 資訊安全中心會處理來自裝置作業系統的原始事件收集、事件匯總以降低成本，以及透過裝置模組對應項進行設定。 安全性訊息會透過您的 IoT 中樞傳送至 IoT 分析服務的 Azure 資訊安全中心。

使用下列工作流程來部署及測試 IoT 安全性代理程式的 Azure 資訊安全中心：

1. [對您的 IoT 中樞啟用 IoT 服務的 Azure 資訊安全中心](quickstart-onboard-iot-hub.md)
1. 如果您的 IoT 中樞沒有已註冊的裝置，請[註冊新的裝置](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)。
1. 為您的裝置[建立 azureiotsecurity 安全性模組](quickstart-create-security-twin.md)。
1. 若要在 Azure 模擬裝置上安裝代理程式，而不是在實際裝置上安裝，請在可用區域中[加速新的 Azure 虛擬機器（VM）](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 。
1. 在您的 IoT 裝置或新的 VM 上[部署 iot 安全性代理程式的 Azure 資訊安全中心](how-to-deploy-linux-cs.md)。
1. 請遵循[trigger_events](https://aka.ms/iot-security-github-trigger-events)的指示，以執行攻擊的無害模擬。
1. 確認 IoT 警示 Azure 資訊安全中心，以回應上一個步驟中的模擬攻擊。 執行腳本後的五分鐘開始驗證。
1. 探索[警示](concept-security-alerts.md)、[建議](concept-recommendations.md)，以及使用 IoT 中樞的[Log Analytics 深入探討](how-to-security-data-access.md)。

## <a name="next-steps"></a>後續步驟

- 設定您的[解決方案](quickstart-configure-your-solution.md)
- [建立安全性模組](quickstart-create-security-twin.md)
- 設定[自訂警示](quickstart-create-custom-alerts.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
