---
title: 在 Azure 先決條件中開啟 Shift 容器平臺 3.11
description: 在 Azure 中部署 OpenShift 容器平臺 3.11 的先決條件。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 8767a6ee6218223280ea6219e22540c53d1e89be
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409126"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>在 Azure 中部署 OpenShift 容器平臺 3.11 的常見先決條件

本文說明在 Azure 中部署 OpenShift 容器平台或 OKD 的一般必要條件。

OpenShift 安裝是使用 Ansible 腳本。 Ansible 使用安全殼層 (SSH) 連接到所有叢集主機，以完成安裝步驟。

當 Aible 使 SSH 連接到遠端主機時,它無法輸入密碼。 因此，私密金鑰不能有相關聯的密碼 (複雜密碼)，否則部署會失敗。

由於所有虛擬機器 (VM) 是透過 Azure Resource Manager 範本部署的，因此會使用相同的公開金鑰存取所有 VM。 相應的私鑰必須位於執行所有行動手冊的 VM 上。 要安全地執行此操作,使用 Azure 密鑰保管庫將私鑰傳遞到 VM 中。

如果容器有永續性儲存體的需求，則需要永久性磁碟區。 OpenShift 支援持久卷的 Azure 虛擬硬碟 (VHD),但必須先將 Azure 配置為雲端提供程式。

在此模型中，OpenShift 將會：

- 在 Azure 儲存帳戶或託管磁盤中創建 VHD 物件。
- 將 VHD 掛接到 VM 並將磁碟區格式化。
- 將磁碟區掛接到 Pod。

為了使這個設定運作，OpenShift 需要在 Azure 中執行上述工作的權限。 為此,使用服務主體。 服務主體是 Azure Active Directory 中，獲授與資源權限的安全性帳戶。

服務主體必須具備儲存體帳戶和構成叢集之 VM 的存取權。 如果將所有 OpenShift 叢集資源都部署到單一資源群組中，則可以授與服務主體該資源群組的權限。

本指南描述如何建立與必要條件相關聯的成品。

> [!div class="checklist"]
> * 建立金鑰保存庫來管理 OpenShift 叢集的 SSH 金鑰。
> * 建立可供 Azure 雲端提供者使用的服務主體。

如果沒有 Azure 訂閱,請先創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

## <a name="sign-in-to-azure"></a>登入 Azure 
使用 [az login](/cli/azure/reference-index) 命令來登入 Azure 訂用帳戶並遵循畫面上的指示進行，或按一下 [試用]**** 來使用 Cloud Shell。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 應使用專用資源組來承載密鑰保管庫。 此群組與 OpenShift 叢集資源部署所在的資源群組分開。

下面的範例在*東區*位置建立名為*keyvaultrg*資源群組:

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫
使用 [az keyvault create](/cli/azure/keyvault) 命令來建立要儲存叢集之 SSH 金鑰的金鑰保存庫。 密鑰保管庫名稱必須全域唯一,並且必須為範本部署啟用,否則部署將失敗,出現「KeyVault 參數引用秘密檢索失敗」錯誤。

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

如需有關 Windows 上的 SSH 金鑰詳細資訊，請參閱[如何在 Windows 上建立 SSH 金鑰](/azure/virtual-machines/linux/ssh-from-windows)。 請務必以 OpenSSH 格式匯出私密金鑰。

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>將 SSH 私密金鑰儲存在 Azure Key Vault 中
OpenShift 部署會使用您建立的 SSH 金鑰來安全存取 OpenShift 主機。 若要啟用部署以安全地擷取 SSH 金鑰，請使用下列命令將金鑰儲存在金鑰保存庫中：

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>建立服務主體 
OpenShift 會使用使用者名稱與密碼或服務主體與 Azure 進行通訊。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 OpenShift 等自動化工具搭配使用。 您可以控制和定義關於服務主體可以在 Azure 中執行哪些作業的權限。 最好將服務主體的許可權範圍限定為特定資源組,而不是整個訂閱。

使用 [az ad sp create-for-rbac](/cli/azure/ad/sp) 建立服務主體，並輸出 OpenShift 所需的認證。

下面的範例創建一個服務主體,並將其參與者許可權分配給名為*openshiftrg*的資源組。

首先,建立名為*openshiftrg*資源群組 :

```azurecli
az group create -l eastus -n openshiftrg
```

建立服務主體：

```azurecli
az group show --name openshiftrg --query id
```

儲存命令的輸出,並代替下一個指令中的$scope

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

請注意從指令傳回的 appId 屬性與密碼:

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
 > 請務必記下安全密碼,因為無法再次檢索此密碼。

如需有關服務主體的詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>只適用於資源管理員樣本的先決條件

需要為 SSH 私密**金鑰 (sshPrivateKey)、** Azure AD 客戶端金鑰 (**aadClient Secret)、** OpenShift 管理員密碼 (**開放移位密碼**) 和紅帽訂閱管理器密碼或啟動金鑰 **(rhsmPasswordOr 啟動金鑰**) 建立秘密。  此外,如果使用自訂 TLS/SSL 憑證,則需要建立另外六個機密 -**路由快取檔案**、**路由檔案**、**金鑰檔、主檔案**,**主密鑰檔**與**主金鑰檔**。 **routingkeyfile**  這些參數將更詳細地解釋。

樣本引用特定的機密名稱,因此**必須**使用上面列出的粗體名稱(區分大小寫)。

### <a name="custom-certificates"></a>自訂憑證

默認情況下,範本將使用 OpenShift Web 主控台和路由網域的自簽名證書部署 OpenShift 叢集。 如果要使用自定義 TLS/SSL 證書,可以將「路由證書類型」設置為「自訂」,將「主證書類型」設置為「自訂」。  憑證需要以 .pem 格式的 CA、Cert 和 Key 檔。  可以將自定義證書用於其中一個,但不能對另一個證書使用。

您需要將這些檔案儲存在密鑰保管庫機密中。  使用與私鑰相同的密鑰保管庫。  範本無需為機密名稱增加 6 個輸入,而是硬編碼,可為每個 TLS/SSL 憑證檔使用特定機密名稱。  使用下表中的資訊儲存證書數據。

| 秘密名稱      | 憑證檔案   |
|------------------|--------------------|
| 馬斯特卡菲菲     | 主要的 CA 檔案     |
| 主檔案   | 主 CERT 檔案   |
| 主鍵檔案    | 主金鑰檔案    |
| 路由快取檔案    | 路由 CA 檔案    |
| 路由檔案  | 路由 CERT 檔案  |
| 路由金鑰檔案   | 路由金鑰檔案   |

使用 Azure CLI 創建機密。 以下為範例。

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>後續步驟

本文涵蓋下列主題：
> [!div class="checklist"]
> * 建立金鑰保存庫來管理 OpenShift 叢集的 SSH 金鑰。
> * 建立可供 Azure 雲端解決方案提供者使用的服務主體。

接下來，部署 OpenShift 叢集：

- [部署 OpenShift 容器平台](./openshift-container-platform-3x.md)
- [部署 OpenShift 容器平台自管理市場產品/服務](./openshift-container-platform-3x-marketplace-self-managed.md)
