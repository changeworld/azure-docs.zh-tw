---
title: Azure 快速入門 - 使用 Azure Resource Manager 範本建立受控 HSM
description: 說明如何建立 Azure，並使用 Resource Manager 範本建立 Azure Key Vault 受控 HSM 的快速入門
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d47935f76347b2d5272b386942a85643a732e643
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831747"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>快速入門：使用 Azure Resource Manager 範本建立 Key Vault 受控 HSM

受控 HSM 是完全受控、高可用性、單一租用戶、符合標準的雲端服務，可讓您使用經過 **FIPS 140-2 層級 3** 驗證的 HSM，來保護雲端應用程式的密碼編譯金鑰。  

本快速入門主要說明部署 Resource Manager 範本以建立受控 HSM 的程序。  [Resource Manager 範本](../../azure-resource-manager/templates/overview.md)是一個 JavaScript 物件標記法 (JSON) 檔案，定義了專案的基礎結構和組態。 範本會使用宣告式語法，可讓您陳述您要部署的項目，而不需要撰寫一連串程式設計命令來加以建立。 如果您要深入了解如何開發 Resource Manager 範本，請參閱 [Resource Manager 文件](../../azure-resource-manager/index.yml)和[範本參考](/azure/templates/microsoft.keyvault/allversions)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您必須具有下列項目︰

- Microsoft Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial)。
- Azure CLI 2.12.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

若要進一步了解透過 CLI 的登入選項，請參閱[使用 Azure CLI 進行登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-manage-hsm"></a>建立受控 HSM

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/)。

範本中定義的 Azure 資源：

* **Microsoft.KeyVault/managedHSMs**：建立 Azure Key Vault 受控 HSM。

更多 Azure Key Vault 範本範例可在[此處](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)找到。

此範本需要與您的帳戶相關聯的物件識別碼。 若要尋找該識別碼，請使用 Azure CLI [az ad user show](/cli/azure/ad/user?view=azure-cli-latest&preserve-view=true#az_ad_user_show) 命令，將您的電子郵件地址傳至 `--id` 參數。 您可以使用 `--query` 參數，將輸出限定為物件識別碼。

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

您可能也需要租用戶識別碼。 若要尋找該識別碼，請使用 Azure CLI [az ad user show](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_show) 命令。 您可以使用 `--query` 參數，將輸出限定為租用戶識別碼。

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立金鑰保存庫和祕密。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. 選取或輸入下列值。

    除非有指定，否則就使用預設值來建立金鑰保存庫和密碼。

    - 訂用帳戶：選取 Azure 訂用帳戶。
    - **資源群組**：選取 [新建]，輸入資源群組的唯一名稱，然後按一下 [確認]。
    - **位置**：選取位置。 例如，**美國中南部**。
    - **managedHSMName**：輸入受控 HSM 的名稱。
    - **租用戶識別碼**：範本功能會自動擷取您的租用戶識別碼；請勿變更預設值。  如果沒有值，請輸入您在[必要條件](#prerequisites)中擷取的租用戶識別碼。
    * **initialAdminObjectIds**：輸入您在 [必要條件](#prerequisites)中擷取的物件識別碼。

3. 選取 [購買]。 成功部署金鑰保存庫之後，您會收到通知：

Azure 入口網站用於部署範本。 除了 Azure 入口網站以外，您也可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="next-steps"></a>下一步

在本快速入門中，您已建立受控 HSM。 此受控 HSM 在啟動之前將無法完整運作。 請參閱[啟動受控 HSM](quick-create-cli.md#activate-your-managed-hsm)，以了解如何啟動您的 HSM。

- 閱讀[受控 HSM 的概觀](overview.md)
- 了解如何[管理受控 HSM 中的金鑰](key-management.md)
- 檢閱[受控 HSM 最佳做法](best-practices.md)
