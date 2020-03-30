---
title: 在 Azure 網站恢復中設置進程伺服器 VMware/物理故障恢復
description: 本文說明如何在 Azure 中設定處理序伺服器，用於 Azure VMs 到 VMware 的容錯回復。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083960"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>在 Azure 中設定容錯回復的處理序伺服器

使用 [Site Recovery](site-recovery-overview.md) 將 VMware VM 或實體伺服器容錯移轉到 Azure 後，當它們又恢復運作時，可以再容錯回復到內部部署網站。 為了容錯回復，您必須在 Azure 中設定暫時處理序伺服器，以處理從 Azure 到內部部署的複寫。 容錯回復完成後，便可以刪除此 VM。

## <a name="before-you-start"></a>開始之前

深入了解[重新保護](vmware-azure-reprotect.md)和[容錯回復](vmware-azure-failback.md)程序。

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>在 Azure 中部署處理序伺服器

1. 在網站**恢復基礎結構**> **管理** > **佈建服務器**>保存庫中，選擇佈建服務器。
2. 在伺服器頁面中，按一下 [+ 處理序伺服器]****。
3. 在 [新增處理序伺服器]**** 頁面中，選取在 Azure 中部署處理序伺服器。
4. 指定 Azure 的設定，包括用於容錯移轉的訂用帳戶、資源群組、用於容錯移轉的 Azure 區域、Azure VM 所在的虛擬網路。 如果您使用多個 Azure 網路，每個網路中都要有一個處理序伺服器。

   ![新增處理伺服器資源庫項目](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. 在 [伺服器名稱]****、[使用者名稱]****、[密碼]**** 中，指定處理序伺服器的名稱，以及將指派為伺服器上系統管理員權限的認證。
5. 指定要用於伺服器 VM 磁碟的儲存體帳戶、處理序伺服器 VM 所在的子網路、以及在 VM 啟動時要指派的伺服器 IP 位址。
6. 按一下 [確定]**** 按鈕開始部署處理序伺服器 VM。 進程伺服器將部署在Standard_A8_v2 SKU 上。 確保此 VM SKU 可用於您的訂閱。

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>將處理伺服器 (在 Azure 中執行) 註冊到組態伺服器 (在內部部署上執行)

處理序伺服器 VM 啟動並運作後，您必須向內部部署組態伺服器註冊它，做法如下：

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


