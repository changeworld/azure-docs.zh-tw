---
title: Azure Cloud Shell 功能 | Microsoft Docs
description: Azure Cloud Shell 中的功能總覽
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: a052364b06ac1b9b30cef76db10a79e8ed85b9a3
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89470148"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell 的功能和工具

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell 會在 `Ubuntu 16.04 LTS` 上執行。

## <a name="features"></a>功能

### <a name="secure-automatic-authentication"></a>安全的自動驗證

Cloud Shell 會安全地自動驗證 Azure CLI 和 Azure PowerShell 的帳戶存取。

### <a name="home-persistence-across-sessions"></a>工作階段之間的 $HOME 持續性

若要在工作階段之間保存檔案，Cloud Shell 會在第一次啟動時逐步引導您連結 Azure 檔案共用。
完成後，Cloud Shell 會自動連結儲存體 (掛接為 `$HOME\clouddrive`)，供所有未來的工作階段使用。
此外，在 Azure 檔案共用中，您的 `$HOME` 目錄會保存為 .img。
在工作階段之間不會保存 `$HOME` 外面的檔案和電腦狀態。 將祕密儲存為 SSH 金鑰時，請使用最佳做法。 服務例如 [Azure Key Vault 具有設定的教學課程](../key-vault/general/manage-with-cli2.md#prerequisites)。

[深入了解在 Cloud Shell 中保存檔案。](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure 磁碟機 (Azure:)

Cloud Shell 中的 PowerShell 提供 () 的 Azure 磁片磁碟機 `Azure:` 。 您可以使用來切換至您的 `cd Azure:` 主目錄並切換回 Azure 磁片磁碟機 `cd  ~` 。
Azure 磁碟機可讓您輕鬆探索和導覽與檔案系統導覽類似的 Azure 資源 (例如計算、網路、儲存體等等)。
無論您使用哪個磁碟機，都可以繼續使用熟悉的 [Azure PowerShell Cmdlet](/powershell/azure) 來管理這些資源。
對 Azure 資源進行的任何變更 (在 Azure 入口網站中直接進行，或透過 Azure PowerShell Cmdlet) 都會反映在 Azure 磁碟機中。  您可以執行 `dir -Force` 以重新整理您的資源。

![正在初始化的 Azure Cloud Shell 的螢幕擷取畫面，以及目錄資源的清單。](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>管理 Exchange Online

Cloud Shell 中的 PowerShell 包含 Exchange Online 模組的私用組建。  執行 `Connect-EXOPSSession` 以取得您的 Exchange Cmdlet。

![執行命令 EXOPSSession 和取得使用者的 Azure Cloud Shell 螢幕擷取畫面。](media/features-powershell/exchangeonline.png)

 `Get-Command -Module tmp_*`執行 {2}
> [!NOTE]
> 模組名稱的開頭應該是 `tmp_` ，如果您已安裝具有相同前置詞的模組，也會顯示它們的 Cmdlet。 

![執行命令 Get-Command-Module tmp_ * Azure Cloud Shell 的螢幕擷取畫面。](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>與開放原始碼工具深度整合

Cloud Shell 包含預先設定的驗證，可用於開放原始碼工具，例如 Terraform、Ansible 和 Chef InSpec。 從範例逐步解說開始試試看。

## <a name="tools"></a>工具

|類別   |Name   |
|---|---|
|Linux 工具            |Bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure 工具            |[Azure CLI](https://github.com/Azure/azure-cli) 和 [Azure 傳統 CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](../storage/common/storage-use-azcopy-v10.md)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric CLI](../service-fabric/service-fabric-cli.md)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|文字編輯器           |code (Cloud Shell 編輯器)<br> vim<br> nano<br> emacs    |
|原始檔控制         |git                    |
|建置工具            |make<br> maven<br> npm<br> pip         |
|容器             |[Docker 機器](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|資料庫              |MySQL 用戶端<br> PostgreSql 用戶端<br> [sqlcmd 公用程式](/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|其他                  |iPython 用戶端<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli) \(英文\)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Puppet 螺栓](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)<br> [Office 365 CLI](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>語言支援

|語言   |版本   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 和 3.5 (預設)|

## <a name="next-steps"></a>接下來的步驟
[Cloud Shell 快速入門中的 Bash](quickstart.md) <br>
[Cloud Shell 快速入門中的 PowerShell](quickstart-powershell.md) <br>
[了解 Azure CLI](/cli/azure/) <br>
[了解 Azure PowerShell](/powershell/azure/) <br>