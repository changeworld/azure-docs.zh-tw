---
title: 使用 PowerShell 建立或修改 Exchange 對等互連
titleSuffix: Azure
description: 使用 PowerShell 建立或修改 Exchange 對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d098f1ff0f78e37601544619c603b776e0cf22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071749"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>使用 PowerShell 建立或修改 Exchange 對等互連

本文說明如何使用 PowerShell Cmdlet 和 Resource Manager 部署模型來建立 Microsoft Exchange 對等互連。 本文也會說明如何檢查資源的狀態、加以更新，或刪除和取消布建。

如果您想要的話，您可以使用 Azure [入口網站](howto-exchange-portal.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請閱讀[必要條件](prerequisites.md)和 [Exchange 對等互連逐步解說](walkthrough-exchange-all.md)。
* 如果您已經有未轉換成 Azure 資源的 Exchange 對等互連與 Microsoft，請參閱 [使用 PowerShell 將舊版 exchange 對等互連轉換為 azure 資源](howto-legacy-exchange-powershell.md)。

### <a name="work-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>建立及佈建 Exchange 對等互連

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>取得 Exchange 對等互連支援的對等互連位置清單
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>建立 Exchange 對等互連
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>取得交換對等互連
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>修改 Exchange 對等互連
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>取消佈建 Exchange 對等互連

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 建立或修改直接對等互連](howto-direct-powershell.md)
* [使用 PowerShell 將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>其他資源
您可以執行下列命令來取得所有參數的詳細描述：

```powershell
Get-Help Get-AzPeering -detailed
```

如需詳細資訊，請參閱[網際網路對等互連常見問題集](faqs.md)。
