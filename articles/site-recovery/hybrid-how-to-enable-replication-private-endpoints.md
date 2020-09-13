---
title: 使用私人端點啟用內部部署機器的複寫
description: 本文說明如何使用 Site Recovery 中的私人端點來設定內部部署機器的複寫。
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 3d15f4039da85dfa926e7bc9ab96b2c48965d5f0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658804"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>使用私人端點複寫內部部署機器

Azure Site Recovery 可讓您使用 [Azure Private Link](../private-link/private-endpoint-overview.md) 私人端點，將內部部署機器複寫至 Azure 中的虛擬網路。 所有 Azure 商用 & 政府區域都支援復原保存庫的私人端點存取權。

本文說明如何完成下列步驟︰

- 建立 Azure 備份復原服務保存庫來保護您的機器。
- 啟用保存庫的受控識別。 授與存取儲存體帳戶所需的許可權，以啟用從內部部署至 Azure 目標位置的流量複寫。 Private Link 存取保存庫時，需要儲存體的受控識別存取權。

- 進行私人端點所需的 DNS 變更。
- 建立並核准虛擬網路內保存庫的私人端點。
- 建立儲存體帳戶的私人端點。 您可以視需要繼續允許儲存體的公用或防火牆存取。 Azure Site Recovery 不需要建立私人端點來存取儲存體。
  
下圖顯示具有私人端點之混合式災難復原的複寫工作流程。 您無法在內部部署網路中建立私人端點。 若要使用私人連結，您需要在本文中建立稱為「 *略過網路* 」的 Azure 虛擬 (網路) 、在內部部署與略過網路之間建立私人連線，然後在許可網路中建立私人端點。 您可以選擇任何形式的私人連線能力。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="顯示 Azure Site Recovery 和私人端點架構的圖表。":::

## <a name="prerequisites-and-caveats"></a>必要條件和注意事項

- Site Recovery 9.35 和更新版本支援私用連結。
- 您只能為未註冊任何專案的新復原服務保存庫建立私人端點。 因此，在將任何專案新增至保存庫之前，您必須先建立私人端點。 如需定價資訊，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link/) 。
- 當您建立保存庫的私人端點時，保存庫會被鎖定。 只能從具有私人端點的網路存取。
- Azure Active Directory 目前不支援私人端點。 因此，您必須允許從受保護的 Azure 虛擬網路輸出存取到 Azure Active Directory 在區域中工作所需的 Ip 和完整功能變數名稱。
  如果適用，您也可以使用網路安全性群組標記 "Azure Active Directory" 和 Azure 防火牆標籤，以允許存取 Azure Active Directory。
- 在您建立私人端點的許可網路中，需要五個 IP 位址。 當您建立保存庫的私人端點時，Site Recovery 會建立五個私人連結來存取其微服務。
- 略過網路中必須有一個額外的 IP 位址，才能將私人端點連線至快取儲存體帳戶。 您可以使用內部部署與儲存體帳戶端點之間的任何連接方法。 例如，您可以使用網際網路或 Azure [ExpressRoute](../expressroute/index.yml)。 建立 private link 是選擇性的。 您可以只在一般用途 v2 帳戶上建立儲存體的私人端點。 如需一般用途 v2 帳戶之資料傳輸定價的相關資訊，請參閱 [Azure 分頁 blob 定價](https://azure.microsoft.com/pricing/details/storage/page-blobs/) 。

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>針對 site recovery 建立和使用私人端點

 下列各節說明在虛擬網路中建立及使用私人端點進行 site recovery 時所需採取的步驟。

> [!NOTE]
> 建議您依照顯示的循序執行這些步驟。 如果不這麼做，您可能無法使用保存庫中的私人端點，而且您可能需要使用新的保存庫來重新開機該進程。

### <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫包含機器的複寫資訊。 它是用來觸發 Site Recovery 的作業。 如需如何在您想要容錯移轉的 Azure 區域中建立復原服務保存庫的相關資訊（如果發生嚴重損壞），請參閱 [建立復原服務保存庫](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

### <a name="enable-the-managed-identity-for-the-vault"></a>啟用保存庫的受控識別

[受控識別](../active-directory/managed-identities-azure-resources/overview.md)可讓保存庫存取您的儲存體帳戶。 視您的需求而定，Site Recovery 可能需要存取目標儲存體和快取/記錄儲存體帳戶。 當您使用保存庫的 Private Link 服務時，需要受控識別存取權。

1. 移至您的復原服務保存庫。 在 [**設定**] 底下選取 [身分**識別**]：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="顯示 [身分識別設定] 頁面的螢幕擷取畫面。":::

1. 將 **狀態** 變更為 [ **開啟** ]，然後選取 [ **儲存**]。

   系統會產生物件識別碼。 保存庫現在已向 Azure Active Directory 註冊。

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>建立復原服務保存庫的私人端點

若要保護內部部署來源網路中的電腦，您在略過網路中需要有一個保存庫的私人端點。 使用 Azure 入口網站中的 Private Link 中心或使用 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)來建立私人端點。

1. 在 Azure 入口網站搜尋方塊中，搜尋「私用連結」。 選取 **Private Link** 移至 Private Link 中心：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="顯示 Private Link 中心搜尋 Azure 入口網站的螢幕擷取畫面。":::

1. 在左窗格中，選取 [ **私人端點**]。 在 [ **私人端點** ] 頁面上，選取 [ **新增** ] 以開始建立保存庫的私人端點：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="顯示如何在 Private Link 中心建立私人端點的螢幕擷取畫面。":::

1. 在 [ **建立私人端點** ] 頁面上，指定要建立私人端點連線的詳細資料。

   1. **基本概念**。 提供私人端點的基本詳細資料。 使用您用於略過網路的區域：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="顯示建立私人端點之 [基本] 索引標籤的螢幕擷取畫面。":::

   1. **資源**。 在此索引標籤上，您必須指定要建立連線的平臺即服務資源。 在所選訂用帳戶的 **資源類型** 底下，選取 [ **az.recoveryservices]/**[保存庫]。 在 [ **資源**] 下選擇您的復原服務保存庫名稱。 選取 **Azure Site Recovery** 做為 **目標子資源**。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="顯示連結至私人端點之 [資源] 索引標籤的螢幕擷取畫面。":::

   1. **設定**。 在此索引標籤上，指定您要在其中建立私人端點的 [略過網路] 和 [子網]。 

      選取 **[是]**，以啟用與私人 DNS 區域的整合。
      選擇現有的 DNS 區域，或建立一個新的。 選取 [ **是]** 會自動將區域連結至略過網路。 此動作也會新增 dns 解析所需的 DNS 記錄，以供針對私人端點建立的新 Ip 和完整功能變數名稱。

      確定您選擇為每個連線到相同保存庫的私人端點建立新的 DNS 區域。 如果您選擇現有的私人 DNS 區域，則會覆寫先前的 CNAME 記錄。 請參閱 [私用端點指引](../private-link/private-endpoint-overview.md#private-endpoint-properties) ，再繼續進行操作。

      如果您的環境具有中樞和輪輻模型，則整個安裝只需要一個私人端點和一個私人 DNS 區域。 這是因為您的所有虛擬網路都已在兩者之間啟用對等互連。 如需詳細資訊，請參閱 [私人端點 DNS 整合](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手動建立私人 DNS 區域，請遵循 [建立私人 dns 區域中的步驟，並手動新增 DNS 記錄](#create-private-dns-zones-and-add-dns-records-manually)。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="顯示私人端點設定之 [設定] 索引標籤的螢幕擷取畫面。":::

   1. **標籤**。 （選擇性）您可以新增私人端點的標記。

   1. **複習 \+ create**。 驗證完成時，請選取 [ **建立** ] 以建立私人端點。

建立私人端點時， (Fqdn) 的五個完整功能變數名稱會新增至私人端點。 這些連結可讓內部部署網路中的電腦透過略過網路來存取，在保存庫的內容中微服務所有必要的 Site Recovery。 您可以使用相同的私人端點來保護略過網路和所有對等互連網路中的任何 Azure 電腦。

這五個功能變數名稱會以這個模式格式化：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>核准 site recovery 的私人端點

如果您建立私人端點，而且您也是復原服務保存庫的擁有者，您先前建立的私人端點會在幾分鐘內自動核准。 否則，保存庫的擁有者必須先核准私人端點，才能使用它。 若要核准或拒絕要求的私人端點連線，請移至 [復原保存庫] 頁面的 [**設定**] 底下的 [**私人端點**連線]。

您可以先移至私人端點資源來檢查線上狀態，再繼續進行：

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="顯示保存庫的 [私人端點連線] 頁面和連接清單的螢幕擷取畫面。":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a> (選用) 為快取儲存體帳戶建立私人端點

您可以使用私人端點來 Azure 儲存體。 針對 Azure Site Recovery 複寫，建立私人端點以進行儲存存取是選擇性的。 如果您為儲存體建立私人端點，您需要在您的略過虛擬網路中快取/記錄儲存體帳戶的私人端點。

> [!NOTE]
> 儲存體的私人端點只能在一般用途 v2 儲存體帳戶上建立。 如需定價資訊，請參閱 [Azure 分頁 blob 定價](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

遵循 [建立私人儲存體的指導](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) 方針，建立具有私人端點的儲存體帳戶。 請務必在 [**與私人 DNS 區域整合**] 下方選取 **[是]** 。 選取現有的 DNS 區域，或建立一個新的。

### <a name="grant-required-permissions-to-the-vault"></a>將必要的許可權授與保存庫

視您的設定而定，您可能需要在目標 Azure 區域中有一或多個儲存體帳戶。 接下來，將 Site Recovery 所需的所有快取/記錄儲存體帳戶的受控識別許可權授與受控識別。 在此情況下，您必須事先建立必要的儲存體帳戶。

在您啟用虛擬機器的複寫之前，保存庫的受控識別必須具有下列角色許可權，視儲存體帳戶的類型而定。

- 以 Resource Manager 為基礎的儲存體帳戶 (標準類型) ：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 以 Resource Manager 為基礎的儲存體帳戶 (Premium 類型) ：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料擁有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 傳統儲存體帳戶：
  - [傳統儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [傳統儲存體帳戶金鑰操作員服務角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

下列步驟說明如何將角色指派新增至儲存體帳戶：

1. 移至儲存體帳戶。 選取左窗格中的 [ **存取控制] (IAM) ** 。

1. 在 [ **新增角色指派** ] 區段中，選取 [ **新增**：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="顯示儲存體帳戶的存取控制 (IAM) 頁面的螢幕擷取畫面。":::

1. 在 [ **新增角色指派** ] 頁面的 [ **角色** ] 清單中，從此區段開頭的清單中選取角色。 輸入保存庫的名稱，然後選取 [ **儲存**]。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="顯示 [新增角色指派] 頁面的螢幕擷取畫面。":::

新增這些許可權之後，您必須允許存取 Microsoft 受信任的服務。 移至 [ **防火牆和虛擬網路** ]，然後選取 [ **允許信任的 Microsoft 服務存取此儲存體帳戶** （ **例外**狀況）。

### <a name="protect-your-virtual-machines"></a>保護您的虛擬機器

完成上述工作之後，請繼續設定您的內部部署基礎結構。 完成下列其中一項工作，以繼續作業： 

- [部署適用于 VMware 和實體機器的設定伺服器](./vmware-azure-deploy-configuration-server.md)
- [設定 Hyper-v 環境以進行複寫](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

完成設定之後，請為您的來源機器啟用複寫。 在略過網路中建立保存庫的私人端點之前，請勿設定基礎結構。

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>建立私人 DNS 區域，並手動新增 DNS 記錄

如果您在建立保存庫的私人端點時，未選取 [與私人 DNS 區域整合] 選項，請依照本節中的步驟執行。

建立一個私人 DNS 區域，以允許 Hyper-v 電腦的 Site Recovery 提供者 () 或適用于 VMware/實體機器的進程伺服器 () 將私人 Fqdn 解析為私人 Ip。

1. 建立私人 DNS 區域。

   1. 在 [ **所有服務** ] 搜尋方塊中搜尋 [私人 dns 區域]，然後在結果中選取 [ **私人 dns 區域** ]：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="顯示在 Azure 入口網站的 [新增資源] 頁面上搜尋私人 dns 區域的螢幕擷取畫面。":::

   1. 在 [ **私人 DNS 區域** ] 頁面上，選取 [ **新增** ] 按鈕以開始建立新的區域。

   1. 在 [ **建立私人 DNS 區域** ] 頁面上，輸入必要的詳細資料。 輸入 **privatelink.siterecovery.windowsazure.com** 作為私人 DNS 區域的名稱。 您可以選擇任何資源群組和任何訂用帳戶。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="顯示 [建立私人 DNS 區域] 頁面的 [基本] 索引標籤的螢幕擷取畫面。":::

   1. 繼續前往 [ **審核 \+ 建立** ] 索引標籤，以檢查和建立 DNS 區域。

1. 將私人 DNS 區域連結至您的虛擬網路。

   您現在需要將您建立的私人 DNS 區域連結至 [略過]。

   1. 移至您在上一個步驟中建立的私人 DNS 區域，然後移至左窗格中的 [ **虛擬網路] 連結** 。 選取 [新增]。

   1. 輸入所需的詳細資料。 在 [ **訂** 用帳戶和 **虛擬網路** ] 清單中，選取對應于略過網路的詳細資料。 在其他欄位中保留預設值。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="顯示 [新增虛擬網路] 連結頁面的螢幕擷取畫面。":::

1. 新增 DNS 記錄。

   既然您已建立必要的私人 DNS 區域和私人端點，您必須將 DNS 記錄新增至 DNS 區域。

   > [!NOTE]
   > 如果您是使用自訂的私人 DNS 區域，請務必建立類似的專案，如下列步驟所述。

   在此步驟中，您必須將私人端點中每個 FQDN 的專案，放入私人 DNS 區域中。

   1. 移至您的私人 DNS 區域，然後移至左窗格中的 [ **總覽** ] 區段。 選取 [ **記錄集** ] 以開始加入記錄。

   1. 在 [ **新增記錄集** ] 頁面上，新增每個完整功能變數名稱和私人 **IP 的專案做為類型記錄** 。 您可以在 [**總覽**] 的 [**私人端點**] 頁面上取得完整功能變數名稱和 ip 的清單。 如您在下列螢幕擷取畫面中所見，私人端點的第一個完整功能變數名稱會新增至私人 DNS 區域中的記錄集。

      這些完整功能變數名稱符合此模式： `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="顯示 [新增記錄集] 頁面的螢幕擷取畫面。":::

## <a name="next-steps"></a>接下來的步驟

既然您已為虛擬機器複寫啟用私人端點，請參閱下列其他文章，以取得其他相關資訊：

- [部署內部部署設定伺服器](./vmware-azure-deploy-configuration-server.md)
- [設定 Hyper-V VM 至 Azure 的災害復原](./hyper-v-azure-tutorial.md)