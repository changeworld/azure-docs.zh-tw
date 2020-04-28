---
title: 在 Azure 中重新部署 Windows 虛擬機器 | Microsoft Docs
description: 如何在 Azure 中重新部署 Windows 虛擬機器，以減輕 RDP 連線問題。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "71058631"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>將 Windows 虛擬機器重新部署至新的 Azure 節點
如果您在疑難排解以 Windows 為基礎的 Azure 虛擬機器 (VM) 的遠端桌面 (RDP) 連線或應用程式存取時一直遇到問題，重新部署 VM 也許可以解決。 重新部署 VM 時，Azure 會關閉 VM 並將 VM 移到 Azure 基礎結構內的新節點，然後重新開啟它的電源，所有組態選項和相關聯的資源都會保留。 本文將說明如何使用 Azure PowerShell 或 Azure 入口網站來重新部署 VM。

> [!NOTE]
> 重新部署 VM 之後，暫存磁碟會遺失，而系統會更新與虛擬網路介面關聯的動態 IP 位址。 


## <a name="using-azure-powershell"></a>使用 Azure PowerShell
確定您的電腦上已安裝最新版本的 Azure PowerShell 1.x。 如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

下列範例會部署名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM：

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>後續步驟
如果您在連接至 VM 時發生問題，您可以在[針對 RDP 連線進行疑難排解](troubleshoot-rdp-connection.md)或[詳細的 RDP 疑難排解步驟中找到具體的說明](detailed-troubleshoot-rdp.md)。 如果無法存取在您 VM 上執行的應用程式，您也可以參閱[應用程式疑難排解問題](../windows/troubleshoot-app-connection.md)。

