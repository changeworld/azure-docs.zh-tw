---
title: 使用 PowerShell 在直接對等互連上啟用 Azure 對等互連服務
titleSuffix: Azure
description: 使用 PowerShell 在直接對等互連上啟用 Azure 對等互連服務
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079042"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>使用 PowerShell 在直接對等互連上啟用 Azure 對等互連服務

本文說明如何使用 PowerShell Cmdlet 和 Azure Resource Manager 部署模型，在直接對等互連上啟用 Azure 對 [等互連服務](overview-peering-service.md) 。

如果您想要的話，您可以使用 Azure [入口網站](howto-peering-service-portal.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請先檢查 [必要條件](prerequisites.md) 。
* 在您想要啟用對等互連服務的訂用帳戶中，選擇直接對等互連。 如果您沒有帳戶，請轉換舊版直接對等互連，或建立新的直接對等互連：
    * 若要轉換舊版直接對等互連，請遵循 [使用 PowerShell 將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)的指示。
    * 若要建立新的直接對等互連，請遵循 [使用 PowerShell 建立或修改直接對等互連](howto-direct-powershell.md)中的指示。

### <a name="work-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接對等互連上啟用對等互連服務

### <a name="view-direct-peering"></a><a name= get></a>查看直接對等互連
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>啟用對等互連服務的直接對等互連

當您在上一個步驟中取得直接對等互連之後，請針對對等互連服務啟用它。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接對等互連連接

如果您需要修改連接設定，請參閱 [使用 PowerShell 建立或修改直接對等互連](howto-direct-powershell.md)中的「修改直接對等互連」一節。

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 建立或修改 Exchange 對等互連](howto-exchange-powershell.md)
* [使用 PowerShell 將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他資源
您可以執行下列命令來取得所有參數的詳細描述：

```powershell
Get-Help Get-AzPeering -detailed
```

如需常見問題，請參閱對 [等互連服務常見問題](service-faqs.md)。
