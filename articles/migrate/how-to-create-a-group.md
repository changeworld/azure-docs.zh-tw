---
title: 使用 Azure Migrate 分組機器以進行評量 | Microsoft Docs
description: 說明如何在使用 Azure Migrate 服務進行評量之前先分組機器。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 867124a08bbad88b7dac5386ee6bc1c9c4d99c12
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549927"
---
# <a name="create-a-group-for-assessment"></a>建立用於評量的群組

本文說明如何使用 Azure Migrate：伺服器評估來建立機器群組以進行評量。

[Azure Migrate](migrate-services-overview.md)可協助您遷移至 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。 該中樞能提供 Azure 工具以進行評量和移轉，也提供協力廠商獨立軟體廠商 (ISV) 供應項目。 

## <a name="grouping-machines"></a>群組機器

您會將機器收集成群組，以評估它們是否適合遷移至 Azure，以及取得 Azure 的大小調整和成本估計。 建立群組的方法有好幾種：

- 如果您知道需要一起遷移的機器，您可以在 Azure Migrate 中手動建立群組。
- 如果您不確定需要群組在一起的機器，您可以使用 Azure Migrate 中的相依性視覺效果功能來建立群組。 

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

## <a name="create-a-group-manually"></a>手動建立群組

您可以在[建立評](how-to-create-assessment.md)量的同時建立群組。

如果您想要在建立評估以外的地方手動建立群組，請執行下列動作：

1. 在 Azure Migrate 專案 >**總覽**中，按一下 [**評估和遷移伺服器**]。 在 [ **Azure Migrate：伺服器評估**] 中，按一下 [**群組**]
    - 如果您尚未新增 [Azure Migrate：伺服器評估] 工具，請按一下將它新增。 [深入了解](how-to-assess.md)。
    - 如果您尚未建立 Azure Migrate 專案，請[深入瞭解](how-to-add-tool-first-time.md)。

    ![選取群組](./media/how-to-create-a-group/select-groups.png)

2. 按一下 [**群組**] 圖示。
3. 在 [**建立群組**] 中指定組名，然後在 [**設備名稱**] 中，選取您要用於機器探索的 Azure Migrate 設備。
4. 從 [電腦] 清單中，選取您要新增至群組 >**建立**的電腦。

    ![建立群組](./media/how-to-create-a-group/create-group.png)

當您[建立 AZURE VM 評估](how-to-create-assessment.md)或[azure VMWARE 解決方案（AVS）評估](how-to-create-azure-vmware-solution-assessment.md)時，您現在可以使用此群組。 請注意，您可以在只有 VMware Vm 的群組上建立 AVS 評量。 

## <a name="refine-a-group-with-dependency-mapping"></a>使用相依性對應來精簡群組

相依性對應可協助您將跨電腦的相依性視覺化。 當您想要以更高的信賴程度評估電腦群組時，通常會使用相依性對應。
- 這可協助您在執行評量之前，先交叉檢查機器相依性。 
- 它也有助於有效地規劃遷移至 Azure，方法是確保不會留下任何內容，進而避免在遷移期間發生意外的中斷。
- 您可以探索需要一起遷移的相互相依系統，以及識別執行中的系統是否仍為使用者提供服務，或者是解除委任而非遷移的候選。

如果您已經設定相依性[對應](how-to-create-group-machine-dependencies.md)，而且想要縮小現有群組，請執行下列動作：

1. 在 [**伺服器**] 索引標籤的 [ **Azure Migrate：伺服器評估**] 磚中，按一下 [**群組**]。
2. 按一下您要精簡的群組。
    - 如果您尚未設定相依性對應，[相依性 **]** 欄將會顯示 [**需要安裝**狀態]。 針對您要將相依性視覺化的每個 VM，按一下 [**需要安裝**]。 在每部 VM 上安裝幾個代理程式，然後才能對應機器相依性。 [深入了解](how-to-create-group-machine-dependencies.md)。

        ![新增相依性對應](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 如果您已經設定相依性對應，請在 [群組] 頁面上，按一下 [視圖相依性 **]** 以開啟群組相依性對應。

3. 按一下 [**視圖**相依性] 之後，群組相依性對應會顯示下列各項：

    - 輸入（用戶端）和輸出（伺服器） TCP 連線與群組中已安裝相依性代理程式的所有電腦。
    - 未安裝相依性代理程式的相依機器會依埠號碼分組。
    - 已安裝 dependency agent 的相依機器會顯示為不同的方塊。
    - 在機器內執行的進程。 展開每個電腦方塊以查看處理常式。
    - 電腦屬性（包括 FQDN、作業系統、MAC 位址）。 按一下每個機器方塊以查看詳細資料。

4. 若要在您選擇的時間間隔內查看相依性，請指定開始和結束日期或持續時間，以修改時間範圍（預設為小時）。

    > [!NOTE]
    > 時間範圍最長可達一小時。 如果您需要較長的範圍，請使用[Azure 監視器來查詢相依資料](how-to-create-group-machine-dependencies.md)的時間較長。

5. 識別您想要在群組中新增或移除的相依性之後，您就可以修改該群組。 使用 Ctrl + 按一下來新增或移除群組中的機器。

    - 您只能新增已探索到的機器。
    - 新增和移除機器會使群組的過去評估失效。
    - 修改群組時，可以選擇性地建立新評量。


## <a name="next-steps"></a>後續步驟

瞭解如何設定和使用相依性[對應](how-to-create-group-machine-dependencies.md)來建立高度信賴群組。

