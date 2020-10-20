---
title: 設定您的 Azure Red Hat OpenShift 開發環境
description: 以下是使用 Microsoft Azure Red Hat OpenShift 的必要條件。
keywords: red hat openshift 安裝程式設定
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.custom: devx-track-azurecli
ms.openlocfilehash: c189c0902e694dc49d81a48433e3269e9f2a438c
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216873"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>設定您的 Azure Red Hat OpenShift 開發環境

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 將于2022年6月30日淘汰。 支援建立新的 Azure Red Hat OpenShift 3.11 叢集會繼續到2020年11月30日。 淘汰之後，剩餘的 Azure Red Hat OpenShift 3.11 叢集將會關閉以防止安全性弱點。
> 
> 遵循本指南來 [建立 Azure Red Hat OpenShift 4](tutorial-create-cluster.md)叢集。
> 如果您有特定問題， [請洽詢我們](mailto:arofeedback@microsoft.com)。

若要建立並執行 Microsoft Azure Red Hat OpenShift 應用程式，您必須：

* 安裝版本2.0.65 版 (或更高的 Azure CLI)  (或使用 Azure Cloud Shell) 。
* 註冊 `AROGA` 功能和相關聯的資源提供者。
* 建立 Azure Active Directory (Azure AD) 租使用者。
* 建立 Azure AD 應用程式物件。
* 建立 Azure AD 使用者。

下列指示將引導您完成所有必要條件。

## <a name="install-the-azure-cli"></a>安裝 Azure CLI

Azure Red Hat OpenShift 需要版本2.0.65 版或更高版本的 Azure CLI。 如果您已經安裝了 Azure CLI，您可以執行下列動作來檢查您的版本：

```azurecli
az --version
```

例如，輸出的第一行會有 CLI 版本 `azure-cli (2.0.65)` 。

以下是當您需要新的安裝或升級時， [安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 的指示。

或者，您也可以使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 使用 Azure Cloud Shell 時，如果您打算遵循「[建立及管理 Azure Red Hat OpenShift](tutorial-create-cluster.md)叢集」教學課程系列，請務必選取**Bash**環境。

## <a name="register-providers-and-features"></a>註冊提供者和功能

在 `Microsoft.ContainerService AROGA` `Microsoft.Solutions` `Microsoft.Compute` `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` 部署您的第一個 Azure Red Hat OpenShift 叢集之前，必須先手動將功能、、、和提供者註冊至您的訂用帳戶。

若要手動註冊這些提供者和功能，請使用 Bash shell 中的下列指示（如果您已安裝 CLI），或從 Azure 入口網站中的 Azure Cloud Shell (Bash) 會話：

1. 如果您有多個 Azure 訂用帳戶，請指定相關的訂用帳戶識別碼：

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 註冊 Microsoft >microsoft.containerservice AROGA 功能：

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. 註冊 Microsoft 儲存提供者：

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 註冊 Microsoft Compute provider：

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. 註冊 Microsoft 解決方案提供者：

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. 註冊 Microsoft 網路提供者：

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. 註冊 KeyVault 提供者：

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. 重新整理 >microsoft.containerservice 資源提供者的註冊：

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Azure AD) 租使用者建立 Azure Active Directory (

Azure Red Hat OpenShift 服務需要相關聯的 Azure Active Directory (Azure AD) 租使用者，代表您的組織及其與 Microsoft 之間的關係。 您的 Azure AD 租使用者可讓您註冊、建立和管理應用程式，以及使用其他 Azure 服務。

如果您沒有 Azure AD 可作為 Azure Red Hat OpenShift 叢集的租使用者，或想要建立用於測試的租使用者，請遵循 [建立 Azure Red Hat OpenShift 叢集的 Azure AD 租使用者](howto-create-tenant.md) 中的指示，再繼續進行本指南。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>建立 Azure AD 使用者、安全性群組和應用程式物件

Azure Red Hat OpenShift 需要在您的叢集上執行工作的許可權，例如設定存放裝置。 這些許可權是透過 [服務主體](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)來表示。 您也會想要建立新的 Active Directory 使用者，以測試在 Azure Red Hat OpenShift 叢集上執行的應用程式。

遵循 [建立 Azure AD 應用程式物件和使用者](howto-aad-app-configuration.md) 中的指示來建立服務主體、產生應用程式的用戶端密碼和驗證回呼 URL，以及建立新的 Azure AD 安全性群組和使用者來存取叢集。

## <a name="next-steps"></a>後續步驟

您現在已準備好使用 Azure Red Hat OpenShift！

試用教學課程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli