---
title: 整合 Azure 自動化更新管理與 Windows Endpoint Configuration Manager
description: 本文說明如何使用更新管理來設定 Microsoft Endpoint Configuration Manager，以將軟體更新部署至管理員用戶端。
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: ae8c4f09c0133dde7b0a73b7c2fcd0a28aa22ae3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84013192"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>整合更新管理與 Windows Endpoint Configuration Manager

投資了 Microsoft Endpoint Configuration Manager 以管理電腦、伺服器和行動裝置的客戶，也需仰賴其管理軟體更新的強度和成熟度，作為軟體更新管理 (SUM) 週期的一部分。

您可以透過在 Windows Endpoint Configuration Manager 中建置並預先暫存軟體更新部署，並使用[更新管理](automation-update-management.md)取得所完成更新部署的詳細狀態，藉此報告及更新受控 Windows 伺服器。 如果您將 Windows Endpoint Configuration Manager 用於更新合規性報告，而不是用於管理 Windows 伺服器的更新部署，您可以繼續向 Configuration Manager 報告，同時安全性更新則是使用 Azure 自動化更新管理來進行管理。

## <a name="prerequisites"></a>必要條件

* 您必須已將 [Azure 自動化更新管理](automation-update-management.md)新增至您的自動化帳戶。
* 目前由 Windows Endpoint Configuration Manager 環境所管理的 Windows 伺服器，也需要向也已啟用更新管理的 Log Analytics 工作區報告。
* Windows Endpoint Configuration Manager 目前分支，1606 版和更高版本已啟用此功能。 若要將 Windows Endpoint Configuration Manager 中央管理網站或獨立主要站台整合與 Azure 監視器記錄整合並匯入集合，請檢閱[將 Configuration Manager 連線至 Azure 監視器記錄](../azure-monitor/platform/collect-sccm.md)。  
* Windows 代理程式如果未從 Windows Endpoint Configuration Manager 收到任何安全性更新，則必須設定為可與 Windows Server Update Services (WSUS) 伺服器通訊，或必須能夠存取 Microsoft Update。

您使用現有的 Windows Endpoint Configuration Manager 環境管理裝載在 Azure IaaS 中用戶端的方式，主要取決於在 Azure 資料中心與您的基礎結構之間具有的連線。 此連線會影響您可能需要對 Windows Endpoint Configuration Manager 基礎結構進行的任何設計變更，與支援這些必要變更的相關成本。 若要了解繼續之前需要評估哪些規劃考，請檢閱 [Azure 上的 Configuration Manager - 常見問題集](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking)。

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>從 Windows Endpoint Configuration Manager 管理軟體更新

如果您要繼續從 Windows Endpoint Configuration Manager 管理更新部署，請執行下列步驟。 Azure 自動化會連線至 Windows Endpoint Configuration Manager，將更新套用至連線到您 Log Analytics 工作區的用戶端電腦。 更新內容可從用戶端電腦快取取得，就如同部署受到 Windows Endpoint Configuration Manager 管理一般。

1. 使用[部署軟體更新](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)中描述的程序，從 Windows Endpoint Configuration Manager 階層中的頂層站台建立軟體更新部署。 必須與標準部署不同的唯一設定為可控制部署封裝下載行為的選項 [不要安裝軟體更新]。 此行為是由更新管理在下一個步驟中建立排定的更新部署來進行管理。

1. 在 Azure 自動化中，選取 [更新管理]。 遵循[建立更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)中所述的步驟來建立新的部署，然後在 [類型] 下拉式清單中選取 [已匯入的群組]，以選取適當的 Windows Endpoint Configuration Manager 集合。 請記住下列重點：a. 如果已在所選的 Windows Endpoint Configuration Manager 裝置集合上定義維護時段，該集合的成員會接受，而不是排程部署中定義的 [持續時間] 設定。
    b. 目標集合的成員必須具有網際網路連線 (不論是直接、透過 Proxy 伺服器，還是透過 Log Analytics 閘道)。

透過 Azure 自動化完成更新部署之後，屬於所選電腦群組成員的目標電腦會在排定的時間，從其本機用戶端快取安裝更新。 您可以[檢視更新部署狀態](automation-tutorial-update-management.md#check-deployment-status)，以監視您的部署結果。

## <a name="manage-software-updates-from-azure-automation"></a>從 Azure 自動化管理軟體更新

若要為身為 Windows Endpoint Configuration Manager 用戶端的 Windows Server VM 管理更新，您必須設定用戶端原則，為更新管理所管理的所有用戶端停用軟體更新管理功能。 根據預設，用戶端設定是以階層中的所有裝置為目標。 如需有關此原則設定和設定方式的詳細資訊，請檢閱[如何在 Configuration Manager 中設定用戶端設定](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)。

在執行此設定變更之後，您可以遵循[建立更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)中所述的步驟來建立新的部署，然後在 [類型] 下拉式清單中選取 [已匯入的群組]，以選取適當的 Windows Endpoint Configuration Manager 集合。

## <a name="next-steps"></a>後續步驟

若要設定整合排程，請參閱[排程更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)。