---
title: 使用 Azure Migrate 伺服器評定來評定要移轉到 Azure 的實體伺服器
description: 說明如何使用 Azure Migrate 伺服器評定來評定內部部署實體伺服器是否可移轉到 Azure。
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 3669658100681d08e754c19377b82faff5bce1ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090420"
---
# <a name="tutorial-assess-physical-servers-for-migration-to-azure"></a>教學課程：評估要移轉至 Azure 的實體伺服器

在您移轉至 Azure 的過程中，您會評估您的內部部署工作負載，以測量雲端整備程度、找出風險，以及預估成本和複雜度。

此文章說明如何使用 Azure Migrate 來評定內部部署實體伺服器是否可移轉至 Azure：伺服器評量工具。


在本教學課程中，您會了解如何：
> [!div class="checklist"]
- 根據電腦中繼資料和設定資訊執行評量。
- 根據效能資料執行評量。

> [!NOTE]
> 教學課程顯示嘗試案例的最快路徑，並且在可行時使用預設選項。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>必要條件

- 在您遵循此教學課程來評估您的機器是否可移轉至 Azure VM 之前，請確定您已探索到您想要評估的機器：
    - 若要使用 Azure Migrate 設備探索機器，請[遵循此教學課程](tutorial-discover-physical.md)。 
    - 若要使用匯入的 CSV 檔案探索機器，請[遵循此教學課程](tutorial-discover-import.md)。
- 請確定您想要評定的實體機器不是執行 Windows Server 2003 或 SUSE Linux。 這些機器不支援評量。


## <a name="decide-which-assessment-to-run"></a>決定要執行的評量


決定您是否想要根據依內部部署或動態效能資料收集的電腦設定資料/中繼資料，使用調整大小準則來執行評量。

**評量** | **詳細資料** | **建議**
--- | --- | ---
**依內部部署** | 根據電腦設定資料/中繼資料進行評估。  | 建議的 Azure VM 大小是根據內部部署 VM 大小。<br/><br> 建議的 Azure 磁碟類型是根據您在評量的儲存體類型設定中所選取的內容而定。
**以效能為基礎** | 根據所收集的動態效能資料進行評估。 | 建議的 Azure VM 大小是根據 CPU 和記憶體使用量資料。<br/><br/> 建議的磁碟類型是根據內部部署磁碟的 IOPS 和輸送量。

## <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 在 [伺服器] 頁面 > [Windows 和 Linux 伺服器] 上，按一下 [評估和遷移伺服器]。

   ![評估和遷移伺服器按鈕的位置](./media/tutorial-assess-physical/assess.png)

2. 在 [**Azure Migrate：伺服器評量]** 中，按一下 [評估]。

    ![[評估] 按鈕的位置](./media/tutorial-assess-physical/assess-servers.png)

3. 在 [評估伺服器]  >  [評量類型] 中，請選取 [Azure VM]。
4. 在 [探索來源] 中：

    - 如果您使用設備探索到機器，請選取 [從 Azure Migrate 設備探索到的機器]。
    - 如果您使用匯入的 CSV 檔案探索到機器，請選取 [匯入的機器]。 
    
5. 指定評量的名稱。 
6. 按一下 [檢視全部] 來檢閱評估屬性。

    ![[檢視全部] 按鈕的位置，該按鈕是用來檢閱評量屬性](./media/tutorial-assess-physical/assessment-name.png)

7. 在 [評量屬性]  >  [目標屬性] 中：
    - 在 [目標位置] 中，指定您要將機器遷移到其中的 Azure 區域。
        - 大小和成本建議是根據您指定的位置。
        - 在 Azure Government 中，您可以將評量的目標設定為[這些區域](migrate-support-matrix.md#supported-geographies-azure-government)
    - 在 [儲存體類型] 中，
        - 如果您想要在評量中使用以效能為基礎的資料，請針對 Azure Migrate 選取 [自動]，以根據磁碟 IOPS 和輸送量來建議儲存體類型。
        - 或者，選取您想要在遷移 VM 時使用的儲存體類型。
    - 在 [保留執行個體] 中，指定是否要在遷移 VM 時使用保留執行個體。
        - 如果您選取使用保留執行個體，則無法指定**折扣 (%)** 或 **VM 執行時間**。 
        - [深入了解](https://aka.ms/azurereservedinstances)。
8. 在 [VM 大小] 中：
 
    - 在 [調整大小準則] 中，選取是否要根據電腦設定資料/中繼資料或以效能為基礎的資料進行評量。 如果您使用效能資料：
        - 在 [效能歷程記錄] 中，指出您想要作為評量基礎的資料持續時間
        - 在 [百分位數使用率] 中，指定您想要用於效能範例的百分位數值。 
    - 在 [VM 系列] 中，指定您想要考量的 Azure VM 系列。
        - 如果您使用的是以效能為基礎的評量，Azure Migrate 會為您建議值。
        - 視需要調整設定。 例如，如果您沒有實際執行環境需要 Azure 中的 A 系列 VM，則可以從系列清單中排除 A 系列。
    - 在 [緩和因數] 中，指出您想要在評量期間使用的緩衝區。 這會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。 例如，如果您使用兩個緩和因數：**元件** | **有效使用率** | **新增緩和因素 (2.0)** 核心 | 2 | 4 記憶體 | 8 GB | 16 GB    
   
9. 在 [價格] 中：
    - 如果您已註冊，請在 [供應項目] 中指定 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 伺服器評量會評估該供應項目的成本。
    - 在 [貨幣] 中，選取您帳戶的帳單貨幣。
    - 在 [折扣 (%)] 中，在 Azure 供應項目上新增您獲得的任何訂用帳戶特定折扣。 預設設定為 0%。
    - 在 [VM 執行時間] 中，指定 VM 將會執行的持續時間 (每月天數/每天時數)。
        - 這適用於不會連續執行的 Azure VM。
        - 成本預估是根據指定的持續時間。
        - 預設值是每月 31 天/每天 24 小時。

    - 在 [EA 訂用帳戶] 中，指定是否要將 Enterprise 合約 (EA) 訂用帳戶折扣納入考量，以預估成本。 
    - 在 [Azure Hybrid Benefit] 中，指定您是否已經有 Windows Server 授權。 如果您有授權，且授權涵蓋 Windows Server 訂用帳戶的作用中軟體保證，您可以在將授權帶入 Azure 時，針對 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) 套用。

10. 如果您進行變更，請按一下 [儲存]。

    ![評量屬性](./media/tutorial-assess-physical/assessment-properties.png)

11. 在 [評估伺服器] 中，按 [下一步]。
12. 在 [選取要評估的機器] 中，選取 [新建]，然後指定群組名稱。 
13. 選取設備，然後選取您想要新增至群組的 VM。 然後按一下 [下一步]  。
14. 在 [**檢閱 + 建立評量] 中，檢閱評量詳細資料，然後按一下 [建立評量] 以建立群組並執行評量。


    > [!NOTE]
    > 針對以效能為基礎的評量，建議您在建立評量之前，先等待至少一天後再開始探索。 這可提供更高的信賴度來收集效能資料。 在理想的情況下，在您開始探索之後，請等候您指定的效能持續時間 (天/週/月)，以取得高信賴等級。

## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 移轉整備程度**：VM 是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行 VM 的預估每月計算和儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。

若要檢視評估：

1. 在 [伺服器] >  **[Azure Migrate：伺服器評量]** 中，按一下 [評量] 旁邊的數字。
2. 在 [評估] 中，選取評估來加以開啟。 作為範例 (估計和成本僅供範例使用)： 

    ![評量摘要](./media/tutorial-assess-physical/assessment-summary.png)

3. 檢閱評量摘要。 您也可以編輯評量屬性，或重新計算評量。
 
 
### <a name="review-readiness"></a>檢閱整備程度

1. 按一下 [Azure 移轉整備程度]。
2. 在 [Azure 移轉整備程度] 中，檢閱 VM 狀態：
    - **可供 Azure 使用**：當 Azure Migrate 針對評估中的 VM 建議 VM 大小和成本估計值時會用到。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：當 Azure Migrate 因資料可用性問題而無法評估整備程度時會用到。

3. 選取 [Azure 移轉整備程度] 狀態。 您可以檢視 VM 移轉整備程度的詳細資料。 您也可以向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。

### <a name="review-cost-estimates"></a>檢閱成本估計

此評估摘要會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。 

1. 檢閱每月總成本。 系統會針對評估群組中的所有 VM 匯總成本。

    - 系統會根據機器的大小建議、其磁碟和其屬性來預估成本。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 執行 Azure VM 上的內部部署 VM 時，才能估計成本。 估計不會考量 PaaS 或 SaaS 成本。

2. 檢閱每月儲存體成本。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。 
3. 您可以向下切入以查看特定 VM 的成本詳細資料。

### <a name="review-confidence-rating"></a>檢閱信賴評等

伺服器評量會將信賴評等指派給以效能為基礎的評量。 評等從一顆星 (最低) 到五顆星 (最高)。

![信賴評等](./media/tutorial-assess-physical/confidence-rating.png)

信賴評等可協助您預估評量中的大小建議是否可靠。 此評等會以計算評估所需的資料點可用性作為基礎。

> [!NOTE]
> 如果您根據 CSV 檔案建立評量，則不會指派信賴評等。

信賴評等如下。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[深入了解](concepts-assessment-calculation.md#confidence-ratings-performance-based)信賴評等。

## <a name="next-steps"></a>下一步

- 使用[相依性對應](concepts-dependency-visualization.md)尋找電腦相依性。
- 設定[以代理程式為基礎的](how-to-create-group-machine-dependencies.md)相依性對應。
