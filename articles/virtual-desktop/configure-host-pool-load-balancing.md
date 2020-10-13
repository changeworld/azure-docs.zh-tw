---
title: 設定 Windows 虛擬桌面負載平衡-Azure
description: 如何設定 Windows 虛擬桌面環境的負載平衡方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2c57ac10fbd318dd4bbb2dc86457e186dd824834
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951650"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>設定 Windows 虛擬桌面負載平衡方法

設定主機集區的負載平衡方法，可讓您調整 Windows 虛擬桌面環境，使其更符合您的需求。

>[!NOTE]
> 這並不適用于持續性桌面主機集區，因為使用者一律會有與主機集區中的工作階段主機之間的1:1 對應。

## <a name="prerequisites"></a>必要條件

本文假設您已遵循 [設定 Windows 虛擬桌面 powershell 模組](powershell-module.md) 中的指示，下載並安裝 powershell 模組並登入您的 Azure 帳戶。

## <a name="configure-breadth-first-load-balancing"></a>設定廣度優先的負載平衡

廣度優先的負載平衡是新的非持續性主機集區的預設設定。 廣度優先的負載平衡會將新的使用者會話分散到主機集區中所有可用的工作階段主機。 設定廣度優先的負載平衡時，您可以設定主機集區中每個工作階段主機的最大會話限制。

若要設定主機集區以執行廣度優先的負載平衡，而不調整最大會話限制，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

之後，為了確保您已設定廣度優先的負載平衡方法，請執行下列 Cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

若要設定主機集區以執行廣度優先的負載平衡，並使用新的最大會話限制，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>設定深度優先的負載平衡

深度優先的負載平衡會將新的使用者會話發佈到具有最大連線數目，但尚未達到會話限制閾值上限的可用工作階段主機。

>[!IMPORTANT]
>設定深度優先的負載平衡時，您必須設定主機集區中每個工作階段主機的最大會話限制。

若要設定主機集區以執行深度優先的負載平衡，請執行下列 PowerShell Cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> 深度優先的負載平衡演算法會根據最大工作階段主機限制 () ，將會話分配至工作階段主機 `-MaxSessionLimit` 。 這個參數的預設值是 `999999` ，這也是您可以將此變數設定為的最高可能數目。 當您使用深度優先的負載平衡演算法時，此為必要參數。 為了獲得最佳的使用者體驗，請務必將最大工作階段主機限制參數變更為最適合您環境的數位。

若要確認設定是否已更新，請執行此 Cmdlet：

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
2. 在 [服務] 下搜尋並選取 [ **Windows 虛擬桌面** ]。
3. 在 [Windows 虛擬桌面] 頁面中，選取 [ **主機**集區]。
4. 選取您要編輯之主機集區的名稱。
5. 選取 [屬性] 。
6. 在欄位中輸入 [ **最大會話限制** ]，然後在下拉式功能表中選取您要用於此主機集區的 **負載平衡演算法** 。
7. 選取 [儲存]。 這會套用新的負載平衡設定。