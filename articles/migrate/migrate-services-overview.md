---
title: 關於 Azure Migrate
description: 了解 Azure Migrate 服務。
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: d90c40447dc4928a34332685aea728b13c3775b1
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771252"
---
# <a name="about-azure-migrate"></a>關於 Azure Migrate

本文提供 Azure Migrate 服務的快速概觀。

Azure Migrate 提供集中式的中樞，可評估內部部署伺服器、基礎結構、應用程式與資料，並將其遷移至 Azure。 其提供下列各項：

- **整合式移轉平台**：單一入口網站，用來啟動、執行及追蹤您遷移至 Azure 的工作。
- **各種工具**：用於評量和遷移的各種工具。 Azure Migrate 工具包括伺服器評量和 Azure Migrate：伺服器移轉。 Azure Migrate 也可與其他 Azure 服務整合，以及與獨立軟體廠商 (ISV) 供應項目整合。
- **評量及移轉**：在 Azure Migrate 中樞，您可以評估和遷移：
    - **伺服器**：評估內部部署伺服器，並將其遷移至 Azure 虛擬機器。
    - **資料庫**：評估內部部署資料庫，並將其遷移至 Azure SQL Database 或 SQL 受控執行個體。
    - **Web 應用程式**：評估內部部署 Web 應用程式，並使用 Azure App Service 移轉小幫手將其遷移至 Azure App Service。
    - **虛擬桌面**：評估您的內部部署虛擬桌面基礎結構 (VDI)，並將其遷移至 Azure 中的 Windows 虛擬桌面。
    - **Data**︰使用 Azure 資料箱產品，以快速且符合成本效益的方式將大量資料遷移至 Azure。

## <a name="integrated-tools"></a>整合式工具

Azure Migrate 中樞包含這些工具：

**工具** | **評估和遷移** | **詳細資料**
--- | --- | ---
**Azure Migrate：伺服器評量** | 評估伺服器。 | 探索及評估內部部署 VMware VM、Hyper-V VM 和實體伺服器，使其準備好遷移至 Azure。
**Azure Migrate：伺服器移轉** | 遷移伺服器。 | 將 VMware VM、Hyper-V VM、實體伺服器以及其他虛擬化機器和公用雲端 VM 遷移至 Azure。
**資料移轉小幫手** | 評估 SQL Server 資料庫，以遷移至 Azure SQL Database、Azure SQL 受控執行個體，或執行 SQL Server 的 Azure VM。 | Data Migration Assistant 有助於找出阻礙移轉的潛在問題。 此功能可識別不支援的功能、在移轉後可供您利用的新功能，以及資料庫移轉的正確路徑。 [深入了解](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)。
**Azure Database Migration Service** | 將內部部署資料庫遷移至執行 SQL Server、Azure SQL Database 或 SQL 受控執行個體的 Azure VM。 | [深入了解](https://docs.microsoft.com/azure/dms/dms-overview)資料庫移轉服務。
**Movere** | 評估伺服器。 | [深入了解](#movere) Movere。
**Web 應用程式移轉小幫手** | 評估內部部署 Web 應用程式並將其遷移至 Azure。 |  使用 Azure App Service 移轉小幫手來評量內部部署網站，以移轉至 Azure App Service。<br/><br/> 使用移轉小幫手將 .NET 和 PHP Web 應用程式遷移至 Azure。 [深入了解](https://appmigration.microsoft.com/) Azure App Service 移轉小幫手。
**Azure 資料箱** | 遷移離線資料。 | 使用 Azure 資料箱產品將大量離線資料移至 Azure。 [深入了解](https://docs.microsoft.com/azure/databox/)。

> [!NOTE]
> 如果您是使用 Azure Government，外部整合工具和 ISV 供應項目無法將資料傳送至 Azure Migrate 專案。 您可以單獨使用工具。

## <a name="isv-integration"></a>ISV 整合

Azure Migrate 可與數項 ISV 供應項目整合。 

**ISV**    | **功能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) \(英文\) | 遷移伺服器。
[Cloudamize](https://www.cloudamize.com/platform) | 評估伺服器。
[Corent Technology](https://www.corenttech.com/AzureMigrate/) \(英文\) | 評估和遷移伺服器。
[Device42](https://docs.device42.com/) | 評估伺服器。
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 評估 VDI。
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 遷移伺服器。
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) \(英文\) | 評估伺服器。
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) \(英文\) | 評估伺服器和資料庫。

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate：伺服器評量工具

Azure Migrate：伺服器評量工具可探索並評估內部部署 VMware VM、Hyper-V VM 和實體伺服器，以遷移至 Azure。

此工具的作用如下：

- **Azure 移轉整備程度**：評估內部部署機器是否已準備好移轉至 Azure。
- **Azure 大小調整**：估計 Azure VM 在移轉之後的大小。
- **Azure 成本估計**：估計在 Azure 中執行內部部署伺服器的成本。
- **相依性分析**：識別將相互依存的伺服器移至 Azure 時的伺服器相依性和最佳化策略。 深入了解具有[相依性分析](concepts-dependency-visualization.md)的伺服器評量。

伺服器評量會使用您在內部部署環境中部署的輕量 [Azure Migrate 設備](migrate-appliance.md)。

- 設備會在 VM 或實體伺服器上執行。 您可以使用已下載的範本輕鬆地進行安裝。
- 設備會探索內部部署機器。 設備會將機器中繼資料和效能資料持續傳送至 Azure Migrate。
- 設備探索無須代理程式。 探索到的機器上未安裝任何內容。
- 探索設備之後，您可以將探索到的機器收集到群組中，然後執行每個群組的評量。

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate：伺服器移轉工具

Azure Migrate：伺服器移轉工具可協助您遷移至 Azure：

**移轉** | **詳細資料**
--- | ---
內部部署 VMware VM | 使用無代理程式或代理程式型移轉，將 VM 遷移至 Azure。<br/><br/> 針對無代理程式移轉，伺服器移轉會使用您在內部部署環境中部署的 Azure Migrate 設備。 其與您用於伺服器評量的設備類型相同。<br/><br/> 針對代理程式型移轉，伺服器評量會使用複寫設備。
內部部署 Hyper-V VM | 將 VM 遷移至 Azure。<br/><br/> 伺服器評量會使用 Hyper-V 主機上安裝的提供者代理程式進行移轉。
內部部署實體伺服器 | 您可以將實體機器遷移至 Azure。 您也可以為了移轉目的，將其他虛擬化機器及其他公用雲端中的 VM 視為虛擬機器，以便進行移轉。 | 伺服器評量會使用複寫設備來進行移轉。


## <a name="selecting-assessment-and-migration-tools"></a>選取評量和移轉工具

在 Azure Migrate 中樞，您可以選取要用於評量或移轉的工具，並將其新增至 Azure Migrate 專案。 如果您新增 ISV 工具或 Movere：

- 請依照工具指示取得授權或註冊免費試用，以展開作業。 每個 ISV 或工具都會指定工具授權。
- 每個工具都會有連線至 Azure Migrate 的選項。 請遵循工具指示來連線。
- 從 Azure Migrate 專案內，跨所有工具追蹤您的移轉。

## <a name="movere"></a>Movere

Movere 是一個軟體即服務 (SaaS) 平台。 此平台可在一天內精確呈現整個 IT 環境，藉此提升商業智慧。 組織和企業會成長、變更並進行數位最佳化。 於此同時，Movere 可讓他們有足夠的信心可查看及控制其環境，無論是平台、應用程式或地理位置為何。

Movere 已由 Microsoft [收購](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)，不再以獨立供應項目的形式出售。 您可以透過 Microsoft 解決方案評量和 Microsoft 雲端經濟學方案取得 Movere。 [深入了解](https://www.movere.io) Movere。

我們鼓勵您同時查看 Azure Migrate，這是我們內建的移轉服務。 Azure Migrate 提供的中央中樞可簡化您的雲端移轉工作。 此中樞可針對工作負載 (例如實體和虛擬伺服器、資料庫和應用程式) 提供全方位的支援。 端對端可見度可讓您輕鬆追蹤探索、評量和移轉的進度。

Azure Migrate 同時內建了 Azure 和合作夥伴 ISV 工具，可提供多種功能，包括：

- 探索虛擬和實體伺服器。
- 以效能為基礎的大小調整。
- 成本規劃。
- 以匯入為基礎的評量。
- 無代理程式應用程式相依性分析。

如果想要讓專家協助您開始使用服務，Microsoft 有技術熟練的 [Azure 專家受控服務提供者](https://azure.microsoft.com/partners)可引導您。 查看 [Azure Migrate 網站](https://azure.microsoft.com/services/azure-migrate/)。 

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務有兩個版本。

- **目前的版本**：使用此版本來建立 Azure Migrate 專案、探索內部部署電腦，以及協調評量與移轉。 [深入了解](whats-new.md)此版本的新功能。
- **先前版本**：舊版的 Azure Migrate 僅支援內部部署 VMware VM 的評量。 如果您使用舊版，您現在應改用目前的版本。 您無法再使用舊版建立 Azure Migrate 專案。 我們建議您不要用舊版執行新的探索。

    若要存取現有專案，請在 Azure 入口網站中搜尋並選取 [Azure Migrate]****。 [Azure Migrate]**** 儀表板具有通知以及可存取舊有 Azure Migrate 專案的連結。

## <a name="next-steps"></a>後續步驟

- 嘗試進行我們的教學課程以評估 [VMware VM](tutorial-prepare-vmware.md)、[Hyper-V VM](tutorial-prepare-hyper-v.md) 或[實體伺服器](tutorial-prepare-physical.md)。
- 針對 Azure Migrate [檢閱相關常見問題](resources-faq.md)。
