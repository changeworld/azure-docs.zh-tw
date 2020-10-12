---
title: 使用 Azure 入口網站在直接對等互連上啟用 Azure 對等互連服務
titleSuffix: Azure
description: 使用 Azure 入口網站在直接對等互連上啟用 Azure 對等互連服務
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84700037"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>使用 Azure 入口網站在直接對等互連上啟用 Azure 對等互連服務

本文說明如何使用 Azure 入口網站，在直接對等互連上啟用 Azure 對 [等互連服務](overview-peering-service.md) 。

如果您想要的話，可以使用 [PowerShell](howto-peering-service-powershell.md) 完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請先檢查 [必要條件](prerequisites.md) 。
* 在您想要啟用對等互連服務的訂用帳戶中，選擇直接對等互連。 如果您沒有帳戶，請轉換舊版直接對等互連，或建立新的直接對等互連：
    * 若要轉換舊版直接對等互連，請遵循 [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)的指示。
    * 若要建立新的直接對等互連，請遵循 [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)中的指示。

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接對等互連上啟用對等互連服務

### <a name="view-direct-peering"></a><a name= get></a>查看直接對等互連
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>啟用對等互連服務的直接對等互連

當您在上一個步驟中開啟直接對等互連之後，請針對對等互連服務啟用此對等互連。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接對等互連連接

若要修改連接設定，請參閱 [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)中的「修改直接對等互連」一節。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改交換服務對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版交換服務對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

如需常見問題，請參閱對 [等互連服務常見問題](service-faqs.md)。