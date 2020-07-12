---
title: 使用 Azure 自動化管理 Azure API 管理
description: 了解如何使用 Azure 自動化服務來管理 Azure API 管理。
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: c808d4659b5987b099dd96d73bb8c18c08fe3c99
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86249390"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>使用 Azure 自動化管理 Azure API 管理
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure API 管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？
[Azure 自動化](https://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。 使用 Azure 自動化，可以自動執行手動、重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、效率和時間價值。

Azure 自動化提供高可靠性、高可用性的工作流程執行引擎，可經由調整符合您的需求。 在 Azure 自動化中，程序可透過手動方式、經由協力廠商系統，或依照排程的間隔啟動，讓工作精準地在需要時執行。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Azure 自動化如何協助管理 Azure API 管理？
您可以在 Azure 自動化中利用 [適用於 API 管理 API 的 Windows PowerShell Cmdlet](/powershell/module/az.apimanagement)來管理「API 管理」。 在 Azure 自動化內，您可以利用 Cmdlet 撰寫 PowerShell 工作流程指令碼，以執行許多 API 管理工作。 您也可以在 Azure 自動化中將這些 Cmdlet 與其他 Azure 服務的 Cmdlet 配對，將跨 Azure 服務和協力廠商系統的複雜工作自動化。

以下是一些搭配 PowerShell 使用「API 管理」的範例︰

* [API 管理的 Azure PowerShell 範例](./powershell-samples.md)

## <a name="next-steps"></a>後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure API 管理之後，請參考下列連結以深入了解。

* 請參閱 Azure 自動化[入門教學](../automation/learn/automation-tutorial-runbook-graphical.md)課程。
