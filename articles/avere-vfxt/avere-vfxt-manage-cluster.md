---
title: 管理 Avere vFXT 叢集 - Azure
description: 如何管理 Avere 叢集 - 新增或移除節點、重新啟動、停止或終結 vFXT 叢集
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 94db4a93025b6e3d633368d924e3e0c518d108ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153474"
---
# <a name="manage-the-avere-vfxt-cluster"></a>管理 Avere vFXT 叢集

在 Azure 群集的 Avere vFXT 生命週期的某個階段，您可能需要添加叢集節點，或啟動或重新開機群集。 專案完成後，您需要瞭解如何停止群集並永久刪除群集。

本文介紹如何添加或刪除叢集節點和其他基本群集操作。 如果需要更改群集設置或監視其工作，請使用[Avere 控制台](avere-vfxt-cluster-gui.md)。

根據管理工作的不同，您可能需要使用三種不同的工具之一：Avere 控制台、vfxt.py命令列群集管理腳本和 Azure 門戶。

下表提供可用於每個工作之工具的概觀。

| 動作 | Avere 控制台 | vfxt.py  | Azure 入口網站 |
| --- | --- | --- | --- |
| 新增叢集節點 | 否 | 是 | 否 |
| 移除叢集節點 | 是 | 否 | 否 |
| 停止叢集節點 | 是 (也可以重新啟動服務或重新啟動) | 否 | 從入口網站關閉節點 VM 會解譯為節點失敗 |
| 啟動已停止的節點 | 否 | 否 | 是 |
| 終結單一叢集節點 | 否 | 否 | 是 |
| 重新啟動叢集 |  |  |  |
| 安全地關閉或停止叢集 | 是 | 是 | 否 |
| 終結叢集  | 否 | 是 | 是，但不保證資料完整性 |

各項工具的詳細指示都包含在下方。

## <a name="about-stopped-instances-in-azure"></a>關於 Azure 中已停止的執行個體

當您關閉或停止任何 Azure VM 時，它會停止產生計算費用，但您仍必須支付其儲存體的費用。 如果您關閉 vFXT 節點或整個 vFXT 叢集，而且您不想要重新啟動它，您應該使用 Azure 入口網站刪除相關的 VM。

在 Azure 門戶中，*停止*的節點（可以重新開機）顯示 Azure 門戶中**已停止**的狀態。 *已刪除*的節點顯示**狀態已停止（已處理），** 並且不再產生計算或存儲費用。

刪除 VM 之前，請確定所有變更的資料已從快取寫回後端儲存體，方法是，使用 Avere 控制台或 vfxt.py 選項停止或關閉叢集。

## <a name="manage-the-cluster-with-avere-control-panel"></a>使用 Avere 控制台管理叢集

Avere 控制台可用於下列工作：

* 停止或重新啟動個別的節點
* 從叢集移除節點
* 停止或重新啟動整個叢集

Avere 控制台將資料完整性列為優先順序，因此它嘗試在可能破壞性操作之前將任何更改的資料寫入後端存儲。 這使得它比 Azure 門戶更安全。

從網頁瀏覽器存取 Avere 控制台。 如果您需要協助，請依照[存取 vFXT 叢集](avere-vfxt-cluster-gui.md)中的指示進行。

### <a name="manage-nodes-with-avere-control-panel"></a>使用 Avere 控制台管理節點

[FXT 節點]**** 設定頁面提供管理個別節點的控制項。

若要關閉、重新啟動或移除節點，在 [FXT 節點]**** 頁面上的清單中尋找節點，然後在其 [動作]**** 資料行中按一下適當的按鈕。

> [!NOTE]
> 當作用中節點的數目變更時，IP 位址可能會在叢集節點之間移動。

如需詳細資訊，請閱讀 Avere 叢集設定指南中的[叢集 > FXT 節點](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>)。

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>使用 Avere 控制台停止或重新啟動叢集

[系統維護]**** 設定頁面提供重新啟動叢集服務、重新啟動叢集，或安全地關閉叢集的命令。 如需詳細資訊，請閱讀[管理 > 系統維護](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (在 Avere 叢集設定指南中)。

當群集開始關閉時，它會將狀態訊息發佈到 **"儀表板"** 選項卡。幾分鐘後，消息將停止，最終 Avere 控制台會話停止回應，這意味著群集已關閉。

## <a name="manage-the-cluster-with-vfxtpy"></a>使用 vfxt.py 管理叢集

vfxt.py 是用來建立和管理叢集的命令列工具。

vfxt.py 已預先安裝在叢集控制器 VM 上。 如果您想要將其安裝在另一個系統上，請參閱 <https://github.com/Azure/AvereSDK> 上的文件。

vfxt.py 指令碼可以用於以下叢集管理工作：

* 將新節點加入至叢集
* 停止或啟動叢集  
* 終結叢集

如同 Avere 控制台，vfxt.py 作業會嘗試確認已變更的資料永久儲存在後端儲存體，然後再關閉或終結叢集或節點。 這使得它比 Azure 門戶更安全。

完整的 vfxt.py 使用指南可在 GitHub 上取得：[使用 vfxt.py 進行雲端叢集管理](https://github.com/azure/averesdk/blob/master/docs/README.md) \(英文\)

### <a name="add-cluster-nodes-with-vfxtpy"></a>使用 vfxt.py 新增叢集節點

新增叢集節點的範例命令指令碼包含在叢集控制器上。 在控制器上找出 ``./add-nodes``，並在編輯器中開啟它，以使用您的叢集資訊加以自訂。

叢集必須在執行中，才能使用此命令。

提供下列值：

* 群集的資源組名稱，以及網路和存儲資源的名稱（如果它們與群集不位於同一資源組中）
* 叢集位置
* 叢集網路和子網路
* 叢集節點訪問角色（使用內置角色[Avere 運算子](../role-based-access-control/built-in-roles.md#avere-operator)）
* 叢集管理 IP 位址和系統管理密碼
* 要新增的節點數目 (1、2 或 3)
* 節點執行個體類型和快取大小值

如果您未使用原型，則必須建構諸如以下的命令，包括上述的所有資訊。

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

如需詳細資訊，請閱讀 vfxt.py 使用指南中的[將節點新增至叢集](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster)。

### <a name="stop-a-cluster-with-vfxtpy"></a>使用 vfxt.py 停止叢集

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>使用 vfxt.py 啟動已停止的叢集

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

由於叢集已停止，因此您必須傳遞執行個體識別碼以指定叢集節點。 若要深入了解，請閱讀 vfxt.py 使用指南中的[指定要修改的叢集](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify)。

### <a name="destroy-a-cluster-with-vfxtpy"></a>使用 vfxt.py 終結叢集

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

如果不想``--quick-destroy``從群集緩存中保存更改的資料，可以使用該選項。

如需其他資訊，請閱讀 [vfxt.py 使用指南](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>)。

## <a name="manage-cluster-vms-from-the-azure-portal"></a>從 Azure 入口網站管理叢集 VM

Azure 入口網站可用來個別終結叢集 VM，但如果沒有先完全關閉叢集，則不保證資料完整性。

Azure 入口網站可以用於以下叢集管理工作：

* 啟動已停止的 vFXT 節點
* 停止個別的 vFXT 節點 (叢集會將其解譯節點失敗)
* *如果*您不需要確保叢集快取中已變更的資料寫入至核心篩選，則終結 vFXT 叢集
* 安全地關閉 vFXT 節點和其他叢集資源之後，請將其永久移除

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>從 Azure 入口網站重新啟動 vFXT 執行個體

如果您需要重新啟動已停止的節點，您必須使用 Azure 入口網站。 選取左側功能表中的 [虛擬機器]****，然後按一下清單中的 VM 名稱，以開啟其概觀頁面。

按一下概觀頁面頂端的 [啟動]**** 按鈕，重新啟用 VM。

![Azure 入口網站畫面，其中顯示啟動已停止之 VM 的選項](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>刪除叢集節點

如果您想要刪除 vFXT 叢集中的一個節點，但保留叢集的其餘極點，應該先使用 Avere 控制台，[從叢集移除節點](#manage-nodes-with-avere-control-panel)。

> [!CAUTION]
> 如果您沒有先從 vFXT 叢集移除節點就加以刪除，可能會遺失資料。

若要永久終結當作 vFXT 節點使用的一個或多個執行個體，請使用 Azure 入口網站。
選取左側功能表中的 [虛擬機器]****，然後按一下清單中的 VM 名稱，以開啟其概觀頁面。

按一下概觀頁面頂端的 [刪除]**** 按鈕，永久終結 VM。

您可以在安全地關閉叢集節點之後，使用此方法永久移除它們。

### <a name="destroy-the-cluster-from-the-azure-portal"></a>從 Azure 入口網站終結叢集

> [!NOTE]
> 如果您想要將快取中任何其餘的用戶端變更寫入後端儲存體，請使用 vfxt.py `--destroy` 選項或使用 Avere 控制台，完全關閉叢集，然後再移除 Azure 入口網站中的節點執行個體。

您可以刪除 Azure 入口網站中的節點執行個體，藉此永久終結它們。 您也可以如上述一次刪除一個，或者您可以使用 [虛擬機器]**** 頁面找出所有叢集 VM，選取其核取方塊，然後按一下 [刪除]**** 按鈕，將它們一次全部移除。

![入口網站中的 VM 清單，依「叢集」一詞篩選，核取並醒目提示其中三個](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>從 Azure 入口網站刪除額外的叢集資源

如果您專為 vFXT 叢集建立額外的資源，您可能想要在卸除叢集時加以移除。 不要銷毀包含所需資料的元素，或與其他專案共用的任何專案。

除了刪除叢集節點之外，請考慮移除下列元件：

* 叢集控制器 VM
* 與叢集節點相關聯的資料磁碟
* 與叢集元件相關聯的網路介面和公用 IP
* 虛擬網路
* 存儲容器和存儲帳戶（**僅當**它們不包含重要資料時）
* 可用性設定組

![Azure 入口網站「所有資源」清單，其中顯示針對測試叢集建立的資源](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>從 Azure 入口網站刪除叢集的資源群組

如果您專為放入叢集而建立資源群組，可以終結資源群組來終結叢集的所有相關資源。

> [!Caution]
> 只有在您確定群組中沒有任何值時，才能終結資源群組。 例如，請確定您已經從資源群組內的任何儲存體容器中，移動所需的任何資料。  

若要刪除資源群組，請按一下入口網站左側功能表中的 [資源群組]****，然後篩選資源群組清單，以找出您針對 vFXT 叢集建立的資源群組。 選取該資源群組，然後按一下面板右側的三個點。 選擇 [刪除資源群組]****。 入口網站會要求您確認刪除，這是無法復原的。

![顯示「刪除資源群組」動作的資源群組](media/avere-vfxt-delete-resource-group.png)
