---
title: 使用 Azure 門戶在直接對等互連上啟用 Azure 對等互連服務
titleSuffix: Azure
description: 使用 Azure 門戶在直接對等互連上啟用 Azure 對等互連服務
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687062"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>使用 Azure 門戶在直接對等互連上啟用 Azure 對等互連服務

本文介紹如何使用 Azure 門戶在直接對等互連上啟用 Azure[對等](overview-peering-service.md)服務。

如果您願意,您可以使用[PowerShell](howto-peering-service-powershell.md)完成本指南。

## <a name="before-you-begin"></a>開始之前
* 在開始設定之前,請檢視[先決條件](prerequisites.md)。
* 在訂閱中選擇要為其啟用對等服務的直接對等互連。 如果沒有,請轉換舊版直接對等互連或創建新的直接對等互連:
    * 要轉換舊版直接對等互連,請[按照使用門戶將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)的說明進行操作。
    * 要建立新的直接對等互連,請[以建立或修改「直接對等」中的說明使用門戶](howto-direct-portal.md)。

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接對等互連上啟用對等互連服務

### <a name="view-direct-peering"></a><a name= get></a>檢視直接對等互連
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>為對等互連服務啟用直接對等互連

在上一步驟中打開直接對等互連后,將其啟用到對等服務。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>變更直接對等互連連線

要修改連接設定,請參閱"建立"''修改直接對等互連"部分[,或使用門戶修改直接對等互連](howto-direct-portal.md)。

## <a name="next-steps"></a>後續步驟

* [使用門戶建立或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用門戶將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

有關常見問題,請參閱[對等服務常見問題解答](service-faqs.md)。