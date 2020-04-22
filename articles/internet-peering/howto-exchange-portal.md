---
title: 使用 Azure 門戶建立或修改 Exchange 對等互連
titleSuffix: Azure
description: 使用 Azure 門戶建立或修改 Exchange 對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680915"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>使用 Azure 門戶建立或修改 Exchange 對等互連

本文介紹如何使用 Azure 門戶創建 Microsoft Exchange 對等互連。 本文還演示如何檢查資源的狀態、更新資源或刪除和取消預配資源。

如果您願意,您可以使用[PowerShell](howto-exchange-powershell.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始設定之前,請檢視[先決條件](prerequisites.md)與[Exchange 對等演練](walkthrough-exchange-all.md)。
* 如果已與 Microsoft 的 Exchange 對等互連未轉換為 Azure 資源,請參閱[使用門戶將舊 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-portal.md)。

## <a name="create-and-provision-an-exchange-peering"></a>建立與預先 Exchange 對等互連

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登入門戶並選擇訂閱
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>建立 Exchange 對等互連

可以使用**對等互連**資源創建新的對等互連請求。

#### <a name="launch-the-resource-and-configure-basic-settings"></a>啟動資源並設定基本設定
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>設定連線並提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>認證 Exchange 對等互連
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>變更 Exchange 對等互連
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>取消預配 Exchange 對等互連
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用門戶建立或修改直接對等互連](howto-direct-portal.md)
* [使用門戶將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>其他資源

有關詳細資訊,請參閱[互聯網對等常見問題解答](faqs.md)。
