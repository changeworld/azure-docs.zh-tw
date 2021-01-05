---
title: 安全性代理程式架構
description: 瞭解適用于 IoT 的 Azure Defender 服務中所使用之代理程式的安全性代理程式架構。
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 5773f7af3bb065976e8f05d7b54c58b90da2d3d2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835120"
---
# <a name="security-agent-reference-architecture"></a>安全性代理程式參考架構

適用于 IoT 的 Azure Defender 提供可透過 IoT 中樞記錄、處理、匯總和傳送安全性資料的安全性代理程式的參考架構。

安全性代理程式是設計用來在受限制的 IoT 環境中運作，而且在與其取用的資源相較之下，可高度自訂它們所提供的值。

安全性代理程式支援下列功能：

- 從基礎作業系統 (Linux、Windows) 收集原始安全性事件。 若要深入瞭解可用的安全性資料收集器，請參閱 [Defender For IoT 代理程式](how-to-agent-configuration.md)設定。

- 將原始安全性事件匯總至透過 IoT 中樞傳送的訊息。

- 使用現有的裝置身分識別或專用模組身分識別進行驗證。 若要深入瞭解，請參閱 [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md) 。

- 使用 **azureiotsecurity** 模組對應項來遠端設定。 若要深入瞭解，請參閱 [設定適用于 IoT 的 Defender 代理程式](how-to-agent-configuration.md)。

適用于 IoT 的 Defender 安全性代理程式會以開放原始碼專案的形式開發，並可從 GitHub 取得：

- [適用于 IoT C 型代理程式的 Defender](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [適用于 IoT c # 型代理程式的 Defender](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理程式支援的平臺

Defender for IoT 針對32位和64位的 Windows 提供不同的安裝程式代理程式，對於32位和64位的 Linux 則是相同的。 請根據下表，確定您的每個裝置都有正確的代理程式安裝程式：

| 架構 | Linux | Windows | 詳細資料 |
|--|--|--|--|
| 32 位元 | C | C# |  |
| 64 位元 | C # 或 C | C# | 針對具有更多限制或最少量裝置資源的裝置，我們建議使用 C 代理程式。 |


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解適用于 IoT 安全性代理程式架構的 Defender，以及可用的安裝程式。

若要繼續開始使用 Defender 進行 IoT 部署，請使用下列文章：

- 瞭解 [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
- 選取和部署 [安全性代理程式](how-to-deploy-agent.md)
- 查看 Defender for IoT [系統必要條件](quickstart-system-prerequisites.md)
- 瞭解如何 [在 Iot 中樞啟用 Defender For iot 服務](quickstart-onboard-iot-hub.md)
- 深入瞭解[Defender For IoT](resources-frequently-asked-questions.md)的服務常見問題
