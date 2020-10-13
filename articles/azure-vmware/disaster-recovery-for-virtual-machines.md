---
title: 完成虛擬機器的嚴重損壞修復
description: 本文說明如何使用 Azure VMware 解決方案完成虛擬機器的嚴重損壞修復
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579671"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>使用 Azure VMware 解決方案完成虛擬機器的嚴重損壞修復

本文包含的程式可完成虛擬機器的災難復原， (Vm) VMware HCX 解決方案，並使用 Azure VMware 解決方案私人雲端作為復原或目標網站。

VMware HCX 提供各種作業，可在複寫原則中提供細微的控制和細微性。 可用的作業包括：

- **反向** –發生災難之後。 反向有助於讓網站 B 成為受保護 VM 現在存留所在的來源網站和網站 A。

- **暫停** –暫停與選取之 VM 相關聯的目前複寫原則。

- **繼續** -暫停與選取的 VM 相關聯的目前複寫原則。

- **移除** -移除與所選 VM 相關聯的目前複寫原則。

- **立即同步** 至受保護 VM 的系結同步來源 VM。

本指南涵蓋下列複寫案例：

- 保護 VM 或一組 Vm。

- 完成 VM 或 vm 群組的測試復原。

- 復原 VM 或 Vm 群組。

- 反向保護 VM 或 Vm 群組。

## <a name="protect-vms"></a>保護 VM

1. 登入來源網站上的 **VSphere 用戶端** ，並存取 **HCX 外掛程式**。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="VSphere 中的 HCX 選項" border="true":::

1. 輸入 [嚴重損壞 **修復** ] 區域，然後選取 [ **保護 vm**]。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. 選取來源和遠端網站。 此案例中的遠端網站應為 Azure VMware 解決方案私人雲端。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="VSphere 中的 HCX 選項" border="true":::

1. 如有需要，請選取 **預設** 的複寫選項：

   - **啟用壓縮：** 針對低輸送量案例，建議使用。

   - **啟用靜止：** 暫停 VM，以確保將一致的複本同步處理至遠端網站。

   - **目的地儲存體：** 適用于受保護 Vm 的遠端資料存放區，以及在 Azure VMware 解決方案私人雲端中，這應該是 vSAN 資料存放區。

   - **計算容器：** 遠端 vSphere 叢集或資源集區。

   - **目的地資料夾：** 遠端目的地資料夾是選擇性的，如果未選取任何資料夾，則 Vm 會直接放在選取的叢集下。

   - **RPO：** 來源 VM 與受保護 VM 之間的同步處理間隔，可從5分鐘到24小時的任何時間。

   - **快照間隔：** 快照之間的間隔。

   - **快照集數目：** 在設定的快照間隔內的快照集總數。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. 從清單中選取一或多個 Vm，並視需要設定複寫選項。

   根據預設，Vm 會繼承在預設複寫選項中設定的全域設定原則。 針對所選 VM 中的每個網路介面，設定 [遠端 **網路埠] 群組** ，然後選取 **[完成]** 以開始保護處理常式。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. 在相同的嚴重損壞修復區域中，監視每個所選 Vm 的進程。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. 在 VM 受到保護之後，您可以在 [ **快照** 集] 索引標籤中查看不同的快照集。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   黃色三角形表示快照集和虛擬的測試尚未在測試復原操作中測試過。

   已關閉電源的 VM 與開啟電源的 VM 之間有一些主要差異。 此映射會顯示已開機的 VM 的同步處理常式。 它會啟動同步處理常式，直到它完成第一個快照集（也就是 VM 的完整複本），然後以設定的間隔完成下一個快照集。 針對已關閉電源的 VM，它會同步複本，然後 VM 會顯示為 [非作用中]，且保護作業會顯示為 [已完成]。  當 VM 開機時，它會開始同步處理至遠端網站。

## <a name="complete-a-test-recover-of-vms"></a>完成 Vm 的測試復原

1. 登入遠端網站上的 **VSphere 用戶端** ，也就是 Azure VMware 解決方案私人雲端。 
1. 在 **HCX 外掛程式**的 [嚴重損壞修復] 區域中，選取任何 VM 上的垂直省略號，以顯示操作功能表，然後選取 [ **測試復原 VM**]。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="VSphere 中的 HCX 選項" border="true":::

1. 選取測試的選項，以及您想要用來測試 VM 不同狀態的快照集。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="VSphere 中的 HCX 選項" border="true":::

1. 選取 [ **測試**] 之後，就會開始復原操作。

1. 完成時，您可以在 Azure VMware 解決方案私人雲端 vCenter 中檢查新的 VM。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. 在 VM 或任何執行的應用程式上完成測試之後，請執行清除以刪除測試實例。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>復原 Vm

1. 登入遠端網站上的 **VSphere 用戶端** ，也就是 Azure VMware 解決方案私人雲端，並存取 **HCX 外掛程式**。

   在復原案例中，這是用於此範例的 Vm 群組。

1. 從清單中選取要復原的 VM，開啟 [ **動作** ] 功能表，然後選取 [ **復原 vm**]。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="VSphere 中的 HCX 選項" border="true":::

1. 設定每個實例的復原選項，然後選取 [復原] 以開始 **復原** 操作。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="VSphere 中的 HCX 選項" border="true":::

1. 復原作業完成後，新的 Vm 會出現在遠端 vCenter Server 清查中。

## <a name="complete-a-reverse-replication-on-vms"></a>在 Vm 上完成反向複寫

1. 登入 Azure VMware 解決方案私人雲端上的 **VSphere 用戶端** ，並存取 **HCX 外掛程式**。
   
   >[!NOTE]
   >開始反向複寫之前，請確定來源網站上的原始 Vm 已關閉電源。 如果 Vm 未關閉電源，作業就會失敗。

1. 從清單中選取要複寫回來源網站的 Vm，開啟 [ **動作** ] 功能表，然後選取 [ **反向**]。 
1. 選取 [ **反向** ] 以開始複寫。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="VSphere 中的 HCX 選項" border="true":::

1. 監視每個 VM 的詳細資料區段。

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="VSphere 中的 HCX 選項" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>嚴重損壞修復計畫自動化

VMware HCX 目前沒有內建的機制，可建立和自動化嚴重損壞修復計畫。 不過，VMware HCX 會提供一組 REST Api，包括用於損毀修復作業的 Api。 您可以在 [VMware HCX 管理員] 中的 URL 記憶體取 API 規格。

這些 Api 涵蓋了嚴重損壞修復的下列作業。

- Protect

- 復原

- 測試復原

- 規劃的復原

- Reverse

- 查詢

- 測試清除

- 暫停

- 繼續

- 移除保護

- 重新設定

JSON 中的復原操作承載範例如下所示。

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

客戶可以使用這些 Api 來建立自訂機制，以將建立和執行嚴重損壞修復計畫自動化。
