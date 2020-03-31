---
title: 在 Azure 網站恢復中為添加的 Azure VM 磁片啟用複製
description: 本文介紹如何為添加到 Azure VM 的磁片啟用複製，該磁片已啟用用於使用 Azure 網站恢復進行災害復原
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973804"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>為添加到 Azure VM 的磁片啟用複製


本文介紹如何使用[Azure 網站恢復](site-recovery-overview.md)，為已啟用災害復原的 Azure VM 的資料磁片啟用複製。

對於具有託管磁片的 Azure VM，支援為添加到 VM 的磁片啟用複製。

將新磁片添加到複製到另一個 Azure 區域的 Azure VM 時，將發生以下情況：

-   VM 的複製運行狀況顯示警告，門戶中的注釋會通知您一個或多個磁片可供保護。
-   如果為添加的磁片啟用保護，則該警告將在磁片的初始複製後消失。
-   如果選擇不為磁片啟用複製，則可以選擇關閉該警告。

![添加了新磁片](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>開始之前

本文假定您已經為要向其添加磁片的 VM設置了災害復原。 如果沒有，請按照[Azure 到 Azure 災害復原教程](azure-to-azure-tutorial-enable-replication.md)。

## <a name="enable-replication-for-an-added-disk"></a>為新增的磁碟啟用複寫

要啟用添加磁片的複製，執行以下操作：

1. 在複製**專案**>保存庫中，按一下向其添加磁片的 VM。
2. 按一下**磁片**，然後選擇要為其啟用複製的資料磁片（這些磁片的狀態**未受保護**）。
3.  在**磁片詳細資訊**中，按一下啟用**複製**。

    ![為添加的磁片啟用複製](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

啟用複製作業運行並完成初始複製後，將刪除磁片問題的複製運行狀況警告。



## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
