---
title: 整合 Azure 自動化更新管理與 Microsoft Endpoint Configuration Manager
description: 本文說明如何使用更新管理來設定 Microsoft Endpoint Configuration Manager，以將軟體更新部署至管理員用戶端。
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 09c8ef818428157c7de8c3a87bcce8a7b80e62ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88245905"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>整合更新管理與 Microsoft Endpoint Configuration Manager

投資了 Microsoft Endpoint Configuration Manager 以管理電腦、伺服器和行動裝置的客戶，也需仰賴其管理軟體更新的強度和成熟度，作為軟體更新管理 (SUM) 週期的一部分。

您可以藉由在 Microsoft Endpoint Configuration Manager 中建立和預先預備軟體更新部署，並使用 [更新管理](update-mgmt-overview.md)來取得已完成的更新部署的詳細狀態，以報告和更新受管理的 Windows 伺服器。 如果您使用 Microsoft Endpoint Configuration Manager 進行更新合規性報告，但不是用來管理 Windows 伺服器的更新部署，您可以繼續向 Microsoft Endpoint Configuration Manager 報告，同時以 Azure 自動化更新管理管理安全性更新。

>[!NOTE]
>雖然更新管理支援 Windows Server 2008 R2 的更新評估和修補，但它不支援 Microsoft Endpoint Configuration Manager 執行此作業系統所管理的用戶端。

## <a name="prerequisites"></a>必要條件

* 您必須已將 [Azure 自動化更新管理](update-mgmt-overview.md)新增至您的自動化帳戶。
* 目前由您 Microsoft Endpoint Configuration Manager 環境管理的 Windows server 也需要向 Log Analytics 工作區報告也已啟用更新管理。
* 這項功能已在 Microsoft Endpoint Configuration Manager 最新分支1606版和更高版本中啟用。 若要整合您的 Microsoft Endpoint Configuration Manager 管理中心網站或獨立主要網站與 Azure 監視器記錄和匯入集合，請參閱 [連接 Configuration Manager 以 Azure 監視器記錄](../../azure-monitor/platform/collect-sccm.md)。  
* Windows 代理程式必須設定為與 Windows Server Update Services (WSUS) Server 進行通訊，或者如果沒有從 Microsoft Endpoint Configuration Manager 收到安全性更新，就可以存取 Microsoft Update。

您如何使用現有的 Microsoft Endpoint Configuration Manager 環境來管理 Azure IaaS 中裝載的用戶端，主要取決於您在 Azure 資料中心和基礎結構之間的連線。 此連接會影響您可能需要對 Microsoft Endpoint Configuration Manager 基礎結構進行的任何設計變更，以及支援這些必要變更的相關成本。 若要了解繼續之前需要評估哪些規劃考，請檢閱 [Azure 上的 Configuration Manager - 常見問題集](/configmgr/core/understand/configuration-manager-on-azure#networking)。

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>從 Microsoft Endpoint Configuration Manager 管理軟體更新

如果您要繼續從 Microsoft Endpoint Configuration Manager 管理更新部署，請執行下列步驟。 Azure 自動化連接到 Microsoft Endpoint Configuration Manager，以將更新套用至連線到您 Log Analytics 工作區的用戶端電腦。 您可以從用戶端電腦快取取得更新內容，就像是 Microsoft Endpoint Configuration Manager 管理部署一樣。

1. 使用 [部署軟體更新](/configmgr/sum/deploy-use/deploy-software-updates)中所述的程式，在 Microsoft Endpoint Configuration Manager 階層中的頂層網站建立軟體更新部署。 必須與標準部署不同的唯一設定為可控制部署封裝下載行為的選項 [不要安裝軟體更新]。 此行為是由更新管理在下一個步驟中建立排定的更新部署來進行管理。

2. 在 Azure 自動化中，選取 [更新管理]。 遵循[建立更新部署](update-mgmt-deploy-updates.md#schedule-an-update-deployment)中所述的步驟，然後在 [**類型**] 下拉式清單中選取 [匯**入的群組**]，以選取適當的 Microsoft Endpoint Configuration Manager 集合，以建立新的部署。 請記住下列重點：

    a. 如果在選取的 Microsoft Endpoint Configuration Manager 裝置集合上定義維護時段，則集合的成員會接受它，而不是排程部署中定義的 **持續時間** 設定。

    b. 目標集合的成員必須具有網際網路連線 (不論是直接、透過 Proxy 伺服器，還是透過 Log Analytics 閘道)。

透過 Azure 自動化完成更新部署之後，屬於所選電腦群組成員的目標電腦會在排定的時間，從其本機用戶端快取安裝更新。 您可以[檢視更新部署狀態](update-mgmt-deploy-updates.md#check-deployment-status)，以監視您的部署結果。

## <a name="manage-software-updates-from-azure-automation"></a>從 Azure 自動化管理軟體更新

若要管理 Microsoft Endpoint Configuration Manager 用戶端之 Windows Server Vm 的更新，您必須設定用戶端原則，以停用更新管理所管理之所有用戶端的軟體更新管理功能。 根據預設，用戶端設定是以階層中的所有裝置為目標。 如需有關此原則設定和設定方式的詳細資訊，請檢閱[如何在 Configuration Manager 中設定用戶端設定](/configmgr/core/clients/deploy/configure-client-settings)。

執行這項設定變更之後，您可以遵循[建立更新部署](update-mgmt-deploy-updates.md#schedule-an-update-deployment)中所述的步驟，然後選取 [**類型**] 下拉式清單中的 [匯**入的群組**]，選取適當的 Microsoft Endpoint Configuration Manager 集合，以建立新的部署。

## <a name="next-steps"></a>後續步驟

若要設定整合排程，請參閱[排程更新部署](update-mgmt-deploy-updates.md#schedule-an-update-deployment)。
