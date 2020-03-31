---
title: 關於 Azure Migrate
description: 了解 Azure Migrate 服務。
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127737"
---
# <a name="about-azure-migrate"></a>關於 Azure Migrate

本文提供 Azure Migrate 服務的快速概觀。

Azure Migrate 提供集中式的中樞，可評估內部部署伺服器、基礎結構、應用程式與資料，並將其遷移至 Azure。 Azure Migrate 提供下列功能：

- **整合式移轉平台**：單一入口網站，用來啟動、執行及追蹤您遷移至 Azure 的旅途。
- **各種工具**：用於評量和遷移的各種工具。 工具包括 Azure Migrate：伺服器評量和 Azure Migrate：伺服器移轉。 Azure Migrate 可與其他 Azure 服務整合，也可與其他工具和獨立軟體廠商 (ISV) 供應項目整合。
- **評量及移轉**：在 Azure Migrate 中樞，您可以評估和遷移：
    - **伺服器**：評估內部部署伺服器並將其遷移至 Azure VM。
    - **資料庫**：評估內部部署資料庫並將其遷移至 Azure SQL DB，或遷移至 Azure SQL 受控執行個體。
    - **Web 應用程式**：使用 Azure App Service 小幫手來評估內部部署 Web 應用程式，並將其遷移至 Azure App Service。
    - **虛擬桌面**：評估您的內部部署虛擬桌面基礎結構 (VDI)，並將其遷移至 Azure 中的 Windows 虛擬桌面。
    - **Data**︰使用 Azure 資料箱產品，以快速且符合成本效益的方式將大量資料遷移至 Azure。 


## <a name="integrated-tools"></a>整合式工具

Azure Migrate 中樞包含這些工具。

**工具** | **評估/遷移** | **詳細資料**
--- | --- | ---
**Azure Migrate：伺服器評量** | 評估伺服器。 | 探索及評估內部部署 VMware VM、Hyper-V VM 和實體伺服器，使其準備好遷移至 Azure。
**Azure Migrate：伺服器移轉** | 遷移伺服器。 | 將 VMware VM、Hyper-V VM、實體伺服器以及其他虛擬化機器和公用雲端 VM 遷移至 Azure。 
**Database Migration Assistant (DMA)** | 評估內部部署 SQL Server 資料庫，以遷移至 Azure SQL DB、Azure SQL 受控執行個體或執行 SQL Server 的 Azure VM。 | DMA 有助於找出可能的移轉封鎖問題。 此功能可識別不支援的功能，以及您在移轉後可以利用的新功能，並協助您識別資料庫移轉的正確路徑。 [深入了解](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。
**資料庫移轉服務 (DMS)** | 將內部部署資料庫遷移至執行 SQL 的 Azure VM、Azure SQL DB 與 Azure SQL 受控執行個體。 | [深入了解](https://docs.microsoft.com/azure/dms/dms-overview) DMS。
**Movere** | 評估伺服器。 | [深入了解](#movere) Movere。
**Web 應用程式移轉小幫手** | 評估內部部署 Web 應用程式並將其遷移至 Azure。 |  使用 Azure App Service 移轉小幫手來評估內部部署網站，以遷移至 Azure App Service。<br/><br/> 使用小幫手將 .NET 和 PHP Web 應用程式遷移至 Azure。 [深入了解](https://appmigration.microsoft.com/) Azure App Service 移轉小幫手。
**Azure 資料箱** | 離線資料移轉。 | 使用 Azure 資料箱產品將大量資料離線移至 Azure。 [深入了解](https://docs.microsoft.com/azure/databox/)。

## <a name="isv-integration"></a>ISV 整合

Azure Migrate 可與許多 ISV 供應項目整合。 

**ISV**    | **功能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) \(英文\) | 移轉伺服器
[Cloudamize](https://www.cloudamize.com/platform) | 評定伺服器
[Corent Technology](https://www.corenttech.com/AzureMigrate/) \(英文\) | 評估和遷移伺服器
[Device 42](https://docs.device42.com/) \(英文\) | 評定伺服器
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 評定 VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 移轉伺服器
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) \(英文\) | 評定伺服器
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) \(英文\) | 評定伺服器和資料庫


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate 伺服器評量工具

Azure Migrate：伺服器評量工具能探索並評量內部部署 VMware VM、Hyper-V VM 和實體伺服器，以針對 Azure 進行移轉。 此工具的作用如下：

- **Azure 移轉整備程度：** 評估內部部署機器是否已準備好移轉至 Azure。
- **Azure 大小調整：** 估計 Azure VM 在移轉之後的大小。
- **Azure 成本估計：** 在 Azure 中執行內部部署伺服器的估計成本。
- **相依性分析**如果您搭配使用「伺服器評量」與[相依性分析](concepts-dependency-visualization.md)，您將可有效識別跨伺服器相依性，以及最佳化將相依伺服器移至 Azure 的策略。


伺服器評量會使用您在內部部署環境中部署的輕量 [Azure Migrate 設備](migrate-appliance.md)。

- 設備會在 VM 或實體伺服器上執行。 您可以使用已下載的範本輕鬆地進行安裝。
- 設備會探索內部部署機器，並持續將機器中繼資料和效能資料傳送至 Azure Migrate。
- 設備探索無須代理程式。 探索到的機器上未安裝任何內容。
- 探索設備之後，您會將探索到的機器收集到群組中，然後執行群組的評量。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate 伺服器移轉工具

Azure Migrate：伺服器移轉工具可協助您將內部部署 VMware VM、Hyper-V VM、實體伺服器、其他虛擬化電腦，以及公用雲端 VM 移轉至 Azure。 您可以先對機器進行評估再加以遷移，或直接遷移而不進行評估。 

針對 VMware VM 的無代理程式移轉和 Hyper-V VM 的移轉，伺服器移轉會使用您在內部部署環境中部署的 Azure Migrate 設備。 您設定伺服器評量時也會用到該設備，如前一節所述。


## <a name="selecting-assessmentmigration-tools"></a>選取評量/移轉工具

在 Azure Migrate 中樞，您可以選取要用於評量或移轉的工具，並將其新增至 Azure Migrate 專案。 如果您新增 ISV 工具或 Movere：

- 根據工具指示取得授權，或註冊免費試用，以展開作業。 工具授權由 ISV 或工具所決定。 
- 每個工具中都會有連線至 Azure Migrate 的選項。 請遵循工具指示來連線。
- 您可以從 Azure Migrate 專案內，跨所有工具追蹤您的移轉旅程。


## <a name="movere"></a>Movere

Movere 是一個 SaaS 平台，可在一天內精確呈現整個 IT 環境，藉此提升商業智慧。 隨著組織成長、變更和數位最佳化，此解決方案讓企業有信心無論平台、應用程式或地理位置為何，都能掌握其環境的可見度和控制權。 Movere 已由 Microsoft [收購](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)，不再以獨立供應項目的形式出售。  您可透過 Microsoft 解決方案評量和雲端經濟學方案取得 Movere。 [深入了解](https://www.movere.io) Movere。 

我們鼓勵您同時查看 Azure Migrate，這是我們內建的移轉服務。 Azure Migrate 會提供中央中樞，以簡化您遷移至雲端的工作。 此中樞會針對不同的工作負載 (包括實體和虛擬伺服器、資料庫和應用程式) 提供全方位的支援。 端對端可見度可讓您輕鬆地追蹤探索、評量和移轉的進度。 Azure Migrate 內建 Azure 和合作夥伴 ISV 工具，也提供了廣泛的功能，包括虛擬和實體伺服器探索、以效能為基礎的適當大小調整、成本規劃、匯入型評量和無代理程式應用程式相依性分析。 如果想尋求專家協助您開始使用該服務，Microsoft 有技術熟練的 [Azure 專家受控服務提供者](https://azure.microsoft.com/partners)可引導您完成旅程。 查看 [Azure Migrate 網站](https://azure.microsoft.com/services/azure-migrate/)。 
 

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務有兩個版本：

- **目前的版本**：使用此版本來建立 Azure Migrate 專案、探索內部部署電腦，以及協調評量與移轉。 [深入了解](whats-new.md)此版本的新功能。
- **先前版本**：如果您使用舊版 Azure Migrate (僅支援內部部署 VMware VM 的評量)，則您現在應該使用目前的版本。 您已無法使用舊版來建立 Azure Migrate 專案，且我們建議您不要執行新的探索。 若要存取現有專案，請在 Azure 入口網站中，搜尋並選取 [Azure Migrate]  。 在 [Azure Migrate]  儀表板上，會有可以存取舊 Azure Migrate 專案的通知與連結。



## <a name="next-steps"></a>後續步驟

- 嘗試進行我們的教學課程以評估 [VMware VM](tutorial-prepare-vmware.md)、[Hyper-V VM](tutorial-prepare-hyper-v.md) 或[實體伺服器](tutorial-prepare-physical.md)。
- 針對 Azure Migrate [檢閱相關常見問題](resources-faq.md)。
