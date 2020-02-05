---
title: Azure VMware 解決方案（AVS）-AVS 節點配額
description: 說明 AVS 節點的配額限制，以及如何要求增加配額
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa1b056c8c96fb09def63ca1cd696fc2da5e9bed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019633"
---
# <a name="avs-node-quota-limits"></a>AVS 節點配額限制

當您的訂用帳戶啟用了「AVS」服務時，有四個節點是可用來購買的預設數量。 您可以從 Azure 入口網站購買任何[節點類型](cloudsimple-node.md)。 至少必須有三個相同 SKU 的節點，才能建立 AVS 私人雲端。 如果您已購買節點，當您嘗試購買其他節點時，可能會看到錯誤。

## <a name="quota-increase"></a>配額增加

您可以透過提交支援要求來增加節點配額。 服務營運小組會評估要求，並與您合作以增加節點配額。 當您開啟新的票證時，請選取下列選項：

* 問題類型：**技術**
* 訂用帳戶：**您的訂**用帳戶識別碼
* 服務類型：**由 AVS 的 VMware 解決方案**
* 問題類型：**專用節點配額**
* 問題子類型：**增加專用節點的配額**
* 主旨：**增加配額**

在支援票證的詳細資料中，提供必要的節點數目和節點 SKU。

* Node SKU
* 您正在要求增加配額的其他節點數目

## <a name="next-steps"></a>後續步驟

* [購買節點](create-nodes.md)
* [AVS 節點總覽](cloudsimple-node.md)
