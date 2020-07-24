---
title: 啟用 Azure 虛擬機器的區域損毀修復區域
description: 本文說明何時及如何使用區域來進行 Azure 虛擬機器的損毀修復。
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: a1952f6dccf12de4cb1571dacabecf78c65cd01b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021642"
---
# <a name="enable-zone-to-zone-disaster-recovery-for-azure-virtual-machines"></a>針對 Azure 虛擬機器啟用區域的損毀修復

本文說明如何將 Azure 虛擬機器從一個可用性區域複寫、容錯移轉及容錯回復到相同 Azure 區域內的另一個可用性區域。

>[!NOTE]
>
>- 區域對區域嚴重損壞修復的支援目前限制為兩個區域：東南亞和英國南部。  
>- Site Recovery 不會在客戶使用區域進行嚴重損壞修復時，將客戶資料從其部署所在的區域中移動或儲存。 如果客戶選擇復原服務保存庫，則可以從不同的區域中選取。 復原服務保存庫包含中繼資料，但沒有實際的客戶資料。

Site Recovery 服務會在規劃和非計畫的中斷期間，讓您的商務應用程式繼續運作，藉此提供您的商務持續性和嚴重損壞修復策略。 這是建議的嚴重損壞修復選項，可讓您的應用程式在發生區域性中斷的情況下繼續運作。

「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域都有一或多個資料中心。 

## <a name="using-availability-zones-for-disaster-recovery"></a>使用可用性區域進行嚴重損壞修復 

一般來說，可用性區域是用來在高可用性設定中部署 Vm。 它們可能太接近彼此，以作為嚴重損壞時的災難修復解決方案。

不過，在某些情況下，您可以利用可用性區域來進行嚴重損壞修復：

- 在內部部署裝載應用程式時，許多具有 metro 嚴重損壞修復策略的客戶，有時會在將應用程式遷移至 Azure 時，模擬此策略。 這些客戶承認，metro 嚴重損壞修復策略在大規模實體嚴重損壞的情況下可能無法使用，並接受此風險。 針對這類客戶，區域嚴重損壞修復可以做為嚴重損壞修復選項使用。

- 許多其他客戶具有複雜的網路基礎結構，而且不想在次要區域中重新建立，因為相關聯的成本和複雜度。 區域嚴重損壞修復會降低複雜性，因為它會利用跨可用性區域的重複網路概念，讓設定變得更簡單。 這類客戶偏好簡單，也可以使用可用性區域進行嚴重損壞修復。

- 在某些區域中，如果在相同的法律管轄區中沒有配對的區域（例如東南亞），區域的嚴重損壞修復可以做為刪除的嚴重損壞修復解決方案，因為它有助於確保法律合規性，因為您的應用程式和資料不會跨越全國界限。 

- 區域嚴重損壞修復意味著在與 Azure 相較之下，將資料複寫到 Azure 嚴重損壞修復，因此您可能會看到較低的延遲，因而降低 RPO。

雖然這些都是強大的優點，但是在發生全區域自然嚴重損壞的情況下，區域嚴重損壞修復可能會導致短期恢復需求。

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>區域到區域嚴重損壞修復的網路功能

如先前所述，區域嚴重損壞修復會降低複雜性，因為它會利用跨可用性區域的重複網路概念，讓設定變得更簡單。 區域嚴重損壞修復案例中網路元件的行為如下所述： 

- 虛擬網路：您可以使用與來源網路相同的虛擬網路來進行實際的容錯移轉。 對來源虛擬網路使用不同的虛擬網路來進行測試容錯移轉。

- 子網：支援容錯移轉至相同的子網。

- 私人 IP 位址：如果您要使用靜態 IP 位址，您可以在目的地區域中使用相同的 ip，如果您選擇以這種方式進行設定。

- 加速網路：類似 Azure 到 Azure 的嚴重損壞修復，如果 VM SKU 支援加速網路，您可以啟用它。

- 公用 IP 位址：您可以將先前建立的標準公用 IP 位址，附加至目標 VM。 基本公用 IP 位址不支援可用性區域的相關案例。

- 負載平衡器：標準負載平衡器是區域資源，因此目標 VM 可以連接到相同負載平衡器的後端集區。 不需要新的負載平衡器。

- 網路安全性群組：您可以使用與來源 VM 相同的網路安全性群組。

## <a name="pre-requisites"></a>必要條件

將區域部署到 Vm 的區域嚴重損壞修復之前，請務必確保 VM 上啟用的其他功能可與區域的損毀修復互通。

|功能  | 支援聲明  |
|---------|---------|
|傳統 VM   |     不支援    |
|ARM Vm    |    支援    |
|Azure 磁碟加密 v1 （雙通路，含 AAD）     |     支援 |
|Azure 磁碟加密 v2 （單一階段，不含 AAD）    |    支援    |
|非受控磁碟    |    不支援    |
|受控磁碟    |    支援    |
|客戶管理的金鑰    |    支援    |
|鄰近位置群組    |    支援    |
|備份互通性    |    支援檔案層級備份和還原。 磁片和 VM 層級的備份與還原，不受支援。    |
|熱新增/移除    |    啟用區域對區域複寫之後，可以新增磁片。 不支援在啟用區域到區域複寫後移除磁片。    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>設定區域嚴重損壞修復的 Site Recovery 區域

### <a name="log-in"></a>登入

登入 Azure 入口網站。

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>為區域性 Azure 虛擬機器啟用複寫

1. 在 Azure 入口網站功能表上，選取 [虛擬機器]，或在任何頁面上搜尋並選取 [虛擬機器]。 選取您要複寫的 VM。 針對區域損毀修復，此 VM 必須已在可用性區域中。

2. 在 [作業] 中，選取 [災害復原]。

3. 如下所示，在 [基本] 索引標籤中，針對 [可用性區域間的嚴重損壞修復] 選取 [是]

    ![基本設定頁面](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. 如果您接受所有預設值，請按一下 [檢查 + 開始複寫]，後面接著 [開始複寫]。

5. 如果您想要對複寫設定進行變更，請按一下 [下一步： Advanced settings]。

6. 適當時，將設定從 [預設] 變更為 [開]。 針對 Azure 到 Azure 嚴重損壞修復的使用者，此頁面可能會很熟悉。 您可以在[這裡](./azure-to-azure-tutorial-enable-replication.md)找到有關此分頁上所提供選項的詳細資料。

    ![[高級設定] 頁面](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. 按一下 [下一步：審查 + 開始複寫]，然後按 [開始複寫]。

## <a name="faqs"></a>常見問題集

**1. 區域對區域嚴重損壞修復的價格如何運作？**
區域到區域嚴重損壞修復的定價與 Azure 到 Azure 嚴重損壞修復的定價相同。 您可以在[這裡](https://azure.microsoft.com/pricing/details/site-recovery/)和[這裡](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)找到更多有關定價頁面的詳細資料。 請注意，您會在區域對區域嚴重損壞修復中看到的輸出費用，會低於區域嚴重損壞修復。

**2. RTO 和 RPO 的 SLA 為何？**
RTO SLA 等同于整體 Site Recovery。 我們承諾最多2小時的 RTO。 沒有針對 RPO 定義的 SLA。

**3. 在次要區域中保證容量嗎？**
Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您開始進行容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例會部署到目的地區域。

**4. 支援哪些作業系統？**
區域的嚴重損壞修復支援與 Azure 相同的作業系統與 azure 的嚴重損壞修復。 請參閱[這裡](./azure-to-azure-support-matrix.md)的支援矩陣。

**5. 來源和目標資源群組可以是相同的嗎？**
否，您必須容錯移轉至不同的資源群組。

## <a name="next-steps"></a>後續步驟

執行嚴重損壞修復演練、容錯移轉、重新保護和容錯回復所需遵循的步驟，與 Azure 到 Azure 嚴重損壞修復案例中的步驟相同。

若要執行嚴重損壞修復演練，請遵循[這裡](./azure-to-azure-tutorial-dr-drill.md)所述的步驟。

若要執行損毀修復並重新保護次要區域中的 Vm，請遵循[這裡](./azure-to-azure-tutorial-failover-failback.md)所述的步驟。

若要容錯回復到主要區域，請遵循[這裡](./azure-to-azure-tutorial-failback.md)所述的步驟。
