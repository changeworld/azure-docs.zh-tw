---
title: 完成虛擬機器的嚴重損壞修復
description: 本文說明如何使用 AVS 完成虛擬機器的嚴重損壞修復
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 5ccaa009c8e3e059597636a8bb78cc3bd255fe68
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749943"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>使用 Azure VMware 解決方案完成虛擬機器的嚴重損壞修復

本文包含使用 VMware 混合式雲端擴充功能（HCX）解決方案完成虛擬機器嚴重損壞修復的程式，並將 Azure VMware 解決方案私用雲端作為復原或目標網站。

VMware HCX 提供各種作業，可在複寫原則中提供精確的控制和細微性。 可用的作業包括：

- 反向–發生災難之後。 [反轉] 有助於讓網站 B 成為受保護 VM 現在所在的來源網站和網站 A。

- 暫停–暫停與所選虛擬機器相關聯的目前複寫原則。

- 繼續-暫停與所選虛擬機器相關聯的目前複寫原則。

- 移除-移除與所選虛擬機器相關聯的目前複寫原則。

- 立即同步–超出限制的同步來源虛擬機器到受保護的 VM。

本指南涵蓋下列複寫案例：

- 保護 VM 或一組 Vm。

- 完成 VM 或一組 Vm 的測試復原。

- 復原 VM 或一組 Vm。

- 反向保護 VM 或一組 Vm。

## <a name="protect-virtual-machines"></a>保護虛擬機器

登入來源網站上的**VSphere 用戶端**和存取權**HCX 外掛程式**。

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="VSphere 中的 HCX 選項" border="true":::

輸入 [嚴重損壞**修復**] 區域，然後按一下 [**保護 vm**]。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="選取 [保護 vm]" border="true":::

在開啟的視窗中，選取來源和遠端網站，在此案例中的遠端網站應該是 AVS 私用雲端。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="[保護 Vm] 視窗" border="true":::

如有需要，請選取預設的複寫選項：

- **啟用壓縮：** 針對低輸送量案例，建議使用。

- **啟用靜止：** 暫停 VM，以確保一致的複本會同步處理至遠端網站。

- **目的地儲存體：** 選取受保護 VM 的遠端資料存放區。 在 AVS 私人雲端中，此選項應為 vSAN 資料存放區。

- **計算容器：** 遠端 vSphere 叢集或資源集區。

- **目的地資料夾：** 遠端目的地資料夾，這是選擇性設定，如果未選取任何資料夾，則 VM 會直接在選取的叢集下進行。

- **RPO：** 此值是來源虛擬機器與受保護虛擬機器之間的同步處理間隔，可以從5分鐘到24小時。

- **快照間隔：** 快照之間的間隔。

- **快照集數目：** 設定的快照間隔內的快照集總數。

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="保護虛擬機器選項" border="true":::

從清單中選取一或多個虛擬機器，並視需要設定虛擬機器的複寫選項。

根據預設，虛擬機器將會繼承在預設複寫選項中設定的全域設定原則。 針對所選 VM 中的每個網路介面，設定遠端**網路埠群組**，然後選取 **[完成]** 以啟動保護程式。

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="網路介面選項" border="true":::

如下圖所示，您可以在相同的嚴重損壞修復區域中監視每個所選虛擬機器的進程。

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="監視保護的進度" border="true":::

在 VM 受到保護之後，您可以在 [**快照**集] 索引標籤中看到不同的快照集。

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="快照集清單" border="true":::

黃色三角形表示快照集和虛擬尚未在測試復原操作中進行測試。

關閉電源的 VM 與電源開啟的虛擬機器之間有一些主要差異。
上述螢幕擷取畫面顯示開機虛擬機器的同步處理常式。 它會啟動同步處理常式，直到完成第一個快照集（VM 的完整複本），然後在設定的間隔內完成下一個快照集。

針對已關閉電源的 VM，它會同步處理一個複本，然後 VM 將會顯示為非作用中，而保護作業會顯示為已完成。

當虛擬機器開機時，它會開始同步處理至遠端網站。

## <a name="complete-a-test-recover-of-virtual-machines"></a>完成虛擬機器的測試復原

登入遠端網站上的**VSphere 用戶端**，也就是 AVS 私用雲端。 在**HCX 外掛程式**的 [嚴重損壞修復] 區域中，選取任何 VM 上的垂直省略號，以顯示 [作業] 功能表。 選取 [測試] [**復原 VM**]。

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="選取 [測試] [復原 VM]" border="true":::

在新視窗中，選取測試的選項。 選取您想要用來測試虛擬機器不同狀態的快照集。

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="選擇快照集，然後按一下 [測試]" border="true":::

按一下 [**測試**] 之後，就會開始復原作業。

當測試復原作業完成時，可以在 AVS 私用雲端 vCenter 中檢查新的 VM。

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="檢查復原操作" border="true":::

最後，在 VM 上進行測試或在其上執行的任何應用程式進行清除，以刪除測試實例。

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="清除測試實例" border="true":::

## <a name="recover-virtual-machines"></a>復原虛擬機器

在遠端網站（也就是 AVS 私人雲端）上登入**VSphere 用戶端**，並存取**HCX 外掛程式**。

針對復原案例，這是用於此範例的一組虛擬機器。

從清單中選取要復原的虛擬機器，開啟 [**動作**] 功能表，然後選取 [**復原 vm**]。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="復原虛擬機器" border="true":::

設定每個實例的復原選項，然後按一下 [**復原**] 以開始修復操作。

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="復原虛擬機器確認" border="true":::

完成復原操作之後，新的 Vm 會出現在遠端 vCenter Server 清查中。

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>完成虛擬機器上的反向複寫

登入您的 AVS 私用雲端上的**VSphere 用戶端**，並存取**HCX 外掛程式**。
在您開始反向複寫之前，必須先關閉來源網站上的原始虛擬機器電源。 如果虛擬機器未關閉電源，作業會失敗。

從清單中選取要複製回來源網站的虛擬機器，開啟 [**動作**] 功能表，然後選取 [**反向**]。 在快顯視窗中，按一下 [**反向**] 以開始複寫。

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="選取 [保護作業] 底下的 [反向動作]" border="true":::

您可以在每個虛擬機器的 [詳細資料] 區段中監視複寫。

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="檢查反向動作的結果" border="true":::

## <a name="disaster-recovery-plan-automation"></a>嚴重損壞修復計畫自動化

VMware HCX 目前沒有內建的機制可建立和自動化嚴重損壞修復計畫。 此功能不存在於 HCX 中。 不過，它會提供一組 REST Api，包括用於嚴重損壞修復作業的 Api。

在 URL 中，您可以在 HCX 管理員記憶體取 API 規格。

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
