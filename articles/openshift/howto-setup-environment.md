---
title: 設置 Azure 紅帽開放移位開發環境
description: 以下是使用 Microsoft Azure 紅帽 OpenShift 的先決條件。
keywords: 紅帽開檔設置設置
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477029"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>設定您的 Azure Red Hat OpenShift 開發環境

要構建和運行 Microsoft Azure 紅帽 OpenShift 應用程式，您需要：

* 安裝 Azure CLI 的版本 2.0.65（或更高版本）（或使用 Azure 雲外殼）。
* 註冊功能`AROGA`和相關資來源提供者。
* 創建 Azure 活動目錄 （Azure AD） 租戶。
* 創建 Azure AD 應用程式物件。
* 創建 Azure AD 使用者。

以下說明將引導您完成所有這些先決條件。

## <a name="install-the-azure-cli"></a>安裝 Azure CLI

Azure 紅帽 OpenShift 需要 Azure CLI 的版本 2.0.65 或更高版本。 如果已經安裝了 Azure CLI，則可以通過運行以下版本來檢查具有的版本：

```azurecli
az --version
```

第一行輸出將具有 CLI 版本，例如`azure-cli (2.0.65)`。

以下是安裝 Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)的說明，如果需要新安裝或升級。

或者，可以使用[Azure 雲外殼](https://docs.microsoft.com/azure/cloud-shell/overview)。 使用 Azure 雲外殼時，如果計畫遵循["創建和管理 Azure 紅帽 OpenShift"群集](tutorial-create-cluster.md)教程系列，請確保選擇**Bash**環境。

## <a name="register-providers-and-features"></a>註冊提供程式和功能

在`Microsoft.ContainerService AROGA`部署第`Microsoft.Solutions`一`Microsoft.Compute`個`Microsoft.Storage` `Microsoft.KeyVault` Azure 紅帽 OpenShift 群集之前，必須手動將功能 、、、和`Microsoft.Network`提供程式註冊到訂閱中。

要手動註冊這些提供程式和功能，請在安裝了 CLI 或 Azure 門戶中的 Azure 雲外殼 （Bash） 會話時使用 Bash shell 中的以下說明：

1. 如果您有多個 Azure 訂閱，請指定相關的訂閱 ID：

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 註冊 Microsoft.集裝箱服務 AROGA 功能：

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. 註冊 Microsoft.存儲提供程式：

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 註冊 Microsoft.計算提供程式：

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. 註冊 Microsoft.解決方案供應商：

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. 註冊 Microsoft.網路供應商：

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. 註冊 Microsoft.KeyVault 提供程式：

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. 刷新 Microsoft.Container 服務資來源提供者的註冊：

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>創建 Azure 活動目錄 （Azure AD） 租戶

Azure 紅帽 OpenShift 服務需要一個關聯的 Azure 活動目錄 （Azure AD） 租戶，該租戶表示您的組織及其與 Microsoft 的關係。 Azure AD 租戶使您能夠註冊、生成和管理應用，以及使用其他 Azure 服務。

如果沒有 Azure AD 用作 Azure 紅帽 OpenShift 群集的租戶，或者希望創建租戶進行測試，請按照[Azure 紅帽 OpenShift 群集創建 Azure AD 租戶](howto-create-tenant.md)中的說明操作，然後再繼續本指南。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>創建 Azure AD 使用者、安全性群組和應用程式物件

Azure 紅帽 OpenShift 需要許可權才能在群集上執行任務，例如配置存儲。 這些許可權通過[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)表示。 您還需要創建新的活動目錄使用者，用於測試在 Azure 紅帽 OpenShift 群集上運行的應用。

按照創建 Azure [AD 應用物件和使用者](howto-aad-app-configuration.md)中的說明創建服務主體，為應用生成用戶端機密和身份驗證回檔 URL，並創建新的 Azure AD 安全性群組和使用者以訪問群集。

## <a name="next-steps"></a>後續步驟

現在，您已準備好使用 Azure 紅帽 OpenShift！

請嘗試本教程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
