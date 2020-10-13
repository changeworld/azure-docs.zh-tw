---
title: 快速入門 - 佈建並啟動 Azure 受控 HSM
description: 說明如何使用 Azure CLI 來佈建並啟動受控 HSM 的快速入門
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756807"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>快速入門：使用 Azure CLI 佈建並啟動受控 HSM

Azure Key Vault 受控 HSM 是完全受控、高可用性、單一租用戶、符合標準的雲端服務，可讓您使用經過 **FIPS 140-2 層級 3** 驗證的 HSM，來保護雲端應用程式的密碼編譯金鑰。 如需受控 HSM 的詳細資訊，您可以檢閱[概觀](overview.md)。 

在本快速入門中，您會使用 Azure CLI 來建立並啟動受控 HSM。 一旦完成該作業，您就會儲存祕密。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您必須具有下列項目︰

* Microsoft Azure 訂用帳戶。 如果您沒有帳戶，您可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 2.12.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。
* 訂用帳戶中的受控 HSM。 請參閱[快速入門：使用 Azure CLI 佈建並啟動受控 HSM](quick-create-cli.md)，以佈建並啟動受控 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus2 位置建立名為 ContosoResourceGroup 的資源群組。

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>建立受控 HSM

建立受控 HSM 的程序包含兩個步驟：
1. 佈建受控 HSM 資源。
1. 下載安全性網域以啟動受控 HSM。

### <a name="provision-a-managed-hsm"></a>佈建受控 HSM

使用 `az keyvault create` 命令建立受控 HSM。 此指令碼包含三個必要參數：資源群組名稱、HSM 名稱和地理位置。

您必須提供下列輸入，才能建立受控 HSM 資源：
- 要放置在訂用帳戶中的資源群組。
- Azure 位置。
- 初始管理員的清單。

下列範例會在位於**美國東部 2** 位置的資源群組 **ContosoResourceGroup** 中建立名為 **ContosoMHSM** 的 HSM，並將**目前登入的使用者**設為唯一管理員。

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> 執行 create 命令可能需要幾分鐘的時間。 成功傳回之後，您即可啟動 HSM。

此命令的輸出會顯示您所建立之受控 HSM 的屬性。 兩個最重要屬性是：

* **名稱**：在此範例中，名稱是 ContosoMHSM。 您將在其他 Key Vault 命令中使用此名稱。
* **hsmUri**：在此範例中，URI 是 https://contosohsm.managedhsm.azure.net 。 透過其 REST API 使用 HSM 的應用程式必須使用此 URI。

您的 Azure 帳戶現已取得在此受控 HSM 上執行任何作業的授權。 而且，沒有其他人已獲授權。

### <a name="activate-your-managed-hsm"></a>啟動您的受控 HSM

在 HSM 啟動前，所有資料平面命令都會停用。 您將無法建立金鑰或指派角色。 只有在 create 命令執行期間指派的指定管理員，才可以啟用 HSM。 若要啟動 HSM，您必須下載[安全性網域](security-domain.md)。

若要啟動 HSM，您需要：
- 至少 3 個 RSA 金鑰組 (最多 10 個)
- 指定解密安全性網域所需的金鑰數目下限 (仲裁)

若要啟動 HSM，您至少必須將 3 個 (最多 10 個) RSA 公開金鑰傳送至 HSM。 HSM 會使用這些金鑰將安全性網域加密，並加以傳回。 此安全性網域下載順利完成後，您的 HSM 即可供使用。 您也需要指定仲裁，這是解密安全性網域所需的私密金鑰數目下限。

下列範例說明如何使用 `openssl` 產生 3 個自我簽署憑證。

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> 請安全地建立並儲存在此步驟中產生的 RSA 金鑰組和安全性網域檔案。

使用 `az keyvault security-domain download` 命令下載安全性網域，並啟動您的受控 HSM。 下列範例會使用 3 個 RSA 金鑰組 (此命令只需要公開金鑰)，並將仲裁設定為 2。

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

請安全地儲存安全性網域檔案和 RSA 金鑰組。 進行災害復原，或建立另一個共用相同安全性網域的受控 HSM 以便共用金鑰時，將需要這些資料。

成功下載安全性網域後，您的 HSM 會處於作用中狀態，並可供您使用。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。

若不再需要，您可以使用 [az group delete](/cli/azure/group) 命令來移除資源群組和所有相關資源。 您可以刪除資源，如下所示：

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並在其中儲存祕密。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀[受控 HSM 的概觀](overview.md)
- 了解如何[管理受控 HSM 中的金鑰](key-management.md)
- 檢閱[受控 HSM 最佳做法](best-practices.md)
