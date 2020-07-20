---
title: 補救不相容的 Azure 自動化狀態設定伺服器
description: 本文說明如何視需要將設定重新套用至設定狀態已漂移的伺服器。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: 4430b8cdfe9414ddbfd7aad3c3fe7827adbc8705
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186362"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>補救不相容的 Azure 自動化狀態設定伺服器

當伺服器向 Azure 自動化狀態設定註冊時，設定模式會設為 `ApplyOnly`、`ApplyandMonitor` 或 `ApplyAndAutoCorrect`。 如果模式不是設定為 `ApplyAndAutoCorrect`，則因任何原因而偏離符合規範狀態的伺服器，在手動更正之前都會保持不符合規範。

Azure 計算提供名為「執行命令」的功能，可讓客戶在虛擬機器中執行指令碼。
本文件提供以這項功能手動更正設定漂移的範例指令碼。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>使用 PowerShell 更正 Windows 虛擬機器的漂移

您可以使用 `Run` 命令功能來更正 Windows 虛擬機器的漂移。 請參閱[使用執行命令在 Windows VM 中執行 PowerShell 指令碼](../virtual-machines/windows/run-command.md)。

若要強制 Azure 自動化狀態設定節點下載最新的設定並加以套用，請使用 [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) Cmdlet。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>更正 Linux 虛擬機器的漂移

針對 Linux 虛擬機器，您無法選擇使用 `Run` 命令。 只能以重複註冊程序的方式為這些機器更正漂移。 

針對 Azure 節點，您可以從 Azure 入口網站或使用 Az module Cmdlet 來更正漂移。 此程序的詳細資料記載於[使用 Azure 入口網站啟用 VM](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal) 之中。

針對混合式節點，您可以使用 Python 指令碼來更正漂移。 請參閱 [從 Linux 電腦執行 DSC 作業](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)。

## <a name="next-steps"></a>後續步驟

- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
- 如需在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
