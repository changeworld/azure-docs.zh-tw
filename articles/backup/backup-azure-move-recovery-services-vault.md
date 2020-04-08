---
title: 如何移動 Azure 備份復原服務保管庫
description: 有關如何跨 Azure 訂閱和資源組移動恢復服務保管庫的說明。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 3cfd442d49de2661d68de3c4e4b3575119504eb4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804413"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>跨 Azure 訂閱和資源組移動復原服務保管庫

本文說明如何跨 Azure 訂用帳戶或往相同訂用帳戶中的另一個資源群組移動為 Azure 備份所設定的復原服務保存庫。 您可以使用 Azure 入口網站或 PowerShell 來移動復原服務保存庫。

## <a name="supported-regions"></a>支援區域

恢復服務金庫的資源移動支援在澳大利亞東部、澳大利亞東南部、加拿大中部、加拿大東部、東南亞、東亞、美國中部、美國中北部、美國東部、美國東部、美國中南部、美國中西部、美國中西部、美國西部、印度中部、印度南部、日本東部、日本西部、韓國中部、韓國、北歐、西歐、南非北部,南非西部,英國南部,英國西部。

## <a name="unsupported-regions"></a>不支援的區域

法國中部、法國南部、德國東北部、德國中部、美國愛荷華州、中國北部、華北、中國東部、中國東部2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>移動復原服務保管庫的先決條件

- 在跨資源組移動保管庫期間,源和目標資源組都將鎖定,以防止寫入和刪除操作。 有關詳細資訊,請參閱[本文](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
- 只有管理員訂閱才具有移動保管庫的許可權。
- 要跨訂閱移動保管庫,目標訂閱必須駐留在與源訂閱相同的租戶中,並且應啟用其狀態。
- 您必須擁有在目標資源群組上執行寫入作業的權限。
- 移動保存庫只會變更資源群組。 恢復服務保管庫將駐留在同一位置,無法更改。
- 一次只能移動每個區域一個恢復服務保管庫。
- 如果 VM 未跨訂閱或新資源組隨恢復服務保管庫一起移動,則當前 VM 恢復點將保持不變,直到過期。
- 無論 VM 是否隨著保存庫移動，您一律可以從保留在保存庫內的備份記錄還原 VM。
- Azure 磁碟加密要求密鑰保管庫和 VM 駐留在同一 Azure 區域和訂閱中。
- 若要移動具有受控磁碟的虛擬機器，請參閱這篇[文章](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)。
- 移動透過經典模型部署的資源的選項因是將資源移動到訂閱中或移動到新訂閱而異。 有關詳細資訊,請參閱[本文](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
- 在保存庫跨訂用帳戶或往新的資源群組移動之後，為保存庫所定義的備份原則會保留下來。
- 不支援跨訂閱和資源組在 IaaS VM 中移動具有 Azure 檔案、Azure 檔同步或 SQL 的保管庫。
- 如果在訂閱之間移動包含 VM 備份數據的保管庫,則必須將 VM 移至同一訂閱,並使用相同的目標 VM 資源組名稱(與舊訂閱中的名稱相同)繼續備份。

> [!NOTE]
> 不支援跨 Azure 區域移動 Azure 備份的恢復服務保管庫。<br><br>
> 如果已配置任何 VM(Azure IaaS、Hyper-V、VMware)或物理電腦進行災難**恢復**,則移動操作將被阻止。 如果要移動保管庫以進行 Azure 網站恢復,請查看[本文](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions)以瞭解手動移動保管庫。

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>使用 Azure 門戶將回復服務保管庫移至不同的資源群組

將復原服務保存庫和其相關聯的資源移動到不同的資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟**復原服務保存庫**的清單，然後選取您想要移動的保存庫。 保存庫儀表板開啟時會如下圖所示。

   ![開啟復原服務保存庫](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   如果您沒有看到保管庫的 **「基本」** 資訊,請按下下拉圖示。 您現在應該就會看到保存庫的 [基本資訊] 資訊。

   ![[基本資訊] 索引標籤](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保存庫的 [概觀] 功能表中，按一下 [資源群組]**** 旁的 [變更]****，以開啟 [移動資源]**** 刀鋒視窗。

   ![變更資源群組](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. 在 **「移動資源」** 邊欄選項卡中,建議對於選定的保管庫,建議通過選中下圖所示的複選框來移動可選的相關資源。

   ![移動訂用帳戶](./media/backup-azure-move-recovery-services/move-resource.png)

5. 若要新增目標資源群組，請在 [資源群組]**** 下拉式清單中選取現有資源群組，或按一下 [建立新群組]**** 選項。

   ![建立資源](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 在新增資源群組之後，請確認 [我了解我必須先更新移動之資源所關聯的工具與指令碼，這些工具與指令碼才能使用新的資源識別碼]**** 選項，然後按一下 [確定]**** 以完成保存庫移動作業。

   ![確認訊息](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>使用 Azure 門戶將恢復服務保管庫移動到其他訂閱

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

執行上述 cmdlet 後,系統將要求您確認要移動指定的資源。 輸入 **Y** 來確認。 成功通過驗證後，資源便會移動。

## <a name="use-cli-to-move-recovery-services-vault"></a>使用 CLI 行動復原服務保存庫

若要將復原服務保存庫移動到另一個資源群組，請使用下列 Cmdlet：

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

## <a name="post-migration"></a>移轉後

1. 設置/驗證資源組的訪問控制。  
2. 必須再次設定備份的報告和監視功能，保存庫的移動才會完成。 移動作業期間會失去先前的設定。

## <a name="next-steps"></a>後續步驟

您可以在資源群組和訂用帳戶之間移動許多不同類型的資源。

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
