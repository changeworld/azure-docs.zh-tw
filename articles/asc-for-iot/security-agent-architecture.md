---
title: 瞭解 IoT 安全代理體系結構的 Azure 安全中心*微軟文檔
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
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596901"
---
# <a name="security-agent-reference-architecture"></a>安全代理引用體系結構

IoT 的 Azure 安全中心為通過 IoT 中心記錄、處理、聚合和發送安全資料的安全代理提供了參考體系結構。

安全代理設計為在受約束的 IoT 環境中工作，並且與它們使用的資源相比，它們提供的值是高度可定制的。

安全代理支援以下功能：

- 從基礎作業系統（Linux、Windows）收集原始安全事件。 要瞭解有關可用安全資料收集器的更多資訊，請參閱[Azure 安全中心進行 IoT 代理配置](how-to-agent-configuration.md)。

- 將原始安全事件聚合到通過 IoT 中心發送的消息中。

- 使用現有設備標識或專用模組標識進行身份驗證。 有關詳細資訊，請參閱[安全代理身份驗證方法](concept-security-agent-authentication-methods.md)。

- 使用**Azureiot 安全**模組孿生進行遠端配置。 要瞭解更多資訊，請參閱[為 IoT 代理配置 Azure 安全中心](how-to-agent-configuration.md)。

IoT 安全代理的 Azure 安全中心開發為開源專案，可從 GitHub 獲得： 

- [基於 IoT C 的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [基於 IoT C# 的代理的 Azure 安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理支援的平臺

IoT 的 Azure 安全中心為 32 位和 64 位 Windows 提供了不同的安裝程式代理，32 位和 64 位 Linux 也提供了相同的安裝程式代理。 確保根據下表為每個設備具有正確的代理安裝程式：

| 架構 | Linux | Windows |    詳細資料|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 位元  | C  | C#  ||
| 64 位元  | C# 或 C           | C#      | 我們建議對設備資源受限或最少的設備使用 C 代理。|
|

## <a name="next-steps"></a>後續步驟

在本文中，您瞭解了適用于 IoT 安全代理體系結構的 Azure 安全中心以及可用的安裝程式。

要繼續使用用於 IoT 部署的 Azure 安全中心，請使用以下文章：

- 瞭解[安全代理身份驗證方法](concept-security-agent-authentication-methods.md)
- 選擇並部署[安全代理](how-to-deploy-agent.md)
- 查看 Azure 安全中心，查看 IoT[服務先決條件](service-prerequisites.md)
- 瞭解如何在[IoT 中心啟用 IoT 服務的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 從[IoT 常見問題解答的 Azure 安全中心](resources-frequently-asked-questions.md)瞭解有關該服務
