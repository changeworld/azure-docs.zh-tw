---
title: 如何使用 Azure 入口網站標記 VM
description: 瞭解如何使用 Azure 入口網站來標記虛擬機器。
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897403"
---
# <a name="tagging-a-vm-using-the-portal"></a>使用入口網站標記 VM

本文說明如何使用入口網站將標記新增至 VM。 標記是使用者定義的成對「索引鍵/值」，可直接置於資源或資源群組。 Azure 目前最多支援每個資源和資源群組50個標記。 標記可在建立或加入至現有資源時置於資源上。


1. 在入口網站中流覽至您的 VM。
1. 在 [ **基本** 功能] 中，選取 [ **按一下這裡加入標記**]。

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="VM 頁面的 [基本] 區段的螢幕擷取畫面。":::

1. 新增 [ **名稱** ] 和 [ **值**] 的值，然後選取 [ **儲存**]。

    :::image type="content" source="media/tag/key-value.png" alt-text="將機碼值組新增為標記之頁面的螢幕擷取畫面。":::

### <a name="next-steps"></a>後續步驟

- 如需深入了解如何標記您的 Azure 資源，請參閱 [Azure Resource Manager 概觀](../azure-resource-manager/management/overview.md)與[使用標記來組織您的 Azure 資源](../azure-resource-manager/management/tag-resources.md)。
- 若要瞭解標記如何協助您管理 Azure 資源的使用方式，請參閱 [瞭解您的 Azure 帳單](../cost-management-billing/understand/review-individual-bill.md)。
