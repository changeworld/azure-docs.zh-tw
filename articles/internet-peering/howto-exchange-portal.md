---
title: 使用入口網站建立或修改 Exchange 對等互連
titleSuffix: Azure
description: 使用入口網站建立或修改 Exchange 對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774571"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>使用入口網站建立或修改 Exchange 對等互連

本文介紹如何使用門戶創建 Microsoft Exchange 對等互連。 本文還演示如何檢查資源的狀態、更新資源或刪除和取消預配資源。

如果您願意，您可以使用[PowerShell](howto-exchange-powershell.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始配置之前，請查看[先決條件](prerequisites.md)和[交換對等演練](walkthrough-exchange-all.md)。
* 如果已與 Microsoft 已具有 Exchange 對等互連（未轉換為 Azure 資源），請參閱[使用門戶將舊 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>創建和預配 Exchange 對等互連

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登錄到門戶並選擇訂閱
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>創建 Exchange 對等互連

可以使用**對等互連**資源創建新的對等互連請求。

#### <a name="launch-resource-and-configure-basic-settings"></a>啟動資源並配置基本設置
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>配置連接並提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>驗證 Exchange 對等互連
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>修改 Exchange 對等互連
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>取消預配 Exchange 對等互連
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>其他資源

有關詳細資訊，請訪問[互聯網對等常見問題解答](faqs.md)
