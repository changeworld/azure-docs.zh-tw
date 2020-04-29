---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174372"
---
### <a name="troubleshoot-azure-diagnostics"></a>針對 Azure 診斷進行疑難排解

如果您收到下列錯誤訊息，則未註冊 Microsoft.insights 資源提供者︰

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

若要註冊資源提供者，請在 Azure 入口網站中執行下列步驟：

1.  在左側的導覽窗格中，按一下 [訂用帳戶]**。
2.  選取錯誤訊息中識別的訂用帳戶
3.  按一下 [資源提供者]**
4.  尋找 Microsoft.insights** 提供者
5.  按一下 [註冊]** 連結

![註冊 microsoft.insights 資源提供者](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

註冊 Microsoft.insights** 資源提供者後，請重試設定診斷。


在 PowerShell 中，如果您收到下列錯誤訊息，則需要更新 PowerShell 版本︰

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

更新您的 Azure PowerShell 版本，遵循[安裝 Azure PowerShell](/powershell/azure/install-az-ps)文章中的指示。
