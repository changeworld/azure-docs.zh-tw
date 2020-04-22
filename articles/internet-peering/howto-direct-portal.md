---
title: 使用 Azure 門戶建立或修改直接對等互連
titleSuffix: Azure
description: 使用 Azure 門戶建立或修改直接對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681034"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>使用 Azure 門戶建立或修改直接對等互連

本文介紹如何使用 Azure 門戶創建 Microsoft 直接對等互連。 本文還演示如何檢查資源的狀態、更新資源或刪除和取消預配資源。

如果您願意,可以使用 Azure [PowerShell](howto-direct-powershell.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始設定之前,請檢視[先決條件](prerequisites.md)和[直接對等演練](walkthrough-direct-all.md)。
* 如果已與 Microsoft 具有未轉換為 Azure 資源的直接對等互連連接,請參閱[使用門戶將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)。

## <a name="create-and-provision-a-direct-peering"></a>建立並預先對等互連

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登入門戶並選擇訂閱
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>建立直接對等互連

可以使用**對等互連**資源創建新的對等互連請求。

#### <a name="launch-resource-and-configure-basic-settings"></a>啟動資源並設定基本設定
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>設定連線並提交
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>驗證直接對等互連
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>修改直接對等互連
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>取消預配直接對等互連
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用門戶建立或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用門戶將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

有關詳細資訊,請參閱[互聯網對等常見問題解答](faqs.md)。
