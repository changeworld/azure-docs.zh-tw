---
title: Azure Migrate 中的新功能
description: 了解 Azure Migrate 服務中的新功能和最新更新。
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: e39b8f7ee7b76a1955e02a7140524f812be086b5
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752442"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate 中的新功能

[Azure Migrate](migrate-services-overview.md) 可協助您探索及評定內部部署伺服器、應用程式與資料，並將其移轉至 Microsoft Azure 雲端。 此文章摘要說明 Azure Migrate 中的新版本及新功能。

## <a name="update-september-2020"></a>更新 (2020 年 9 月)
- 現在支援將伺服器移轉至可用性區域。
- 現在支援將 UEFI 型 VM 和實體伺服器移轉至 Azure 第 2 代 VM。 在此版本中，Azure Migrate：伺服器移轉工具不會在移轉期間執行從 Gen 2 VM 至 Gen 1 VM 的轉換。
- 新的 Azure Migrate Power BI 評量儀表板可協助您比較不同評量設定的成本。 儀表板隨附 PowerShell 公用程式，其會自動建立可插入至 Power BI 儀表板的評量。 [深入了解。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- 相依性分析 (無代理程式) 現在可以同時在 1000 個 VM 上執行。
- 現在可以使用 PowerShell 指令碼，大規模地啟用或停用相依性分析 (無代理程式)。 [深入了解。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- 使用利用相依性分析 (無代理程式) 收集的資料，將 Power BI 中的網路連線視覺化 [深入了解。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- 現在使用 Azure Migrate 可支援移轉資料磁碟達 32 TB 的 VMware VM：伺服器移轉無代理程式 VMware 移轉方法。 

## <a name="update-august-2020"></a>更新 (2020 年 8 月)

- 改善的上線體驗，其中 Azure Migrate 專案金鑰會從入口網站產生並用於完成設備註冊。
- 從入口網站下載 OVA/VHD 檔案或安裝程式指令碼，以便分別設定 VMware 和 Hyper-V 設備的選項。
- 以增強的使用者體驗，重新整理了設備設定管理員。
- 用於 Hyper-V VM 探索的多個認證支援。

## <a name="update-july-2020"></a>更新 (2020 年 7 月)

- 無代理程式 VMware 移轉現在支援每個 vCenter 並行複寫 300 個 VM

## <a name="update-june-2020"></a>更新 (2020 年 6 月)

- 現在支援將內部部署 VMware VM 遷移至 [Azure VMware 解決方案 (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) 的評量。 [深入了解](how-to-create-azure-vmware-solution-assessment.md)
- 支援應用裝置上的多個認證以進行實體伺服器探索。
- 對於已設定租用戶限制的租用戶，支援允許從設備進行 Azure 登入。


## <a name="update-april-2020"></a>更新 (2020 年 4 月)

Azure Migrate 支援 Azure Government 中的部署。 

- 您可以探索及評估 VMware VM、Hyper-V VM 和實體伺服器。
- 您可以將 VMware VM、Hyper-V VM 和實體伺服器移轉至 Azure。
- 針對 VMware 的移轉，您可以使用無代理程式或代理程式型移轉。 [深入了解](server-migrate-overview.md)。
- [請檢閱](migrate-support-matrix.md#supported-geographies-azure-government) Azure Government 支援的地理位置和區域。
- Azure Government [不支援代理程式型的相依性分析](concepts-dependency-visualization.md#agent-based-analysis)。
- Azure Government 支援功能預覽，特別是[無代理程式的相依性分析](concepts-dependency-visualization.md#agentless-analysis)及[應用程式探索](how-to-discover-applications.md)。


## <a name="update-march-2020"></a>更新 (2020 年 3 月)

您現在可以使用指令碼型安裝來設定 [Azure Migrate 設備](migrate-appliance.md)：

- 指令碼型安裝可代替設備的 .OVA (VMware)/VHD (Hyper-V) 安裝。
- 其提供 PowerShell 安裝程式指令碼，可用於在執行 Windows Server 2016 的現有機器上，為 VMware/Hyper-V 設定設備。

## <a name="update-november-2019"></a>更新 (2019 年 11 月)

Azure Migrate 加入了一些新功能：

- **實體伺服器評量**。 除了已支援的實體伺服器移轉之外，現在也支援內部部署實體伺服器的評量。
- **以匯入為基礎的評量**。 現在支援使用 CSV 檔案中提供的中繼資料和效能資料來進行機器評量。
- **應用程式探索**：Azure Migrate 現在支援使用 Azure Migrate 設備，對應用程式、角色及功能進行應用層級探索。 這目前僅支援 VMware VM，而且僅限於探索 (目前不支援評量)。 [深入了解](how-to-discover-applications.md)
- **無代理程式的相依性視覺效果**：您不再需要明確安裝代理程式來取得相依性視覺效果。 目前支援無代理程式和代理程式型。
- **虛擬桌面**：使用 ISV 工具來評定內部部署虛擬桌面基礎結構 (VDI)，並將其移轉移至 Azure 中的 Windows 虛擬桌面。
- **Web 應用程式**：用於評定和移轉 Web 應用程式的 Azure App Service 移轉小幫手現在已與 Azure Migrate 整合。

Azure Migrate 新增了新的評量和移轉工具：

- **Rackware**：供應項目雲端移轉。
- **Movere**：供應項目評量。

[深入了解](migrate-services-overview.md)如何使用工具和 ISV 供應項目在 Azure Migrate 中進行評量和移轉。

## <a name="azure-migrate-current-version"></a>Azure Migrate 目前版本

目前的 Azure Migrate 版本 (於 2019 年 7 月發行) 提供數個新功能：

- **整合式移轉平台**：Azure Migrate 現在會提供單一入口網站，以針對您移轉至 Azure 的旅途進行集中化、管理及追蹤，並提供改善的部署流程和入口網站體驗。
- **評量及移轉工具**：Azure Migrate 提供原生工具，並與其他 Azure 服務和獨立軟體廠商 (ISV) 工具整合。 [深入了解](migrate-services-overview.md#isv-integration) ISV 整合。
- **Azure Migrate 評量**：透過使用 Azure Migrate 伺服器評量工具，您可以評量 VMware VM 和 Hyper-V VM 以針對 Azure 進行移轉。 您也可以使用其他 Azure 服務及 ISV 工具來針對移轉進行評量。
- **Azure Migrate 移轉**：透過使用 Azure Migrate 伺服器移轉工具，您可以將內部部署 VMware VM 與 Hyper-V VM 移轉至 Azure，也可以移轉至實體伺服器、其他虛擬化伺服器，以及私人/公用雲端 VM。 此外，您可以使用 ISV 工具來移轉至 Azure。
- **Azure Migrate 設備**：Azure Migrate 會部署輕量型設備，以進行針對內部部署 VMware VM 與 Hyper-V VM 的探索及評量。
    - 此設備會由 Azure Migrate 伺服器評量，以及 Azure Migrate 伺服器移轉用來進行無代理程式移轉。
    - 該設備會持續探索伺服器中繼資料與效能資料，以用於評量及移轉的目的。  
- **VMware VM 移轉**：Azure Migrate 伺服器移轉會提供數種方法，來將內部部署 VMware VM 移轉至 Azure。  其中一個是使用 Azure Migrate 設備的無代理程式移轉，而另一個則是使用複寫設備，且會在您想要移轉的每部 VM 上部署代理程式的代理程式型移轉。 [深入了解](server-migrate-overview.md)
 - **資料庫評量及移轉**：從 Azure Migrate，您可以使用 Azure Data Migration Assistant 來針對內部部署資料庫進行移轉上的評量。 您可以使用 Azure 資料庫移轉服務來移轉資料庫。
- **Web 應用程式移轉**：您可以搭配 Azure App Service 使用公用端點 URL 來存取 Web 應用程式。 針對內部 .NET 應用程式的部署，您可以下載並執行 App Service 移轉小幫手。
- **資料箱**：在 Azure Migrate 中使用 Azure 資料箱將大量離線檔案匯入 Azure。

## <a name="azure-migrate-previous-version"></a>Azure Migrate 舊版本

如果您使用的是舊版 Azure Migrate (僅支援內部部署 VMware VM 的評量)，則您現在應該使用目前的版本。 在舊版中，您已無法建立新的 Azure Migrate 專案，或是執行新的探索。 您仍然可以存取現有的專案。 若要這樣做，請在 Azure 入口網站 > [所有服務] 中搜尋 **Azure Migrate**。 在 [Azure Migrate] 的通知中，會有可存取舊 Azure Migrate 專案的連結。



## <a name="next-steps"></a>後續步驟

- [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。
- 針對 Azure Migrate [檢閱相關常見問題](resources-faq.md)。
- 請嘗試進行我們的教學課程來評量 [VMware VM](./tutorial-assess-vmware-azure-vm.md) 與 [Hyper-V VM](tutorial-assess-hyper-v.md)。