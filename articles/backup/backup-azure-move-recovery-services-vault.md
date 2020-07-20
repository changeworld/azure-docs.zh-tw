---
title: 如何移動 Azure 備份復原服務保存庫
description: 有關如何跨 Azure 訂用帳戶和資源群組移動復原服務保存庫的指示。
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 9373ea41c3cd5d35c86b8b306a20b5c106105217
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368221"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>跨 Azure 訂用帳戶和資源群組移動復原服務保存庫

本文說明如何跨 Azure 訂用帳戶或往相同訂用帳戶中的另一個資源群組移動為 Azure 備份所設定的復原服務保存庫。 您可以使用 Azure 入口網站或 PowerShell 來移動復原服務保存庫。

## <a name="supported-regions"></a>支援區域

澳大利亞東部、澳大利亞東南部、加拿大中部、加拿大東部、南部東亞、東亞、美國中部、美國中北部、美國東部、東部美國2、美國中南部、美國中西部、西歐美國2、美國西部、印度中部、印度南部、日本東部、日本西部、韓國中部、南韓南部、北歐、西歐、南非北部，皆可支援復原服務保存庫的資源移動、南非西部、英國南部和英國西部。

## <a name="unsupported-regions"></a>不支援的區域

法國中部、法國南部、德國東北部、德國中部、US Gov 愛荷華州、中國北部、中國北部2、中國東部、中國東部2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>移動復原服務保存庫的必要條件

- 在跨資源群組移動保存庫期間，來源和目標資源群組都會被鎖定，而導致寫入和刪除作業無法執行。 如需詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
- 只有管理訂用帳戶具有移動保存庫的許可權。
- 針對跨訂用帳戶移動保存庫，目標訂用帳戶必須位於與來源訂用帳戶相同的租使用者中，且其狀態應為 [已啟用]。
- 您必須擁有在目標資源群組上執行寫入作業的權限。
- 移動保存庫只會變更資源群組。 復原服務保存庫將位於相同的位置，而且無法變更。
- 您一次只能移動一個復原服務保存庫（每個區域）。
- 如果 VM 不會在訂用帳戶之間移動復原服務保存庫，或移至新的資源群組，則目前的 VM 復原點會在保存庫中保持不變，直到過期為止。
- 無論 VM 是否隨著保存庫移動，您一律可以從保留在保存庫內的備份記錄還原 VM。
- Azure 磁碟加密需要金鑰保存庫和 Vm 位於相同的 Azure 區域和訂用帳戶中。
- 若要移動具有受控磁碟的虛擬機器，請參閱這篇[文章](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)。
- 移動透過傳統模型所部署之資源的選項，會根據您是要移動訂用帳戶內的資源還是新的訂用帳戶而有所不同。 如需詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
- 在保存庫跨訂用帳戶或往新的資源群組移動之後，為保存庫所定義的備份原則會保留下來。
- 您只能移動保存庫，其中包含下列任何類型的備份專案。 下面未列出之類型的任何備份專案都必須停止，且資料在移動保存庫之前會永久刪除。
  - Azure 虛擬機器
  - Microsoft Azure 復原服務（MARS）代理程式
  - Microsoft Azure 備份伺服器（MABS）
  - Data Protection Manager (DPM)
- 如果您移動的保存庫包含 VM 備份資料，則您必須將 Vm 移至相同的訂用帳戶，並使用相同的目標 VM 資源組名（如同舊的訂用帳戶）來繼續備份。

> [!NOTE]
> 不支援跨 Azure 區域移動復原服務保存庫以進行 Azure 備份。<br><br>
> 如果您已使用**Azure Site Recovery**設定任何 Vm （Azure IaaS、Hyper-v、VMware）或實體機器，以進行嚴重損壞修復，則移動作業將會遭到封鎖。 如果您想要移動保存庫以進行 Azure Site Recovery，請參閱[這篇文章](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions)，以瞭解如何手動移動保存庫。

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>使用 Azure 入口網站將復原服務保存庫移至不同的資源群組

將復原服務保存庫和其相關聯的資源移動到不同的資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟**復原服務保存庫**的清單，然後選取您想要移動的保存庫。 保存庫儀表板開啟時會如下圖所示。

   ![開啟復原服務保存庫](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   如果您沒有看到保存庫的**基本**資訊，請按一下下拉式圖示。 您現在應該就會看到保存庫的 [基本資訊] 資訊。

   ![[基本資訊] 索引標籤](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保存庫的 [概觀] 功能表中，按一下 [資源群組]**** 旁的 [變更]****，以開啟 [移動資源]**** 刀鋒視窗。

   ![變更資源群組](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. 在 [**移動資源**] 分頁中，針對選取的保存庫，建議您選取核取方塊來移動選擇性的相關資源，如下圖所示。

   ![移動訂用帳戶](./media/backup-azure-move-recovery-services/move-resource.png)

5. 若要新增目標資源群組，請在 [資源群組]**** 下拉式清單中選取現有資源群組，或按一下 [建立新群組]**** 選項。

   ![建立資源](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 在新增資源群組之後，請確認 [我了解我必須先更新移動之資源所關聯的工具與指令碼，這些工具與指令碼才能使用新的資源識別碼]**** 選項，然後按一下 [確定]**** 以完成保存庫移動作業。

   ![確認訊息](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>使用 Azure 入口網站將復原服務保存庫移至不同的訂用帳戶

您可以將復原服務保存庫和其相關聯的資源移動到不同的訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟復原服務保存庫的清單，然後選取您想要移動的保存庫。 保存庫儀表板開啟時會如下圖所示。

    ![開啟復原服務保存庫](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    如果您沒看到保存庫的 [基本資訊]**** 資訊，請按下拉圖示。 您現在應該就會看到保存庫的 [基本資訊] 資訊。

    ![[基本資訊] 索引標籤](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保存庫的 [概觀] 功能表中，按一下 [訂用帳戶]**** 旁的 [變更]****，以開啟 [移動資源]**** 刀鋒視窗。

   ![變更訂用帳戶](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 選取要移動的資源，建議您在這裡使用 [全選]**** 選項來選取所有列出的選擇性資源。

   ![移動資源](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 從 [訂用帳戶]**** 下拉式清單選取您想要作為保存庫移動目的地的目標訂用帳戶。
6. 若要新增目標資源群組，請在 [資源群組]**** 下拉式清單中選取現有資源群組，或按一下 [建立新群組]**** 選項。

   ![新增訂用帳戶](./media/backup-azure-move-recovery-services/add-subscription.png)

7. 按一下 [我了解我必須先更新移動之資源所關聯的工具與指令碼，這些工具與指令碼才能使用新的資源識別碼]**** 選項來進行確認，然後按一下 [確定]****。

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

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
