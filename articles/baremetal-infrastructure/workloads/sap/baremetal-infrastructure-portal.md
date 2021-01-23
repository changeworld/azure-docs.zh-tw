---
title: Azure 中的 BareMetal 實例單位
description: 瞭解如何透過 Azure 入口網站識別 BareMetal 實例單位並與其互動。
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: b089b45c35ff05f10ae59f8ce793645361be1e9b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733258"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>透過 Azure 入口網站管理 BareMetal 執行個體
 
本文說明 [Azure 入口網站](https://portal.azure.com/) 如何顯示 [BareMetal 實例](baremetal-overview-architecture.md)。 本文也會說明您可以在 Azure 入口網站中使用已部署的 BareMetal 實例單位進行的活動。 
 
## <a name="register-the-resource-provider"></a>註冊資源提供者
BareMetal 實例的 Azure 資源提供者可讓您在目前處於公開預覽狀態的 Azure 入口網站中，提供實例的可見度。 根據預設，您用於 BareMetal 實例部署的 Azure 訂用帳戶會註冊 *BareMetalInfrastructure* 資源提供者。 如果您沒有看到已部署的 BareMetal 實例單位，則必須向您的訂用帳戶註冊資源提供者。 

有兩種方式可以註冊 BareMetal 實例資源提供者：
 
* [Azure CLI](#azure-cli)
 
* [Azure 入口網站](#azure-portal)
 
### <a name="azure-cli"></a>Azure CLI
 
登入您用來透過 Azure CLI 部署 BareMetal 實例的 Azure 訂用帳戶。 您可以向註冊 BareMetalInfrastructure 資源提供者：

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
如需詳細資訊，請參閱 [Azure 資源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)文章。
 
### <a name="azure-portal"></a>Azure 入口網站
 
您可以透過 Azure 入口網站註冊 BareMetalInfrastructure 資源提供者。
 
您必須在 Azure 入口網站中列出您的訂用帳戶，然後按兩下用來部署 BareMetal 實例單位的訂用帳戶。
 
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站功能表上，選取 [所有服務]。

1. 在 [所有服務] 方塊中，輸入 [訂用帳戶]，然後選取 [訂用帳戶]。

1. 從訂用帳戶清單中選取要檢視的訂用帳戶。

1. 選取 **資源提供者** ，並在搜尋中輸入 **BareMetalInfrastructure** 。 應該 **註冊** 資源提供者，如影像所示。
 
>[!NOTE]
>若資源提供者未註冊，請選取 [註冊]。
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="顯示已註冊之 BareMetal 實例單位的螢幕擷取畫面":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>Azure 入口網站中的 BareMetal 實例單位
 
當您提交 BareMetal 實例部署要求時，您會指定要連接到 BareMetal 實例的 Azure 訂用帳戶。 使用您用來部署適用于 BareMetal 實例單位之應用層的相同訂用帳戶。
 
部署 BareMetal 實例時，會在您于部署要求中使用的 Azure 訂用帳戶中建立新的 [azure 資源群組](../../../azure-resource-manager/management/manage-resources-portal.md) 。 這個新的資源群組會列出您已在特定訂用帳戶中部署的所有 BareMetal 實例單位。

1. 在 BareMetal 訂用帳戶的 Azure 入口網站中，選取 [ **資源群組**]。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="顯示資源群組清單的螢幕擷取畫面":::

1. 在清單中，找出新的資源群組。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="螢幕擷取畫面，顯示已篩選之資源群組清單中的 BareMetal 實例單位" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >您可以針對用來部署 BareMetal 實例的訂用帳戶進行篩選。 篩選到適當的訂用帳戶之後，您可能會有較長的資源群組清單。 尋找一個具有 **-Txxx** 的修正後，其中 xxx 是三位數，例如 **-T250**。

1. 選取新的資源群組，以顯示其詳細資料。 此影像會顯示一個已部署的 BareMetal 實例單位。
   
   >[!NOTE]
   >如果您在相同的 Azure 訂用帳戶下部署數個 BareMetal 實例租使用者，您會看到多個 Azure 資源群組。
 
## <a name="view-the-attributes-of-a-single-instance"></a>查看單一實例的屬性
 
您可以查看單一單位的詳細資料。 在 BareMetal 實例的清單中，選取您要查看的單一實例。
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="顯示單一實例之 BareMetal 實例單位屬性的螢幕擷取畫面" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
映射中的屬性看起來與 Azure 虛擬機器 (VM) 屬性的差異不大。 在左側，您會看到資源群組、Azure 區域和訂用帳戶名稱和識別碼。 如果您已指派標籤，則也會在這裡看到這些標記。 根據預設，BareMetal 實例單位沒有指派的標記。
 
在右側，您會看到單位的名稱、作業系統 (OS) 、IP 位址，以及顯示 CPU 執行緒和記憶體數目的 SKU。 您也會看到 BareMetal 實例戳記) 的電源狀態和硬體版本 (修訂。 電源狀態指出硬體單位是否開啟電源或關閉電源。 但是，作業系統詳細資料並不會指出它是否已啟動並執行。
 
可能的硬體修訂如下：

* 修訂 3 (Rev 3) 

* 修訂 4 (Rev 4) 
 
* 修訂 4.2 (Rev 4.2) 
 
>[!NOTE]
>Rev 4.2 是最新的更名 BareMetal 基礎結構，使用現有的 Rev 4 架構。 Rev 4 可 (VM) 主機，更接近 Azure 虛擬機器。 它大幅改善了 Azure Vm 和 BareMetal 實例單位（部署于 Rev 4 戳記或資料列）之間的網路延遲。 您可以透過 Azure 入口網站存取和管理您的 BareMetal 實例。 如需詳細資訊，請參閱 [Azure 上的 BareMetal 基礎結構](baremetal-overview-architecture.md)。
 
此外，您可以在右側找到 [Azure 鄰近放置群組的](../../../virtual-machines/co-location.md) 名稱，此名稱會針對每個已部署的 BareMetal 實例單位自動建立。 當您部署裝載應用層的 Azure Vm 時，請參考鄰近位置群組。 當您使用與 BareMetal 實例單位相關聯的鄰近位置群組時，請確定 Azure Vm 會部署到接近 BareMetal 實例單位。
 
>[!TIP]
>若要在與修訂版4.x 相同的 Azure 資料中心尋找應用層，請參閱 [Azure 鄰近放置群組以取得最佳的網路延遲](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)。
 
## <a name="check-activities-of-a-single-instance"></a>檢查單一實例的活動
 
您可以檢查單一單位的活動。 記錄的其中一個主要活動是重新開機單位。 列出的資料包含活動的狀態、觸發活動的時間戳記、訂用帳戶識別碼，以及觸發活動的 Azure 使用者。
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="顯示 BareMetal 實例單位活動的螢幕擷取畫面" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
在 Azure 中對單元中繼資料所做的變更也會記錄在活動記錄中。 除了啟動重新開機之外，您還可以看到 **寫入 BareMetallnstances** 的活動。 此活動不會對 BareMetal 實例單位本身進行任何變更，但會記錄 Azure 中單元中繼資料的變更。
 
另一個記錄的活動是當您新增或刪除實例的 [標記](../../../azure-resource-manager/management/tag-resources.md) 時。
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>新增和刪除實例的 Azure 標記
 
您可以將 Azure 標記新增至 BareMetal 實例單位，或將其刪除。 指派標籤的方式不同于將標籤指派給 Vm。 就像 Vm 一樣，這些標記會存在於 Azure 中繼資料中，而針對 BareMetal 實例，它們具有與 Vm 標記相同的限制。
 
刪除標記的運作方式與 Vm 相同。 套用和刪除標記會列在 BareMetal 實例單位的活動記錄中。
 
## <a name="check-properties-of-an-instance"></a>檢查實例的屬性
 
當您取得實例時，您可以移至 [屬性] 區段來查看針對實例所收集的資料。 收集的資料包括 Azure 連線能力、儲存體後端、ExpressRoute 線路識別碼、唯一資源識別碼和訂用帳戶識別碼。 您將在支援要求中或在設定儲存體快照集設定時使用此資訊。
 
您將會看到的另一項重要資訊是存放裝置 NFS IP 位址。 它會將您的儲存體隔離到 BareMetal 實例堆疊中的 **租** 使用者。 當您編輯 [儲存體快照集備份的設定檔](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)時，將會使用此 IP 位址。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="顯示 BareMetal 實例屬性設定的螢幕擷取畫面" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>透過 Azure 入口網站重新開機單位
 
在許多情況下，作業系統不會完成重新開機，這需要 BareMetal 實例單位的電源重新開機。 您可以直接從 Azure 入口網站執行單元的電源重新開機：
 
選取 [ **重新開機** ]，然後選取 [ **是]** 以確認重新開機單位。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="顯示如何重新開機 BareMetal 實例單位的螢幕擷取畫面":::
 
當您重新開機 BareMetal 實例單位時，您將會遇到延遲。 在這段延遲期間，電源狀態會從 **開始** 到 **啟動**，這表示作業系統已完全啟動。 如此一來，重新開機之後，當狀態切換為 [ **已啟動**] 時，就無法登入單位。
 
>[!IMPORTANT]
>視 BareMetal 實例單位中的記憶體數量而定，重新開機和重新開機硬體和作業系統最多可能需要一小時的時間。
 
## <a name="open-a-support-request-for-baremetal-instances"></a>開啟 BareMetal 實例的支援要求
 
您可以特別針對 BareMetal 實例單位提交支援要求。
1. 在 Azure 入口網站的 [說明 **+ 支援**] 下，建立 **[新的支援要求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，並為票證提供下列資訊：
 
   - **問題類型：** 選取問題類型
 
   - **訂** 用帳戶：選取您的訂用帳戶
 
   - **服務：** BareMetal 基礎結構
 
   - **資源：** 提供實例的名稱
 
   - **摘要：** 提供要求的摘要
 
   - **問題類型：** 選取問題類型
 
   - **問題子類型：** 選取問題的子類型

1. 選取 [ **方案** ] 索引標籤，尋找問題的解決方案。 如果找不到解決方案，請移至下一個步驟。

1. 選取 [ **詳細資料** ] 索引標籤，然後選取問題是否與 Vm 或 BareMetal 實例單位有關。 此資訊可協助將支援要求導向至正確的專家。

1. 指出問題的開始時間，並選取實例區域。

1. 提供更多有關要求的詳細資料，並視需要上傳檔案。

1. 選取 [ **審核 + 建立** ] 以提交要求。
 
支援代表需要五個工作天的時間，才能確認您的要求。

## <a name="next-steps"></a>後續步驟

如果您想要深入瞭解工作負載，請參閱 [BareMetal 工作負載類型](../../../virtual-machines/workloads/sap/get-started.md)。