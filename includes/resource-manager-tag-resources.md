---
title: 包含檔案
description: 包含檔案
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80132216"
---
如果使用者沒有套用標記的必要存取權，您可以將**標記參與者**角色指派給使用者。 如需詳細資訊，請參閱[教學課程：使用 RBAC 和 Azure 入口網站來授與使用者對 Azure 資源的存取權](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)。

1. 若要查看資源或資源群組的標記，請在總覽中尋找現有的標記。 如果您先前尚未套用標記，清單就會是空的。

   ![檢視資源或資源群組的標記](./media/resource-manager-tag-resources/view-tags.png)

1. 若要新增標記，請選取 [按一下這裡以新增標記]****。

1. 提供名稱和值。

   ![新增標記](./media/resource-manager-tag-resources/add-tag.png)

1. 視需要繼續新增標記。 完成時，選取 [儲存]****。

   ![儲存標記](./media/resource-manager-tag-resources/save-tags.png)

1. 這些標記現在會顯示在 [概觀] 中。

   ![顯示標記](./media/resource-manager-tag-resources/view-new-tags.png)

1. 若要新增或刪除標記，請選取 [變更]****。

1. 若要刪除標記，請選取垃圾桶圖示。 然後選取 [儲存]。

   ![刪除標記](./media/resource-manager-tag-resources/delete-tag.png)

將標籤大量指派給多個資源：

1. 從任何資源的清單中，勾選您要指派標記的資源核取方塊。 然後，選取 [**指派**標籤]。

   ![選取多個資源](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. 新增名稱和值。 完成時，選取 [儲存]****。

   ![選取 [指派]](./media/resource-manager-tag-resources/select-assign.png)

若要檢視具有標記的所有資源：

1. 在 [Azure 入口網站] 功能表上，搜尋**標記**。 從可用的選項中選取它。

   ![依標記尋找](./media/resource-manager-tag-resources/find-tags-general.png)

1. 選取標記以檢視資源。

   ![選取標記](./media/resource-manager-tag-resources/select-tag.png)

1. 會顯示具有該標記的所有資源。

   ![依標記檢視資源](./media/resource-manager-tag-resources/view-resources-by-tag.png)
