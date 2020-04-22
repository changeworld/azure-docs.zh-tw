---
title: Azure 自動化概觀
description: 了解如何使用 Azure 自動化，將基礎結構和應用程式的生命週期自動化。
services: automation
ms.subservice: process-automation
keywords: azure 自動化, DSC, powershell, 狀態組態, 更新管理, 變更追蹤, DSC, 清查, Runbook, python, 圖形
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010234"
---
# <a name="an-introduction-to-azure-automation"></a>Azure 自動化簡介

本文提供 Azure 自動化的簡短概觀，並回答一些常見問題。 如需不同功能的詳細資訊，請瀏覽本概觀的所有連結。

## <a name="about-azure-automation"></a>關於 Azure 自動化

Azure 自動化會提供以雲端為基礎的自動化和設定服務，在您的 Azure 和非 Azure 環境之間支援一致的管理； 其中包含流程自動化、組態管理、更新管理、共用功能及異質功能。 自動化會在部署、作業和解除委任工作負載與資源期間，提供完整的控制權。

![自動化功能](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>程序自動化

Azure 自動化中的流程自動化能讓您將頻繁、費時且容易產生錯誤的雲端管理工作自動化。 這項服務有助於您專注在可增加商務價值的工作。 減少錯誤並提高效率，也有助於降低營運成本。 流程自動化作業環境會在 [Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)中詳述。

流程自動化可將 Azure 服務與部署、設定及管理您端對端程序所需的其他公用系統進行整合。 服務可讓您使用 PowerShell 或 Python，以圖形方式撰寫 [Runbook](automation-runbook-types.md)。 您可以使用[混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)，在內部部署環境之間協調，從而整合管理。 [Webhook](automation-webhooks.md) 可讓您透過從 ITSM、DevOps 和監視系統觸發自動化來滿足要求，並確保持續傳遞和作業。 

## <a name="configuration-management"></a>設定管理

Azure 自動化[狀態組態](automation-dsc-overview.md) 是適用於 PowerShell 預期ˊ狀態組態 (DSC) 的以雲端為基礎架構解決方案，可提供企業環境所需的服務。 使用此功能，您可以在 Azure 自動化中管理 DSC 資源，並將組態從 Azure 雲端中的 DSC 提取伺服器套用至虛擬或實體機器。 您可以在位於雲端或內部部署的 Windows 或 Linux 實體及虛擬機器上，監視及自動更新機器設定。 清查支援可讓您查詢客體內的資源，看見已安裝的應用程式和其他設定項目。
 
Azure 自動化狀態組態服務提供豐富的報告和搜尋功能。 您可以使用這些功能來尋找有關作業系統內所設定內容的詳細資訊。 此服務支援跨服務、精靈、軟體、登錄和您環境中的檔案變更追蹤，以協助您診斷不必要的變更並引發警示。 重要的相關功能是主要事件的報告；例如，當節點偏離其指派的組態時所發出的事件。 

## <a name="update-management"></a>更新管理

Azure 自動化包括跨混合式環境中適用於 Windows 和 Linux 系統的[更新管理](automation-update-management.md)解決方案。 使用此解決方案，您可以看見跨 Azure 和其他雲端，以及內部部署間的更新相容性。 更新管理可讓您建立排程部署，在定義的維護時間內協調更新安裝。 如果不應該在機器上安裝更新，您可以使用更新管理功能將其從部署中排除。

## <a name="shared-capabilities"></a>共用功能

Azure 自動化提供一些共用的功能，包括共用的資源、角色型存取控制、彈性排程、原始檔控制整合、稽核及標記。

### <a name="shared-resources"></a><a name="shared-resources"></a>共用資源

Azure 自動化包含一組共用資源，讓您能更輕鬆地自動化，並大量設定您的環境。

* **[排程](automation-schedules.md)** - 在預先定義的時間觸發自動化作業。
* **[模組](automation-integration-modules.md)** - 管理 Azure 和其他系統。 您可以將模組匯入適用於 Microsoft、第三方、社群或自訂定義 Cmdlet 和 DSC 資源的自動化帳戶中。
* **[模組資源庫](automation-runbook-gallery.md)** - 支援原生整合至 PowerShell 資源庫，以檢視 Runbook 並將其匯入自動化帳戶。 資源庫可讓您快速開始從 PowerShell 資源庫和 Microsoft 指令碼中心整合及撰寫您的流程。
* **[Python 2 套件](python-packages.md)** -支援自動化帳戶的 Python 2 Runbook。
* **[認證](automation-credentials.md)** - 安全地儲存可供 Runbook 和組態在執行階段使用的敏感性資訊。
* **[連線](automation-connections.md)** - 存放系統連線的一般資訊名稱/值配對。 連線是由模組建立者所定義，可在執行階段中用於 Runbook 和組態。
* **[憑證](automation-certificates.md)** - 定義資訊，以在執行階段由 Runbook 或 DSC 組態存取時，用於驗證和保護已部署的資源。 
* **[變數](automation-variables.md)** - 保存可在 Runbook 和組態之間使用的內容。 您可以變更變數值，而不必修改任何參考這些變數值的 Runbook 及組態。

### <a name="role-based-access-control"></a>角色型存取控制

Azure 自動化支援角色型存取控制 (RBAC)，以控制自動化帳戶和其資源的存取權。 若要深入了解如何在您的自動化帳戶、Runbook 和作業上設定 RBAC，請參閱 [Azure 自動化的角色型存取控制](automation-role-based-access-control.md)。

### <a name="source-control-integration"></a>原始檔控制整合

Azure 自動化允許[原始檔控制整合](source-control-integration.md)。 此功能將組態升階為程式碼，Runbook 或設定可在其中簽入原始檔控制系統。

## <a name="heterogeneous-support-windows-and-linux"></a>異質支援 (Windows 和 Linux)

自動化旨在讓您可跨混合式雲端環境運作，並適用於 Windows 和 Linux 系統； 其會提供一致的方式，將部署的工作負載和其所執行的作業系統進行自動化及設定。

## <a name="common-scenarios-for-automation"></a>自動化的常見情節

Azure 自動化可支援透過基礎結構和應用程式的生命週期進行管理。 常見案例包括：

* **撰寫 Runbook** - 以 PowerShell、PowerShell 工作流程、圖形化、Python 2 和 DSC Runbook 等通用語言編寫。 
* **建置和部署資源** - 使用 Runbook 和 Azure Resource Manager 範本，跨混合式環境部署虛擬機器。 整合至諸如 Jenkins 和 Azure DevOps 等開發工具。
* **設定 VM** - 使用基礎結構和應用程式的組態，評估及設定 Windows 和 Linux 電腦。
* **共用知識** - 將組織傳遞和維護工作負載方式的知識傳送到系統。 
* **擷取清查** - 取得已部署資源的完整清查，以了解目標、報告與相容性。 
* **尋找變更** - 識別可能造成設定不正確的變更，並且改善作業相容性。
* **監視** - 隔離造成問題的機器變更，並加以修復或向管理系統呈報。
* **保護** - 在引發安全性警示時隔離機器。 設定客體需求。
* **管理** - 設定小組的 RBAC。 復原未使用的資源。

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>自動化的定價

您可以在[定價](https://azure.microsoft.com/pricing/details/automation/)頁面上檢閱 Azure 自動化的相關價格。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立自動化帳戶](automation-quickstart-create-account.md)

