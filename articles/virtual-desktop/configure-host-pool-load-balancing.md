---
title: 設定 Windows 虛擬桌面負載平衡-Azure
description: 如何設定 Windows 虛擬桌面環境的負載平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 1b341732dd844ba172c7e682fb7089c98ca9c165
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367600"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>設定 Windows 虛擬桌面負載平衡方法

設定主機集區的負載平衡方法，可讓您調整 Windows 虛擬桌面環境，使其更符合您的需求。

>[!NOTE]
> 這不會套用至持續性桌面主機集區，因為使用者一律會有1:1 對應到主機集區中的工作階段主機。

## <a name="configure-breadth-first-load-balancing"></a>設定廣度優先的負載平衡

廣度優先的負載平衡是新的非持續性主機集區的預設設定。 廣度優先的負載平衡會將新的使用者會話分散到主機集區中所有可用的工作階段主機。 設定廣度優先的負載平衡時，您可以在主機集區中，為每個工作階段主機設定最大會話限制。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

若要設定主機集區以執行廣度優先的負載平衡，而不調整最大會話限制，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

若要將主機集區設定為執行廣度優先的負載平衡，並使用新的最大會話限制，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>設定深度優先的負載平衡

深度優先的負載平衡會將新的使用者會話散發到具有最大連線數目，但未達到其會話限制閾值上限的可用工作階段主機。 設定深度優先的負載平衡時，您**必須**為主機集區中的每個工作階段主機設定最大會話限制。

若要設定主機集區以執行深度優先的負載平衡，請執行下列 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
