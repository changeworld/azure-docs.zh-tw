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
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814753"
---
這項功能處於預覽狀態。 若要使用這項功能，您必須安裝此模組的預覽擴充功能。

### <a name="install-extension-for-azure-cli"></a>安裝 Azure CLI 擴充功能

對於 Azure CLI，您需要[事件方格擴充功能](/cli/azure/azure-cli-extensions-list)。

在[CloudShell](/azure/cloud-shell/quickstart)中：

* 如果您先前已安裝過該擴充功能，請更新它 `az extension update -n eventgrid`
* 如果您先前尚未安裝過該擴充功能，請安裝它 `az extension add -n eventgrid`

針對本機安裝：

1. [安裝 Azure CLI](/cli/azure/install-azure-cli)。 檢查以`az --version`確定您擁有最新版本。
1. 將舊版的擴充功能解除安裝 `az extension remove -n eventgrid`
1. 使用安裝`eventgrid`延伸模組`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>安裝 PowerShell 模組

對於 PowerShell，您需要 [AzureRM.EventGrid 模組](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)。

在[CloudShell](/azure/cloud-shell/quickstart-powershell)中：

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
