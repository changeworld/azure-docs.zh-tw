---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 644669ea27938e385e11f3b1911a23ab30829a95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027092"
---
這項功能處於預覽狀態。 若要使用這項功能，您必須安裝此模組的預覽擴充功能。

### <a name="install-extension-for-azure-cli"></a>安裝 Azure CLI 擴充功能

對於 Azure CLI，您需要[事件方格擴充功能](/cli/azure/azure-cli-extensions-list)。

在 [CloudShell](../articles/cloud-shell/quickstart.md) 中：

* 如果您先前已安裝過該擴充功能，請更新它 `az extension update -n eventgrid`
* 如果您先前尚未安裝過該擴充功能，請安裝它 `az extension add -n eventgrid`

針對本機安裝：

1. [安裝 Azure CLI](/cli/azure/install-azure-cli)。 使用 `az --version` 檢查，以確定所使用的是最新版本。
1. 將舊版的擴充功能解除安裝 `az extension remove -n eventgrid`
1. 使用 `az extension add -n eventgrid` 安裝 `eventgrid` 延伸模組

### <a name="install-module-for-powershell"></a>安裝 PowerShell 模組

對於 PowerShell，您需要 [AzureRM.EventGrid 模組](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)。

在 [CloudShell](../articles/cloud-shell/quickstart-powershell.md) 中：

* 安裝模組 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

針對本機安裝：

1. 以系統管理員身分開啟 PowerShell 主控台
1. 安裝模組 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

如果 `-AllowPrerelease` 參數無法使用，請使用下列步驟：

1. `Install-Module PowerShellGet -Force`執行
1. `Update-Module PowerShellGet`執行
1. 關閉 PowerShell 主控台
1. 以系統管理員身分重新啟動 PowerShell
1. 安裝模組 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`