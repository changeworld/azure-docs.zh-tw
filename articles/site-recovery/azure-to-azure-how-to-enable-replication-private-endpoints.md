---
title: 在 Azure Site Recovery 中啟用私人端點的複寫
description: 本文說明如何使用 Site Recovery，將具有私人端點的 Vm 的複寫設定至另一個 Azure 區域。
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 16cde1cf43c6463cbbe640d9e0a80a9ea88f1f1f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096177"
---
# <a name="replicate-machines-with-private-endpoints"></a>複寫具有私人端點的電腦

Azure Site Recovery 可讓您使用[Azure 私人連結](../private-link/private-endpoint-overview.md)私人端點，從隔離的虛擬網路內部複寫您的機器。 下欄區域支援對復原保存庫的私用端點存取：

- Azure 商業：美國中南部、美國西部2、美國東部
- Azure Government： US Gov 維吉尼亞州、US Gov 亞利桑那州、US Gov 德克薩斯州、US DoD 東部、US DoD 中部

本文提供指示，讓您執行下列步驟：

- 建立 Azure 備份復原服務保存庫，以保護您的電腦。
- 啟用保存庫的受控識別，並授與存取客戶儲存體帳戶所需的許可權，以將流量從來源複製到目標位置。 當您設定保存庫的私人連結存取權時，必須要有儲存體的受控識別存取權。
- 進行私人端點所需的 DNS 變更
- 在虛擬網路內建立和核准保存庫的私人端點
- 建立儲存體帳戶的私人端點。 您可以視需要繼續允許儲存體的公用或防火牆存取。 Azure Site Recovery 不一定要建立用來存取儲存體的私用端點。
  
以下是複寫工作流程如何與私用端點變更的參考架構。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="具有私用端點之 Site Recovery 的參考架構。":::

## <a name="prerequisites-and-caveats"></a>必要條件和注意事項

- 只有在沒有任何專案向保存庫註冊的新復原服務保存庫，才能建立私人端點。 因此，在將**任何專案新增至保存庫之前，必須先建立**私人端點。 檢查[私人端點](https://azure.microsoft.com/pricing/details/private-link/)的定價結構。
- 建立保存庫的私用端點時，保存庫會遭到鎖定，而且**無法從具有私人端點**的網路以外的網路存取。
- Azure Active Directory 目前不支援私用端點。 因此，在區域中工作 Azure Active Directory 所需的 Ip 和完整功能變數名稱，必須允許來自受保護網路的輸出存取。 您也可以使用網路安全性群組標記「Azure Active Directory」和 Azure 防火牆標籤，以允許存取 Azure Active Directory。
- 來源電腦和復原電腦的子網中**至少需要有七個 IP 位址**。 當您建立保存庫的私人端點時，Site Recovery 會建立五個私人連結以存取其微服務。 此外，當您啟用複寫時，它會為來源和目的地區域配對新增兩個額外的私人連結。
- 來源和復原子網中都**需要一個額外的 IP 位址**。 只有當您需要使用連接到快取儲存體帳戶的私人端點時，才需要此 IP 位址。
  只能在一般用途 v2 類型上建立儲存體的私人端點。 請參閱[GPv2 上資料傳輸](https://azure.microsoft.com/pricing/details/storage/page-blobs/)的定價結構。

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>建立和使用 Site Recovery 的私用端點

 本節討論在您的虛擬網路內建立和使用 Azure Site Recovery 私用端點的相關步驟。

> [!NOTE]
> 強烈建議您依照所提供的相同順序來遵循這些步驟。 若未這麼做，可能會導致轉譯的保存庫無法使用私人端點，並要求您以新的保存庫重新開機此程式。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個包含機器複寫資訊的實體，用來觸發 Site Recovery 作業。 如需詳細資訊，請參閱[建立復原服務保存庫](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

## <a name="enable-the-managed-identity-for-the-vault"></a>啟用保存庫的受控識別。

[受控識別](../active-directory/managed-identities-azure-resources/overview.md)可讓保存庫取得客戶儲存體帳戶的存取權。 Site Recovery 需要根據案例需求來存取來源儲存體、目標儲存體和快取/記錄儲存體帳戶。
當您使用保存庫的私人連結服務時，受控識別存取是不可或缺的。

1. 移至您的復原服務保存庫。 選取 [_設定_] 底下的 [身分**識別**]。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="顯示 Azure 入口網站和 [復原服務] 頁面。":::

1. 將**狀態**變更為 [_開啟_]，然後選取 [**儲存**]。

1. 系統會產生**物件識別碼**，表示保存庫現在已向 Azure Active Directory 註冊。

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>建立復原服務保存庫的私人端點

若要同時啟用 Azure 虛擬機器的容錯移轉和容錯回復，您需要有兩個私人端點供保存庫使用。 一個私人端點，用於保護來源網路中的電腦，另一個則用於復原網路中已故障的機器重新保護。

在此安裝程式期間，請確定您也在目的地區域中建立復原虛擬網路。

使用入口網站中的私人連結中心，或透過[Azure PowerShell](../private-link/create-private-endpoint-powershell.md)，為您在來源虛擬網路內的保存庫建立第一個私人端點。 為您的復原網路內的保存庫建立第二個私人端點。 以下是在來源網路中建立私人端點的步驟。 重複相同的指引來建立第二個私人端點。

1. 在 [Azure 入口網站搜尋] 列中，搜尋並選取 [私人連結]。 此動作會帶您前往私人連結中心。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="顯示搜尋私人連結中心的 Azure 入口網站。":::

1. 在左側導覽列上，選取 [**私人端點**]。 在 [私人端點] 頁面上，選取 [ ** \+ 新增**] 開始建立保存庫的私人端點。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="顯示在私人連結中心建立私人端點。":::

1. 一旦進入「建立私用端點」體驗，您就必須指定建立私人端點連線的詳細資料。

   1. **基本概念**：填入私人端點的基本詳細資料。 該區域應該與來源電腦相同。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="顯示 [基本] 索引標籤、專案詳細資料、訂用帳戶，以及在 Azure 入口網站中建立私用端點的其他相關欄位。":::

   1. **資源**：此索引標籤會要求您提及您想要為其建立連線的平臺即服務資源。 從所選訂用帳戶的**資源類型**中選取 [ _azurerm.recoveryservices/保存庫_]。 然後，選擇**資源**的復原服務保存庫名稱，並將_Azure Site Recovery_設定為**目標子資源**。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="顯示 [資源] 索引標籤、[資源類型]、[資源] 和 [目標子資源] 欄位，用於連結至 Azure 入口網站中的私用端點。":::

   1. 設定 **：在**[設定] 中，指定您要在其中建立私人端點的虛擬網路和子網。 此虛擬網路是虛擬機器所在的網路。 選取 **[是]** 以啟用與私人 DNS 區域的整合。 選擇已建立的 DNS 區域，或建立一個新的。 選取 **[是]** 會自動將該區域連結至來源虛擬網路，並新增 dns 解析所需的 dns 記錄，以供為私人端點建立的新 ip 和完整功能變數名稱。

      請確定您選擇為每個連線到相同保存庫的新私人端點建立新的 DNS 區域。 如果您選擇現有的私人 DNS 區域，則會覆寫先前的 CNAME 記錄。 繼續進行之前，請參閱[私用端點指引](../private-link/private-endpoint-overview.md#private-endpoint-properties)。

      如果您的環境具有中樞和輪輻模型，則整個設定只需要一個私人端點和一個私人 DNS 區域，因為您所有的虛擬網路都已在兩者之間啟用對等互連。 如需詳細資訊，請參閱[私用端點 DNS 整合](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手動建立私人 DNS 區域，請依照[建立私人 dns 區域和手動新增 DNS 記錄](#create-private-dns-zones-and-add-dns-records-manually)中的步驟進行。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="顯示 [設定] 索引標籤，其中包含在 Azure 入口網站中設定私用端點的網路功能和 DNS 整合欄位。":::

   1. 標籤：（選擇性）您可以為私用端點**新增標記。**

   1. **審查 \+ 建立**：當驗證完成時，請選取 [**建立**] 以建立私用端點。

建立私用端點之後，會將五個完整功能變數名稱新增至私用端點。 這些連結可讓虛擬網路中的電腦取得保存庫內容中所有必要的 Site Recovery 微服務的存取權。 之後，當您啟用複寫時，會將兩個額外的完整功能變數名稱新增至相同的私用端點。

這五個功能變數名稱會以下列模式格式化：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>核准 Site Recovery 的私用端點

如果建立私人端點的使用者也是復原服務保存庫的擁有者，則會在幾分鐘內自動核准上面建立的私用端點。 否則，保存庫的擁有者必須先核准私用端點，才能使用它。 若要核准或拒絕要求的私用端點連線，請移至 [復原保存庫] 頁面上 [設定] 下的 [**私人端點連接**]。

在繼續之前，您可以移至私用端點資源來檢查線上狀態。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="顯示保存庫的 [私人端點連接] 頁面，以及 Azure 入口網站中的連接清單。":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>選擇性建立快取儲存體帳戶的私用端點

可能會使用 Azure 儲存體的私用端點。 針對 Azure Site Recovery 複寫，建立儲存體存取的私用端點是_選擇性_的。 建立儲存體的私用端點時，適用下列需求：

- 您需要來源虛擬網路中的快取/記錄儲存體帳戶的私用端點。
- 在復原網路中重新保護故障的機器時，您需要第二個私人端點。 此私用端點適用于在目的地區域中建立的新儲存體帳戶。

> [!NOTE]
> 儲存體的私人端點只能在**一般用途 v2**儲存體帳戶上建立。 如需定價資訊，請參閱[標準分頁 blob 價格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

遵循[建立私人儲存體的指引](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint)，以建立具有私人端點的儲存體帳戶。 請務必選取 **[是]** ，以與私人 DNS 區域整合。 選取已建立的 DNS 區域，或建立一個新的。

## <a name="grant-required-permissions-to-the-vault"></a>將必要的許可權授與保存庫

如果您的虛擬機器使用受控磁片，您只需要將受控識別許可權授與快取儲存體帳戶。 如果虛擬機器使用非受控磁片，您必須為來源、快取和目標儲存體帳戶授與受控識別許可權。 在此情況下，您必須事先建立目標儲存體帳戶。

啟用虛擬機器的複寫之前，保存庫的受控識別必須具有下列角色許可權，視儲存體帳戶類型而定：

- 以 Resource Manager 為基礎的儲存體帳戶（標準類型）：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 以 Resource Manager 為基礎的儲存體帳戶（Premium 類型）：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料擁有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 傳統儲存體帳戶：
  - [傳統儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [傳統儲存體帳戶金鑰操作員服務角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

下列步驟說明如何將角色指派新增至您的儲存體帳戶，一次一個：

1. 移至儲存體帳戶，然後流覽至頁面左側的 **[存取控制（IAM）** ]。

1. 一次在 [**存取控制（IAM）**] 上的 [新增角色指派] 方塊中，選取 [**新增**]。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="顯示儲存體帳戶上的 [存取控制（IAM）] 頁面，以及 [Azure 入口網站中的 [新增角色指派] 按鈕。":::

1. 在 [新增角色指派] 端頁面的 [**角色**] 下拉式清單中，選擇上述清單中的角色。 輸入保存庫的**名稱**，然後選取 [**儲存**]。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="顯示儲存體帳戶上的 [存取控制（IAM）] 頁面，以及用來選取角色的選項，以及要在 Azure 入口網站中授與該角色的主體。":::

除了這些許可權之外，也必須允許 MS 信任的服務進行存取。 移至 [防火牆和虛擬網路]，然後選取 [允許受信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊中的**例外**狀況。

## <a name="protect-your-virtual-machines"></a>保護您的虛擬機器

完成上述所有設定後，請繼續針對您的虛擬機器啟用複寫。 如果在保存庫上建立私人端點時使用了 DNS 整合，所有 Site Recovery 作業都不需要任何額外的步驟。 不過，如果已手動建立和設定 DNS 區域，則在啟用複寫之後，您需要額外的步驟，才能在來源和目標 DNS 區域中新增特定的 DNS 記錄。 如需詳細資訊和步驟，請參閱[建立私人 DNS 區域和手動新增 DNS 記錄](#create-private-dns-zones-and-add-dns-records-manually)。

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>建立私人 DNS 區域並手動新增 DNS 記錄

如果您在建立保存庫的私用端點時未選取 [與私人 DNS 區域整合] 選項，請遵循本節中的步驟。

建立一個私人 DNS 區域，以允許行動代理程式將私人連結完整功能變數名稱解析為私人 Ip。

1. 建立私人 DNS 區域

   1. 在 [**所有服務**] 搜尋列中搜尋「私人 dns 區域」，然後從下拉式選單選取 [私人 dns 區域]。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="在 Azure 入口網站的 [新資源] 頁面上，顯示搜尋 [私人 dns 區域]。":::

   1. 一旦在 [私人 DNS 區域] 頁面上，選取 [ ** \+ 新增**] 按鈕以開始建立新的區域。

   1. 在 [建立私人 DNS 區域] 頁面上，填入必要的詳細資料。 輸入私人 DNS 區域的名稱做為 `privatelink.siterecovery.windowsazure.com` 。 您可以選擇任何資源群組和任何訂用帳戶來建立它。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="顯示 [建立私人 DNS 區域] 頁面的 [基本] 索引標籤，以及 Azure 入口網站中的相關專案詳細資料。":::

   1. 繼續前往 [**審查 \+ ** ] [建立] 索引標籤，以審查並建立 DNS 區域。

1. 將私人 DNS 區域連結至您的虛擬網路

   上述建立的私人 DNS 區域現在必須連結到您的伺服器目前所在的虛擬網路。 您也需要事先將私人 DNS 區域連結到目標虛擬網路。

   1. 移至您在上一個步驟中建立的私人 DNS 區域，然後流覽至頁面左側的 [**虛擬網路] 連結**。 一旦出現，請選取 [ ** \+ 新增**] 按鈕。

   1. 填入必要的詳細資料。 [**訂**用帳戶] 和 [**虛擬網路**] 欄位必須填入您的伺服器所在之虛擬網路的對應詳細資料。 其他欄位必須保持為 [是]。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="顯示在 Azure 入口網站中，新增連結名稱、訂用帳戶和相關虛擬網路的虛擬網路連結的頁面。":::

1. 新增 DNS 記錄

   建立必要的私人 DNS 區域和私人端點之後，您必須將 DNS 記錄新增至您的 DNS 區域。

   > [!NOTE]
   > 如果您使用自訂的私人 DNS 區域，請確定已如下所述來進行類似的專案。

   此步驟會要求您將私人端點中每個完整功能變數名稱的專案，都放入私人 DNS 區域中。

   1. 移至您的私人 DNS 區域，然後流覽至頁面左側的 [**總覽**] 區段。 之後，選取 [ ** \+ 記錄集**] 以開始加入記錄。

   1. 在開啟的 [新增記錄集] 頁面中，為每個完整功能變數名稱和私人 IP_新增一個專案作為類型記錄_。 完整功能變數名稱和 Ip 的清單可以**從 [私**用端點] 頁面取得。 如下列範例所示，私人端點的第一個完整功能變數名稱會新增至私人 DNS 區域中的記錄集。

      這些完整功能變數名稱符合模式：`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="顯示頁面，以將完整功能變數名稱的 DNS A 類型記錄新增至 Azure 入口網站中的私用端點。":::

   > [!NOTE]
   > 啟用複寫之後，會在兩個區域的私用端點上建立兩個以上的完整功能變數名稱。 請確定您也為這些新建立的完整功能變數名稱新增 DNS 記錄。

## <a name="next-steps"></a>後續步驟

既然您已啟用虛擬機器複寫的私人端點，請參閱下列其他頁面，以取得其他相關資訊：

- [將 Azure Vm 複寫到另一個 Azure 區域](./azure-to-azure-how-to-enable-replication.md)
- [教學課程：設定 Azure Vm 的嚴重損壞修復](./azure-to-azure-tutorial-enable-replication.md)