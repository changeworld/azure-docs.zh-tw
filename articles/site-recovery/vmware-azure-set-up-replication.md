---
title: 使用 Azure Site Recovery 設定 VMware 嚴重損壞修復的複寫原則 |Microsoft Docs
description: 說明如何使用 Azure Site Recovery 設定從 VMware 災害復原至 Azure 的複寫設定。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257117"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>設定和管理 VMware 嚴重損壞修復的複寫原則

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 複寫至 Azure 時，如何設定複寫原則。

## <a name="create-a-policy"></a>建立原則

1. 選取 [**管理** > **Site Recovery 基礎結構**]。
2. 在 [VMware 和實體機器]**** 中選取 [複寫原則]****。
3. 按一下 [+複寫原則]****，並指定原則名稱。
4. 在 [ **rpo 臨界值**] 中，指定 rpo 限制。 連續複寫超過此限制時，會產生警示。
5. 在 [復原點保留]**** 中，指定每個復原點的保留週期 (以小時為單位)。 受保護的機器可以復原到保留週期內的任意點。 針對複寫到進階儲存體的機器支援最多保留 24 小時。 標準儲存體支援最多 72 小時。
6. 在 [**應用程式一致快照頻率**] 中，從下拉式清單中選擇要建立包含應用程式一致快照的復原點頻率（以小時為單位）。 如果您想要關閉產生應用程式一致性點，請選擇下拉式清單中的 [關閉] 值。
7. 按一下 [確定]  。 原則應該會在 30 至 60 秒內建立。

當您建立複寫原則時，會自動建立相符的容錯回復複寫原則，並加上 "failback" 尾碼。 原則建立之後, 您可以選取並按一下 [編輯設定]**** 加以編輯。

## <a name="associate-a-configuration-server"></a>關聯組態伺服器

將複寫原則與您的內部部署組態伺服器建立關聯。

1. 按一下 [關聯]****，選取組態伺服器。

    ![關聯組態伺服器](./media/vmware-azure-set-up-replication/associate1.png)
2. 按一下 [確定]  。 組態伺服器應該會在 1 至 2 分鐘內產生關聯。

    ![關聯組態伺服器](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>編輯原則

您可以在建立複寫原則之後加以修改。

- 原則中的變更會套用至所有使用原則的電腦。
- 如果您想要將複寫的機器與不同的複寫原則建立關聯，您必須停用並重新啟用相關機器的保護。

編輯原則，如下所示：
1. 選取 [**管理** > **Site Recovery 基礎結構** > **複寫原則**]。
2. 選取您想要修改的複寫原則。
3. 按一下 [**編輯設定**]，並視需要更新 [RPO 閾值]/[復原點保留時數]/[應用程式一致快照頻率] 欄位。
4. 如果您想要關閉產生應用程式一致性點，請在 [**應用程式一致快照頻率**] 欄位的下拉式清單中選擇 [關閉] 值。
5. 按一下 **[儲存]** 。 原則應該會在30到60秒內更新。



## <a name="disassociate-or-delete-a-replication-policy"></a>中斷關聯或刪除複寫原則

1. 選擇複寫原則。
    a. 若要中斷原則與組態伺服器的關聯，請確定沒有複寫機器使用該原則。 然後按一下 [中斷關聯]****。
    b. 若要刪除原則，請確定它沒有與組態伺服器相關聯。 然後，按一下 [**刪除**]。 刪除應需要 30-60 秒。
2. 按一下 [確定]  。
