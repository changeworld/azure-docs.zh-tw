---
title: 完成虛擬機器的嚴重損壞修復
description: 本文說明如何使用 Azure VMware 解決方案完成虛擬機器的嚴重損壞修復
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 76a417b9ba00c4c0e6e958e5a04d19aecfe24563
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752261"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>使用 Azure VMware 解決方案完成虛擬機器的嚴重損壞修復

本文包含使用 VMware 混合式雲端擴充功能來完成虛擬機器的嚴重損壞修復的程式 (HCX) 解決方案，以及使用 Azure VMware 解決方案私人雲端作為復原或目標網站。

VMware HCX 提供各種作業，可在複寫原則中提供細微的控制和細微性。 可用的作業包括：

- 反向–發生災難之後。 反向有助於讓網站 B 成為受保護 VM 現在存留所在的來源網站和網站 A。

- 暫停–暫停與所選虛擬機器相關聯的目前複寫原則。

- 繼續-暫停與所選虛擬機器相關聯的目前複寫原則。

- 移除-移除與所選虛擬機器相關聯的目前複寫原則。

- 立即同步至受保護 VM 的系結同步來源虛擬機器。

本指南涵蓋下列複寫案例：

- 保護 VM 或一組 Vm。

- 完成 VM 或 vm 群組的測試復原。

- 復原 VM 或 Vm 群組。

- 反向保護 VM 或 Vm 群組。

## <a name="protect-virtual-machines"></a>保護虛擬機器

登入來源網站上的 **VSphere 用戶端** ，並存取 **HCX 外掛程式**。

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="VSphere 中的 HCX 選項" border="true":::

輸入 [故障 **修復** ] 區域，然後按一下 [ **保護 vm**]。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="選取保護 vm" border="true":::

在開啟的視窗中，選取來源和遠端網站，在此案例中，遠端網站應為 Azure VMware 解決方案私人雲端。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="保護 Vm 視窗" border="true":::

如有需要，請選取預設的複寫選項：

- **啟用壓縮：** 針對低輸送量案例，建議使用。

- **啟用靜止：** 暫停 VM，以確保將一致的複本同步處理至遠端網站。

- **目的地儲存體：** 為受保護的 VM (s) 選取遠端資料存放區。 在 Azure VMware 解決方案私人雲端中，此選項應為 vSAN 資料存放區。

- **計算容器：** 遠端 vSphere 叢集或資源集區。

- **目的地資料夾：** [遠端目的地] 資料夾，此設定是選擇性的，如果未選取任何資料夾，則 VM (s) 將直接在選取的叢集下進行進度。

- **RPO：** 此值是來源虛擬機器和受保護虛擬機器之間的同步處理間隔，可在5分鐘到24小時之間的任何位置。

- **快照間隔：** 快照之間的間隔。

- **快照集數目：** 在設定的快照間隔內的快照集總數。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="保護虛擬機器選項" border="true":::

從清單中選取一或多部虛擬機器，並視需要將複寫選項設定為虛擬機器。

根據預設，虛擬機器將會繼承在預設複寫選項中設定的全域設定原則。 針對所選 VM 中的每個網路介面，設定 [遠端 **網路埠] 群組** ，然後選取 **[完成]** 以開始保護處理常式。

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="網路介面選項" border="true":::

如下圖所示，您可以在相同的嚴重損壞修復區域中監視每個所選虛擬機器的進程。

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="監視保護的進度" border="true":::

在 VM 受到保護之後，您可以在 [ **快照** 集] 索引標籤中看到不同的快照集。

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="快照集清單" border="true":::

黃色三角形表示快照集和虛擬的測試尚未在測試復原操作中測試過。

已關閉電源的 VM 與開啟電源的 VM 之間有一些主要差異。
上述螢幕擷取畫面顯示已啟用虛擬機器電源的同步處理常式。 它會開始同步處理常式，直到它完成第一個快照集（也就是 VM 的完整複本），然後以設定的間隔完成下一個快照集。

針對已關閉電源的 VM，它會同步處理複本，然後 VM 會顯示為非作用中，且保護作業會顯示為已完成。

當虛擬機器開機時，它會啟動同步處理至遠端網站的程式。

## <a name="complete-a-test-recover-of-virtual-machines"></a>完成虛擬機器的測試復原

登入遠端網站上的 **VSphere 用戶端** ，也就是 Azure VMware 解決方案私人雲端。 在 **HCX 外掛程式**的 [嚴重損壞修復] 區域中，選取任何 VM 上的垂直省略號，以顯示 [作業] 功能表。 選取 [ **測試復原 VM**]。

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="選取測試復原 VM" border="true":::

在新視窗中，選取測試的選項。 選取您要用來測試虛擬機器之不同狀態的快照集。

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="選擇快照集，然後按一下 [測試]" border="true":::

按一下 [ **測試**] 之後，就會開始復原操作。

當測試復原作業完成時，您可以在 Azure VMware 解決方案私人雲端 vCenter 中檢查新的 VM。

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="檢查復原操作" border="true":::

最後，在 VM 上執行測試之後，或在其上執行的任何應用程式都會進行清除，以刪除測試實例。

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="清除測試實例" border="true":::

## <a name="recover-virtual-machines"></a>復原虛擬機器

登入遠端網站上的 **VSphere 用戶端** ，也就是 Azure VMware 解決方案私人雲端，並存取 **HCX 外掛程式**。

在復原案例中，這是針對此範例所使用的一組虛擬機器。

從清單中選取要復原的虛擬機器，開啟 [ **動作** ] 功能表，然後選取 [ **復原 vm**]。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="復原虛擬機器" border="true":::

設定每個實例的復原選項，然後按一下 [復原] 以開始 **復原** 操作。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="復原虛擬機器確認" border="true":::

復原作業完成後，新的 Vm 將會出現在遠端 vCenter Server 清查中。

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>完成虛擬機器上的反向複寫

登入 Azure VMware 解決方案私人雲端上的 **VSphere 用戶端** ，並存取 **HCX 外掛程式**。
在開始反向複寫之前，必須先關閉來源網站上的原始虛擬機器的電源。 如果虛擬機器未關閉電源，作業就會失敗。

從清單中選取要複寫回來源網站的虛擬機器，開啟 [ **動作** ] 功能表，然後選取 [ **反向**]。 在快顯視窗中，按一下 [ **反向** ] 開始複寫。

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="選取 [保護作業] 下的 [反向動作]" border="true":::

您可以在每個虛擬機器的 [詳細資料] 區段中監視複寫。

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="檢查反轉動作的結果" border="true":::

## <a name="disaster-recovery-plan-automation"></a>嚴重損壞修復計畫自動化

VMware HCX 目前沒有內建的機制，可建立和自動化嚴重損壞修復計畫。 這項功能並不存在於 HCX 中。 不過，它會提供一組 REST Api，包括用於損毀修復作業的 Api。

API 規格可以在 URL 中的 HCX Manager 內進行存取。

這些 Api 涵蓋了嚴重損壞修復的下列作業。

- 保護

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
