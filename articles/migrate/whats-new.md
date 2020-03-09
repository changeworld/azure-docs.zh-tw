---
title: Azure Migrate 中的新功能
description: 了解 Azure Migrate 服務中的新功能和最新更新。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 63b6783a2f36d5bc9e84ce8291e7025b27359b6c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361829"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate 中的新功能

[Azure Migrate](migrate-services-overview.md) 可協助您探索及評定內部部署伺服器、應用程式與資料，並將其移轉至 Microsoft Azure 雲端。 此文章摘要說明 Azure Migrate 中的新功能。



## <a name="update-november-2019"></a>更新 (2019 年 11 月)

Azure Migrate 加入了一些新功能：

- **實體伺服器評量**。 除了已支援的實體伺服器移轉之外，現在也支援內部部署實體伺服器的評量。
- **以匯入為基礎的評量**。 現在支援使用 CSV 檔案中提供的中繼資料和效能資料來進行機器評量。
- **應用程式探索**： Azure Migrate 現在可使用 Azure Migrate 設備，支援應用層級的應用程式、角色和功能探索。 這目前僅支援 VMware VM，而且僅限於探索 (目前不支援評量)。 [深入了解](how-to-discover-applications.md)
- **無代理**程式相依性視覺效果：您不再需要明確安裝用於相依性視覺效果的代理程式。 目前支援無代理程式和代理程式型。
- **虛擬桌面**：使用 ISV 工具來評估內部部署虛擬桌面基礎結構（VDI），並將其遷移至 Azure 中的 Windows 虛擬桌面。
- **Web 應用程式**：用於評估和遷移 web 應用程式的 Azure App Service 移轉小幫手現在已整合至 Azure Migrate。

Azure Migrate 新增了新的評量和移轉工具：

- **Rackware**：提供雲端遷移。
- **Movere**：提供評量。

[深入了解](migrate-services-overview.md)如何使用工具和 ISV 供應項目在 Azure Migrate 中進行評量和移轉。

## <a name="release-version-july-2019"></a>發行版本 (2019 年 7 月)

目前的 Azure Migrate 版本已於 2019 年 7 月發行。

- **目前版本**：使用此版本來建立 Azure Migrate 專案、探索內部部署機器，以及協調評量與遷移。
- **先前版本**：針對使用舊版 Azure Migrate 的客戶（僅支援內部部署 VMware vm 的評估），您現在應該使用目前的版本。 在舊版中，您已無法建立新的 Azure Migrate 專案，或是執行新的探索。 您仍然可以存取現有的專案。 若要這樣做，請在 Azure 入口網站 > [所有服務] 中搜尋 **Azure Migrate**。 在 [Azure Migrate] 的通知中，會有可存取舊 Azure Migrate 專案的連結。


### <a name="azure-migrate-features"></a>Azure Migrate 功能

目前的 Azure Migrate 版本提供數個新功能：


- **整合的遷移平臺**： Azure Migrate 現在提供單一入口網站，可將您的遷移旅程集中、管理及追蹤至 Azure，並具有改良的部署流程和入口網站體驗。
- **評估和遷移工具**： Azure Migrate 提供原生工具，並與其他 Azure 服務及獨立軟體廠商（ISV）工具整合。 [深入了解](migrate-services-overview.md#isv-integration) ISV 整合。
- **Azure Migrate 評估**：使用 Azure Migrate 伺服器評估工具，您可以評估要遷移至 Azure 的 VMware Vm 和 hyper-v vm。 您也可以使用其他 Azure 服務及 ISV 工具來針對移轉進行評量。
- **Azure Migrate 遷移**：使用 Azure Migrate 伺服器遷移工具，您可以將內部部署 VMware Vm 和 hyper-v vm 遷移至 Azure，以及實體伺服器、其他虛擬化伺服器和私人/公用雲端 vm。 此外，您可以使用 ISV 工具來移轉至 Azure。
- **Azure Migrate 設備**： Azure Migrate 部署輕量設備，以探索及評估內部部署 VMware Vm 和 hyper-v vm。
    - 此設備會由 Azure Migrate 伺服器評量，以及 Azure Migrate 伺服器移轉用來進行無代理程式移轉。
    - 該設備會持續探索伺服器中繼資料與效能資料，以用於評量及移轉的目的。  
- **VMWARE VM 遷移**： Azure Migrate Server 遷移提供幾種方法，可將內部部署 VMware vm 遷移至 Azure。  其中一個是使用 Azure Migrate 設備的無代理程式移轉，而另一個則是使用複寫設備，且會在您想要移轉的每部 VM 上部署代理程式的代理程式型移轉。 [深入了解](server-migrate-overview.md)
 - **資料庫評估和遷移**：從 Azure Migrate，您可以使用 Azure 資料庫移轉小幫手評估內部部署資料庫，以遷移至 azure。 您可以使用 Azure 資料庫移轉服務來移轉資料庫。
- **Web 應用程式遷移**：您可以使用具有 Azure App Service 的公用端點 URL 來評估 Web 應用程式。 針對內部 .NET 應用程式的部署，您可以下載並執行 App Service 移轉小幫手。
- **資料箱**：使用 Azure Migrate 中的 Azure 資料箱，將大量離線資料匯入至 Azure。


## <a name="next-steps"></a>後續步驟

- [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。
- 針對 Azure Migrate [檢閱相關常見問題](resources-faq.md)。
- 請嘗試進行我們的教學課程來評量 [VMware VM](tutorial-assess-vmware.md) 與 [Hyper-V VM](tutorial-assess-hyper-v.md)。
