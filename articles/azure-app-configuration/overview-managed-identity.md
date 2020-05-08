---
title: 使用 Azure 應用程式組態設定受控識別
description: 瞭解受控識別在 Azure 應用程式組態中的使用方式，以及如何設定受控識別
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 560a2df5946b9cfe1160864752595e86cc6eeb65
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981216"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>如何使用 Azure 應用程式組態的受控識別

本主題說明如何建立 Azure 應用程式組態的受控識別。 Azure Active Directory （AAD）的受控識別可讓 Azure 應用程式組態輕鬆地存取其他受 AAD 保護的資源，例如 Azure Key Vault。 身分識別是由 Azure 平臺所管理。 您不需要布建或輪替任何秘密。 如需有關 ADD 中受控身分識別的詳細資訊，請參閱 [Azure 資源的受控身分識別](../active-directory/managed-identities-azure-resources/overview.md)。

您的應用程式可以授與兩種類型的身分識別：

- **系統指派**的身分識別會系結至您的設定存放區。 如果您的設定存放區已刪除，則會予以刪除。 設定存放區只能有一個系統指派的身分識別。
- **使用者指派**的身分識別是一種獨立的 Azure 資源，可以指派給您的設定存放區。 設定存放區可以有多個使用者指派的身分識別。

## <a name="adding-a-system-assigned-identity"></a>新增系統指派的身分識別

使用系統指派的身分識別建立應用程式組態存放區時，必須在存放區上設定額外的屬性。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 來設定受控識別，請針對現有的設定存放區使用[az appconfig identity assign]命令。 有三個選項可供您執行本節中的範例︰

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 透過位於下方每個程式碼區塊右上角的 [試試看] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您偏好使用本機 CLI 主控台，請[安裝最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) （2.1 或更新版本）。

下列步驟將逐步引導您建立應用程式組態存放區，並使用 CLI 為其指派身分識別：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login] 登入 Azure。 使用與您的 Azure 訂用帳戶相關聯的帳戶：

    ```azurecli-interactive
    az login
    ```

1. 使用 CLI 建立應用程式組態存放區。 如需如何搭配使用 CLI 與 Azure 應用程式組態的更多範例，請參閱[應用程式組態 CLI 範例](scripts/cli-create-service.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. 執行[az appconfig identity assign]命令，為此設定存放區建立系統指派的身分識別：

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>新增使用者指派的身分識別

建立具有使用者指派身分識別的應用程式組態存放區時，需要您建立身分識別，然後將其資源識別碼指派給您的存放區。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要使用 Azure CLI 來設定受控識別，請針對現有的設定存放區使用[az appconfig identity assign]命令。 有三個選項可供您執行本節中的範例︰

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 透過位於下方每個程式碼區塊右上角的 [試試看] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您偏好使用本機 CLI 主控台，請[安裝最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 或更新版本)。

下列步驟將逐步引導您建立使用者指派的身分識別和應用程式組態存放區，然後使用 CLI 將身分識別指派給存放區：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login] 登入 Azure。 使用與您的 Azure 訂用帳戶相關聯的帳戶：

    ```azurecli-interactive
    az login
    ```

1. 使用 CLI 建立應用程式組態存放區。 如需如何搭配使用 CLI 與 Azure 應用程式組態的更多範例，請參閱[應用程式組態 CLI 範例](scripts/cli-create-service.md)：

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. 使用 CLI 建立名`myUserAssignedIdentity`為的使用者指派身分識別。

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    在此命令的輸出中，請記下`id`屬性的值。

1. 執行[az appconfig identity assign]命令，將新使用者指派的身分識別指派給這個設定存放區。 使用您在上一個`id`步驟中記下的屬性值。

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>移除身分識別

藉由使用 Azure CLI 中的[az appconfig identity remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove)命令停用此功能，即可移除系統指派的身分識別。 使用者指派的身分識別可以個別移除。 以這種方式將系統指派的身分識別移除，也會從 AAD 刪除它。 當您刪除應用程式資源時，系統指派的身分識別會自動從 AAD 移除。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
