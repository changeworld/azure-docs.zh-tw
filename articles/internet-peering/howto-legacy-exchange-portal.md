---
title: 使用入口網站將舊版直接對等互連轉換為 Azure 資源
titleSuffix: Azure
description: 使用入口網站將舊版直接對等互連轉換為 Azure 資源
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775195"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源

本文介紹如何使用門戶將現有舊版 Exchange 對等互連轉換為 Azure 資源。

如果您願意，您可以使用[PowerShell](howto-legacy-exchange-powershell.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始配置之前，請查看[先決條件](prerequisites.md)和[交換對等演練](walkthrough-exchange-all.md)。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>將舊版 Exchange 對等互連轉換為 Azure 資源

### <a name="sign-in-to-portal-and-select-your-subscription"></a>登錄到門戶並選擇訂閱
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>轉換舊交易所對等互連

您可以使用**對等互連**資源轉換舊體向互連連接。

#### <a name="launch-resource-and-configure-basic-settings"></a>啟動資源並配置基本設置
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>配置連接並提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>驗證交換對等互連
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>其他資源

有關詳細資訊，請訪問[互聯網對等常見問題解答](faqs.md)

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改 Exchange 對等互連](howto-exchange-portal.md)
