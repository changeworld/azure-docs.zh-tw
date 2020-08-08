---
title: 設定 Windows 虛擬桌面負載平衡-Azure
description: 如何設定 Windows 虛擬桌面環境的負載平衡方法。
author: Heidilohr
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07eae73a36bf4051925547fa375f46963a162881
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010101"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>設定 Windows 虛擬桌面負載平衡方法

設定主機集區的負載平衡方法，可讓您調整 Windows 虛擬桌面環境，使其更符合您的需求。

>[!NOTE]
> 這不會套用至持續性桌面主機集區，因為使用者一律會有1:1 對應到主機集區中的工作階段主機。

## <a name="prerequisites"></a>必要條件

本文假設您已遵循[設定 Windows 虛擬桌面 powershell 模組](powershell-module.md)中的指示，下載並安裝 powershell 模組，並登入您的 Azure 帳戶。

## <a name="configure-breadth-first-load-balancing"></a>設定廣度優先的負載平衡

廣度優先的負載平衡是新的非持續性主機集區的預設設定。 廣度優先的負載平衡會將新的使用者會話分散到主機集區中所有可用的工作階段主機。 設定廣度優先的負載平衡時，您可以在主機集區中，為每個工作階段主機設定最大會話限制。

若要設定主機集區以執行廣度優先的負載平衡，而不調整最大會話限制，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

之後，若要確定您已設定廣度優先的負載平衡方法，請執行下列 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

若要將主機集區設定為執行廣度優先的負載平衡，並使用新的最大會話限制，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>設定深度優先的負載平衡

深度優先的負載平衡會將新的使用者會話散發到具有最大連線數目，但未達到其會話限制閾值上限的可用工作階段主機。 設定深度優先的負載平衡時，您必須為主機集區中的每個工作階段主機設定最大會話限制。

若要設定主機集區以執行深度優先的負載平衡，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

若要確定設定已更新，請執行此 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>使用 Azure 入口網站設定負載平衡

您也可以使用 Azure 入口網站設定負載平衡。

若要設定負載平衡：

1. 登入 Azure 入口網站 https://portal.azure.com。
2. 搜尋並選取 [服務] 底下的**Windows 虛擬桌面**。
3. 在 [Windows 虛擬桌面] 頁面中，選取 [**主機**集區]。
4. 選取您想要編輯的主機集區名稱。
5. 選取 [屬性] 。
6. 在欄位中輸入 [**最大會話限制**]，然後在下拉式功能表中選取您想要用於此主機集區的**負載平衡演算法**。
7. 選取 [儲存]。 這會套用新的負載平衡設定。