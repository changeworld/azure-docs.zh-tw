---
title: Azure 必要條件中的 OpenShift 容器平臺3.11
description: 在 Azure 中部署 OpenShift 容器平臺3.11 的必要條件。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 68bd748e890659e4b79d76e4ccab038f251a937a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87368178"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>在 Azure 中部署 OpenShift 容器平臺3.11 的一般必要條件

本文說明在 Azure 中部署 OpenShift 容器平台或 OKD 的一般必要條件。

OpenShift 安裝是使用 Ansible 腳本。 Ansible 使用安全殼層 (SSH) 連接到所有叢集主機，以完成安裝步驟。

當 ansible 讓 SSH 連線到遠端主機時，無法輸入密碼。 因此，私密金鑰不能有相關聯的密碼 (複雜密碼)，否則部署會失敗。

由於所有虛擬機器 (VM) 是透過 Azure Resource Manager 範本部署的，因此會使用相同的公開金鑰存取所有 VM。 相對應的私密金鑰也必須位於執行所有操作手冊的 VM 上。 為了安全地執行此動作，系統會使用 Azure 金鑰保存庫將私密金鑰傳遞至 VM。

如果容器有永續性儲存體的需求，則需要永久性磁碟區。 OpenShift 支援針對永久性磁片區 (Vhd) 的 Azure 虛擬硬碟，但必須先將 Azure 設定為雲端提供者。

在此模型中，OpenShift 將會：

- 在 Azure 儲存體帳戶或受控磁片中建立 VHD 物件。
- 將 VHD 掛接到 VM 並將磁碟區格式化。
- 將磁碟區掛接到 Pod。

為了使這個設定運作，OpenShift 需要在 Azure 中執行上述工作的權限。 服務主體會用於此用途。 服務主體是 Azure Active Directory 中，獲授與資源權限的安全性帳戶。

服務主體必須具備儲存體帳戶和構成叢集之 VM 的存取權。 如果將所有 OpenShift 叢集資源都部署到單一資源群組中，則可以授與服務主體該資源群組的權限。

本指南描述如何建立與必要條件相關聯的成品。

> [!div class="checklist"]
> * 建立金鑰保存庫來管理 OpenShift 叢集的 SSH 金鑰。
> * 建立可供 Azure 雲端提供者使用的服務主體。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure 
使用 [az login](/cli/azure/reference-index) 命令來登入 Azure 訂用帳戶並遵循畫面上的指示進行，或按一下 [試用]**** 來使用 Cloud Shell。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 您應該使用專用的資源群組來裝載金鑰保存庫。 此群組與 OpenShift 叢集資源部署所在的資源群組分開。

下列範例會在*eastus*位置建立名為 *>keyvaultrg*的資源群組：

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫
使用 [az keyvault create](/cli/azure/keyvault) 命令來建立要儲存叢集之 SSH 金鑰的金鑰保存庫。 金鑰保存庫名稱必須是全域唯一的，且必須針對範本部署啟用，否則部署將會失敗並出現 "KeyVaultParameterReferenceSecretRetrieveFailed" 錯誤。

下列範例會在 *keyvaultrg* 資源群組中，建立名為 *keyvault* 的金鑰保存庫：

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>建立 SSH 金鑰 
需要 SSH 金鑰才能安全存取 OpenShift 叢集。 使用 `ssh-keygen` 命令建立 SSH 金鑰組 (在 Linux 或 macOS 上)：
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> 您的 SSH 金鑰組不能有密碼 / 複雜密碼。

如需有關 Windows 上的 SSH 金鑰詳細資訊，請參閱[如何在 Windows 上建立 SSH 金鑰](./ssh-from-windows.md)。 請務必以 OpenSSH 格式匯出私密金鑰。

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>將 SSH 私密金鑰儲存在 Azure Key Vault 中
OpenShift 部署會使用您建立的 SSH 金鑰來安全存取 OpenShift 主機。 若要啟用部署以安全地擷取 SSH 金鑰，請使用下列命令將金鑰儲存在金鑰保存庫中：

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>建立服務主體 
OpenShift 會使用使用者名稱與密碼或服務主體與 Azure 進行通訊。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 OpenShift 等自動化工具搭配使用。 您可以控制和定義關於服務主體可以在 Azure 中執行哪些作業的權限。 最好將服務主體的許可權範圍限定在特定資源群組，而不是整個訂用帳戶。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp) 建立服務主體，並輸出 OpenShift 所需的認證。

下列範例會建立服務主體，並將其參與者許可權指派給名為 *openshiftrg*的資源群組。

首先，建立名為 *openshiftrg*的資源群組：

```azurecli
az group create -l eastus -n openshiftrg
```

建立服務主體：

```azurecli
az group show --name openshiftrg --query id
```

儲存命令的輸出，並在下一個命令中用來取代 $scope

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

記下命令傳回的 appId 屬性和密碼：

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > 請務必寫下安全密碼，因為無法再次取得此密碼。

如需有關服務主體的詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>僅適用于 Resource Manager 範本的必要條件

您必須為 SSH 私密金鑰建立密碼 (**sshPrivateKey**) 、Azure AD 用戶端密碼 (**AadClientSecret**) 、OpenShift 系統管理員密碼 (**OpenshiftPassword**) ，以及 Red Hat 訂用帳戶管理員密碼或啟用金鑰 (**rhsmPasswordOrActivationKey**) 。  此外，如果使用自訂的 TLS/SSL 憑證，則必須建立六個額外的秘密- **routingcafile**、 **routingcertfile**、 **routingkeyfile**、 **mastercafile**、 **mastercertfile**和 **masterkeyfile**。  這些參數將會更詳細地說明。

範本會參考特定的秘密名稱，因此您 **必須** 使用上列粗體名稱 (區分大小寫的) 。

### <a name="custom-certificates"></a>自訂憑證

根據預設，範本會使用 OpenShift web 主控台和路由網域的自我簽署憑證來部署 OpenShift 叢集。 如果您想要使用自訂的 TLS/SSL 憑證，請將 ' routingCertType ' 設為 ' custom '，並將 ' masterCertType ' 設定為 ' custom '。  您需要憑證的 pem 格式的 CA、憑證和金鑰檔。  您可以使用自訂憑證，而不是另一個。

您必須將這些檔案儲存在 Key Vault 秘密中。  使用與用於私密金鑰的 Key Vault 相同的。  範本已硬式編碼，以針對每個 TLS/SSL 憑證檔案使用特定的秘密名稱，而不需要額外6個秘密名稱輸入。  使用下表中的資訊儲存憑證資料。

| 秘密名稱      | 憑證檔案   |
|------------------|--------------------|
| mastercafile     | 主要 CA 檔案     |
| mastercertfile   | 主要憑證檔案   |
| masterkeyfile    | 主要金鑰檔案    |
| routingcafile    | 路由 CA 檔案    |
| routingcertfile  | 路由證書檔  |
| routingkeyfile   | 路由金鑰檔   |

使用 Azure CLI 建立秘密。 以下為範例。

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>接下來的步驟

本文涵蓋下列主題：
> [!div class="checklist"]
> * 建立金鑰保存庫來管理 OpenShift 叢集的 SSH 金鑰。
> * 建立可供 Azure 雲端解決方案提供者使用的服務主體。

接下來，部署 OpenShift 叢集：

- [部署 OpenShift 容器平台](./openshift-container-platform-3x.md)
- [將 OpenShift 容器平臺部署 Self-Managed Marketplace 供應專案](./openshift-container-platform-3x-marketplace-self-managed.md)
