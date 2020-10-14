---
title: 如何移動 Azure 備份復原服務保存庫
description: 如何跨 Azure 訂用帳戶和資源群組移動復原服務保存庫的指示。
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 55c906585e6f6d4a2ae3f2279b2c3ffbaaccb025
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056424"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>跨 Azure 訂用帳戶和資源群組移動復原服務保存庫

本文說明如何跨 Azure 訂用帳戶或往相同訂用帳戶中的另一個資源群組移動為 Azure 備份所設定的復原服務保存庫。 您可以使用 Azure 入口網站或 PowerShell 來移動復原服務保存庫。

## <a name="supported-regions"></a>支援區域

澳大利亞東部、澳大利亞東南部、加拿大中部、加拿大東部、南部東亞、東亞、美國中部、美國中北部、美國東部、美國東部2、美國中南部、美國中西部、美國中西部2、美國西部、美國西部2、巴西南部、印度中部、印度南部、日本東部、日本西部、韓國中部、南韓南部中支援復原服務保存庫的資源移動、北歐、西歐、南非北部、南非西部、英國南部和英國西部。

## <a name="unsupported-regions"></a>不支援的區域

法國中部、法國南部、德國東北部、德國中部、US Gov 愛荷華州、中國北部、中國北部2、中國東部、中國東部2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>移動復原服務保存庫的必要條件

- 在保存庫于資源群組之間移動時，來源和目標資源群組都會遭到鎖定，而無法進行寫入和刪除作業。 如需詳細資訊，請參閱這篇[文章](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
- 只有系統管理員訂用帳戶才有移動保存庫的許可權。
- 若要在訂用帳戶之間移動保存庫，目標訂用帳戶必須位於與來源訂用帳戶相同的租使用者中，而且應該啟用其狀態。
- 您必須擁有在目標資源群組上執行寫入作業的權限。
- 移動保存庫只會變更資源群組。 復原服務保存庫將位於相同的位置，而且無法變更。
- 您一次只能移動一個復原服務保存庫（每個區域）。
- 如果 VM 未跨訂用帳戶或新的資源群組移動復原服務保存庫，則目前的 VM 復原點會在保存庫中保持不變，直到過期為止。
- 無論 VM 是否隨著保存庫移動，您一律可以從保留在保存庫內的備份記錄還原 VM。
- Azure 磁碟加密要求金鑰保存庫和 Vm 位於相同的 Azure 區域和訂用帳戶中。
- 若要移動具有受控磁碟的虛擬機器，請參閱這篇[文章](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription)。
- 移動透過傳統模型所部署之資源的選項，會根據您是要移動訂用帳戶內的資源還是新的訂用帳戶而有所不同。 如需詳細資訊，請參閱這篇[文章](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
- 在保存庫跨訂用帳戶或往新的資源群組移動之後，為保存庫所定義的備份原則會保留下來。
- 您只能移動包含下列任何類型之備份專案的保存庫。 以下未列出之類型的任何備份專案都必須停止，並在移動保存庫之前永久刪除資料。
  - Azure 虛擬機器
  - Microsoft Azure 復原服務 (MARS) 代理程式
  - Microsoft Azure 備份 Server (MABS) 
  - Data Protection Manager (DPM)
- 如果您在訂用帳戶之間移動包含 VM 備份資料的保存庫，您必須將 Vm 移至相同的訂用帳戶，並使用相同的目標 VM 資源組名 (與舊的訂用帳戶) ，才能繼續進行備份。

> [!NOTE]
> 不支援在 Azure 區域間移動 Azure 備份的復原服務保存庫。<br><br>
> 如果您已設定任何 Vm (Azure IaaS、Hyper-v、VMware) 或實體機器以使用 **Azure Site Recovery**進行嚴重損壞修復，移動作業將會遭到封鎖。 如果您想要移動 Azure Site Recovery 的保存庫，請參閱 [這篇文章](../site-recovery/move-vaults-across-regions.md) 以瞭解如何手動移動保存庫。

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>使用 Azure 入口網站將復原服務保存庫移至不同的資源群組

若要將復原服務保存庫和其相關聯的資源移至不同的資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟**復原服務保存庫**的清單，然後選取您想要移動的保存庫。 保存庫儀表板開啟時會如下圖所示。

   ![開啟復原服務保存庫](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   如果您沒有看到保存庫的 **基本** 資訊，請選取下拉式清單圖示。 您現在應該就會看到保存庫的 [基本資訊] 資訊。

   ![[基本資訊] 索引標籤](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保存庫總覽功能表中，選取**資源群組**旁的 [**變更**]，以開啟 [**移動資源**] 窗格。

   ![變更資源群組](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. 在 [ **移動資源** ] 窗格中，針對選取的保存庫，建議您選取如下圖所示的核取方塊，以移動選用的相關資源。

   ![移動訂用帳戶](./media/backup-azure-move-recovery-services/move-resource.png)

5. 若要新增目標資源群組，請在 [ **資源群組** ] 下拉式清單中選取現有的資源群組，或選取 [ **建立新群組** ] 選項。

   ![建立資源](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 新增資源群組之後，請確認 **我瞭解與移動資源相關聯的工具和腳本在更新為使用新的資源識別碼選項之前將無法運作** ，然後選取 **[確定]** 完成移動保存庫。

   ![確認訊息](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>使用 Azure 入口網站將復原服務保存庫移至不同的訂用帳戶

您可以將復原服務保存庫和其相關聯的資源移動到不同的訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟復原服務保存庫的清單，然後選取您想要移動的保存庫。 保存庫儀表板開啟時會如下圖所示。

    ![開啟復原服務保存庫](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    如果您沒有看到保存庫的 **基本** 資訊，請選取下拉式清單圖示。 您現在應該就會看到保存庫的 [基本資訊] 資訊。

    ![[基本資訊] 索引標籤](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保存庫總覽功能表中，選取 [**訂**用帳戶] 旁的 [**變更**]，以開啟 [**移動資源**] 窗格。

   ![變更訂用帳戶](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 選取要移動的資源，建議您在這裡使用 [全選]**** 選項來選取所有列出的選擇性資源。

   ![移動資源](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 從 [訂用帳戶]**** 下拉式清單選取您想要作為保存庫移動目的地的目標訂用帳戶。
6. 若要新增目標資源群組，請在 [ **資源群組** ] 下拉式清單中選取現有的資源群組，或選取 [ **建立新群組** ] 選項。

   ![新增訂用帳戶](./media/backup-azure-move-recovery-services/add-subscription.png)

7. 選取 [ **我瞭解與移動的資源相關聯的工具和腳本在更新為使用新的資源識別碼] 選項之前將無法運作** ，然後選取 **[確定]**。

> [!NOTE]
> 跨訂用帳戶備份 (RS 保存庫和受保護的 Vm 位於不同的訂用帳戶，) 不是支援的案例。 此外，在保存庫移動作業期間，無法修改本機冗余儲存體中的儲存體冗余選項 (LRS) 至全域冗余儲存體 (GRS) ，反之亦然。
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>使用 PowerShell 移動復原服務保存庫

若要將復原服務保存庫移動到另一個資源群組，請使用 `Move-AzureRMResource` Cmdlet。 `Move-AzureRMResource` 需要資源名稱和資源類型。 您可以從 `Get-AzureRmRecoveryServicesVault` Cmdlet 取得這兩項資訊。

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

若要將資源移到不同的訂用帳戶，請納入 `-DestinationSubscriptionId` 參數。

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

執行上述 Cmdlet 之後，系統會要求您確認是否要移動指定的資源。 輸入 **Y** 來確認。 成功通過驗證後，資源便會移動。

## <a name="use-cli-to-move-recovery-services-vault"></a>使用 CLI 移動復原服務保存庫

若要將復原服務保存庫移動到另一個資源群組，請使用下列 Cmdlet：

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

## <a name="post-migration"></a>移轉後

1. 設定/驗證資源群組的存取控制。  
2. 移動完成之後，必須再次針對保存庫設定備份報告和監視功能。 移動作業期間會失去先前的設定。

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>將 Azure 虛擬機器移至不同的復原服務保存庫。 

如果您想要移動已啟用 Azure 備份的 Azure 虛擬機器，則您有兩個選擇。 它們取決於您的業務需求：

- [不需要保留先前的備份資料](#dont-need-to-preserve-previous-backed-up-data)
- [必須保留先前的備份資料](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>不需要保留先前的備份資料

若要在新的保存庫中保護工作負載，您必須在舊的保存庫中刪除目前的保護和資料，然後再次設定備份。

>[!WARNING]
>下列作業是破壞性的作業，無法復原。 所有與受保護伺服器相關聯的備份資料和備份專案都會永久刪除。 請謹慎進行。

**停止並刪除舊保存庫上的目前保護：**

1. 停用保存庫屬性中的虛刪除。 請遵循下列 [步驟](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) 來停用虛刪除。

2. 停止保護並刪除目前保存庫中的備份。 在保存庫儀表板功能表中，選取 [ **備份專案**]。 此處所列的專案必須移至新的保存庫，並與其備份資料一起移除。 瞭解如何 [在雲端中刪除受保護的專案](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) ，以及 [刪除內部部署的受保護專案](backup-azure-delete-vault.md#delete-protected-items-on-premises)。

3. 如果您打算將 AFS (Azure 檔案共用) 、SQL server 或 SAP Hana 伺服器，則您也需要將其取消註冊。 在保存庫儀表板功能表中，選取 [ **備份基礎結構**]。 瞭解如何 [取消註冊 SQL server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)、 [取消登錄與 Azure 檔案共用相關聯的儲存體帳戶](manage-afs-backup.md#unregister-a-storage-account)，以及 [取消註冊 SAP Hana 實例](sap-hana-db-manage.md#unregister-an-sap-hana-instance)。

4. 移除舊的保存庫後，請繼續在新的保存庫中為您的工作負載設定備份。

### <a name="must-preserve-previous-backed-up-data"></a>必須保留先前的備份資料

如果您需要將目前受保護的資料保留在舊的保存庫中，並在新的保存庫中繼續保護，部分工作負載的選項有限：

- 針對 MARS，您可以 [停止保護保留資料](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) ，並在新的保存庫中註冊代理程式。

  - Azure 備份服務會繼續保留舊保存庫的所有現有復原點。
  - 您必須付費，才能將復原點保留在舊的保存庫中。
  - 您只能針對舊保存庫中未到期的復原點還原已備份的資料。
  - 您必須在新的保存庫上建立資料的新初始複本。

- 針對 Azure VM，您可以 [停止保護以保留](backup-azure-manage-vms.md#stop-protecting-a-vm) 舊保存庫中 vm 的資料，將 vm 移至另一個資源群組，然後在新的保存庫中保護 vm。 請參閱將 VM 移至另一個資源群組的 [指導方針和限制](https://docs.microsoft.com/azure/azure-resource-manager/management/move-limitations/virtual-machines-move-limitations) 。

  虛擬機器一次只能在一個保存庫中受到保護。 不過，新的資源群組中的 VM 可以在新的保存庫上受到保護，因為它被視為不同的 VM。

  - Azure 備份服務將會保留在舊的保存庫上備份的復原點。
  - 您必須付費，才能將復原點保留在舊的保存庫中 (如需詳細資料) ，請參閱 [Azure 備份定價](azure-backup-pricing.md) 。
  - 若有需要，您可以從舊的保存庫還原 VM。
  - 新資源中 VM 新保存庫上的第一個備份會是初始複本。

## <a name="next-steps"></a>後續步驟

您可以在資源群組和訂用帳戶之間移動許多不同類型的資源。

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
