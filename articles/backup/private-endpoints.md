---
title: 私人端點
description: 瞭解為 Azure 備份建立私用端點的程式，以及使用私用端點來協助維護資源安全性的案例。
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: bc778506819c44291bb2d8f69cdd9ac0aed51399
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007852"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure 備份的私用端點

Azure 備份可讓您使用[私人端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)，安全地備份和還原復原服務保存庫中的資料。 私人端點會使用您 VNet 中的一或多個私人 IP 位址，有效地將服務帶入您的 VNet。

本文將協助您瞭解為 Azure 備份建立私用端點的程式，以及使用私用端點來協助維護資源安全性的案例。

## <a name="before-you-start"></a>開始之前

- 只能為新的復原服務保存庫建立私人端點（不會有任何專案註冊到保存庫）。 因此，在您嘗試保護保存庫的任何專案之前，必須先建立私人端點。
- 一個虛擬網路可以包含多個復原服務保存庫的私人端點。 此外，一個復原服務保存庫在多個虛擬網路中可以有私人端點。 不過，可以為保存庫建立的私人端點數目上限為12。
- 一旦建立保存庫的私用端點，將會鎖定保存庫。 它無法從網路存取（用於備份和還原），除了包含保存庫私用端點的網路之外。 如果移除保存庫的所有私人端點，則會從所有網路存取保存庫。
- 雖然「復原服務」保存庫是由（兩者） Azure 備份和 Azure Site Recovery 使用，但本文只討論使用私人端點進行 Azure 備份。
- Azure Active Directory 目前不支援私用端點。 因此，在 Azure Vm 中執行資料庫的備份並使用 MARS 代理程式進行備份時，必須允許從受保護的網路對 Azure Active Directory 所需的 Ip 和 Fqdn 進行輸出存取。 您也可以使用 NSG 標籤和 Azure 防火牆標記，以允許 Azure AD 的存取權（如適用）。
- 私人端點不支援具有網路原則的虛擬網路。 您必須先停用網路原則，才能繼續進行。

## <a name="recommended-and-supported-scenarios"></a>建議和支援的案例

雖然已為保存庫啟用私用端點，但它們僅用於備份和還原 Azure VM 和 MARS 代理程式備份中的 SQL 和 SAP Hana 工作負載。 您也可以使用保存庫來備份其他工作負載（但它們不需要私用端點）。 除了使用 MARS 代理程式備份 SQL 和 SAP Hana 工作負載和備份之外，在 Azure VM 備份的情況下，也會使用私人端點來執行檔案復原。 如需詳細資訊，請參閱下列表格：

| Azure VM （SQL、SAP Hana）中的工作負載備份、使用 MARS 代理程式進行備份 | 建議使用私用端點來允許備份和還原，而不需要允許-列出從虛擬網路 Azure 備份或 Azure 儲存體的任何 Ip/Fqdn。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM 備份**                                         | VM 備份不會要求您允許存取任何 Ip 或 Fqdn。 因此，它不需要私用端點來備份和復原磁碟。  <br><br>   不過，從包含私人端點的保存庫進行的檔案復原，將會限制為包含保存庫私人端點的虛擬網路。 <br><br>    使用 ACL'ed 非受控磁片時，請確定包含磁片的儲存體帳戶允許存取**受信任的 Microsoft 服務**（如果已 ACL'ed）。 |
| **Azure 檔案儲存體備份**                                      | Azure 檔案儲存體備份會儲存在本機儲存體帳戶中。 因此，它不需要私用端點來進行備份和還原。 |

## <a name="creating-and-using-private-endpoints-for-backup"></a>建立和使用私人端點以進行備份

本節討論在您的虛擬網路內建立和使用 Azure 備份私用端點的相關步驟。

>[!IMPORTANT]
> 強烈建議您依照本檔中所述的相同順序來遵循步驟。 若未這麼做，可能會導致保存庫轉譯為不相容，因此無法使用私用端點，並要求您以新的保存庫重新開機該進程。

>[!NOTE]
> 目前無法使用 Azure 入口網站體驗的特定元素。 請參閱這類案例中的替代體驗，直到您區域的完整可用性為止。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

請[參閱本節，](#create-a-recovery-services-vault-using-the-azure-resource-manager-client)以瞭解如何使用 Azure Resource Manager 用戶端建立保存庫。 這會建立已啟用其受控識別的保存庫。 [在這裡](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)深入瞭解復原服務保存庫。

## <a name="enable-managed-identity-for-your-vault"></a>為您的保存庫啟用受控識別

受控識別可讓保存庫建立和使用私人端點。 本節討論如何為您的保存庫啟用受控識別。

1. 移至您的復原服務保存庫-> 身分**識別**。

    ![將身分識別狀態變更為開啟](./media/private-endpoints/identity-status-on.png)

1. 將**狀態**變更為 [**開啟**]，然後按一下 [**儲存**]。

1. 系統會產生**物件識別碼**，也就是保存庫的受控識別。

    >[!NOTE]
    >啟用之後，就不能停用受控識別（甚至暫時）。 停用受控識別可能會導致不一致的行為。

## <a name="dns-changes"></a>DNS 變更

使用私人端點需要私人 DNS 區域，才能讓備份擴充功能將私人連結 Fqdn 解析為私人 Ip。 全部都需要三個私人 DNS 區域。 雖然這些區域中的兩個都必須建立 mandatorily，但第三個則可以選擇與私人端點整合（建立私人端點時），也可以另外建立。

您也可以使用您的自訂 DNS 伺服器。 如需使用自訂 DNS 伺服器的詳細資訊，請參閱[自訂 dns 伺服器的 DNS 變更](#dns-changes-for-custom-dns-servers)。

### <a name="creating-mandatory-dns-zones"></a>建立強制 DNS 區域

需要建立兩個必要的 DNS 區域：

- `privatelink.blob.core.windows.net`（適用于備份/還原資料）
- `privatelink.queue.core.windows.net`（適用于服務通訊）

1. 在 [**所有服務**] 搜尋列中搜尋 [**私人 dns 區域**]，然後從下拉式清單中選取 [**私人 dns 區域**]。

    ![選取私人 DNS 區域](./media/private-endpoints/private-dns-zone.png)

1. 在 [**私人 DNS 區域**] 窗格中，按一下 [ **+ 新增**] 按鈕以開始建立新的區域。

1. 在 [**建立私人 DNS 區域**] 窗格中，填入所需的詳細資料。 訂用帳戶必須與將建立私人端點的位置相同。

    區域必須命名為：

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **Zone**                           | **服務** | **訂用帳戶和資源群組（RG）詳細資料**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blob        | **訂**用帳戶：與**需要建立私**用端點的位置相同：您可以是 VNET 的 rg 或私人端點的 rg |
    | `privatelink.queue.core.windows.net` | 佇列       | **Rg**： VNET 或私人端點的 rg |

    ![建立私人 DNS 區域](./media/private-endpoints/create-private-dns-zone.png)

1. 完成後，請繼續審查並建立 DNS 區域。

### <a name="optional-dns-zone"></a>選擇性的 DNS 區域

客戶可以選擇將其私人端點與私人 DNS 區域整合，以進行服務通訊的 Azure 備份（建立私人端點一節中所討論）。 如果您不想要與私人 DNS 區域整合，您可以選擇使用您自己的 DNS 伺服器，或分別建立私人 DNS 區域。 這是前一節所討論的兩個強制私人 DNS 區域的補充。

如果您想要在 Azure 中建立個別的私人 DNS 區域，您可以使用建立必要 DNS 區域所用的相同步驟來執行相同動作。 命名和訂用帳戶詳細資料會在下面共用：

| **Zone**                                                     | **服務** | **訂用帳戶和資源群組詳細資料**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **注意**：這裡的*地理*位置是指地區代碼。 例如， *wcus*和*ne*分別適用于美國中西部和北歐。 | Backup       | **訂**用**帳戶：與需要建立私人**端點的位置相同：訂用帳戶內的任何 rg |

請參閱[這份清單](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)中的區功能變數代碼。

針對國家地區中的 URL 命名慣例：

- [中國](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [德國](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)
- [US Gov](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>連結私人 DNS 區域與您的虛擬網路

上述建立的 DNS 區域現在必須連結到要備份的伺服器所在的虛擬網路。 這必須針對您建立的所有 DNS 區域進行。

1. 移至您的 DNS 區域（您在上一個步驟中建立的），然後流覽至左欄中的 [**虛擬網路] 連結**。 在該處按一下 [ **+ 新增**] 按鈕
1. 填入必要的詳細資料。 [**訂**用帳戶] 和 [**虛擬網路**] 欄位必須填入您的伺服器所在之虛擬網路的對應詳細資料。 其他欄位必須保持為 [是]。

    ![新增虛擬網路連結](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>授與保存庫的許可權，以建立必要的私用端點

若要建立 Azure 備份的必要私用端點，保存庫（保存庫的受控識別）必須具有下列資源群組的許可權：

- 包含目標 VNet 的資源群組
- 要在其中建立私人端點的資源群組
- 包含私人 DNS 區域的資源群組

我們建議您將這三個資源群組的**參與者**角色授與保存庫（受控識別）。 下列步驟說明如何針對特定的資源群組執行此動作（這需要針對這三個資源群組進行這項作業）：

1. 移至資源群組，並流覽至左側列上的 **[存取控制（IAM）** ]。
1. 在**存取控制**中，移至 [**新增角色指派**]。

    ![新增角色指派](./media/private-endpoints/add-role-assignment.png)

1. 在 [**新增角色指派**] 窗格中，選擇 [**參與者**] 做為**角色**，並使用保存庫的**名稱**作為**主體**。 選取您的保存庫，然後在完成時按一下 [**儲存**]。

    ![選擇角色和主體](./media/private-endpoints/choose-role-and-principal.png)

若要以更細微的層級管理許可權，請參閱[手動建立角色和許可權](#create-roles-and-permissions-manually)。

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>建立和核准 Azure 備份的私用端點

### <a name="creating-private-endpoints-for-backup"></a>建立用於備份的私用端點

本節說明為您的保存庫建立私人端點的程式。

1. 在搜尋列中，搜尋並選取 [**私人連結**]。 這會帶您前往**私人連結中心**。

    ![搜尋私人連結](./media/private-endpoints/search-for-private-link.png)

1. 在左側導覽列上，按一下 [**私人端點**]。 在 [**私人端點**] 窗格中，按一下 [ **+ 新增**] 開始建立保存庫的私人端點。

    ![在私人連結中心新增私人端點](./media/private-endpoints/add-private-endpoint.png)

1. 一旦在**建立私用端點**程式中，您將需要指定建立私人端點連線的詳細資料。

    1. **基本概念**：填入私人端點的基本詳細資料。 該區域應該與保存庫和資源相同。

        ![填入基本詳細資料](./media/private-endpoints/basic-details.png)

    1. **資源**：此索引標籤會要求您提及要建立連接的 PaaS 資源。 針對您想要的訂用帳戶，從資源類型中選取 [ **azurerm.recoveryservices/保存庫**]。 完成後，請選擇復原服務保存庫的名稱作為**資源**，並**AzureBackup**為**目標子資源**。

        ![[填寫資源] 索引標籤](./media/private-endpoints/resource-tab.png)

    1. 設定 **：在**[設定] 中，指定您要在其中建立私人端點的虛擬網路和子網。 這會是 VM 所在的 Vnet。 您可以選擇**整合私人端點**與私人 DNS 區域。 或者，您也可以使用您的自訂 DNS 伺服器或建立私人 DNS 區域。

        ![填寫 [設定] 索引標籤](./media/private-endpoints/configuration-tab.png)

    1. （選擇性）您可以為私用端點新增**標記**。

    1. 完成輸入詳細資料之後，請繼續**查看 + 建立**。 當驗證完成時，按一下 [**建立**] 以建立私用端點。

## <a name="approving-private-endpoints"></a>核准私用端點

如果建立私人端點的使用者也是復原服務保存庫的擁有者，則會自動核准上面建立的私用端點。 否則，保存庫的擁有者必須先核准私用端點，才能夠使用它。 本節討論透過 Azure 入口網站手動核准私人端點。

請參閱[使用 Azure Resource Manager 用戶端手動核准私人端點](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client)，以使用 Azure Resource Manager 用戶端來核准私人端點。

1. 在您的復原服務保存庫中，流覽至左側列上的 [**私人端點**連線]。
1. 選取您想要核准的私人端點連線。
1. 在頂端列上選取 [**核准**]。 如果您想要拒絕或刪除端點連線，您也可以選取 [**拒絕**] 或 [**移除**]。

    ![核准私人端點](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>新增 DNS 記錄

>[!NOTE]
> 如果您使用整合的 DNS 區域，則不需要執行此步驟。 不過，如果您已建立自己的 Azure 私人 DNS 區域，或使用自訂的私人 DNS 區域，請確定已依照本節所述的方式進行專案。

一旦您建立了保存庫的選擇性私人 DNS 區域和私人端點之後，就必須將 DNS 記錄新增至您的 DNS 區域。 您可以手動方式或使用 PowerShell 腳本來執行此動作。 這只需要針對您的備份 DNS 區域完成，Blob 和佇列的就會自動更新。

### <a name="add-records-manually"></a>手動新增記錄

這會要求您將私人端點中每個 FQDN 的專案，都放入私人 DNS 區域中。

1. 移至您的**私人 DNS 區域**，然後流覽至左側工具列上的 [**總覽**] 選項。 一旦出現之後，請按一下 [ **+ 記錄集**] 以開始加入記錄。

    ![選取 [+ 記錄集] 以新增記錄](./media/private-endpoints/select-record-set.png)

1. 在開啟的 [**新增記錄集**] 窗格中，為每個 FQDN 和私人 IP 新增一個專案作為**類型**記錄。 Fqdn 和 Ip 的清單可以從您的私人端點取得（在 **[總覽**] 底下）。 如下列範例所示，私人端點的第一個 FQDN 會新增至私人 DNS 區域中的記錄集。

    ![Fqdn 和 Ip 的清單](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![新增記錄集](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>使用 PowerShell 腳本新增記錄

1. 啟動 Azure 入口網站中的**Cloud Shell** ，然後在 PowerShell 視窗中選取 [**上傳**檔案]。

    ![在 PowerShell 視窗中選取 [上傳檔案]](./media/private-endpoints/upload-file-in-powershell.png)

1. 上傳此腳本： [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. 移至您的主資料夾（例如： `cd /home/user` ）

1. 執行下列指令碼：

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    這些是參數：

    - **訂**用帳戶：資源（保存庫的私人端點和私人 DNS 區域）所在的訂用帳戶
    - **vaultPEName**：為保存庫建立的私用端點名稱
    - **vaultPEResourceGroup**：包含保存庫私用端點的資源群組
    - **dnsResourceGroup**：包含私人 DNS 區域的資源群組
    - **Privatezone**：私人 DNS 區域的名稱

## <a name="using-private-endpoints-for-backup"></a>使用私用端點進行備份

在您的 VNet 中為保存庫建立的私用端點一經核准，您就可以開始使用它們來執行備份和還原。

>[!IMPORTANT]
>請確定您已成功完成上述檔中所述的所有步驟，再繼續進行。 若要回顧，您必須已完成下列檢查清單中的步驟：
>
>1. 建立（新的）復原服務保存庫
>1. 已啟用保存庫以使用系統指派的受控識別
>1. 建立三個私人 DNS 區域（如果使用整合的 DNS 區域來進行備份，則為兩個）
>1. 將私人 DNS 區域連結至您的 Azure 虛擬網路
>1. 已將相關許可權指派給保存庫的受控識別
>1. 建立保存庫的私用端點
>1. 已核准私用端點（如果未自動核准）
>1. 已將必要的 DNS 記錄新增至您的私人 DNS 區域以進行備份（僅適用于未使用整合式私人 DNS 區域時）

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>備份和還原 Azure VM 中的工作負載（SQL、SAP Hana）

一旦建立並核准私用端點，用戶端就不需要進行任何其他變更即可使用私用端點。 所有從您安全的網路到保存庫的通訊和資料傳輸，都會透過私人端點來執行。
不過，如果您在伺服器（SQL/SAP Hana）註冊之後移除保存庫的私用端點，則必須使用保存庫重新註冊容器。 您不需要停止保護它們。

### <a name="backup-and-restore-through-mars-agent"></a>透過 MARS 代理程式進行備份和還原

使用 MARS 代理程式來備份您的內部部署資源時，請確定您的內部部署網路（包含要備份的資源）是使用包含保存庫私人端點的 Azure VNet 對等互連的，因此您可以使用它。 接著，您可以繼續安裝 MARS 代理程式並設定備份，詳細資訊請參閱這裡。 不過，您必須確保所有用於備份的通訊只會透過對等互連網路進行。

不過，如果您在 MARS 代理程式註冊後移除保存庫的私用端點，則必須使用保存庫重新註冊容器。 您不需要停止保護它們。

## <a name="additional-topics"></a>其他主題

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>使用 Azure Resource Manager 用戶端建立復原服務保存庫

您可以建立復原服務保存庫，並啟用其受控識別（如稍後所見，我們將會使用 Azure Resource Manager 用戶端來啟用受控識別）。 以下是共用此作業的範例：

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
>在此範例中透過 Azure Resource Manager 用戶端建立的保存庫，已經使用系統指派的受控識別來建立。

### <a name="managing-permissions-on-resource-groups"></a>管理資源群組的許可權

保存庫的受控識別必須在將建立私人端點的資源群組和虛擬網路中擁有下列許可權：

- `Microsoft.Network/privateEndpoints/*`若要在資源群組中的私人端點上執行 CRUD，這是必要的。 它應該在資源群組上指派。
- `Microsoft.Network/virtualNetworks/subnets/join/action`在私人 IP 與私人端點連接的虛擬網路上，這是必要的。
- `Microsoft.Network/networkInterfaces/read`這是資源群組上的必要資訊，以取得為私人端點建立的網路介面。
- 私人 DNS 區域參與者角色此角色已存在，可用於提供 `Microsoft.Network/privateDnsZones/A/*` 和 `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` 許可權。

您可以使用下列其中一種方法來建立具有必要許可權的角色：

#### <a name="create-roles-and-permissions-manually"></a>手動建立角色和許可權

建立下列 JSON 檔案，並在區段結尾使用 PowerShell 命令來建立角色：

PrivateEndpointContributorRoleDef json

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

NetworkInterfaceReaderRoleDef json

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

PrivateEndpointSubnetContributorRoleDef json

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

1. 啟動 Azure 入口網站中的**Cloud Shell** ，然後在 PowerShell 視窗中選取 [**上傳**檔案]。

    ![在 PowerShell 視窗中選取 [上傳檔案]](./media/private-endpoints/upload-file-in-powershell.png)

1. 上傳下列腳本： [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. 移至您的主資料夾（例如： `cd /home/user` ）

1. 執行下列指令碼：

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    這些是參數：

    - **訂**用帳戶： * * 具有資源群組的 SubscriptionId，其中將會建立保存庫的私用端點，以及將附加保存庫私人端點的子網

    - **vaultPEResourceGroup**：將在其中建立保存庫私人端點的資源群組

    - **vaultPESubnetResourceGroup**：將聯結私人端點之子網的資源群組

    - **vaultMsiName**：保存庫 MSI 的名稱，與**VaultName**相同

1. 完成驗證，腳本會採用上述所提供的指定訂用帳戶的內容。 如果租使用者中缺少適當的角色，則會加以建立，並將角色指派給保存庫的 MSI。

### <a name="creating-private-endpoints-using-azure-powershell"></a>使用 Azure PowerShell 建立私用端點

#### <a name="auto-approved-private-endpoints"></a>自動核准的私用端點

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

1. 使用**GetVault**來取得私用端點的私用端點連接識別碼。

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    這會傳回私用端點連接識別碼。 您可以使用連接識別碼的第一個部分來抓取連接的名稱，如下所示：

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 從回應中取得**私人端點連線識別碼**（和**私人端點名稱**），並將它取代為下列 JSON 並 Azure Resource Manager URI，然後嘗試將狀態變更為「已核准/已拒絕/已中斷」，如下列範例所示：

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

您需要建立三個私人 DNS 區域，並將其連結至您的虛擬網路。

| **Zone**                                                     | **服務** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup       |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | 佇列       |

>[!NOTE]
>在上述文字中， *geo*指的是地區碼。 例如， *wcus*和*ne*分別適用于美國中西部和北歐。

請參閱[這份清單](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)中的區功能變數代碼。

#### <a name="adding-dns-records-for-custom-dns-servers"></a>新增自訂 DNS 伺服器的 DNS 記錄

這會要求您將私人端點中每個 FQDN 的專案，都放入私人 DNS 區域中。

請注意，我們將使用針對備份、Blob 和佇列服務所建立的私用端點。

- 保存庫的私用端點會使用建立私用端點時所指定的名稱。
- Blob 和佇列服務的私用端點前面會加上保存庫的相同名稱。

例如，下圖顯示針對名稱為*pee2epe*的私用端點連線所建立的三個私人端點：

![私用端點連接的三個私人端點](./media/private-endpoints/three-private-endpoints.png)

備份服務的 DNS 區域（ `privatelink.<geo>.backup.windowsazure.com` ）：

1. 在**私人連結中心**流覽至您的私用端點以進行備份。 [總覽] 頁面會列出私人端點的 FQDN 和私人 Ip。

1. 為每個 FQDN 和私人 IP 新增一個專案作為類型記錄。

    ![為每個 FQDN 和私人 IP 新增專案](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Blob 服務的 DNS 區域（ `privatelink.blob.core.windows.net` ）：

1. 在**私人連結中心**流覽至您的 Blob 私人端點。 [總覽] 頁面會列出私人端點的 FQDN 和私人 Ip。

1. 新增 FQDN 和私人 IP 的專案做為類型記錄。

    ![將 FQDN 和私人 IP 的專案新增為 Blob 服務的類型記錄](./media/private-endpoints/add-type-a-record-for-blob.png)

佇列服務的 DNS 區域（ `privatelink.queue.core.windows.net` ）：

1. 在**私人連結中心**，流覽至您的佇列私人端點。 [總覽] 頁面會列出私人端點的 FQDN 和私人 Ip。

1. 新增 FQDN 和私人 IP 的專案做為類型記錄。

    ![將 FQDN 和私人 IP 的專案新增為佇列服務的類型記錄](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>常見問題集

Q. 我可以為現有的備份保存庫建立私人端點嗎？<br>
A. 否，只能為新的備份保存庫建立私人端點。 因此，保存庫不一定有任何專案受到保護。 事實上，在建立私用端點之前，都不會嘗試保護保存庫的任何專案。

Q. 我嘗試保護保存庫中的某個專案，但它失敗，而且保存庫仍未包含任何受保護的專案。 我可以建立此保存庫的私人端點嗎？<br>
A. 不可以，保存庫在過去不能嘗試保護任何專案。

Q. 我有一個使用私人端點進行備份和還原的保存庫。 我稍後可以在此保存庫中新增或移除私人端點，即使我的備份專案受到保護嗎？<br>
A. 是。 如果您已為保存庫建立私人端點，並將受保護的備份專案加入其中，您稍後可以視需要新增或移除私人端點。

Q. Azure 備份的私用端點也可以用於 Azure Site Recovery 嗎？<br>
A. 否，備份的私用端點只能用於 Azure 備份。 如果服務支援，您將需要為 Azure Site Recovery 建立新的私用端點。

Q. 我錯過了這篇文章中的其中一項步驟，並已進入保護我的資料來源。 我仍然可以使用私人端點嗎？<br>
A. 未遵循本文中的步驟並繼續保護專案可能會導致保存庫無法使用私人端點。 因此，建議您先參閱這份檢查清單，然後再繼續保護專案。

Q. 我可以使用自己的 DNS 伺服器，而不是使用 Azure 私人 DNS 區域或整合式私人 DNS 區域嗎？<br>
A. 是，您可以使用自己的 DNS 伺服器。 不過，請確定所有必要的 DNS 記錄都會依照本節中的建議新增。

Q. 在遵循本文中的程式之後，我是否需要在我的伺服器上執行任何額外的步驟？<br>
A. 遵循本文中詳述的程式之後，您不需要執行其他工作來使用私人端點來進行備份和還原。

## <a name="next-steps"></a>後續步驟

- 閱讀[Azure 備份中的所有安全性功能](security-overview.md)
