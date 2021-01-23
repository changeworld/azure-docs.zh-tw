---
title: 使用 Azure 自動化管理 Azure 雲端服務 (傳統) |Microsoft Docs
description: 了解如何使用 Azure 自動化服務大規模地管理 Azure 雲端服務。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743486"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>使用 Azure 自動化管理 Azure 雲端服務 (傳統) 

> [!IMPORTANT]
> [Azure 雲端服務 (延伸支援) ](../cloud-services-extended-support/overview.md) 是 Azure 雲端服務產品的新 Azure Resource Manager 型部署模型。透過這種變更，在以 Azure Service Manager 為基礎的部署模型上執行的 Azure 雲端服務，已重新命名為雲端服務 (傳統) ，而且所有新的部署都應該使用 [雲端服務 (延伸支援) ](../cloud-services-extended-support/overview.md)。
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化您的 Azure 雲端服務管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？
[Azure 自動化](https://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。 透過 Azure 自動化，長時間執行、手動、容易發生錯誤和經常重複的工作都可以自動化，以提高可靠性、效率，並為您的組織縮短創造價值時程。

Azure 自動化提供非常可靠且高度可用的工作流程執行引擎，可隨著組織的成長根據您的需求進行調整。 在 Azure 自動化中，程序可透過手動方式、經由協力廠商系統，或依照排程的間隔啟動，讓工作精準地在需要時執行。

將您的雲端管理工作交由「Azure 自動化」自動執行，以降低營運負擔並釋出 IT/開發維運人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Azure 自動化為何有助於管理 Azure 雲端服務？
Azure 雲端服務可透過 [Azure PowerShell 工具](/powershell/)中提供的 PowerShell Cmdlet，在 Azure 自動化中受到管理。 Azure 自動化的這些雲端服務 PowerShell Cmdlet 都是內建的，以便您在服務內執行所有雲端服務管理工作。 您也可以在 Azure 自動化中將這些 Cmdlet 與其他 Azure 服務的 Cmdlet 配對，將跨 Azure 服務和協力廠商系統的複雜工作自動化。

某些範例使用 Azure 自動化來管理 Azure 雲端服務，包括︰

* [每當 Azure Blob 儲存體中更新 cscfg 或 cspkg 時，即連續部署雲端服務](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [以平行方式重新啟動雲端服務執行個體，一次升級一個網域](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure 雲端服務之後，請參考下列連結，以深入了解 Azure 自動化。

* [Azure 自動化總覽](../automation/automation-intro.md)
* [我的第一個 Runbook](../automation/learn/automation-tutorial-runbook-graphical.md)