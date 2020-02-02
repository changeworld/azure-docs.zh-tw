---
title: 管理 Azure DNS 中的 DNS 區域 - Azure 入口網站 | Microsoft Docs
description: 您可以使用 Azure 入口網站管理 DNS 區域。 本文說明如何在 Azure DNS 上更新、刪除及建立 DNS 區域
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936766"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>如何在 Azure 入口網站中管理 DNS 區域

> [!div class="op_single_selector"]
> * [入口網站](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure 傳統 CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

本文說明如何使用 Azure 入口網站管理 DNS 區域。 您也可以使用跨平台的 [Azure CLI](dns-operations-dnszones-cli.md) 或 Azure [PowerShell](dns-operations-dnszones.md) 來管理 DNS 區域。

## <a name="create-a-dns-zone"></a>建立 DNS 區域

1. 登入 Azure 入口網站
2. 在 [中樞] 功能表上，瀏覽至 [建立資源] > [網路] > [DNS 區域] 以開啟 [建立 DNS 區域] 刀鋒視窗。

    ![DNS 區域](./media/dns-operations-dnszones-portal/openzone650.png)

4. 在 [建立 DNS 區域] 刀鋒視窗中輸入下列的值，然後按一下 [建立]：


   | **設定** | **ReplTest1** | **詳細資料** |
   |---|---|---|
   |**名稱**|contoso.com|DNS 區域的名稱|
   |**訂用帳戶**|[您的訂用帳戶]|選取要建立 DNS 區域的訂用帳戶。|
   |**資源群組**|**建立新的︰** contosoDNSRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

> [!NOTE]
> 資源群組是指資源群組的位置，不會對 DNS 區域有任何影響。 DNS 區域一定是「全域」位置，並不會顯示出來。

## <a name="list-dns-zones"></a>列出 DNS 區域

在 Azure 入口網站中，巡覽至 [More services] \(更多服務) > [網路] > [DNS 區域]。 每個 DNS 區域都是它自己的資源，而記錄集數目和名稱伺服器數目等資訊皆可在此檢視中檢視。 **名稱伺服器**資料行不在預設檢視中。 若要新增它，請按一下 [資料行]、選取 [名稱伺服器]，然後按一下 [完成]。

![列出 DNS 區域](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

巡覽至入口網站中的 DNS 區域。 在 [DNS 區域] 刀鋒視窗中，按一下 [刪除區域]。 系統接著會提示您確認要刪除 DNS 區域。 刪除 DNS 區域也會刪除該區域包含的所有記錄。

## <a name="next-steps"></a>後續步驟

了解如何使用 DNS 區域和記錄，請瀏覽[使用 Azure 入口網站開始使用 Azure DNS](dns-getstarted-portal.md)。