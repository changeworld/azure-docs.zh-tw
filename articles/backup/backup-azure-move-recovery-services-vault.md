---
title: 如何移動 Azure 備份復原服務保存庫
description: 如何跨 Azure 訂用帳戶和資源群組移動復原服務保存庫的指示。
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: fbbe914b3e567a1a136d735fc52965524bc17b67
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612566"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>跨 Azure 訂用帳戶和資源群組移動復原服務保存庫

本文說明如何跨 Azure 訂用帳戶或往相同訂用帳戶中的另一個資源群組移動為 Azure 備份所設定的復原服務保存庫。 您可以使用 Azure 入口網站或 PowerShell 來移動復原服務保存庫。

## <a name="supported-regions"></a>支援區域

澳大利亞東部、澳大利亞東南部、加拿大中部、加拿大東部、南部東亞、東亞、美國中部、美國中北部、美國東部、美國東部2、美國中南部、美國中西部、美國中西部2、美國西部、美國西部2、印度中部、印度南部、日本東部、日本西部、韓國中部、南韓南部、北歐、西歐、南非北部、南非西部、英國南部和英國西部。

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
- 若要移動具有受控磁碟的虛擬機器，請參閱這篇[文章](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)。
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

將復原服務保存庫和其相關聯的資源移動到不同的資源群組

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
> 不支援跨訂用帳戶備份 (RS 保存庫和受保護的 VM 位於不同訂用帳戶) 的案例。 此外，在保存庫移動作業進行期間，將無法修改從本機備援儲存體 (LRS) 到全域備援儲存體 (GRS) (反之亦然) 的儲存體備援選項。
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
2. 必須再次設定備份的報告和監視功能，保存庫的移動才會完成。 移動作業期間會失去先前的設定。

## <a name="next-steps"></a>後續步驟

您可以在資源群組和訂用帳戶之間移動許多不同類型的資源。

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
