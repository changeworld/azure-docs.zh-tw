---
title: 安全代理體系結構
description: 瞭解 Azure 安全中心中用於 IoT 服務的代理的安全代理體系結構。
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 9029ece923b7cda09c7a57d07736791e241c9e70
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310676"
---
# <a name="security-agent-reference-architecture"></a>安全代理引用架構結構

IoT 的 Azure 安全中心為透過 IoT 中心記錄、處理、聚合和發送安全資料的安全代理提供了參考體系結構。

安全代理設計為在受約束的 IoT 環境中工作,並且與它們使用的資源相比,它們提供的值是高度可定制的。

安全代理支援以下功能:

- 從基礎作業系統(Linux、Windows)收集原始安全事件。 要瞭解有關可用安全資料收集器的更多資訊,請參閱[Azure 安全中心進行 IoT 代理設定](how-to-agent-configuration.md)。

- 將原始安全事件聚合到通過 IoT 中心發送的消息中。

- 使用現有設備標識或專用模塊標識進行身份驗證。 有關詳細資訊,請參閱[安全代理身份驗證方法](concept-security-agent-authentication-methods.md)。

- 使用**Azureiot 安全**模組孿生進行遠端配置。 要瞭解更多資訊,請參閱[為 IoT 代理設定 Azure 安全中心](how-to-agent-configuration.md)。

IoT 安全代理的 Azure 安全中心開發為開源專案,可從 GitHub 獲得:

- [基於 IoT C 的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [基於 IoT C# 的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理支援的平臺

IoT 的 Azure 安全中心為 32 位元和 64 位 Windows 提供了不同的安裝程式代理,32 位元和 64 位 Linux 也提供了相同的安裝程式代理。 確保使用表格為每個裝置具有正確的代理安裝程式:

| 架構 | Linux | Windows |    詳細資料|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 位元  | C  | C#  ||
| 64 位元  | C# 或 C           | C#      | 我們建議對設備資源受限或最少的設備使用C代理。|
|

## <a name="next-steps"></a>後續步驟

在本文中,您瞭解了適用於 IoT 安全代理體系結構的 Azure 安全中心以及可用的安裝程式。

要繼續使用用於 IoT 部署的 Azure 安全中心,請使用以下文章:

- 瞭解[安全性代理身份驗證方法](concept-security-agent-authentication-methods.md)
- 選擇並部署[安全代理](how-to-deploy-agent.md)
- 檢視 Azure 安全中心,查看 IoT[服務先決條件](service-prerequisites.md)
- 瞭解如何在[IoT 中心啟用 IoT 服務的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 從[IoT 常見問題解答的 Azure 安全中心](resources-frequently-asked-questions.md)瞭解有關該服務
