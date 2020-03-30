---
title: 使用 Azure 網站恢復設置 VMware 災害復原的複寫原則*微軟文檔
description: 說明如何使用 Azure Site Recovery 設定從 VMware 災害復原至 Azure 的複寫設定。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257117"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>配置和管理 VMware 災害復原的複寫原則

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 複寫至 Azure 時，如何設定複寫原則。

## <a name="create-a-policy"></a>建立原則

1. 選擇 **"管理** > **網站恢復基礎結構**"。
2. 在 [VMware 和實體機器]**** 中選取 [複寫原則]****。
3. 按一下 [+複寫原則]****，並指定原則名稱。
4. 在**RPO 閾值**中 ，指定 RPO 限制。 連續複寫超過此限制時，會產生警示。
5. 在 [復原點保留]**** 中，指定每個復原點的保留週期 (以小時為單位)。 受保護的機器可以復原到保留週期內的任意點。 針對複寫到進階儲存體的機器支援最多保留 24 小時。 標準儲存體支援最多 72 小時。
6. 在**應用一致的快照頻率**中，從下拉清單中選擇應創建包含應用程式一致快照的復原點的頻率（以小時表示）。 如果要關閉生成應用程式一致性點，請選擇下拉清單中的"關閉"值。
7. 按一下 [確定]****。 原則應該會在 30 至 60 秒內建立。

當您建立複寫原則時，會自動建立相符的容錯回復複寫原則，並加上 "failback" 尾碼。 原則建立之後, 您可以選取並按一下 [編輯設定]**** 加以編輯。

## <a name="associate-a-configuration-server"></a>關聯組態伺服器

將複寫原則與您的內部部署組態伺服器建立關聯。

1. 按一下 [關聯]****，選取組態伺服器。

    ![關聯組態伺服器](./media/vmware-azure-set-up-replication/associate1.png)
2. 按一下 [確定]****。 組態伺服器應該會在 1 至 2 分鐘內產生關聯。

    ![關聯組態伺服器](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>編輯原則

創建複寫原則後，可以對其進行修改。

- 策略中的更改將應用於使用 該策略的所有電腦。
- 如果要將複製的電腦與其他複寫原則相關聯，則需要禁用並重新啟用相關電腦的保護。

編輯策略如下：
1. 選擇 **"管理** > **網站恢復基礎結構** > **複寫原則**"。
2. 選擇要修改的複寫原則。
3. 按一下 **"編輯設置**"，並根據需要更新 RPO 閾值/復原點保留時間/應用一致的快照頻率欄位。
4. 如果要關閉生成應用程式一致性點，請在現場**應用一致的快照頻率**的下拉清單中選擇"關閉"值。
5. 按一下 [儲存]****。 策略應在 30 到 60 秒內更新。



## <a name="disassociate-or-delete-a-replication-policy"></a>中斷關聯或刪除複寫原則

1. 選擇複寫原則。
    a. 若要中斷原則與組態伺服器的關聯，請確定沒有複寫機器使用該原則。 然後按一下 [中斷關聯]****。
    b. 若要刪除原則，請確定它沒有與組態伺服器相關聯。 然後，按一下"**刪除**"。 刪除應需要 30-60 秒。
2. 按一下 [確定]****。
