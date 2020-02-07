---
title: 有關 Azure Migrate 的常見問題
description: 取得有關 Azure Migrate 服務常見問題的解答。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: baf01c0a0d5c6154305f7137c24deb0365b5e812
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062093"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常見的問題

本文會回答有關 Azure Migrate 的常見問題。 如果您在閱讀本文後有進一步的查詢，請將其張貼在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)。 如果您有其他問題，請參閱下列文章：

- Azure Migrate 設備的相關[問題](common-questions-appliance.md)。
- 探索、評估和相依性視覺效果的相關[問題](common-questions-discovery-assessment.md)。


## <a name="what-is-azure-migrate"></a>什麼是 Azure Migrate？

Azure Migrate 提供中央中樞，以追蹤內部部署應用程式和工作負載的探索、評估和遷移，以及私人/公用雲端 Vm 至 Azure。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。 [詳細資訊](migrate-services-overview.md)。


## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate 可以怎麼做？

使用 Azure Migrate 來探索、評估內部部署的基礎結構、應用程式和資料，並將其遷移至 Azure。 Azure Migrate 支援評估和遷移內部部署 VMware Vm、Hyper-v Vm、實體伺服器、其他虛擬化 Vm、資料庫、web 應用程式和虛擬桌面。 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate 和 Site Recovery 之間有何差異？

Azure Migrate 提供集中式的中樞，供您評估和遷移至 Azure。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)是一種嚴重損壞修復解決方案。 Azure Migrate：伺服器遷移工具會使用一些後端 Site Recovery 功能，以進行部分內部部署機器的隨即轉移。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server 評估與地圖工具組之間有何差異？

伺服器評估提供評估以協助進行遷移，以及評估要遷移至 Azure 的工作負載。 [Microsoft 評估及規劃（MAP）工具](https://www.microsoft.com/download/details.aspx?id=7826)組可協助進行其他工作，包括較新版本的 Windows 用戶端/伺服器作業系統的遷移規劃，以及軟體使用方式追蹤。 在這些情況下，請繼續使用地圖工具組。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>伺服器評估與 Site Recovery 部署規劃工具之間有何差異？

伺服器評估是一種遷移計畫工具。 Site Recovery 部署規劃工具是一種嚴重損壞修復規劃工具。

- **規劃將內部部署遷移至 azure**：如果您想要將內部部署伺服器遷移至 azure，請使用伺服器評估來進行遷移計畫。 它會評估內部部署工作負載，並提供指引和工具來協助您進行遷移。 準備好遷移計畫之後，您可以使用工具（包括 Azure Migrate 伺服器遷移），將機器遷移至 Azure。
- **規劃 Azure**的嚴重損壞修復：如果您想要使用 Site Recovery 來設定從內部部署環境到 Azure 的嚴重損壞修復，請使用 Site Recovery 部署規劃工具。 針對嚴重損壞修復的目的，部署規劃工具提供內部部署環境的深度、Site Recovery 特定評估。 它會提供有關嚴重損壞修復的建議，例如複寫和容錯移轉。

## <a name="how-does-server-migration-work-with-site-recovery"></a>伺服器遷移如何與 Site Recovery 搭配運作？

- 如果您使用 Azure Migrate：伺服器遷移來執行內部部署 VMware Vm 的無代理程式遷移，則遷移是 Azure Migrate 的原生，而且 Site Recovery 不會使用。
- 如果您使用 Azure Migrate：伺服器遷移來執行 VMware Vm 的代理程式型遷移，或遷移 Hyper-v Vm 或實體伺服器，則 Azure Migrate 伺服器遷移會使用 Azure Site Recovery 的複寫引擎。


## <a name="which-geographies-are-supported"></a>支援哪些地理位置？

查看適用于[VMWARE VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)和[hyper-v vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v)的 Azure Migrate 支援地理位置。

## <a name="how-do-i-get-started"></a>如何開始使用？

您需識別自己所需的工具，並將它新增至 Azure Migrate 專案。 如果您要新增 ISV 工具或 Movere：
- 根據工具原則來取得授權，或註冊免費試用來開始著手。 這些工具會根據 ISV 或工具授權模型進行授權。
- 每個工具中都會有連線至 Azure Migrate 的選項。 遵循工具指示與文件來搭配 Azure Migrate 連線至該工具。
您可以跨越 Azure 和其他工具，從 Azure Migrate 專案內集中追蹤您的移轉旅途。

## <a name="how-do-i-delete-a-project"></a>如何? 刪除專案嗎？

[瞭解如何](how-to-delete-project.md)刪除專案。 




## <a name="next-steps"></a>後續步驟
閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。
