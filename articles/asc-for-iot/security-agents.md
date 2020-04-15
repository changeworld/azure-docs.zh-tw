---
title: 安全代理概述
description: 開始瞭解、配置、部署和使用IoT設備上的IoT安全服務代理的Azure安全中心。
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310656"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>使用適用於 IoT 裝置安全代理的 Azure 安全中心開始

IoT 安全代理 Azure 安全中心提供增強的安全功能,例如監視遠端連接、活動應用程式、登錄事件和作業系統配置最佳實務。 通過單一服務控制設備現場威脅防護和安全狀態。

提供了 C# 和 C 中的 Linux 和 Windows 安全代理的參考體系結構。

IoT 安全代理的 Azure 安全中心處理來自設備作業系統的原始事件集合、事件聚合以降低成本以及通過設備模組孿生進行配置。 安全消息通過IoT中心發送到Azure安全中心,用於IoT分析服務。

使用以下工作流為 IoT 安全代理部署和測試 Azure 安全中心:

1. [將 IoT 服務的 Azure 安全中心啟用到 IoT 中心](quickstart-onboard-iot-hub.md)
1. 如果您的 IoT 中心沒有註冊的裝置,[請註冊新裝置](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)。
1. 為裝置[建立 Azureiot 安全模組](quickstart-create-security-twin.md)。
1. 要在 Azure 模擬裝置上安裝代理,而不是在實際設備上安裝,請在可用區域中[啟動新的 Azure 虛擬機器 (VM)。](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)
1. [在 IoT 設備上部署 IoT 安全代理的 Azure 安全中心](how-to-deploy-linux-cs.md),或部署新的 VM。
1. 按照[trigger_events](https://aka.ms/iot-security-github-trigger-events)的說明運行攻擊的無害類比。
1. 驗證 Azure 安全中心是否發出 IoT 警報,以回應上一步的模擬攻擊。 運行腳本五分鐘后開始驗證。
1. 使用 IoT 中心使用[日誌分析](how-to-security-data-access.md)探索[警報](concept-security-alerts.md),[建議](concept-recommendations.md)和深度潛水。

## <a name="next-steps"></a>後續步驟

- 設定[解決方案](quickstart-configure-your-solution.md)
- [建立安全性模組](quickstart-create-security-twin.md)
- 設定[自訂警示](quickstart-create-custom-alerts.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
