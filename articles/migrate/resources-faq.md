---
title: Azure Migrate 常見問題
description: 取得 Azure Migrate 服務相關常見問題的解答。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 95fd2ea9ecf5e4bcdf3fb2291e4cf7bf5b111c5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87847460"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常見問題

本文將回答有關 Azure Migrate 的常見問題。 如果您在閱讀本文後有任何問題，可以在 [Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)張貼文章。 您也可以參閱下列文章：

- [Azure Migrate 設備](common-questions-appliance.md)的相關問題
- [探索、評量和相依性視覺效果](common-questions-discovery-assessment.md)的相關問題

## <a name="what-is-azure-migrate"></a>什麼是 Azure Migrate？

Azure Migrate 提供中央中樞，可追蹤內部部署應用程式和工作負載的探索、評量和遷移，以及將私人和公用雲端 Vm 移至 Azure。 中樞提供適用于評定和遷移的 Azure Migrate 工具，以及協力廠商 ISV 供應專案。 [深入了解](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate 可以做什麼？

使用 Azure Migrate 探索、評估內部部署基礎結構、應用程式和資料，並將其遷移至 Azure。 Azure Migrate 支援評定和遷移內部部署 VMware Vm、Hyper-v Vm、實體伺服器、其他虛擬化 Vm、資料庫、web 應用程式和虛擬桌面。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate 與 Azure Site Recovery 之間有何差異？

[Azure Migrate](migrate-services-overview.md) 提供集中式的中樞，以供評估及遷移至 Azure。 

- 使用 Azure Migrate 可透過 Azure Migrate 工具、其他 Azure 服務及協力廠商工具，提供互通性和未來的擴充性。
- Azure Migrate：伺服器遷移工具的目的是為了將伺服器遷移至 Azure 而建立。 它已針對遷移進行優化。 您不需要瞭解與遷移不直接相關的概念和案例。 
- 從 VM 的複寫開始，開始進行180天的遷移不會產生任何工具使用費用。 這讓您有時間完成遷移。 您只需針對複寫中使用的儲存體和網路資源支付費用，以及在測試遷移期間使用的計算費用。
- Azure Migrate 支援 Site Recovery 支援的所有遷移案例。 此外，對於 VMware Vm，Azure Migrate 提供無代理程式遷移選項。
- 我們會優先針對 Azure Migrate：伺服器遷移工具的新遷移功能。 這些功能不是 Site Recovery 的目標。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) 只能用於嚴重損壞修復。

Azure Migrate：伺服器遷移工具會使用部分內部部署機器的後端 Site Recovery 功能，來隨即轉移。

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>我有一個具有舊版傳統體驗的專案 Azure Migrate。 如何? 開始使用新版本嗎？

您無法將舊版中的專案或元件升級至新版本。 您需要[建立新的 Azure Migrate 專案](create-manage-projects.md)，並[將評量與移轉工具新增到其中](how-to-add-tool-first-time.md)。 使用教學課程來了解如何使用可用的評量和移轉工具。 如果您已將 Log Analytics 工作區連結至傳統專案，可以在刪除傳統專案之後，將其連結加至目前版本的專案。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate：伺服器評量和地圖工具組之間有何差異？

伺服器評量提供評量以協助進行遷移，並評估工作負載以遷移至 Azure。 [Microsoft 評定和規劃 (對應) 工具](https://www.microsoft.com/download/details.aspx?id=7826)組有助於其他工作，包括較新版本的 Windows 用戶端和伺服器作業系統的遷移規劃，以及軟體使用方式追蹤。 在這些情況下，請繼續使用地圖工具組。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>伺服器評量和 Site Recovery 部署規劃工具之間有何差異？

伺服器評定是一項遷移規劃工具。 Site Recovery 部署規劃工具是嚴重損壞修復計畫工具。

根據您想要執行的動作來選擇您的工具：

- **規劃將內部部署遷移至 azure**：如果您打算將內部部署伺服器遷移至 azure，請使用伺服器評定進行遷移規劃。 伺服器評量會評估內部部署工作負載，並提供可協助您遷移的指引和工具。 準備好遷移計畫之後，您可以使用 Azure Migrate：伺服器遷移等工具將機器遷移至 Azure。
- **規劃 Azure**的嚴重損壞修復：如果您打算使用 Site Recovery 設定從內部部署至 azure 的災難復原，請使用 Site Recovery 部署規劃工具。 部署規劃工具針對嚴重損壞修復的目的，提供內部部署環境的深度、Site Recovery 特定評量。 它會提供與嚴重損壞修復相關的建議，例如複寫和容錯移轉。

## <a name="how-does-server-migration-work-with-site-recovery"></a>伺服器遷移如何與 Site Recovery 搭配運作？

- 如果您使用 Azure Migrate：伺服器遷移來執行內部部署 VMware Vm 的 *無代理* 程式遷移，則遷移是 Azure Migrate 的原生，且不會使用 Site Recovery。
- 如果您使用 Azure Migrate：伺服器遷移來執行 VMware Vm 的 *代理程式型* 遷移，或如果您遷移 hyper-v vm 或實體伺服器，Azure Migrate：伺服器遷移會使用 Azure Site Recovery 的複寫引擎。

## <a name="which-geographies-are-supported"></a>支援哪些地理位置？

請檢閱[公用](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府雲端](migrate-support-matrix.md#supported-geographies-azure-government)支援的地理位置。

## <a name="how-do-i-get-started"></a>如何開始？

識別您需要的工具，然後將工具新增至 Azure Migrate 專案。 

若要新增 ISV 工具或 Movere：

1. 根據工具原則取得授權，或註冊免費試用版，以開始使用。 這些工具會根據 ISV 或工具授權模型進行授權。
2. 每個工具中都會有連線至 Azure Migrate 的選項。 遵循工具指示與檔，將此工具與 Azure Migrate 連接。

您可以從 Azure Migrate 專案、跨 Azure 和其他工具中追蹤您的遷移旅程。

## <a name="how-do-i-delete-a-project"></a>如何? 刪除專案？

瞭解如何 [刪除專案](how-to-delete-project.md)。 

## <a name="next-steps"></a>後續步驟

閱讀 [Azure Migrate 總覽](migrate-services-overview.md)。
