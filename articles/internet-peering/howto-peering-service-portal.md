---
title: 使用入口網站在直接對等互連上啟用對等互連服務
titleSuffix: Azure
description: 使用入口網站在直接對等互連上啟用對等互連服務
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129908"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>使用入口網站在直接對等互連上啟用對等互連服務

本文介紹如何使用門戶在直接對等互連上啟用[對等互連服務](overview-peering-service.md)。

如果您願意，您可以使用[PowerShell](howto-peering-service-powershell.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始配置之前，請查看[先決條件](prerequisites.md)。
* 選擇要啟用對等服務的訂閱中的直接對等互連。 如果沒有，請轉換舊版直接對等互連或創建新的直接對等互連。
    * 要轉換舊版直接對等互連，請按照將[舊版直接對等互連轉換為 Azure 資源的說明使用門戶](howto-legacy-direct-portal.md)。
    * 要創建新的直接對等互連，請按照[創建或修改使用門戶中的直接對等互連中的](howto-direct-portal.md)說明。

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接對等互連上啟用對等互連服務

### <a name="view-direct-peering"></a><a name= get></a>查看直接對等互連
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>為對等互連服務啟用直接對等互連

在上一步驟中打開直接對等互連後，將其啟用到對等服務。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接對等互連連接

如果需要修改連接設置，請參閱在"創建 **"中修改直接對等部分**[，或使用門戶修改直接對等互連](howto-direct-portal.md)

## <a name="next-steps"></a>後續步驟

* [使用門戶創建或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

有關常見問題，請參閱[對等服務常見問題解答](service-faqs.md)。