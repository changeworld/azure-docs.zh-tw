---
title: 安全性代理程式架構
description: 瞭解 IoT 服務的 Azure 資訊安全中心中使用之代理程式的安全性代理程式架構。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310676"
---
# <a name="security-agent-reference-architecture"></a>安全性代理程式參考架構

適用于 IoT 的 Azure 資訊安全中心會提供安全性代理程式的參考架構，以透過 IoT 中樞記錄、處理、匯總和傳送安全性資料。

安全性代理程式是設計用來在受條件約束的 IoT 環境中工作，而且在與取用的資源相較之下，可高度自訂其所提供的值。

安全性代理程式支援下列功能：

- 從基礎作業系統（Linux、Windows）收集原始安全性事件。 若要深入瞭解可用的安全性資料收集器，請參閱[適用于 IoT 代理程式設定的 Azure 資訊安全中心](how-to-agent-configuration.md)。

- 將原始安全性事件匯總到透過 IoT 中樞傳送的訊息。

- 使用現有的裝置身分識別或專用模組身分識別進行驗證。 若要深入瞭解，請參閱[安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)。

- 透過使用**azureiotsecurity**模組對應項來遠端設定。 若要深入瞭解，請參閱[設定 IoT 代理程式的 Azure 資訊安全中心](how-to-agent-configuration.md)。

IoT 安全性代理程式的 Azure 資訊安全中心是以開放原始碼專案的方式開發，並可從 GitHub 取得：

- [以 IoT C 為基礎的代理程式 Azure 資訊安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [適用于 IoT c # 型代理程式的 Azure 資訊安全中心](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>代理程式支援的平臺

適用于 IoT 的 Azure 資訊安全中心提供32位和64位 Windows 的不同安裝程式代理程式，而32位和64位 Linux 則相同。 請根據下表，確定您的每個裝置都有正確的代理程式安裝程式：

| 架構 | Linux | Windows |    詳細資料|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 位元  | C  | C#  ||
| 64 位元  | C # 或 C           | C#      | 針對具有更多限制或最少裝置資源的裝置，我們建議使用 C 代理程式。|
|

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 IoT 安全性代理程式架構的 Azure 資訊安全中心，以及可用的安裝程式。

若要繼續開始使用 Azure 資訊安全中心進行 IoT 部署，請使用下列文章：

- 瞭解[安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
- 選取並部署[安全性代理程式](how-to-deploy-agent.md)
- 審查 IoT[服務必要條件](service-prerequisites.md)的 Azure 資訊安全中心
- 瞭解如何[在您的 IoT 中樞中啟用 IoT 服務的 Azure 資訊安全中心](quickstart-onboard-iot-hub.md)
- 深入瞭解[適用于 IoT 的 Azure 資訊安全中心](resources-frequently-asked-questions.md)的服務常見問題
