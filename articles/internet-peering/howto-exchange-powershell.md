---
title: 使用 PowerShell 建立或修改 Exchange 對等互連
titleSuffix: Azure
description: 使用 PowerShell 建立或修改 Exchange 對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678640"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>使用 PowerShell 建立或修改 Exchange 對等互連

本文介紹如何使用 PowerShell cmdlet 和資源管理器部署模型創建 Microsoft Exchange 對等互連。 本文還演示如何檢查資源的狀態、更新資源或刪除和取消預配資源。

如果您願意,可以使用 Azure[門戶](howto-exchange-portal.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始設定之前,請檢視[先決條件](prerequisites.md)與[Exchange 對等演練](walkthrough-exchange-all.md)。
* 如果已與 Microsoft 的 Exchange 對等互連未轉換為 Azure 資源,請參閱[使用 PowerShell 將舊 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)。

### <a name="work-with-azure-powershell"></a>使用 Azure 電源外殼
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>建立與預先 Exchange 對等互連

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>取得 Exchange 對等互連支援對等位置的清單
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>建立 Exchange 對等互連
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>取得交換對等互連
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>變更 Exchange 對等互連
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>取消預配 Exchange 對等互連

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 建立或修改直接對等互連](howto-direct-powershell.md)
* [使用 PowerShell 將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>其他資源
您可以執行下列命令來取得所有參數的詳細描述：

```powershell
Get-Help Get-AzPeering -detailed
```

有關詳細資訊,請參閱[互聯網對等常見問題解答](faqs.md)。
