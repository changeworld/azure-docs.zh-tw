---
title: 為 Azure 虛擬機器啟用區域以進列區域嚴重損壞修復
description: 本文說明何時及如何使用區域以區域進行 Azure 虛擬機器的嚴重損壞修復。
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2019
ms.author: sideeksh
ms.openlocfilehash: 001ac4918ed5d87bdb801d1bf918a4450e7cf8e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007786"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>啟用可用性區域之間的 Azure VM 嚴重損壞修復

本文說明如何將 Azure 虛擬機器從一個可用性區域複寫、容錯移轉及容錯回復至相同 Azure 區域內的另一個可用性區域。

>[!NOTE]
>
>- 區域對區域嚴重損壞修復的支援目前僅限於兩個區域：東南亞及英國南部。  
>- Site Recovery 不會在客戶使用區域以進列區域容錯移轉時，將客戶資料從其部署所在的區域中移動或儲存。 客戶可以選擇不同區域中的復原服務保存庫（如果有的話）。 復原服務保存庫包含中繼資料，但不包含實際的客戶資料。

Site Recovery 服務會在規劃和未規劃的中斷期間，讓您的商務應用程式保持啟動並執行，來為您的商務持續性和嚴重損壞修復策略帶來貢獻 這是建議的嚴重損壞修復選項，可讓您的應用程式在發生區域性中斷時保持運作。

「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域都有一或多個資料中心。 

如果您想要將 Vm 移到不同區域中的可用性區域，請 [參閱這篇文章](../resource-mover/move-region-availability-zone.md)。

## <a name="using-availability-zones-for-disaster-recovery"></a>使用可用性區域進行嚴重損壞修復 

一般來說，可用性區域是用來在高可用性設定中部署 Vm。 它們可能太接近彼此，以作為嚴重損壞修復的災難修復解決方案。

不過，在某些情況下，可用性區域可用於損毀修復：

- 許多在內部部署裝載應用程式時，有 metro 嚴重損壞修復策略的客戶，有時會在將應用程式遷移至 Azure 時，將此策略視為模仿。 這些客戶認可了 metro 嚴重損壞修復策略在大規模的實體嚴重損壞情況下可能無法運作的事實，並接受此風險。 對於這類客戶而言，區域到區域的災難復原可以用來做為嚴重損壞修復選項。

- 許多其他客戶都有複雜的網路基礎結構，而且不想在次要區域中重新建立，因為相關聯的成本和複雜性。 區域到區域的災難復原會降低複雜性，因為它會利用跨可用性區域的重複網路概念，讓設定變得更簡單。 這類客戶偏好簡化，也可以使用可用性區域進行嚴重損壞修復。

- 在某些區域中，在相同的法律管轄區內沒有配對的區域 (例如東南亞) ，區域嚴重損壞修復可作為還原的嚴重損壞修復解決方案，因為您的應用程式和資料不會在全國界限內移動。 

- 區域對區域嚴重損壞修復意指將資料複寫到較短的距離（相較于 Azure 與 azure 的災難復原），因此，您可能會看到較低的延遲，因而降低 RPO。

雖然這些都是很強的優點，但在發生全區域自然災害的情況下，可能會導致區域發生嚴重損壞修復的風險降得很短。

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>區域對區域損毀修復的網路功能

如上所述，區域到區域的災難復原會降低複雜性，因為它會利用跨可用性區域的重複網路概念，讓設定變得更簡單。 區域到區域嚴重損壞修復案例的網路元件行為如下所述： 

- 虛擬網路：您可以使用與來源網路相同的虛擬網路來進行實際的容錯移轉。 將不同的虛擬網路用於來源虛擬網路，以進行測試容錯移轉。

- 子網：支援容錯移轉至相同的子網。

- 私人 IP 位址：如果您使用靜態 IP 位址，您可以在目的地區域中使用相同的 ip，如果您選擇以這樣的方式進行設定。

- 加速網路：類似于 Azure 至 Azure 的嚴重損壞修復，如果 VM SKU 支援加速網路，您可以啟用加速網路。

- 公用 IP 位址：您可以將相同區域中先前建立的標準公用 IP 位址附加至目標 VM。 基本公用 IP 位址不支援可用性區域相關的案例。

- 負載平衡器：標準負載平衡器是區域資源，因此目標 VM 可以附加至相同負載平衡器的後端集區。 不需要新的負載平衡器。

- 網路安全性群組：您可以使用與來源 VM 相同的網路安全性群組。

## <a name="pre-requisites"></a>必要條件

在部署區域以針對您的 Vm 進列區域嚴重損壞修復之前，請務必確定 VM 上啟用的其他功能可與區域間的損毀修復互通。

|功能  | 支援聲明  |
|---------|---------|
|傳統 VM   |     不支援    |
|ARM Vm    |    支援    |
|Azure 磁碟加密 v1 (雙重傳遞，Azure Active Directory (Azure AD) # A3     |     支援   |
|Azure 磁碟加密 v2 (單一傳遞，但不 Azure AD)     |    支援    |
|非受控磁碟    |    不支援    |
|受控磁碟    |    支援    |
|客戶管理的金鑰    |    支援    |
|鄰近位置群組    |    支援    |
|備份互通性    |    支援檔案層級備份和還原。 磁片和 VM 層級的備份與還原，不受支援。    |
|熱新增/移除    |    可以在啟用區域至區域複寫之後新增磁片。 在啟用區域對區域複寫之後，不支援移除磁片。    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>設定 Site Recovery 區域以進列區域損毀修復

### <a name="log-in"></a>登入

登入 Azure 管理入口網站。

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>啟用區域性 Azure 虛擬機器的複寫

1. 在 Azure 入口網站功能表上，選取 [虛擬機器]，或在任何頁面上搜尋並選取 [虛擬機器]。 選取您要複寫的 VM。 針對區域對區域的災難復原，此 VM 必須已在可用性區域中。

2. 在 [作業] 中，選取 [災害復原]。

3. 如下所示，在 [基本] 索引標籤中，針對 [可用性區域之間的嚴重損壞修復？] 選取 [是]

    ![基本設定頁面](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. 如果您接受所有預設值，請按一下 [檢查 + 開始複寫]，然後按一下 [開始複寫]。

5. 如果您想要變更複寫設定，請按一下 [下一步： Advanced settings]。

6. 將設定從預設值變更為適當的位置。 針對 Azure 至 Azure 嚴重損壞修復的使用者，此頁面可能看起來很熟悉。 您可以在[這裡](./azure-to-azure-tutorial-enable-replication.md)找到此分頁上所提供選項的詳細資料

    ![[Advanced Settings] 頁面](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. 按一下 [下一步：檢查 + 開始複寫]，然後按一下 [開始複寫]。

## <a name="faqs"></a>常見問題集

**1. 區域對區域嚴重損壞修復的價格如何運作？**
區域對區域嚴重損壞修復的定價與 Azure 到 Azure 嚴重損壞修復的定價相同。 您可以從 [這裡](https://azure.microsoft.com/pricing/details/site-recovery/) 和 [這裡](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)的定價頁面找到更多詳細資料。 請注意，您在區域對區域嚴重損壞修復時所看到的輸出費用，會低於區域損毀修復的範圍。

**2. RTO 和 RPO 的 SLA 為何？**
RTO SLA 與整體 Site Recovery 一樣。 我們承諾最多2小時的 RTO。 未定義 RPO 的 SLA。

**3. 次要區域中是否保證容量？**
Site Recovery 小組和 Azure 容量管理小組會規劃足夠的基礎結構容量。 當您啟動容錯移轉時，小組也有助於確保受 Site Recovery 保護的 VM 實例會部署至目的地區域。

**4. 支援哪些作業系統？**
區域到區域的嚴重損壞修復支援與 Azure 相同的作業系統，以進行 azure 的災難復原。 請參閱 [這裡](./azure-to-azure-support-matrix.md)的支援矩陣。

**5. 來源和目標資源群組可以相同嗎？**
否，您必須容錯移轉至不同的資源群組。

## <a name="next-steps"></a>後續步驟

執行嚴重損壞修復、容錯移轉、重新保護和容錯回復所需遵循的步驟，與 Azure 到 Azure 嚴重損壞修復案例中的步驟相同。

若要執行嚴重損壞修復演練，請遵循 [這裡](./azure-to-azure-tutorial-dr-drill.md)所述的步驟。

若要執行容錯移轉並重新保護次要區域中的 Vm，請遵循 [這裡](./azure-to-azure-tutorial-failover-failback.md)所述的步驟。

若要容錯回復到主要區域，請遵循 [這裡](./azure-to-azure-tutorial-failback.md)所述的步驟。
