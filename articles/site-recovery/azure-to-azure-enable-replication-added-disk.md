---
title: 在 Azure Site Recovery 中為新增的 Azure VM 磁片啟用複寫
description: 本文說明如何針對新增至 Azure VM 的磁片啟用複寫，以利用 Azure Site Recovery 啟用嚴重損壞修復
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75973804"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>針對新增至 Azure VM 的磁片啟用複寫


本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md)，針對新增至 azure VM 的資料磁片啟用複寫，並將其新增至另一個 azure 區域中已啟用的 azure VM。

具有受控磁片的 Azure Vm 支援為您新增至 VM 的磁片啟用複寫。

當您將新的磁片新增至複寫至另一個 Azure 區域的 Azure VM 時，會發生下列情況：

-   VM 的複寫健康情況會顯示警告，而入口網站中的附注會通知您有一或多個磁片可供保護。
-   如果您為新增的磁片啟用保護，則在磁片的初始複寫之後，警告將會消失。
-   如果您選擇不啟用磁片的複寫，您可以選擇關閉警告。

![新增的磁片](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>開始之前

本文假設您已為要新增磁片的 VM 設定嚴重損壞修復。 如果您尚未這麼做，請遵循 [azure 至 azure 的損毀修復教學](azure-to-azure-tutorial-enable-replication.md)課程。

## <a name="enable-replication-for-an-added-disk"></a>為新增的磁碟啟用複寫

若要為新增的磁片啟用複寫，請執行下列動作：

1. 在保存庫 > 複寫的 **專案**中，按一下您要新增磁片的 VM。
2. 按一下 [ **磁片**]，然後選取您要啟用複寫的資料磁片 (這些磁片具有 [ **未受保護** ] 狀態) 。
3.  在 [ **磁片詳細資料**] 中，按一下 [ **啟用**複寫]。

    ![為新增的磁片啟用複寫](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

啟用複寫作業執行且初始複寫完成之後，就會移除磁片問題的複寫健康情況警告。



## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
