---
title: 使用 Azure 自動化更新管理與設定管理員客戶端
description: 本文旨在説明您使用此解決方案配置 Microsoft 終結點設定管理員,以便將軟體更新部署到 ConfigMgr 用戶端。
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 32a077c476d9669c3f32bd4040fdc8ff90156c19
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678738"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>使用更新管理將更新部署到 Microsoft 終結點設定管理員客戶端

投資 Microsoft 端點配置管理員來管理 PC、伺服器和行動裝置的客戶還依靠其在管理軟體更新方面的優勢和成熟度,作為其軟體更新管理 (SUM) 週期的一部分。

您可以透過在設定管理員 中建立和預暫存軟體更新部署來報告和更新託管的 Windows 伺服器,並使用[更新管理](automation-update-management.md)獲取已完成的更新部署的詳細狀態。 如果使用配置管理員 進行更新合規性報告,但不適用於使用 Windows 伺服器管理更新部署,則可以繼續向配置管理器報告,同時使用更新管理解決方案管理安全更新。

## <a name="prerequisites"></a>Prerequisites

* 您必須已將[更新管理解決方案](automation-update-management.md)加入至您的自動化帳戶。
* 當前由配置管理員環境管理的 Windows 伺服器還需要向日誌分析工作區報告,該工作區還啟用了更新管理解決方案。
* 此功能在配置管理器當前分支版本 1606 及更高版本中啟用。 要將設定管理員管理中心管理站台或獨立主站台與 Azure 監視器紀錄和匯入集合整合,請檢視[將設定管理員連接到 Azure 監視器日誌](../azure-monitor/platform/collect-sccm.md)。  
* Windows 代理程式如果未從 Configuration Manager 收到任何安全性更新，則必須設定為可與 Windows Server Update Services (WSUS) 伺服器通訊，或必須能夠存取 Microsoft Update。   

您使用現有的 Configuration Manager 環境管理裝載在 Azure IaaS 中用戶端的方式，主要取決於在 Azure 資料中心與您的基礎結構之間具有的連線。 此連線會影響您可能需要對 Configuration Manager 基礎結構進行的任何設計變更，與支援這些必要變更的相關成本。 若要了解繼續之前需要評估哪些規劃考，請檢閱 [Azure 上的 Configuration Manager - 常見問題集](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking)。

## <a name="configuration"></a>設定

### <a name="manage-software-updates-from-configuration-manager"></a>從 Configuration Manager 管理軟體更新 

如果您要繼續從 Configuration Manager 管理更新部署，請執行下列步驟。 Azure 自動化會連線至 Configuration Manager，將更新套用至連線到您 Log Analytics 工作區的用戶端電腦。 更新內容可從用戶端電腦快取取得，就如同部署受到 Configuration Manager 管理一般。

1. 使用[部署軟體更新](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)中描述的過程,從配置管理器 層次結構中的頂級網站創建軟體更新部署。 必須與標準部署不同的唯一設定為可控制部署封裝下載行為的選項 [不要安裝軟體更新]****。 此行為是由更新管理解決方案透過在下一個步驟中建立排定的更新部署來管理。

1. 在 Azure 自動化中，選取 [更新管理]****。 按照[「創建更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)」中所述步驟創建新部署,並在 **「類型」** 下拉下下下選擇 **「導入」組**以選擇相應的設定管理器集合。 請記住下列重點：a. 如果已在所選的 Configuration Manager 裝置集合上定義維護時段，該集合的成員會接受它，而不是排程部署中定義的 [持續時間]**** 設定。
    b. 目標集合的成員必須具有網際網路連線 (不論是直接、透過 Proxy 伺服器，還是透過 Log Analytics 閘道)。

透過 Azure 自動化完成更新部署之後，屬於所選電腦群組成員的目標電腦會在排定的時間，從其本機用戶端快取安裝更新。 您可以[檢視更新部署狀態](automation-tutorial-update-management.md#view-results-of-an-update-deployment)，以監視您的部署結果。

### <a name="manage-software-updates-from-azure-automation"></a>從 Azure 自動化管理軟體更新

若要為身為 Configuration Manager 用戶端的 Windows Server VM 管理更新，您必須設定用戶端原則，為此解決方案所管理的所有用戶端停用軟體更新管理功能。 根據預設，用戶端設定是以階層中的所有裝置為目標。 關於此政策設定以及如何設定它的詳細資訊,請檢視[如何在設定管理員 中設定客戶端設定](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)。

在執行此設定變更之後，您可以遵循[建立更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)中所述的步驟來建立新的部署，然後在 [類型]**** 下拉式清單中選取 [已匯入的群組]****，以選取適當的 Configuration Manager 集合。

## <a name="next-steps"></a>後續步驟

