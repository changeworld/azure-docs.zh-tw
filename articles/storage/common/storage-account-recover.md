---
title: 復原已刪除的儲存體帳戶
titleSuffix: Azure Storage
description: 瞭解如何在 Azure 入口網站中復原已刪除的儲存體帳戶。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357340"
---
# <a name="recover-a-deleted-storage-account"></a>復原已刪除的儲存體帳戶

在某些情況下，可能會從 Azure 入口網站中復原已刪除的儲存體帳戶。 若要復原儲存體帳戶，下列條件必須成立：

- 儲存體帳戶已在過去14天內刪除。
- 儲存體帳戶是使用 Azure Resource Manager 部署模型所建立。
- 自原始帳戶刪除之後，尚未建立具有相同名稱的新儲存體帳戶。

在您嘗試復原已刪除的儲存體帳戶之前，請確定該帳戶的資源群組存在。 如果資源群組已刪除，您就必須重新建立它。 不可能復原資源群組。 如需的詳細資訊，請參閱 [管理資源群組](../../azure-resource-manager/management/manage-resource-groups-portal.md)。

如果已刪除的儲存體帳戶使用客戶管理的金鑰搭配 Azure Key Vault 且金鑰保存庫也已刪除，則您必須先還原金鑰保存庫，然後再還原儲存體帳戶。 如需詳細資訊，請參閱 [Azure Key Vault recovery 總覽](../../key-vault/general/key-vault-recovery.md)。

> [!IMPORTANT]
> 不保證已刪除之儲存體帳戶的復原。 復原是盡力的嘗試。 Microsoft 建議鎖定資源以防止意外刪除帳戶。 如需資源鎖定的詳細資訊，請參閱 [鎖定資源以防止變更](../../azure-resource-manager/management/lock-resources.md)。
>
> 避免意外刪除帳戶的另一個最佳作法是，透過角色型存取控制來限制有權刪除帳戶的使用者數目 (Azure RBAC) 。 如需詳細資訊，請參閱 [AZURE RBAC 的最佳做法](../../role-based-access-control/best-practices.md)。

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>從 Azure 入口網站復原已刪除的帳戶

若要從另一個儲存體帳戶中復原已刪除的儲存體帳戶，請遵循下列步驟：

1. 流覽至 Azure 入口網站中現有儲存體帳戶的 [總覽] 頁面。
1. 在 [ **支援 + 疑難排解** ] 區段中，選取 [ **復原已刪除的帳戶**]。
1. 從下拉式清單中，選取要復原的帳戶，如下列影像所示。 如果您想要復原的儲存體帳戶不在下拉式清單中，則無法復原。

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="顯示如何在 Azure 入口網站中復原儲存體帳戶的螢幕擷取畫面":::

1. 選取 [ **復原** ] 按鈕來還原帳戶。 入口網站會顯示正在進行復原的通知。

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>透過支援票證來復原已刪除的帳戶

1. 在 Azure 入口網站中，流覽至 [說明 **+ 支援**]。
1. 選取 [新增支援要求]。
1. 在 [ **基本** ] 索引標籤的 [ **問題類型** ] 欄位中，選取 [ **技術**]。
1. 在 [ **訂** 用帳戶] 欄位中，選取包含已刪除之儲存體帳戶的訂用帳戶。
1. 在 [ **服務** ] 欄位中，選取 [ **儲存體帳戶管理**]。
1. 在 [ **資源** ] 欄位中，選取任何儲存體帳戶資源。 已刪除的儲存體帳戶將不會出現在清單中。
1. 新增問題的簡短摘要。
1. 在 [ **問題類型** ] 欄位中，選取 [ **刪除和** 復原]。
1. 在 [ **問題子類型** ] 欄位中，選取 [ **復原已刪除的儲存體帳戶**]。 下圖顯示要填寫的 [ **基本** ] 索引標籤範例：

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="顯示如何透過支援票證-基本索引標籤復原儲存體帳戶的螢幕擷取畫面":::

1. 接下來，流覽至 [ **方案** ] 索引標籤，然後選取 [ **由客戶控制的儲存體帳戶** 復原]，如下圖所示：

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="顯示如何透過支援票證復原儲存體帳戶的螢幕擷取畫面-解決方案索引標籤":::

1. 從下拉式清單中，選取要復原的帳戶，如下列影像所示。 如果您想要復原的儲存體帳戶不在下拉式清單中，則無法復原。

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="顯示如何透過支援票證復原儲存體帳戶的螢幕擷取畫面":::

1. 選取 [ **復原** ] 按鈕來還原帳戶。 入口網站會顯示正在進行復原的通知。

## <a name="next-steps"></a>後續步驟

- [儲存體帳戶概觀](storage-account-overview.md)
- [建立儲存體帳戶](storage-account-create.md)
- [升級至一般用途 v2 儲存體帳戶](storage-account-upgrade.md)
- [將 Azure 儲存體帳戶移至另一個區域](storage-account-move.md)