---
title: Azure 管理概觀 - Azure 控管
description: 管理 Azure 應用程式與資源的區域概觀，並提供 Azure 管理工具內容的連結。
ms.date: 09/09/2020
ms.topic: overview
ms.openlocfilehash: 8ffb37cd1db5740ed022b52cd9687bb3db1632bb
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949491"
---
# <a name="what-are-the-azure-management-areas"></a>什麼是 Azure 管理區域？

在 Azure 中治理是 Azure 管理的其中一個層面。 本文說明在 Azure 中部署及維護資源的各種管理領域。

管理是指維護商務應用程式與支援商務應用程式的資源所需的工作和流程。 Azure 有許多可搭配運作以提供完整管理能力的服務和工具。 這些服務不僅適用於 Azure 中的資源，也可在其他雲端和內部部署使用。 了解各種工具及其搭配運作的方式，是設計完整管理環境的首要步驟。

下圖說明維護應用程式或資源所需的不同管理範圍。 您可以將這些不同的區域視為生命週期。 在資源的存留期間內，各個領域必須接續進行。 此一資源生命週期始於最初的部署，在經歷持續的作業後，因遭到淘汰而告終。

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Azure 中管理專業領域的圖表。" border="false":::
   此圖顯示在支援 Azure 中管理和治理的服務轉輪中有以下元素：遷移、安全、保護、監視、設定及治理。 安全的子項目有安全性管理和威脅防護。 保護的子項目有備份和災害復原。 監視器的子項目有應用程式、基礎和網路監視，以及 Log Analytics 和診斷。 設定的子項目有設定、更新管理、自動化和指令碼。 而治理的子項目有原則管理和成本管理。
:::image-end:::

沒有單一 Azure 服務能完全符合特定管理領域的需求。 必須在多項服務的搭配運作下，才能符合這些需求。 某些服務可提供針對 Web 應用程式進行監視的功能，例如 Application Insights。 有些服務則可儲存其他服務的管理資料，例如 Azure 監視器記錄。 這項功能可讓您對不同服務所收集的不同資料類型進行分析。

下列各節簡短描述不同的管理範圍，並針對處理管理範圍，提供主要 Azure 服務詳細內容的連結。

## <a name="monitor"></a>監視

監視係指經由收集和分析資料來稽核資源的效能、健康情況和可用性的行為。 有效的監視策略可協助您了解您元件的運作情形，並透過通知增加您的有效運作時間。 請經由[監視 Azure 應用程式和資源](../azure-monitor/overview.md)參閱監視的概觀，以了解所使用的各種服務。

## <a name="configure"></a>設定

設定是指資源的初始部署和設定，以及持續進行的維護。
您可以將這些工作自動化，以免於重複進行各項工作、將投注的時間及精力降至最低，並增加正確性和效率。 [Azure 自動化](../automation/automation-intro.md)提供自動化設定工作的主要服務。 Runbook 可處理程序的自動化，而組態和更新管理則有助於您管理組態。

## <a name="govern"></a>治理

治理提供多項機制和流程，以便維持控制 Azure 中的應用程式與資源。 它同時也涉及規劃您的新措施，以及設定策略層級的優先順序。
在 Azure 中的治理主要是透過兩個服務進行實作。 [Azure 原則](./policy/overview.md)可讓您建立、指派和管理原則定義，以強制執行資源的規則。
此功能可確保這些資源能夠符合公司的標準規範。
針對您的 Azure 資源和其他雲端服務提供者，[Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)可讓您追蹤雲端使用量和費用。

## <a name="secure"></a>安全

管理資源和資料的安全性。 安全性計畫包括評估威脅、收集和分析資料，以及確保應用程式和資源的合規性。 安全性監視和威脅分析由 [Azure 資訊安全中心](../security-center/security-center-introduction.md)所提供，其中包含跨混合式雲端工作負載的整合式安全性管理和進階威脅保護。 請參閱 [Azure 安全性簡介](../security/fundamentals/overview.md)，以取得與保護 Azure 資源有關的完整資訊和指導方針。

## <a name="protect"></a>Protect

保護是指即使發生了您無法控制的中斷狀況，也能確保應用程式和資料的可用性。 Azure 中的保護是由兩個服務提供。 [Azure 備份](../backup/backup-overview.md)提供雲端或內部部署中資料的備份和復原。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 可在災害發生時提供商務持續性和立即復原的能力。

## <a name="migrate"></a>遷移

移轉是指將目前正在內部部署環境中執行的工作負載轉換至 Azure 雲端。
[Azure Migrate](../migrate/migrate-services-overview.md) 是一項服務，可協助您評估將內部部署虛擬機器移轉至 Azure 的移轉適用性。 Azure Site Recovery 可[從內部部署](../site-recovery/migrate-tutorial-on-premises-azure.md)或[從 Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) 移轉虛擬機器。 [Azure 資料庫移轉](../dms/dms-overview.md)可協助您將資料庫來源移轉至 Azure 資料平台。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 治理，請參閱下列文章：

- 請參閱 [Azure 治理中樞](./index.yml)。
- 請參閱[在適用於 Azure 的雲端採用架構中治理](/azure/cloud-adoption-framework/govern/)