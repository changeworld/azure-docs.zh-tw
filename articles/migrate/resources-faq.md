---
title: Azure Migrate 常見問題
description: 取得有關 Azure Migrate 服務常見問題的解答。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926720"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常見的問題

本文會回答有關 Azure Migrate 的常見問題。 如果您在閱讀本文後有任何問題，可以在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)中張貼。 您也可以參閱下列文章：

- [Azure Migrate 設備](common-questions-appliance.md)的相關問題
- [探索、評估和相依性視覺效果](common-questions-discovery-assessment.md)的相關問題

## <a name="what-is-azure-migrate"></a>什麼是 Azure Migrate？

Azure Migrate 提供中央中樞來追蹤內部部署應用程式和工作負載的探索、評估和遷移，以及私人和公用雲端 Vm 至 Azure 的能力。 中樞提供評估和遷移的 Azure Migrate 工具，以及協力廠商 ISV 供應專案。 [詳細資訊](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate 可以怎麼做？

使用 Azure Migrate 來探索、評估內部部署的基礎結構、應用程式和資料，並將其遷移至 Azure。 Azure Migrate 支援評估和遷移內部部署 VMware Vm、Hyper-v Vm、實體伺服器、其他虛擬化 Vm、資料庫、web 應用程式和虛擬桌面。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 之間有何差異？

[Azure Migrate](migrate-services-overview.md)提供集中式的中樞，供您評估和遷移至 Azure。 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md)是一種嚴重損壞修復解決方案。 

Azure Migrate：伺服器遷移工具會使用一些後端 Site Recovery 功能，以進行部分內部部署機器的隨即轉移。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate 之間的差異為何：伺服器評估和地圖工具組？

伺服器評估提供評估以協助進行遷移，以及評估要遷移至 Azure 的工作負載。 [Microsoft 評估及規劃（MAP）工具](https://www.microsoft.com/download/details.aspx?id=7826)組可協助進行其他工作，包括較新版本的 Windows 用戶端和伺服器作業系統的遷移規劃，以及軟體使用方式追蹤。 在這些情況下，請繼續使用地圖工具組。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>伺服器評估與 Site Recovery 部署規劃工具之間有何差異？

伺服器評估是一種遷移計畫工具。 Site Recovery 部署規劃工具是一種嚴重損壞修復規劃工具。

根據您想要執行的動作來選擇您的工具：

- **規劃將內部部署遷移至 azure**：如果您想要將內部部署伺服器遷移至 azure，請使用伺服器評估來進行遷移計畫。 伺服器評量會評估內部部署工作負載，並提供指引和工具來協助您進行遷移。 準備好遷移計畫之後，您可以使用 Azure Migrate： Server 遷移之類的工具，將機器遷移至 Azure。
- **規劃 Azure**的嚴重損壞修復：如果您想要使用 Site Recovery 來設定從內部部署環境到 Azure 的嚴重損壞修復，請使用 Site Recovery 部署規劃工具。 針對嚴重損壞修復的目的，部署規劃工具提供內部部署環境的深度、Site Recovery 特定評估。 它提供嚴重損壞修復的相關建議，例如複寫和容錯移轉。

## <a name="how-does-server-migration-work-with-site-recovery"></a>伺服器遷移如何與 Site Recovery 搭配運作？

- 如果您使用 Azure Migrate：伺服器遷移來執行內部部署 VMware Vm 的*無代理*程式遷移，則遷移是 Azure Migrate 的原生，而且不會使用 Site Recovery。
- 如果您使用 Azure Migrate：伺服器遷移來執行 VMware Vm 的*代理程式型*遷移，或如果您遷移 hyper-v vm 或實體伺服器，Azure Migrate：伺服器遷移會使用 Azure Site Recovery 的複寫引擎。

## <a name="which-geographies-are-supported"></a>支援哪些地理位置？

- **Vmware vm**：請參閱 Azure Migrate vmware vm[支援的地理](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)位置。
- **Hyper-v vm**：請參閱 Azure Migrate 支援的 hyper-v vm[地理](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)位置。

## <a name="how-do-i-get-started"></a>如何開始使用？

識別您需要的工具，然後將工具加入至 Azure Migrate 專案。 

若要新增 ISV 工具或 Movere：

1. 根據工具原則，取得授權或註冊免費試用版，以開始使用。 這些工具會根據 ISV 或工具授權模型進行授權。
2. 每個工具中都會有連線至 Azure Migrate 的選項。 請遵循工具指示和檔，將工具連接到 Azure Migrate。

您可以從 Azure Migrate 專案、跨 Azure 和其他工具追蹤您的遷移旅程。

## <a name="how-do-i-delete-a-project"></a>如何? 刪除專案嗎？

瞭解如何[刪除專案](how-to-delete-project.md)。 

## <a name="next-steps"></a>後續步驟

閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。
