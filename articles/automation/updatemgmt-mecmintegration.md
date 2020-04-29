---
title: 搭配使用 Azure 自動化更新管理與 Configuration Manager 用戶端
description: 本文旨在協助您使用此解決方案來設定 Microsoft Endpoint Configuration Manager，以將軟體更新部署至 ConfigMgr 用戶端。
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 32a077c476d9669c3f32bd4040fdc8ff90156c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678738"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>使用更新管理將更新部署至 Microsoft Endpoint Configuration Manager 用戶端

已投資 Microsoft Endpoint Configuration Manager 來管理電腦、伺服器和行動裝置的客戶，也會依賴其在軟體更新管理（SUM）週期中管理軟體更新的強度和成熟度。

您可以藉由在 Configuration Manager 中建立和預先執行軟體更新部署，以及使用[更新管理](automation-update-management.md)取得已完成之更新部署的詳細狀態，來報告和更新受管理的 Windows 伺服器。 如果您使用 Configuration Manager 進行更新相容性報告，而不是用來管理 Windows 伺服器的更新部署，您可以繼續向 Configuration Manager 報告，同時使用更新管理解決方案來管理安全性更新。

## <a name="prerequisites"></a>先決條件

* 您必須已將[更新管理解決方案](automation-update-management.md)加入至您的自動化帳戶。
* 目前由您 Configuration Manager 環境管理的 Windows 伺服器，也需要向 Log Analytics 工作區報告也已啟用更新管理解決方案。
* 這項功能已在 Configuration Manager 最新分支1606版和更高版本中啟用。 若要將您的 Configuration Manager 管理中心網站或獨立主要網站與 Azure 監視器記錄和匯入集合整合，請參閱[將 Configuration Manager 連接到 Azure 監視器記錄](../azure-monitor/platform/collect-sccm.md)。  
* Windows 代理程式如果未從 Configuration Manager 收到任何安全性更新，則必須設定為可與 Windows Server Update Services (WSUS) 伺服器通訊，或必須能夠存取 Microsoft Update。   

您使用現有的 Configuration Manager 環境管理裝載在 Azure IaaS 中用戶端的方式，主要取決於在 Azure 資料中心與您的基礎結構之間具有的連線。 此連線會影響您可能需要對 Configuration Manager 基礎結構進行的任何設計變更，與支援這些必要變更的相關成本。 若要了解繼續之前需要評估哪些規劃考，請檢閱 [Azure 上的 Configuration Manager - 常見問題集](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking)。

## <a name="configuration"></a>設定

### <a name="manage-software-updates-from-configuration-manager"></a>從 Configuration Manager 管理軟體更新 

如果您要繼續從 Configuration Manager 管理更新部署，請執行下列步驟。 Azure 自動化會連線至 Configuration Manager，將更新套用至連線到您 Log Analytics 工作區的用戶端電腦。 更新內容可從用戶端電腦快取取得，就如同部署受到 Configuration Manager 管理一般。

1. 使用[部署軟體更新](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)中所述的程式，從您 Configuration Manager 階層中的頂層網站建立軟體更新部署。 必須與標準部署不同的唯一設定為可控制部署封裝下載行為的選項 [不要安裝軟體更新]****。 此行為是由更新管理解決方案透過在下一個步驟中建立排定的更新部署來管理。

1. 在 Azure 自動化中，選取 [更新管理]****。 遵循[建立更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)中所述的步驟來建立新的部署，然後選取 [**類型**] 下拉式清單上的 [匯**入的群組**]，以選取適當的 Configuration Manager 集合。 請記住下列重點：a. 如果已在所選的 Configuration Manager 裝置集合上定義維護時段，該集合的成員會接受它，而不是排程部署中定義的 [持續時間]**** 設定。
    b. 目標集合的成員必須具有網際網路連線 (不論是直接、透過 Proxy 伺服器，還是透過 Log Analytics 閘道)。

透過 Azure 自動化完成更新部署之後，屬於所選電腦群組成員的目標電腦會在排定的時間，從其本機用戶端快取安裝更新。 您可以[檢視更新部署狀態](automation-tutorial-update-management.md#view-results-of-an-update-deployment)，以監視您的部署結果。

### <a name="manage-software-updates-from-azure-automation"></a>從 Azure 自動化管理軟體更新

若要為身為 Configuration Manager 用戶端的 Windows Server VM 管理更新，您必須設定用戶端原則，為此解決方案所管理的所有用戶端停用軟體更新管理功能。 根據預設，用戶端設定是以階層中的所有裝置為目標。 如需有關此原則設定和如何設定的詳細資訊，請參閱[如何在 Configuration Manager 中設定用戶端設定](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)。

在執行此設定變更之後，您可以遵循[建立更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)中所述的步驟來建立新的部署，然後在 [類型]**** 下拉式清單中選取 [已匯入的群組]****，以選取適當的 Configuration Manager 集合。

## <a name="next-steps"></a>後續步驟

