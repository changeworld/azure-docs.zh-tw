---
title: 使用 Azure 自動化管理資料庫
description: 瞭解如何使用 Azure 自動化服務，大規模地管理 Azure SQL Database。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: faf5b1108e28b352a0b8622feed8bdd99264ff16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327573"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>使用 Azure 自動化管理 Azure SQL Database 中的資料庫

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure SQL Database 中資料庫的管理。

## <a name="about-azure-automation"></a>關於 Azure 自動化

[Azure 自動化](https://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。 透過 Azure 自動化，長時間執行、手動、容易發生錯誤和經常重複的工作都可以自動化，以提高可靠性、效率，並為您的組織縮短創造價值時程。 如需開始使用的相關資訊，請參閱 [Azure 自動化簡介](../../automation/automation-intro.md)

Azure 自動化提供高可靠性且高可用性的工作流程執行引擎，可隨著組織的成長根據您的需求進行調整。 在 Azure 自動化中，可利用手動方式、透過協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以降低營運負擔並釋出 IT/開發維運人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Azure 自動化如何協助管理您的資料庫

使用 Azure 自動化，您可以使用[Azure PowerShell 工具](/powershell/azure/)中提供的[PowerShell Cmdlet](/powershell/module/servicemanagement/azure.service/#sql)來管理 Azure SQL Database 中的資料庫。 Azure 自動化有這些 Azure SQL Database 的 PowerShell Cmdlet 可供使用，因此您可以在服務內執行所有 SQL Database 的管理工作。 您也可以在 Azure 自動化中將這些 Cmdlet 與其他 Azure 服務的 Cmdlet 配對，將跨 Azure 服務和協力廠商系統的複雜工作自動化。

Azure 自動化也可直接與 SQL 伺服器通訊，只要使用 PowerShell 發出 SQL 命令即可。

[Azure 自動化](../../automation/automation-runbook-gallery.md)的 runbook 和模組資源庫提供來自 Microsoft 和您可以匯入 Azure 自動化的各項 runbook。 若要使用 Runbook，請從資源庫下載 Runbook，或者，您可以直接從資源庫匯入 Runbook， 或從 Azure 入口網站的自動化帳戶匯入。

## <a name="next-steps"></a>後續步驟

既然您已瞭解 Azure 自動化的基本概念以及如何用它來管理 Azure SQL Database，請參考下列連結以深入瞭解 Azure 自動化。

- [Azure 自動化總覽](../../automation/automation-intro.md)
- [我的第一個 Runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
