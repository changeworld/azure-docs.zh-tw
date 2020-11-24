---
title: 包含檔案
description: 包含檔案
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 1327ac49920af353d6adbbe592ab4189417e60e8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557808"
---
## <a name="use-cli-shell"></a>使用 CLI Shell

建議使用 [Azure Cloud Shell](../articles/cloud-shell/overview.md?view=azure-cli-latest) 執行 CLI 命令。 **Cloud Shell** 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 Cloud Shell 中已預先安裝和設定共用 Azure 工具，以便您搭配自己的帳戶使用。 它可讓您彈性地選擇最適合您工作方式的殼層體驗。 Linux 使用者可以選擇 Bash 體驗，而 Windows 使用者可以選擇 PowerShell。

您也可以在本機安裝 CLI。 如需您平台的相關指示，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="sign-in"></a>登入

使用本機安裝的 CLI 時需要登入 Azure。 Azure Cloud Shell 不需要這個步驟。 使用 `az login` 命令進行登入。

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器頁面，並遵循命令列中的指示，在瀏覽器中瀏覽至 https://aka.ms/devicelogin 之後，輸入授權碼。

### <a name="specify-location-of-files"></a>指定檔案位置

許多媒體服務 CLI 命令可讓您使用檔案名稱傳遞參數。 如果您使用 **Cloud Shell**，您可以將檔案上傳至您的 clouddrive (使用 Bash 或 PowerShell)。 

![上傳檔案]

無論您使用本機 CLI 還是 **Cloud Shell**，都必須根據您所使用的 OS 或 Cloud Shell (Bash 或 PowerShell) 指定檔案路徑。 以下是一些範例：

檔案的相對路徑 (所有 OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Linux/Mac 和 Windows OS 上的絕對檔案路徑

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

如果命令要求檔案路徑，請使用 `{file}`。 例如： `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json` 。 <br/> 如果命令要載入指定檔案，請使用 `@{file}`。 例如： `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json` 。

[上傳檔案]: ./media/media-services-cli/upload-download-files.png