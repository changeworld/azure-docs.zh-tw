---
title: 刪除 Windows 虛擬桌面主機集區-Azure
description: 如何刪除 Windows 虛擬桌面中的主機集區。
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007584"
---
# <a name="delete-a-host-pool"></a>刪除主機集區

在 Windows 虛擬桌面中建立的所有主機集區會附加至工作階段主機和應用程式群組。 若要刪除主機集區，您需要刪除其相關聯的應用程式群組和工作階段主機。 刪除應用程式群組相當簡單，但刪除工作階段主機會更複雜。 當您刪除工作階段主機時，您必須確定它沒有任何作用中的使用者會話。 工作階段主機上的所有使用者會話都應該登出，以防止使用者遺失資料。

## <a name="delete-a-host-pool-with-powershell"></a>使用 PowerShell 刪除主機集區

若要使用 PowerShell 刪除主機集區，您必須先刪除主機集區中的所有應用程式群組。 若要刪除所有應用程式群組，請執行下列 PowerShell Cmdlet：

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

接下來，執行此 Cmdlet 以刪除主機集區：

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

此 Cmdlet 會移除主機集區工作階段主機上所有現有的使用者會話。 它也會從主機集區取消註冊工作階段主機。  (Vm) 的任何相關虛擬機器仍會存在於您的訂用帳戶中。

## <a name="delete-a-host-pool-with-the-azure-portal"></a>刪除具有 Azure 入口網站的主機集區

若要刪除 Azure 入口網站中的主機集區：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 搜尋並選取 **Windows 虛擬桌面**。

3. 在頁面左側的功能表中選取 [**主機**集區]，然後選取您想要刪除之主機集區的名稱。

4. 在頁面左側的功能表上，選取 [**應用程式群組**]。

5. 選取您要刪除之主機集區中的所有應用程式群組，然後選取 [**移除**]。

6. 移除應用程式群組之後，請移至頁面左側的功能表，然後選取 **[總覽**]。

7. 選取 [移除]。

8. 如果您要刪除的主機集區中有工作階段主機，您會看到一則訊息，要求您提供許可權以繼續。 選取 [是]。

9. Azure 入口網站現在將會移除所有工作階段主機，並刪除主機集區。 與會話主機相關的 Vm 將不會被刪除，而且會保留在您的訂用帳戶中。

## <a name="next-steps"></a>後續步驟

若要瞭解如何建立主機集區，請參閱下列文章：

- [透過 Azure 入口網站建立主機集區](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)

若要瞭解如何設定主機集區設定，請參閱下列文章：

- [自訂主機集區的遠端桌面通訊協定屬性](customize-rdp-properties.md)
- [設定 Windows 虛擬桌面負載平衡方法](configure-host-pool-load-balancing.md)
- [設定個人桌面主機集區指派類型](configure-host-pool-personal-desktop-assignment-type.md)