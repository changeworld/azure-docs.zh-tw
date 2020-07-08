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
ms.openlocfilehash: 1c0620875e7da4582156080c4a52fd6fbe0e573c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700241"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>使用 PowerShell 建立或修改 Exchange 對等互連

本文說明如何使用 PowerShell Cmdlet 和 Resource Manager 部署模型來建立 Microsoft Exchange 對等互連。 本文也會說明如何檢查資源的狀態、加以更新，或刪除並取消布建。

如果您想要的話，可以使用 Azure[入口網站](howto-exchange-portal.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請閱讀[必要條件](prerequisites.md)和 [Exchange 對等互連逐步解說](walkthrough-exchange-all.md)。
* 如果您已有 Microsoft 的 Exchange 對等互連未轉換成 Azure 資源，請參閱[使用 PowerShell 將舊版 exchange 對等互連轉換成 azure 資源](howto-legacy-exchange-powershell.md)。

### <a name="work-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>建立及佈建 Exchange 對等互連

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>取得 Exchange 對等互連的支援對等位置清單
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>建立 Exchange 對等互連
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>取得 Exchange 對等互連
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>修改 Exchange 對等互連
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>取消佈建 Exchange 對等互連

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 建立或修改直接對等互連](howto-direct-powershell.md)
* [使用 PowerShell 將舊版直接對等互連轉換成 Azure 資源](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>其他資源
您可以執行下列命令來取得所有參數的詳細描述：

```powershell
Get-Help Get-AzPeering -detailed
```

如需詳細資訊，請參閱[網際網路對等互連常見問題集](faqs.md)。
