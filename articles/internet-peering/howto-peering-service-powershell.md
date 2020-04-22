---
title: 使用 PowerShell 在直接對等互連上啟用 Azure 對等互連服務
titleSuffix: Azure
description: 使用 PowerShell 在直接對等互連上啟用 Azure 對等互連服務
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686986"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>使用 PowerShell 在直接對等互連上啟用 Azure 對等互連服務

本文介紹如何使用 PowerShell cmdlet 和 Azure 資源管理員部署模型在直接對等互連上啟用 Azure[對等服務](overview-peering-service.md)。

如果您願意,可以使用 Azure[門戶](howto-peering-service-portal.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始設定之前,請檢視[先決條件](prerequisites.md)。
* 在訂閱中選擇要為其啟用對等服務的直接對等互連。 如果沒有,請轉換舊版直接對等互連或創建新的直接對等互連:
    * 要轉換舊版直接對等互連,請[按照使用 PowerShell 將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)的說明進行操作。
    * 要創建新的直接對等互連,請使用 PowerShell 按照["創建"或修改直接對等互連](howto-direct-powershell.md)中的說明。

### <a name="work-with-azure-powershell"></a>使用 Azure 電源外殼
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接對等互連上啟用對等互連服務

### <a name="view-direct-peering"></a><a name= get></a>檢視直接對等互連
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>為對等互連服務啟用直接對等互連

在上一步驟中獲取直接對等互連后,將其啟用到對等服務。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>變更直接對等互連連線

如果需要修改連接設置,請參閱[使用 PowerShell 創建或修改直接對等互連](howto-direct-powershell.md)中的「修改直接對等互連」部分。

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 建立或修改 Exchange 對等互連](howto-exchange-powershell.md)
* [使用 PowerShell 將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他資源
您可以執行下列命令來取得所有參數的詳細描述：

```powershell
Get-Help Get-AzPeering -detailed
```

有關常見問題,請參閱[對等服務常見問題解答](service-faqs.md)。