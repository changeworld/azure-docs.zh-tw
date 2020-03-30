---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 2c7d312910c6d38c54b291da34bfb827246c7dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504295"
---
## <a name="prepare-for-akv-integration"></a>準備進行 AKV 整合
若要使用 Azure 金鑰保存庫整合以設定 SQL Server VM，有幾項必要條件： 

1. [安裝 Azure 電源外殼](#install)
2. [建立 Azure Active Directory](#register)
3. [創建金鑰保存庫](#createkeyvault)

下列各節說明這些必要條件和您必須收集以在稍後執行 PowerShell Cmdlet 的資訊。

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a>安裝 Azure 電源外殼
確保已安裝最新的 Azure PowerShell 模組。 有關詳細資訊，請參閱[如何安裝和配置 Azure PowerShell](/powershell/azure/install-az-ps)。

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a>在 Azure 活動目錄中註冊應用程式

首先，您必須在您的訂用帳戶中具有 [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD)。 在許多優點中，這可讓您將金鑰保存庫的權限授與特定使用者和應用程式。

接下來，向 AAD 註冊應用程式。 如此將會提供您服務主體帳戶，可存取您 VM 需要的金鑰保存庫。 在 Azure 金鑰保存庫一文中，您可以在["使用 Azure 活動目錄註冊應用程式](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory)"部分中找到這些步驟，也可以在此[博客文章](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)的應用程式**部分"獲取標識**"中看到帶有螢幕截圖的步驟。 完成這些步驟之前，您需要收集此註冊期間的下列資訊，稍後當您在 SQL VM 上啟用 Azure 金鑰保存庫整合時需要該資訊。

* 添加應用程式後，在 **"已註冊"應用**邊欄選項卡上查找**應用程式 ID（** 也稱為 AAD 用戶端 ID 或 AppID）。
    應用程式識別碼稍後會指派給 PowerShell 指令碼中的 **$spName** (服務主體名稱) 參數，以啟用 Azure Key Vault 整合。

   ![應用程式識別碼](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* 在建立金鑰的這些步驟中，複製您的金鑰的密碼，如下列螢幕擷取畫面所示。 這個金鑰密碼稍後會指派給 PowerShell 指令碼中的 **$spSecret** (服務主體密碼) 參數。

   ![AAD 祕密](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* 應用程式識別碼和秘密也將用來在 SQL Server 中建立認證。

* 您必須授權此新的應用程式 ID（或用戶端 ID） 具有以下存取權限：**獲取**、**包裝金鑰**、**解包金鑰**。 做法是使用 [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) \(英文\) Cmdlet。 如需詳細資訊，請參閱 [Azure Key Vault 概觀](../articles/key-vault/key-vault-overview.md)。

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a>創建金鑰保存庫
若要使用 Azure 金鑰保存庫來儲存您在 VM 中用於加密的金鑰，您需要金鑰保存庫的存取權。 如果您尚未設定您的金鑰保存庫，請依照[開始使用 Azure 金鑰保存庫](../articles/key-vault/key-vault-overview.md)一文中的步驟建立一個金鑰保存庫。 完成這些步驟之前，有一些資訊您需要在此安裝期間收集，稍後當您在 SQL VM 上啟用 Azure 金鑰保存庫整合時需要該資訊。

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

當您進行「建立」金鑰保存庫步驟時，請注意傳回的 **vaultUri** 屬性，它是金鑰保存庫 URL。 在該步驟提供的範例中 (如下所示)，金鑰保存庫名稱是 ContosoKeyVault，因此金鑰保存庫 URL 會是 https://contosokeyvault.vault.azure.net/。

金鑰保存庫 URL 稍後會指派給 PowerShell 指令碼中的 **$akvURL** 參數，以啟用 Azure 金鑰保存庫整合。

建立金鑰保存庫之後，我們需要將金鑰新增至金鑰保存庫，稍後當我們在 SQL Server 中建立非對稱金鑰時需要參考該金鑰。
