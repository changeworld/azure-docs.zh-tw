---
title: 針對具有私人端點的內部部署機器啟用複寫
description: 本文說明如何使用 Site Recovery 中的私人端點來設定內部部署機器的複寫。
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 13c19f07ac21f986a5523407e46c59c050ebf96d
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142072"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>使用私人端點複寫內部部署機器

Azure Site Recovery 可讓您使用[Azure 私人連結](../private-link/private-endpoint-overview.md)私人端點，將內部部署機器複寫至 azure 中的虛擬網路。 下欄區域支援對復原保存庫的私用端點存取：

- Azure 商業：美國中南部、美國西部2、美國東部
- Azure Government： US Gov 維吉尼亞州、US Gov 亞利桑那州、US Gov 德克薩斯州、US DoD 東部、US DoD 中部

本文說明如何完成下列步驟︰

- 建立 Azure 備份復原服務保存庫，以保護您的電腦。
- 啟用保存庫的受控識別。 授與存取儲存體帳戶所需的許可權，以啟用從內部部署到 Azure 目標位置的流量複寫。 必須有儲存體的受控識別存取權，才能存取保存庫的私人連結。

- 進行私人端點所需的 DNS 變更。
- 為虛擬網路內的保存庫建立和核准私人端點。
- 建立儲存體帳戶的私人端點。 您可以視需要繼續允許儲存體的公用或防火牆存取。 Azure Site Recovery 不需要建立私人端點來存取儲存體。
  
下圖顯示使用私人端點進行混合式嚴重損壞修復的複寫工作流程。 您無法在內部部署網路中建立私人端點。 若要使用私人連結，您必須在本文中建立稱為*略過網路*的 Azure 虛擬網路 () 、在內部部署和略過網路之間建立私人連線，然後在略過網路中建立私人端點。 您可以選擇任何形式的私用連線能力。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="此圖顯示 Azure Site Recovery 和私用端點的架構。":::

## <a name="prerequisites-and-caveats"></a>必要條件和注意事項

- Site Recovery 9.35 和更新版本支援私用連結。
- 您只能為未向其註冊任何專案的新復原服務保存庫建立私人端點。 因此，在將任何專案新增至保存庫之前，您必須先建立私人端點。 如需定價資訊，請參閱[Azure 私人連結定價](https://azure.microsoft.com/pricing/details/private-link/)。
- 當您建立保存庫的私人端點時，保存庫會遭到鎖定。 只能從具有私人端點的網路存取它。
- Azure Active Directory 目前不支援私用端點。 因此，您需要允許從受保護的 Azure 虛擬網路對 Ip 和完整功能變數名稱進行輸出存取，以供 Azure Active Directory 在區域中工作。
  同樣地，您也可以使用網路安全性群組標籤「Azure Active Directory」和 Azure 防火牆標記來允許存取 Azure Active Directory。
- 在您建立私用端點的略過網路中，需要五個 IP 位址。 當您建立保存庫的私人端點時，Site Recovery 會建立五個私人連結以存取其微服務。
- 略過網路中需要一個額外的 IP 位址，以供私人端點連線到快取儲存體帳戶。 您可以在內部部署和儲存體帳戶端點之間使用任何連線方法。 例如，您可以使用網際網路或 Azure [ExpressRoute](../expressroute/index.yml)。 建立私用連結是選擇性的。 您只能針對一般用途 v2 帳戶建立儲存體的私人端點。 如需一般用途 v2 帳戶上資料傳輸價格的相關資訊，請參閱[Azure 分頁 blob 定價](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>建立和使用適用于 site recovery 的私用端點

 下列各節說明在您的虛擬網路中建立和使用 site recovery 的私用端點所需採取的步驟。

> [!NOTE]
> 我們建議您依照所示順序來遵循這些步驟。 如果您沒有這麼做，您可能無法在保存庫中使用私人端點，而且您可能需要使用新的保存庫重新開機處理常式。

### <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫包含機器的複寫資訊。 它是用來觸發 Site Recovery 作業。 如需如何在 Azure 區域中建立復原服務保存庫的相關資訊，以便在發生嚴重損壞時進行損毀修復，請參閱[建立復原服務保存庫](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

### <a name="enable-the-managed-identity-for-the-vault"></a>啟用保存庫的受控識別

[受控識別](../active-directory/managed-identities-azure-resources/overview.md)可讓保存庫存取您的儲存體帳戶。 視您的需求而定，Site Recovery 可能需要存取目標儲存體和快取/記錄儲存體帳戶。 當您使用保存庫的私人連結服務時，需要存取受控識別。

1. 移至您的復原服務保存庫。 在 [**設定**] 底下選取 [身分**識別**]：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="顯示 [身分識別設定] 頁面的螢幕擷取畫面。":::

1. 將**狀態**變更為 [**開啟**]，然後選取 [**儲存**]。

   系統會產生物件識別碼。 保存庫現在已向 Azure Active Directory 註冊。

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>建立復原服務保存庫的私人端點

若要保護內部部署來源網路中的機器，您需要在略過網路中保存庫的一個私人端點。 在 Azure 入口網站中使用私人連結中心，或使用[Azure PowerShell](../private-link/create-private-endpoint-powershell.md)建立私人端點。

1. 在 [Azure 入口網站搜尋] 方塊中，搜尋「私用連結」。 選取 [**私人連結**] 以前往 [私人連結中心]：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="顯示搜尋私人連結中心 Azure 入口網站的螢幕擷取畫面。":::

1. 在左窗格中選取 [**私人端點**]。 在 [**私人端點**] 頁面上，選取 [**新增**] 以開始建立保存庫的私人端點：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="顯示如何在私人連結中心建立私人端點的螢幕擷取畫面。":::

1. 在 [**建立私人端點**] 頁面上，指定要建立私人端點連線的詳細資料。

   1. **基本概念**。 提供私人端點的基本詳細資料。 使用您用於略過網路的區域：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="螢幕擷取畫面：顯示用來建立私用端點的 [基本] 索引標籤。":::

   1. **資源**。 在此索引標籤上，您必須指定您要為其建立連線的平臺即服務資源。 在所選訂用帳戶的 [**資源類型**] 底下，選取 [ **azurerm.recoveryservices/保存庫**]。 選擇 [**資源**] 下的復原服務保存庫名稱。 選取 [ **Azure Site Recovery** ] 做為**目標子資源**。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="顯示連結至私人端點之 [資源] 索引標籤的螢幕擷取畫面。":::

   1. **設定**。 在此索引標籤上，指定您想要在其中建立私人端點的 [略過網路] 和 [子網]。 

      選取 **[是]** 以啟用與私人 DNS 區域的整合。
      選擇現有的 DNS 區域或建立一個新的。 選取 [**是]** 會自動將區域連結到略過網路。 此動作也會新增 dns 解析所需的 DNS 記錄，以供為私人端點建立的新 Ip 和完整功能變數名稱。

      請確定您選擇為每個連線到相同保存庫的新私人端點建立新的 DNS 區域。 如果您選擇現有的私人 DNS 區域，則會覆寫先前的 CNAME 記錄。 繼續進行之前，請參閱[私用端點指引](../private-link/private-endpoint-overview.md#private-endpoint-properties)。

      如果您的環境具有中樞和輪輻模型，則整個安裝程式只需要一個私人端點和一個私人 DNS 區域。 這是因為您所有的虛擬網路都已在兩者之間啟用對等互連。 如需詳細資訊，請參閱[私用端點 DNS 整合](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手動建立私人 DNS 區域，請依照[建立私人 dns 區域和手動新增 DNS 記錄](#create-private-dns-zones-and-add-dns-records-manually)中的步驟進行。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="顯示私人端點設定之 [設定] 索引標籤的螢幕擷取畫面。":::

   1. **標籤**。 （選擇性）您可以為私用端點新增標記。

   1. 請**參閱 \+ 建立**。 驗證完成時，請選取 [**建立**] 以建立私用端點。

建立私用端點時，會將五個完整功能變數名稱 (Fqdn) 新增至私用端點。 這些連結可讓內部部署網路中的電腦透過略過網路，在保存庫的內容中微服務所有必要的 Site Recovery。 您可以使用相同的私人端點來保護 [略過網路] 和 [所有對等互連網路] 中的任何 Azure 機器。

這五個功能變數名稱會以下列模式格式化：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>核准 site recovery 的私人端點

如果您建立私人端點，而且您也是復原服務保存庫的擁有者，您先前建立的私人端點會在幾分鐘內自動核准。 否則，保存庫的擁有者必須先核准私用端點，才能使用它。 若要核准或拒絕要求的私用端點連線，請移至 [復原保存庫] 頁面上 [**設定**] 下的 [**私人端點連接**]。

在繼續之前，您可以移至私用端點資源來檢查線上狀態：

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="螢幕擷取畫面：顯示保存庫的 [私人端點連接] 頁面和連線清單。":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a> (選擇性) 建立快取儲存體帳戶的私用端點

您可以使用私用端點來 Azure 儲存體。 針對 Azure Site Recovery 複寫，建立儲存體存取的私用端點是選擇性的。 如果您為儲存體建立私人端點，您需要在略過虛擬網路中快取/記錄儲存體帳戶的私人端點。

> [!NOTE]
> 只能在一般用途 v2 儲存體帳戶上建立儲存體的私人端點。 如需定價資訊，請參閱[Azure 分頁 blob 定價](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

遵循[建立私人儲存體的指導](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint)方針，建立具有私人端點的儲存體帳戶。 請務必在 [**與私人 DNS 區域整合**] 底下選取 **[是]** 。 選取現有的 DNS 區域或建立一個新的。

### <a name="grant-required-permissions-to-the-vault"></a>將必要的許可權授與保存庫

視您的設定而定，您可能需要目標 Azure 區域中的一個或多個儲存體帳戶。 接下來，針對 Site Recovery 所需的所有快取/記錄儲存體帳戶，授與受控識別許可權。 在此情況下，您必須事先建立所需的儲存體帳戶。

在您啟用虛擬機器的複寫之前，保存庫的受控識別必須具有下列角色許可權，視儲存體帳戶的類型而定。

- Resource Manager 為基礎的儲存體帳戶 (標準類型) ：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager 為基礎的儲存體帳戶 (Premium 類型) ：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料擁有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 傳統儲存體帳戶：
  - [傳統儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [傳統儲存體帳戶金鑰操作員服務角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

這些步驟說明如何將角色指派新增至您的儲存體帳戶：

1. 移至儲存體帳戶。 在左窗格中選取 [**存取控制] (IAM) ** 。

1. 在 [**新增角色指派**] 區段中，選取 [**新增**]：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="顯示儲存體帳戶的存取控制 (IAM) 頁面的螢幕擷取畫面。":::

1. 在 [**新增角色指派**] 頁面的 [**角色**] 清單中，從本節開頭的清單中選取角色。 輸入保存庫的名稱，然後選取 [**儲存**]。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="顯示 [新增角色指派] 頁面的螢幕擷取畫面。":::

新增這些許可權之後，您必須允許存取 Microsoft 信任的服務。 移至 [**防火牆和虛擬網路**]，然後選取 [**允許受信任的 Microsoft 服務存取此儲存體帳戶**]**例外**狀況。

### <a name="protect-your-virtual-machines"></a>保護您的虛擬機器

完成上述工作之後，請繼續進行內部部署基礎結構的設定。 繼續完成下列其中一項工作： 

- [為 VMware 和實體機器部署設定伺服器](./vmware-azure-deploy-configuration-server.md)
- [設定複寫的 Hyper-v 環境](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

安裝完成之後，請為您的來源機器啟用複寫。 在略過網路中建立保存庫的私人端點之前，請勿設定基礎結構。

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>建立私人 DNS 區域並手動新增 DNS 記錄

如果您在建立保存庫的私人端點時未選取與私人 DNS 區域整合的選項，請依照本節中的步驟進行。

建立一個私人 DNS 區域，以允許 Hyper-v 電腦的 Site Recovery 提供者 () 或適用于 VMware/實體機器的進程伺服器 () 將私人 Fqdn 解析為私人 Ip。

1. 建立私人 DNS 區域。

   1. 在 [**所有服務**] 搜尋方塊中搜尋「私人 dns 區域」，然後在結果中選取 [**私人 dns 區域**]：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="螢幕擷取畫面：顯示在 Azure 入口網站的 [新增資源] 頁面上搜尋私人 dns 區域。":::

   1. 在 [**私人 DNS 區域**] 頁面上，選取 [**新增**] 按鈕以開始建立新的區域。

   1. 在 [**建立私人 DNS 區域**] 頁面上，輸入必要的詳細資料。 輸入**privatelink.siterecovery.windowsazure.com**作為私人 DNS 區域的名稱。 您可以選擇任何資源群組和任何訂用帳戶。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="顯示 [建立私人 DNS 區域] 頁面的 [基本] 索引標籤的螢幕擷取畫面。":::

   1. 繼續前往 [**審查 \+ ** ] [建立] 索引標籤，以審查並建立 DNS 區域。

1. 將私人 DNS 區域連結至您的虛擬網路。

   您現在必須將您建立的私人 DNS 區域連結到略過。

   1. 移至您在上一個步驟中建立的私人 DNS 區域，然後移至左窗格中的 [**虛擬網路] 連結**。 選取 [新增]  。

   1. 輸入必要的詳細資料。 在 [**訂**用帳戶] 和 [**虛擬網路**] 清單中，選取對應至略過網路的詳細資料。 在其他欄位中保留預設值。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="顯示 [新增虛擬網路] 連結頁面的螢幕擷取畫面。":::

1. 新增 DNS 記錄。

   既然您已建立必要的私人 DNS 區域和私人端點，您必須將 DNS 記錄新增至您的 DNS 區域。

   > [!NOTE]
   > 如果您使用的是自訂的私人 DNS 區域，請務必建立類似的專案，如下列步驟所述。

   在此步驟中，您必須將私人端點中每個 FQDN 的專案，都放入私人 DNS 區域中。

   1. 移至您的私人 DNS 區域，然後移至左窗格中的 [**總覽**] 區段。 選取 [**記錄集**] 以開始加入記錄。

   1. 在 [**新增記錄集**] 頁面上，為每個完整功能變數名稱和私人 IP 新增一個專案作為 **[類型記錄**]。 您**可以在 [****私人端點**] 頁面上取得完整功能變數名稱和 ip 的清單。 如您在下列螢幕擷取畫面中所見，私人端點的第一個完整功能變數名稱會新增至私人 DNS 區域中的記錄集。

      這些完整功能變數名稱符合此模式：`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="顯示 [新增記錄集] 頁面的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

既然您已啟用虛擬機器複寫的私人端點，請參閱下列其他文章以取得其他相關資訊：

- [部署內部部署設定伺服器](./vmware-azure-deploy-configuration-server.md)
- [設定 Hyper-V VM 至 Azure 的災害復原](./hyper-v-azure-tutorial.md)