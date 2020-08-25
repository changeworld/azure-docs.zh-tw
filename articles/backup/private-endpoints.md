---
title: 私人端點
description: 瞭解建立 Azure 備份私用端點的程式，以及使用私人端點來協助維護資源安全性的案例。
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: bbb59439677f6c7297aabbecbafeb04327f482b5
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762907"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure 備份的私人端點

Azure 備份可讓您使用 [私人端點](../private-link/private-endpoint-overview.md)，從復原服務保存庫安全地備份和還原資料。 私人端點會使用您 VNet 中的一或多個私人 IP 位址，有效地將服務帶入您的 VNet 中。

本文將協助您瞭解建立 Azure 備份私人端點的程式，以及使用私用端點協助維護資源安全性的案例。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 只有在沒有任何專案已註冊至保存庫) 的 (，才能為新的復原服務保存庫建立私人端點。 因此，您必須先建立私人端點，才能嘗試保護保存庫中的任何專案。
- 一個虛擬網路可以包含多個復原服務保存庫的私人端點。 此外，一個復原服務保存庫在多個虛擬網路中可以有私人端點。 不過，可以為保存庫建立的私人端點數目上限是12。
- 一旦為保存庫建立私人端點，保存庫將會遭到鎖定。 它無法 (進行備份，也無法從網路（除了包含保存庫私人端點的網路）進行還原) 。 如果已移除保存庫的所有私人端點，則會從所有網路存取保存庫。
- 備份的私人端點連線會在子網中使用總計11個私人 Ip。 此數目可能會更高 (特定 Azure 區域的最多25個) 。 因此，當您嘗試建立私人端點以進行備份時，建議您有足夠的私人 Ip 可用。
- 雖然 () Azure 備份和 Azure Site Recovery 都使用復原服務保存庫，但本文只討論 Azure 備份使用私人端點。
- Azure Active Directory 目前不支援私人端點。 因此，當您在 Azure Vm 中執行資料庫的備份，並使用 MARS 代理程式進行備份時，必須允許來自受保護網路的輸出存取，才能讓 Azure Active Directory 在區域中運作所需的 Ip 和 Fqdn。 您也可以使用 NSG 標籤和 Azure 防火牆標籤來允許存取 Azure AD （適用）。
- 私人端點不支援具有網路原則的虛擬網路。 您必須先停用網路原則，才能繼續進行。
- 如果您在5月 1 2020 之前註冊了復原服務資源提供者，則必須向該訂用帳戶重新註冊。 若要重新註冊提供者，請移至您在 Azure 入口網站中的訂用帳戶，流覽至左側導覽列上的 [ **資源提供者** ]，然後選取 [ **az.recoveryservices** ]，然後選取 [ **重新註冊**]。

## <a name="recommended-and-supported-scenarios"></a>建議和支援的案例

雖然已啟用保存庫的私人端點，但它們可用來備份和還原 Azure VM 中的 SQL 和 SAP Hana 工作負載，以及僅限 MARS 代理程式備份。 您也可以使用保存庫來備份其他工作負載， (但) 不需要私人端點。 除了使用 MARS 代理程式備份 SQL 和 SAP Hana 工作負載和備份之外，私人端點也會用來執行 Azure VM 備份的檔案復原。 如需詳細資訊，請參閱下列表格：

| 使用 MARS 代理程式備份 Azure VM 中的工作負載 (SQL、SAP Hana) 、備份 | 建議使用私人端點以允許備份和還原，而不需要對虛擬網路中的 Azure 備份或 Azure 儲存體，列出任何 Ip/Fqdn。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM 備份**                                         | VM 備份不會要求您允許存取任何 Ip 或 Fqdn。 因此，它不需要私人端點來備份和復原磁碟。  <br><br>   不過，從包含私人端點的保存庫進行的檔案復原，會限制為包含保存庫私人端點的虛擬網路。 <br><br>    使用 ACL'ed 非受控磁片時，請確定包含磁片的儲存體帳戶可存取 **受信任的 Microsoft 服務** （如果已 ACL'ed）。 |
| **Azure 檔案儲存體備份**                                      | Azure 檔案儲存體備份會儲存在本機儲存體帳戶中。 因此，它不需要私人端點來進行備份和還原。 |

## <a name="creating-and-using-private-endpoints-for-backup"></a>建立和使用私人端點進行備份

本節將討論在您的虛擬網路內建立和使用私人端點以進行 Azure 備份所需的步驟。

>[!IMPORTANT]
> 強烈建議您依照本檔中所述的相同順序來執行步驟。 若未這麼做，可能會導致保存庫轉譯不相容而無法使用私人端點，且需要您以新的保存庫重新開機處理常式。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

請參閱 [這一節](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) ，以瞭解如何使用 Azure Resource Manager 用戶端建立保存庫。 這會建立已啟用其受控識別的保存庫。 若要深入瞭解復原服務 [保存庫，](./backup-azure-recovery-services-vault-overview.md)請參閱。

## <a name="enable-managed-identity-for-your-vault"></a>為您的保存庫啟用受控識別

受控識別可讓保存庫建立和使用私人端點。 本節討論如何啟用您保存庫的受控識別。

1. 移至您的復原服務保存庫-> 身分 **識別**。

    ![將身分識別狀態變更為開啟](./media/private-endpoints/identity-status-on.png)

1. 將 **狀態** 變更為 [ **開啟** ]，然後選取 [ **儲存**]。

1. 系統會產生 **物件識別碼** ，也就是保存庫的受控識別。

    >[!NOTE]
    >啟用之後，就不能 (停用受控識別，即使暫時) 也是如此。 停用受控身分識別可能會導致不一致的行為。

## <a name="dns-changes"></a>DNS 變更

使用私人端點需要私人 DNS 區域，才能讓備份擴充功能將私人連結 Fqdn 解析為私人 Ip。 全部都需要三個私人 DNS 區域。 雖然這兩個區域都必須建立與，但第三個可以選擇要與私人端點整合 (建立私人端點) 或可個別建立。

您也可以使用自訂的 DNS 伺服器。 如需使用自訂 DNS 伺服器的詳細資訊，請參閱 [自訂 dns 伺服器的 DNS 變更](#dns-changes-for-custom-dns-servers) 。

### <a name="creating-mandatory-dns-zones"></a>建立強制 DNS 區域

必須建立兩個必要的 DNS 區域：

- `privatelink.blob.core.windows.net` 備份/還原資料的 () 
- `privatelink.queue.core.windows.net` 服務通訊) 的 (

1. 在 [**所有服務**] 搜尋列中搜尋**私人 dns 區域**，然後從下拉式清單中選取 [**私人 dns 區域**]。

    ![選取私人 DNS 區域](./media/private-endpoints/private-dns-zone.png)

1. 在 [ **私人 DNS 區域** ] 窗格中，選取 [ **+ 新增** ] 按鈕以開始建立新的區域。

1. 在 [ **建立私人 DNS 區域** ] 窗格中，填入所需的詳細資料。 訂用帳戶必須與私人端點建立所在的訂用帳戶相同。

    區域必須命名為：

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **區域**                           | **服務** | **訂用帳戶和資源群組 (RG) 詳細資料**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blob        | **訂**用帳戶：與  **需要建立私人**端點的位置相同：可能是 VNET 的 rg 或私人端點的 rg |
    | `privatelink.queue.core.windows.net` | 佇列       | **Rg**： VNET 的 Rg 或私人端點的 rg |

    ![建立私人 DNS 區域](./media/private-endpoints/create-private-dns-zone.png)

1. 完成之後，請繼續檢查並建立 DNS 區域。

### <a name="optional-dns-zone"></a>選用的 DNS 區域

客戶可以選擇將其私人端點與私人 DNS 區域整合，以進行 Azure 備份 (在建立私人端點) 以進行服務通訊的章節中討論。 如果您不想要與私人 DNS 區域整合，可以選擇使用您自己的 DNS 伺服器，或分別建立私人 DNS 區域。 這是前一節所討論的兩個強制私人 DNS 區域的補充。

如果您想要在 Azure 中建立個別的私人 DNS 區域，您可以使用建立強制 DNS 區域所用的相同步驟來執行相同動作。 命名與訂用帳戶的詳細資料會在下面共用：

| **區域**                                                     | **服務** | **訂用帳戶和資源群組詳細資料**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **注意**：此處的 *地理* 位置是指區域程式碼。 例如， *wcus* 和 *ne* 分別用於美國中西部和北歐。 | 備份      | **訂**用  **帳戶：與需要建立私人**端點的位置相同：訂用帳戶內的任何 rg |

請參閱 [這份清單](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) 中的區功能變數代碼。

適用于國家地區的 URL 命名慣例：

- [中國](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [德國](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>將私人 DNS 區域連結至您的虛擬網路

上述建立的 DNS 區域現在必須連結到您要備份的伺服器所在的虛擬網路。 這必須針對您建立的所有 DNS 區域完成。

1. 移至您在上一個步驟中建立的 DNS 區域 () ，然後流覽至左側列上的 [ **虛擬網路] 連結** 。 一旦出現，請選取 [ **+ 新增** ] 按鈕
1. 填寫必要的詳細資料。 [ **訂** 用帳戶] 和 [ **虛擬網路** ] 欄位必須填入伺服器所在之虛擬網路的對應詳細資料。 其他欄位必須保持原樣。

    ![新增虛擬網路連結](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>授與保存庫的許可權以建立必要的私人端點

若要建立 Azure 備份所需的私人端點，保存庫 (受控識別的保存庫) 必須具有下列資源群組的許可權：

- 包含目標 VNet 的資源群組
- 要在其中建立私人端點的資源群組
- 包含私人 DNS 區域的資源群組

建議您將這三個資源群組的「 **參與者** 」角色授與保存庫 (受控識別) 。 下列步驟說明如何針對特定的資源群組進行這項作業 (這需要針對三個資源群組) 執行這項作業：

1. 移至資源群組，並流覽至左側列上 ** (IAM) 的存取控制 ** 。
1. 在 **存取控制**中，請移至 [ **新增角色指派**]。

    ![新增角色指派](./media/private-endpoints/add-role-assignment.png)

1. 在 [ **新增角色指派** ] 窗格中，選擇 [ **參與者** ] 作為 **角色**，然後使用保存庫的 **名稱** 作為 **主體**。 選取您的保存庫，並在完成時選取 [ **儲存** ]。

    ![選擇角色和主體](./media/private-endpoints/choose-role-and-principal.png)

若要以更細微的層級管理許可權，請參閱 [手動建立角色和許可權](#create-roles-and-permissions-manually)。

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>建立和核准 Azure 備份的私人端點

### <a name="creating-private-endpoints-for-backup"></a>建立私人端點以進行備份

本節說明為您的保存庫建立私人端點的流程。

1. 在搜尋列中，搜尋並選取 [ **Private Link**]。 這會帶您前往 **Private Link Center**。

    ![搜尋 Private Link](./media/private-endpoints/search-for-private-link.png)

1. 在左側導覽列上，選取 [ **私人端點**]。 在 [ **私人端點** ] 窗格中，選取 [ **+ 新增** ] 以開始建立保存庫的私人端點。

    ![在 Private Link 中心新增私人端點](./media/private-endpoints/add-private-endpoint.png)

1. 一旦 **建立私人端點** 程式之後，您將需要指定建立私人端點連線的詳細資料。

    1. **基本概念**：填寫私人端點的基本詳細資料。 區域應與保存庫和資源相同。

        ![填寫基本詳細資料](./media/private-endpoints/basic-details.png)

    1. **資源**：此索引標籤會要求您提及您要建立連線的 PaaS 資源。 針對您想要的訂用帳戶，從資源類型中選取 [ **az.recoveryservices]/** [保存庫]。 完成之後，請選擇您的復原服務保存庫名稱做為 **資源** ，並 **AzureBackup** 為 **目標子資源**。

        ![[填入資源] 索引標籤](./media/private-endpoints/resource-tab.png)

    1. 設定 **：在**[設定] 中，指定您要在其中建立私人端點的虛擬網路和子網。 這會是 VM 所在的 Vnet。 您可以選擇將 **私人端點** 與私人 DNS 區域整合。 或者，您也可以使用自訂 DNS 伺服器或建立私人 DNS 區域。

        ![[填入設定] 索引標籤](./media/private-endpoints/configuration-tab.png)

    1. （選擇性）您可以新增私人端點的 **標記** 。

    1. 完成輸入詳細資料後，繼續進行 **檢查 + 建立** 。 驗證完成時，請選取 [ **建立** ] 以建立私人端點。

## <a name="approving-private-endpoints"></a>核准私人端點

如果建立私人端點的使用者也是復原服務保存庫的擁有者，則會自動核准上述建立的私人端點。 否則，保存庫的擁有者必須先核准私人端點，才能使用它。 本節討論透過 Azure 入口網站手動核准私人端點。

請參閱 [使用 Azure Resource Manager 用戶端手動核准私人端點](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) ，以使用 Azure Resource Manager 用戶端來核准私人端點。

1. 在您的復原服務保存庫中，流覽至左側列上的 [ **私人端點** 連線]。
1. 選取您要核准的私人端點連接。
1. 選取頂端列上的 [ **核准** ]。 如果您想要拒絕或刪除端點連接，也可以選取 [ **拒絕** ] 或 [ **移除** ]。

    ![核准私人端點](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>新增 DNS 記錄

>[!NOTE]
> 如果您使用整合式 DNS 區域，則不需要執行此步驟。 不過，如果您已建立自己的 Azure 私人 DNS 區域，或使用自訂的私人 DNS 區域，請確定已依照本節所述的專案進行。

在您為保存庫建立了選用的私人 DNS 區域和私人端點之後，您必須將 DNS 記錄新增到您的 DNS 區域。 您可以手動或使用 PowerShell 腳本來進行這項作業。 這只需要針對您的備份 DNS 區域完成，Blob 和佇列的更新就會自動更新。

### <a name="add-records-manually"></a>手動新增記錄

這會要求您將私人端點中每個 FQDN 的專案，放入私人 DNS 區域。

1. 移至您的 **私人 DNS 區域** ，並流覽至左方列上的 **總覽** 選項。 然後選取 [ **+ 記錄集** ] 以開始新增記錄。

    ![選取 [+ 記錄集] 以新增記錄](./media/private-endpoints/select-record-set.png)

1. 在開啟的 [ **新增記錄集** ] 窗格中，為每個 FQDN 和私人 IP 新增一個專案做為 **類型** 記錄。 您可以從私人端點取得 Fqdn 和 Ip 清單， (在 **[總覽** ]) 下。 如下列範例所示，私人端點的第一個 FQDN 會新增至私人 DNS 區域中的記錄集。

    ![Fqdn 和 Ip 的清單](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![新增記錄集](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>使用 PowerShell 腳本新增記錄

1. 在 Azure 入口網站中啟動 **Cloud Shell** ，然後在 PowerShell 視窗中選取 **[上傳** 檔案]。

    ![在 PowerShell 視窗中選取上傳檔案](./media/private-endpoints/upload-file-in-powershell.png)

1. 上傳此腳本： [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. 移至您的主資料夾 (例如： `cd /home/user`) 

1. 執行下列指令碼：

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    這些是參數：

    - **訂**用帳戶：資源 (保存庫的私人端點和私人 DNS 區域) 所在的訂用帳戶
    - **vaultPEName**：為保存庫建立的私人端點名稱
    - **vaultPEResourceGroup**：包含保存庫私人端點的資源群組
    - **dnsResourceGroup**：包含私人 DNS 區域的資源群組
    - **Privatezone**：私人 DNS 區域的名稱

## <a name="using-private-endpoints-for-backup"></a>使用私人端點進行備份

一旦為您 VNet 中的保存庫建立的私人端點經過核准，您就可以開始使用它們來執行備份和還原。

>[!IMPORTANT]
>在繼續之前，請確定您已成功完成上述檔中所述的所有步驟。 您必須已完成下列檢查清單中的步驟，才能進行回顧：
>
>1. 已建立 (新的) 復原服務保存庫
>1. 已啟用保存庫以使用系統指派的受控識別
>1. 若使用整合式 DNS 區域進行備份，則建立三個私人 DNS 區域 (兩個) 
>1. 將私人 DNS 區域連結至您的 Azure 虛擬網路
>1. 已將相關許可權指派給保存庫的受控識別
>1. 為您的保存庫建立私人端點
>1. 核准私人端點 (如果未自動核准) 
>1. 將必要的 DNS 記錄新增到您的私人 DNS 區域以進行備份 (僅適用于未使用整合式私人 DNS 區域的情況) 

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>備份和還原 Azure VM 中的工作負載 (SQL、SAP Hana) 

一旦建立並核准私人端點之後，用戶端就不需要進行其他變更即可使用私人端點。 所有從您的安全網路到保存庫的通訊和資料傳輸都會透過私人端點執行。
不過，如果您在伺服器 (SQL/SAP Hana) 註冊之後移除保存庫的私人端點，您必須使用保存庫重新註冊容器。 您不需要停止保護它們。

### <a name="backup-and-restore-through-mars-agent"></a>透過 MARS 代理程式備份和還原

使用 MARS 代理程式來備份您的內部部署資源時，請確定您的內部部署網路 (包含要備份的資源) 與包含保存庫私人端點的 Azure VNet 對等互連，以便您可以使用它。 然後，您可以繼續安裝 MARS 代理程式並設定備份，如下所述。 不過，您必須確保所有進行備份的通訊只會透過對等互連網路進行。

不過，如果您在已註冊 MARS 代理程式之後移除保存庫的私人端點，您必須使用保存庫重新註冊容器。 您不需要停止保護它們。

## <a name="additional-topics"></a>其他主題

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>使用 Azure Resource Manager 用戶端建立復原服務保存庫

您可以建立復原服務保存庫，並啟用其受控識別 (必須啟用受控識別，因為我們稍後會看到使用 Azure Resource Manager 用戶端) 。 以下是以下共用的範例：

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

上述的 JSON 檔案應該具有下列內容：

要求 JSON：

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

回應 JSON：

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>您已使用系統指派的受控識別，透過 Azure Resource Manager 用戶端在此範例中建立的保存庫。

### <a name="managing-permissions-on-resource-groups"></a>管理資源群組的許可權

保存庫的受控識別在將建立私人端點的資源群組和虛擬網路中，必須具備下列許可權：

- `Microsoft.Network/privateEndpoints/*` 這是在資源群組中的私人端點上執行 CRUD 的必要項。 它應該在資源群組上指派。
- `Microsoft.Network/virtualNetworks/subnets/join/action` 這是在私人 IP 與私人端點連接的虛擬網路上的必要項。
- `Microsoft.Network/networkInterfaces/read` 這在資源群組上是必要的，才能取得為私人端點建立的網路介面。
- 私人 DNS 區域參與者角色已存在，而且可以用來提供 `Microsoft.Network/privateDnsZones/A/*` 和 `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` 許可權。

您可以使用下列其中一種方法來建立具有必要許可權的角色：

#### <a name="create-roles-and-permissions-manually"></a>手動建立角色和許可權

建立下列 JSON 檔案，並使用區段結尾的 PowerShell 命令來建立角色：

PrivateEndpointContributorRoleDef.js開啟

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.js開啟

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.js開啟

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>使用腳本

1. 在 Azure 入口網站中啟動 **Cloud Shell** ，然後在 PowerShell 視窗中選取 **[上傳** 檔案]。

    ![在 PowerShell 視窗中選取上傳檔案](./media/private-endpoints/upload-file-in-powershell.png)

1. 上傳下列腳本： [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. 移至您的主資料夾 (例如： `cd /home/user`) 

1. 執行下列指令碼：

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    這些是參數：

    - **訂**用帳戶： * * SubscriptionId 具有資源群組，其中會建立保存庫的私人端點，以及將附加保存庫私人端點的子網

    - **vaultPEResourceGroup**：將在其中建立保存庫私人端點的資源群組

    - **vaultPESubnetResourceGroup**：私人端點將加入之子網的資源群組

    - **vaultMsiName**：保存庫的 MSI 名稱，與**VaultName**相同

1. 完成驗證，腳本將會採用上述提供的指定訂用帳戶內容。 如果租使用者中缺少適當的角色，則會建立適當的角色，並將角色指派給保存庫的 MSI。

### <a name="creating-private-endpoints-using-azure-powershell"></a>使用 Azure PowerShell 建立私人端點

#### <a name="auto-approved-private-endpoints"></a>自動核准的私人端點

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>使用 Azure Resource Manager 用戶端手動核准私人端點

1. 使用 **GetVault** 取得私人端點的私人端點連接識別碼。

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    這會傳回私人端點連接識別碼。 您可以使用連接識別碼的第一個部分抓取連接的名稱，如下所示：

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 從回應中取得 **私人端點連線識別碼** (和 **私人端點名稱**) ，並將它取代為下列 JSON 和 Azure Resource Manager URI，並嘗試將狀態變更為「已核准/已拒絕/已中斷連線」，如下列範例所示：

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON：

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>自訂 DNS 伺服器的 DNS 變更

#### <a name="create-dns-zones-for-custom-dns-servers"></a>建立自訂 DNS 伺服器的 DNS 區域

您必須建立三個私人 DNS 區域，並將其連結至您的虛擬網路。

| **區域**                                                     | **服務** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | 備份      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | 佇列       |

>[!NOTE]
>在上圖中， *geo* 指的是區域程式碼。 例如， *wcus* 和 *ne* 分別用於美國中西部和北歐。

請參閱 [這份清單](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) 中的區功能變數代碼。

#### <a name="adding-dns-records-for-custom-dns-servers"></a>新增自訂 DNS 伺服器的 DNS 記錄

這會要求您將私人端點中每個 FQDN 的專案，放入私人 DNS 區域。

請注意，我們將使用針對備份、Blob 和佇列服務所建立的私人端點。

- 保存庫的私人端點會使用建立私人端點時所指定的名稱
- Blob 和佇列服務的私人端點前面會加上與保存庫相同的名稱。

例如，下圖顯示針對名稱為 *pee2epe*的私人端點連線所建立的三個私人端點：

![私人端點連接的三個私人端點](./media/private-endpoints/three-private-endpoints.png)

備份服務的 DNS 區域 (`privatelink.<geo>.backup.windowsazure.com`) ：

1. 流覽至您的私人端點，以在 **Private Link 中心**進行備份。 [總覽] 頁面會列出私人端點的 FQDN 和私人 Ip。

1. 將每個 FQDN 和私人 IP 的一個專案新增為類型記錄。

    ![新增每個 FQDN 和私人 IP 的專案](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Blob 服務的 DNS 區域 (`privatelink.blob.core.windows.net`) ：

1. 流覽至 **Private Link 中心**內的 Blob 私人端點。 [總覽] 頁面會列出私人端點的 FQDN 和私人 Ip。

1. 將 FQDN 和私人 IP 的專案新增為類型記錄。

    ![將 FQDN 和私人 IP 的專案新增為 Blob 服務的類型記錄](./media/private-endpoints/add-type-a-record-for-blob.png)

佇列服務 () 的 DNS 區域 `privatelink.queue.core.windows.net` ：

1. 在 **Private Link 中心**流覽至佇列的私人端點。 [總覽] 頁面會列出私人端點的 FQDN 和私人 Ip。

1. 將 FQDN 和私人 IP 的專案新增為類型記錄。

    ![將 FQDN 和私人 IP 的專案新增為佇列服務的類型記錄](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>常見問題集

Q. 是否可以為現有的備份保存庫建立私人端點？<br>
A. 否，只能針對新的備份保存庫建立私人端點。 因此，保存庫絕不能有任何受保護的專案。 事實上，在建立私人端點之前，不會嘗試保護保存庫中的任何專案。

Q. 我已嘗試保護保存庫的專案，但該專案失敗，而且保存庫仍未包含任何受保護的專案。 是否可以為此保存庫建立私人端點？<br>
A. 否，保存庫不得在過去任何嘗試保護其任何專案。

Q. 我有一個保存庫使用私人端點進行備份和還原。 我可以稍後新增或移除此保存庫的私人端點，即使我已保護備份專案？<br>
A. 是。 如果您已為保存庫和受保護的備份專案建立私人端點，您稍後可以視需要新增或移除私人端點。

Q. Azure 備份的私人端點也可以用於 Azure Site Recovery 嗎？<br>
A. 否，備份的私人端點只能用於 Azure 備份。 如果服務支援，您必須為 Azure Site Recovery 建立新的私人端點。

Q. 我錯過了本文中的其中一個步驟，並在保護我的資料來源。 我是否仍然可以使用私人端點？<br>
A. 若未遵循本文中的步驟並繼續保護專案，可能會導致保存庫無法使用私人端點。 因此，建議您在繼續保護專案之前，先參考此檢查清單。

Q. 我可以使用自己的 DNS 伺服器，而不是使用 Azure 私人 DNS 區域或整合式私人 DNS 區域嗎？<br>
A. 是的，您可以使用自己的 DNS 伺服器。 不過，請確定已新增所有必要的 DNS 記錄，如本節中所建議。

Q. 遵循本文中的程式之後，我是否需要在我的伺服器上執行任何額外的步驟？<br>
A. 遵循本文中詳述的程式之後，您就不需要執行額外的工作來使用私人端點進行備份和還原。

## <a name="next-steps"></a>下一步

- 閱讀 [Azure 備份中的所有安全性功能](security-overview.md)
