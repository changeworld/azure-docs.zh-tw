---
title: 在 Azure Site Recovery 中啟用私人端點的複寫
description: 本文說明如何使用 Site Recovery，為具有私人端點的 Vm 設定複寫至另一個 Azure 區域的 Vm。
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 37784c4a294ccf296818f2afb1a8a345cb9d813e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658262"
---
# <a name="replicate-machines-with-private-endpoints"></a>複寫具有私人端點的電腦

Azure Site Recovery 可讓您使用 [Azure Private Link](../private-link/private-endpoint-overview.md) 私人端點，從隔離的虛擬網路內複寫您的機器。 所有 Azure 商用 & 政府區域都支援復原保存庫的私人端點存取權。

本文提供您執行下列步驟的指示：

- 建立 Azure 備份復原服務保存庫來保護您的機器。
- 啟用保存庫的受控識別，並授與存取客戶儲存體帳戶所需的許可權，以將流量從來源複寫到目標位置。 當您設定 Private Link 對保存庫的存取權時，需要儲存體的受控識別存取權。
- 進行私人端點所需的 DNS 變更
- 建立和核准虛擬網路內保存庫的私人端點
- 建立儲存體帳戶的私人端點。 您可以視需要繼續允許儲存體的公用或防火牆存取。 Azure Site Recovery 不一定要建立私人端點來存取儲存體。
  
以下是複寫工作流程如何與私人端點進行變更的參考架構。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

## <a name="prerequisites-and-caveats"></a>必要條件和注意事項

- 只有在新的復原服務保存庫沒有任何專案註冊到保存庫時，才能建立私人端點。 因此，在 **將任何專案新增至保存庫之前，必須先建立**私人端點。 檢查 [私人端點](https://azure.microsoft.com/pricing/details/private-link/)的定價結構。
- 為保存庫建立私人端點時，保存庫會被鎖定，而且 **無法從具有私人端點的網路以外的網路存取**。
- Azure Active Directory 目前不支援私人端點。 因此，Azure Active Directory 在區域中工作所需的 Ip 和完整功能變數名稱，必須允許來自安全網路的輸出存取。 您也可以使用網路安全性群組標記 "Azure Active Directory" 和 Azure 防火牆標籤，以允許存取 Azure Active Directory。
- 來源電腦和復原電腦的子網中**至少需要七個 IP 位址**。 當您建立保存庫的私人端點時，Site Recovery 會建立五個私人連結來存取其微服務。 此外，當您啟用複寫時，它會針對來源和目的地區域配對新增兩個額外的私人連結。
- 來源和復原子網中都**需要一個額外的 IP 位址**。 只有當您需要使用連接至快取儲存體帳戶的私人端點時，才需要此 IP 位址。
  儲存體的私人端點只能在一般用途 v2 類型上建立。 請參閱 [GPv2 上資料傳輸](https://azure.microsoft.com/pricing/details/storage/page-blobs/)的定價結構。

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>建立和使用 Site Recovery 的私人端點

 本節將討論在您的虛擬網路內建立和使用私人端點以進行 Azure Site Recovery 所需的步驟。

> [!NOTE]
> 強烈建議您依照所提供的相同順序來遵循這些步驟。 若未這麼做，可能會導致系統轉譯保存庫，而無法使用私人端點，且需要您以新的保存庫重新開機處理常式。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個實體，其中包含機器的複寫資訊，可用來觸發 Site Recovery 操作。 如需詳細資訊，請參閱 [建立復原服務保存庫](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

## <a name="enable-the-managed-identity-for-the-vault"></a>啟用保存庫的受控識別。

[受控識別](../active-directory/managed-identities-azure-resources/overview.md)可讓保存庫取得客戶的儲存體帳戶存取權。 視案例需求而定，Site Recovery 需要存取來源儲存體、目標儲存體和快取/記錄儲存體帳戶。
當您使用私人連結服務作為保存庫時，受控識別存取是不可或缺的。

1. 移至您的復原服務保存庫。 在 [_設定_] 底下選取 [身分**識別**]。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

1. 將 **狀態** 變更為 [ _開啟_ ]，然後選取 [ **儲存**]。

1. 系統會產生 **物件識別碼** ，表示保存庫現在已向 Azure Active Directory 註冊。

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>建立復原服務保存庫的私人端點

若要啟用 Azure 虛擬機器的容錯移轉和容錯回復，您將需要兩個保存庫的私人端點。 一個私人端點，可保護來源網路中的機器，而另一個私人端點用於復原網路中的容錯移轉機器重新保護。

確定您在此設定過程中也會在目的地區域中建立復原虛擬網路。

使用入口網站中的 Private Link 中心或透過 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)，在來源虛擬網路內建立保存庫的第一個私人端點。 在您的復原網路內建立保存庫的第二個私人端點。 以下是在來源網路中建立私人端點的步驟。 重複相同的指引，以建立第二個私人端點。

1. 在 Azure 入口網站搜尋列中，搜尋並選取 [Private Link]。 此動作會帶您前往 Private Link Center。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

1. 在左側導覽列上，選取 [ **私人端點**]。 在 [私人端點] 頁面上，選取 [ ** \+ 新增**] 以開始建立保存庫的私人端點。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

1. 一旦進入「建立私人端點」體驗，您就必須指定建立私人端點連線的詳細資料。

   1. **基本概念**：填寫私人端點的基本詳細資料。 此區域應該與來源電腦相同。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

   1. **資源**：此索引標籤會要求您提及您要建立連線的平臺即服務資源。 針對所選訂用帳戶的**資源類型**，選取 [ _az.recoveryservices]/_ [保存庫]。 然後，選擇 **資源** 的復原服務保存庫名稱，並將 _Azure Site Recovery_ 設定為 **目標子資源**。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

   1. 設定 **：在**[設定] 中，指定您要在其中建立私人端點的虛擬網路和子網。 此虛擬網路是存在虛擬機器的網路。 選取 **[是]**，以啟用與私人 DNS 區域的整合。 選擇已經建立的 DNS 區域，或建立一個新的。 選取 **[是]** 會自動將區域連結至來源虛擬網路，並新增 dns 解析所需的 dns 記錄，以供針對私人端點建立的新 ip 和完整功能變數名稱。

      確定您選擇為每個連線到相同保存庫的私人端點建立新的 DNS 區域。 如果您選擇現有的私人 DNS 區域，則會覆寫先前的 CNAME 記錄。 繼續之前，請參閱 [私人端點指引](../private-link/private-endpoint-overview.md#private-endpoint-properties) 。

      如果您的環境具有中樞和輪輻模型，則整個安裝只需要一個私人端點和一個私人 DNS 區域，因為所有虛擬網路都已在兩者之間啟用對等互連。 如需詳細資訊，請參閱 [私人端點 DNS 整合](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手動建立私人 DNS 區域，請遵循 [建立私人 dns 區域中的步驟，並手動新增 DNS 記錄](#create-private-dns-zones-and-add-dns-records-manually)。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

   1. **標記**：您可以選擇性地新增私人端點的標記。

   1. **檢查 \+ 建立**：當驗證完成時，選取 [ **建立** ] 以建立私人端點。

私人端點建立之後，就會將五個完整功能變數名稱新增至私人端點。 這些連結可讓虛擬網路中的機器取得保存庫內容中所有必要 Site Recovery 微服務的存取權。 稍後，當您啟用複寫時，會將兩個額外的完整功能變數名稱新增至相同的私人端點。

五個功能變數名稱會以下列模式格式化：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>核准 Site Recovery 的私人端點

如果建立私人端點的使用者也是復原服務保存庫的擁有者，就會在幾分鐘內自動核准上面建立的私人端點。 否則，保存庫的擁有者必須先核准私人端點，才能使用它。 若要核准或拒絕要求的私人端點連線，請移至 [復原保存庫] 頁面的 [設定] 下的 [ **私人端點** 連線]。

您可以先移至私人端點資源來檢查線上狀態，再繼續進行。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a> (選用) 為快取儲存體帳戶建立私人端點

您可以使用 Azure 儲存體的私人端點。 針對 Azure Site Recovery 複寫，建立私人端點以進行儲存存取是 _選擇性_ 的。 建立儲存體的私人端點時，適用下列需求：

- 您必須要有來源虛擬網路中快取/記錄儲存體帳戶的私人端點。
- 在復原網路中容錯移轉的機器重新保護時，您需要第二個私人端點。 此私人端點適用于在目的地區域中建立的新儲存體帳戶。

> [!NOTE]
> 儲存體的私人端點只能在 **一般用途 v2** 儲存體帳戶上建立。 如需定價資訊，請參閱 [標準分頁 blob 價格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

遵循 [建立私人儲存體的指導](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) 方針，建立具有私人端點的儲存體帳戶。 請務必選取 **[是]** ，以與私人 DNS 區域整合。 選取已建立的 DNS 區域，或建立一個新的。

## <a name="grant-required-permissions-to-the-vault"></a>將必要的許可權授與保存庫

如果您的虛擬機器使用受控磁片，您只需要將受控識別許可權授與快取儲存體帳戶。 如果虛擬機器使用非受控磁片，您必須將來源、快取和目標儲存體帳戶的受控識別許可權授與受控識別。 在此情況下，您需要事先建立目標儲存體帳戶。

在啟用虛擬機器的複寫之前，保存庫的受控識別必須具有下列角色許可權，視儲存體帳戶的類型而定：

- 以 Resource Manager 為基礎的儲存體帳戶 (標準類型) ：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料參與者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 以 Resource Manager 為基礎的儲存體帳戶 (Premium 類型) ：
  - [參與者](../role-based-access-control/built-in-roles.md#contributor)
  - [儲存體 Blob 資料擁有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 傳統儲存體帳戶：
  - [傳統儲存體帳戶參與者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [傳統儲存體帳戶金鑰操作員服務角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

下列步驟說明如何將角色指派新增至儲存體帳戶，一次一個：

1. 移至儲存體帳戶，然後流覽至頁面左側的 [ **存取控制] (IAM) ** 。

1. 在 **存取控制 (IAM) **之後，請在 [新增角色指派] 方塊中選取 [ **新增**]。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

1. 在 [新增角色指派] 頁面的 [ **角色** ] 下拉式清單中，選擇上方清單中的角色。 輸入保存庫的 **名稱** ，然後選取 [ **儲存**]。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

除了這些許可權之外，也必須允許 MS 受信任的服務存取。 移至 [防火牆和虛擬網路]，然後選取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊（ **例外**狀況）。

## <a name="protect-your-virtual-machines"></a>保護您的虛擬機器

完成上述所有設定之後，請繼續為您的虛擬機器啟用複寫。 如果在保存庫上建立私人端點時使用了 DNS 整合，則所有的 Site Recovery 作業都能運作，而不需要任何額外的步驟。 但是，如果是手動建立和設定 DNS 區域，則在啟用複寫之後，您需要額外的步驟，以在來源和目標 DNS 區域中新增特定的 DNS 記錄。 如需詳細資訊和步驟，請參閱 [建立私人 dns 區域和手動新增 DNS 記錄](#create-private-dns-zones-and-add-dns-records-manually)。

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>建立私人 DNS 區域，並手動新增 DNS 記錄

如果您在建立保存庫的私人端點時未選取 [與私人 DNS 區域整合] 選項，請依照本節中的步驟執行。

建立一個私人 DNS 區域，讓行動代理程式能夠將私人連結的完整功能變數名稱解析為私人 Ip。

1. 建立私人 DNS 區域

   1. 在 [ **所有服務** ] 搜尋列中搜尋「私人 dns 區域」，然後從下拉式清單中選取 [私人 dns 區域]。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

   1. 在 [私人 DNS 區域] 頁面上，選取 [ ** \+ 新增**] 按鈕以開始建立新的區域。

   1. 在 [建立私人 DNS 區域] 頁面上，填寫必要的詳細資料。 輸入私人 DNS 區域的名稱，如下所示 `privatelink.siterecovery.windowsazure.com` 。 您可以選擇任何資源群組和任何訂用帳戶來建立它。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

   1. 繼續前往 [ **審核 \+ 建立** ] 索引標籤，以檢查和建立 DNS 區域。

1. 將私人 DNS 區域連結至您的虛擬網路

   上述建立的私人 DNS 區域現在必須連結到您的伺服器目前所在的虛擬網路。 您也需要事先將私人 DNS 區域連結至目標虛擬網路。

   1. 移至您在上一個步驟中建立的私人 DNS 區域，然後流覽至頁面左側的 [ **虛擬網路] 連結** 。 一旦出現，請選取 [ ** \+ 新增**] 按鈕。

   1. 填寫必要的詳細資料。 [ **訂** 用帳戶] 和 [ **虛擬網路** ] 欄位必須填入伺服器所在之虛擬網路的對應詳細資料。 其他欄位必須保持原樣。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

1. 新增 DNS 記錄

   建立必要的私人 DNS 區域和私人端點之後，您必須將 DNS 記錄新增至 DNS 區域。

   > [!NOTE]
   > 如果您使用自訂的私人 DNS 區域，請確定您已建立類似的專案，如下所述。

   此步驟會要求您將私人端點中的每個完整功能變數名稱輸入至私人 DNS 區域。

   1. 移至您的私人 DNS 區域，然後流覽至頁面左側的 [ **總覽** ] 區段。 一旦出現，請選取 [ ** \+ 記錄集**] 以開始加入記錄。

   1. 在開啟的 [新增記錄集] 頁面中，將每個完整功能變數名稱和私人 _IP 的專案新增為類型記錄_ 。 完整功能變數名稱和 Ip 的清單可從 **總覽**中的「私人端點」頁面取得。 如下列範例所示，私人端點的第一個完整功能變數名稱會新增至私人 DNS 區域中的記錄集。

      這些完整功能變數名稱符合模式： `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="具有私人端點 Site Recovery 的參考架構。":::

   > [!NOTE]
   > 啟用複寫之後，會在兩個區域的私人端點上建立兩個完整的完整功能變數名稱。 確定您也為這些新建立的完整功能變數名稱新增 DNS 記錄。

## <a name="next-steps"></a>後續步驟

既然您已為虛擬機器複寫啟用私人端點，請參閱下列其他頁面，以取得其他相關資訊：

- [將 Azure VM 複寫至另一個 Azure 區域](./azure-to-azure-how-to-enable-replication.md)
- [教學課程：設定 Azure Vm 的嚴重損壞修復](./azure-to-azure-tutorial-enable-replication.md)