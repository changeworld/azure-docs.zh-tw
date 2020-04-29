---
title: 補救不相容的 Azure 自動化狀態設定伺服器
description: 如何視需要將設定重新套用至設定狀態已漂移的伺服器
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406073"
---
# <a name="remediate-noncompliant-dsc-servers"></a>補救不相容的 DSC 伺服器

當伺服器向 Azure 自動化狀態設定註冊時，設定模式會設為`ApplyOnly`、 `ApplyandMonitor`或。 `ApplyAndAutoCorrect` 如果模式未設定為`ApplyAndAutoCorrect`，則從相容狀態漂移的伺服器會保持不相容，直到手動更正為止。

Azure 計算提供名為執行命令的功能，可讓客戶在虛擬機器中執行腳本。
本檔提供手動修正設定漂移時，這項功能的範例腳本。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>使用 PowerShell 更正 Windows 虛擬機器的漂移

如需在 Windows 虛擬機器上使用執行命令功能的逐步指示，請參閱檔頁面[使用執行命令在您的 WINDOWS VM 中執行 PowerShell 腳本](/azure/virtual-machines/windows/run-command)。

若要強制 Azure 自動化狀態設定節點下載最新的設定並加以套用，請使用[update-dscconfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) Cmdlet。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>修正 Linux 虛擬機器的漂移

Linux 伺服器目前不提供類似的功能。
唯一的選項是重複註冊程式。
針對 Azure 節點，您可以從 Azure 入口網站或使用 Az module Cmdlet 來更正漂移。 如需此程式的詳細資料，請閱讀將[電腦上架以供管理，Azure 自動化狀態設定](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal)。
針對混合式節點，您可以使用內含的 Python 腳本來更正漂移。
請參閱[POWERSHELL DSC For Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)存放庫。

## <a name="next-steps"></a>後續步驟

- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)。