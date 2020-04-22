---
title: 使用 PowerShell 建立或修改直接對等互連
titleSuffix: Azure
description: 使用 PowerShell 建立或修改直接對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680813"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>使用 PowerShell 建立或修改直接對等互連

本文介紹如何使用 PowerShell cmdlet 和 Azure 資源管理器部署模型創建 Microsoft 直接對等互連。 本文還演示如何檢查資源的狀態、更新資源或刪除和取消預配資源。

如果您願意,可以使用 Azure[門戶](howto-direct-portal.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始設定之前,請檢視[先決條件](prerequisites.md)和[直接對等演練](walkthrough-direct-all.md)。
* 如果已與 Microsoft 具有未轉換為 Azure 資源的直接對等互連連線,請參閱[使用 PowerShell 將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)。

### <a name="work-with-azure-powershell"></a>使用 Azure 電源外殼
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>建立並預先對等互連

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>取得支援對等位置的清單,以便直接對等互連
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>建立直接對等互連
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>驗證直接對等互連
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>修改直接對等互連
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>取消預配直接對等互連
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 建立或修改 Exchange 對等互連](howto-exchange-powershell.md)
* [使用 PowerShell 將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他資源
您可以執行下列命令來取得所有參數的詳細描述：

```powershell
Get-Help Get-AzPeering -detailed
```

有關詳細資訊,請參閱[互聯網對等常見問題解答](faqs.md)。
