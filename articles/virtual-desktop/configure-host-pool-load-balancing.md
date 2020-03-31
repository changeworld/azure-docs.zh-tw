---
title: 配置 Windows 虛擬桌面負載平衡 - Azure
description: 如何為 Windows 虛擬桌面環境配置負載平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128302"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>設定 Windows 虛擬桌面負載平衡方法

通過配置主機池的負載平衡方法，您可以調整 Windows 虛擬桌面環境以更好地滿足您的需求。

>[!NOTE]
> 這不適用於持久桌面主機池，因為使用者始終具有 1：1 映射到主機池中的工作階段主機。

## <a name="configure-breadth-first-load-balancing"></a>配置廣度優先負載平衡

寬度優先負載平衡是新的非持久性主機池的預設配置。 "最先"負載平衡將在主機池中的所有可用工作階段主機上分發新的使用者會話。 配置廣度優先負載平衡時，可以設置主機池中每個工作階段主機的最大會話限制。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](/powershell/windows-virtual-desktop/overview/)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後，請執行下列 Cmdlet 來登入您的帳戶：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

要將主機池配置為在不調整最大會話限制的情況下執行廣度優先負載平衡，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

要配置主機池以執行廣度優先負載平衡並使用新的最大會話限制，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>配置深度優先負載平衡

深度優先負載平衡將新使用者會話分發到連接次數最多但未達到其最大會話限制閾值的可用工作階段主機。 配置深度優先負載平衡時，**必須在**主機池中設置每個工作階段主機的最大會話限制。

要配置主機池以執行深度優先負載平衡，請運行以下 PowerShell Cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
