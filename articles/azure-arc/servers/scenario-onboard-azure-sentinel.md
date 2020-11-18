---
title: 將 Azure Arc 啟用的伺服器上架到 Azure Sentinel
description: 瞭解如何將已啟用 Azure Arc 的伺服器新增至 Azure Sentinel，並主動監視其安全性狀態。
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 60018e710613a27bcb5c00f38ee90be6ff46c61d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810993"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>將 Azure Arc 啟用的伺服器上架到 Azure Sentinel

本文旨在協助您將 Azure Arc 啟用的伺服器上架，以 [Azure Sentinel](../../sentinel/overview.md) 並開始收集安全性相關事件。 Azure Sentinel 提供單一解決方案，可在整個企業中偵測警示、威脅可見度、主動式搜尋和威脅回應。

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您已符合下列需求：

- [Log Analytics 工作區](../../azure-monitor/platform/data-platform-logs.md)。 如需 Log Analytics 工作區的詳細資訊，請參閱[設計您的 Azure 監視器記錄部署](../../azure-monitor/platform/design-logs-deployment.md)。

- Azure Sentinel [在您的訂用帳戶中啟用](../../sentinel/quickstart-onboard.md)。

- 您是電腦或伺服器已連線到已啟用 Azure Arc 的伺服器。

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>將 Azure Arc 啟用的伺服器上架到 Azure Sentinel

Azure Sentinel 隨附許多適用于 Microsoft 解決方案的連接器，現成可用且提供即時整合。 針對實體和虛擬機器，您可以安裝 Log Analytics 代理程式，該代理程式會收集記錄然後轉送至 Azure Sentinel。 啟用 Arc 的伺服器支援使用下列方法部署 Log Analytics 代理程式：

- 使用 VM 延伸模組架構。

    Azure Arc 啟用的伺服器中的這項功能可讓您將 Log Analytics 代理程式 VM 擴充功能部署至非 Azure Windows 和/或 Linux 伺服器。 您可以在混合式電腦上使用下列方法來管理 VM 擴充功能，或在已啟用 Arc 的伺服器上管理伺服器：

    - [Azure 入口網站](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager 範本](manage-vm-extensions-template.md)

- 使用 Azure 原則。

    使用這種方法時，您可以使用 Azure 原則將 [Log Analytics 代理程式部署到 Linux 或 Windows Azure Arc 機器](../../governance/policy/samples/built-in-policies.md#monitoring) 內建原則，以在啟用 Arc 的伺服器是否已安裝 log analytics 代理程式時進行審核。 如果未安裝代理程式，則會使用補救工作自動進行部署。 或者，如果您打算使用適用於 VM 的 Azure 監視器監視電腦，請改用 [ [啟用適用於 VM 的 Azure 監視器](../../governance/policy/samples/built-in-initiatives.md#monitoring) ] 方案來安裝和設定 Log Analytics 代理程式。

建議您使用 Azure 原則安裝適用于 Windows 或 Linux 的 Log Analytics 代理程式。

當已啟用 Arc 的伺服器連線之後，您的資料就會開始串流至 Azure Sentinel，並準備好開始使用。 您可以在[內建活頁簿](../../sentinel/quickstart-get-visibility.md)中檢視記錄，並且開始在 Log Analytics 中建置查詢以[調查資料](../../sentinel/tutorial-investigate-cases.md)。

## <a name="next-steps"></a>後續步驟

開始[使用 Azure Sentinel 偵測威脅](../../sentinel/tutorial-detect-threats-built-in.md)。