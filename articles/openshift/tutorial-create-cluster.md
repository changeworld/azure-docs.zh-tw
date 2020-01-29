---
title: 教學課程 - 建立 Azure Red Hat OpenShift 叢集
description: 了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 0e6aecccc19572ee980feb4d816fae1f2b0101b7
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274896"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>教學課程：建立 Azure Red Hat OpenShift 叢集

本教學課程是一個系列的第一部分。 您將了解如何使用 Azure CLI 建立 Microsoft Azure Red Hat OpenShift 叢集、進行調整，然後予以刪除來清除資源。

在系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立 Azure Red Hat OpenShift 叢集

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 建立 Azure Red Hat OpenShift 叢集
> * [調整 Azure Red Hat OpenShift 叢集](tutorial-scale-cluster.md)
> * [刪除 Azure Red Hat OpenShift 叢集](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prerequisites

> [!IMPORTANT]
> 本教學課程需要 Azure CLI 2.0.65 版。

開始進行本教學課程之前：

請確定您已[設定開發環境](howto-setup-environment.md)，其中包括：
- 安裝最新的 CLI (2.0.65 版或更新版本)
- 建立租用戶 (如果您還沒有的話)
- 建立 Azure 應用程式物件 (如果您還沒有的話)
- 建立安全性群組
- 建立用來登入叢集的 Active Directory 使用者。

## <a name="step-1-sign-in-to-azure"></a>步驟 1:登入 Azure

如果您在本機執行 Azure CLI，請開啟 Bash 命令殼層並執行 `az login` 來登入 Azure。

```bash
az login
```

 如果您可存取多個訂用帳戶，請執行 `az account set -s {subscription ID}` 並以您要使用的訂用帳戶取代 `{subscription ID}`。

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>步驟 2:建立 Azure Red Hat OpenShift 叢集

在 Bash 命令視窗中，設定下列變數：

> [!IMPORTANT]
> 請為您的叢集選擇唯一且全部小寫的名稱，否則叢集建立將會失敗。

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

選擇要建立叢集的位置。 如需在 Azure 上支援 OpenShift 的 Azure 區域清單，請參閱[支援的區域](supported-resources.md#azure-regions)。 例如： `LOCATION=eastus` 。

```bash
LOCATION=<location>
```

將 `APPID` 設定為您在[建立 Azure AD 應用程式註冊](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)的步驟 5 中儲存的值。

```bash
APPID=<app ID value>
```

將 'GROUPID' 設定為您在[建立 Azure AD 安全性群組](howto-aad-app-configuration.md#create-an-azure-ad-security-group)的步驟 10 中儲存的值。

```bash
GROUPID=<group ID value>
```

將 `SECRET` 設定為您在[建立用戶端密碼](howto-aad-app-configuration.md#create-a-client-secret)的步驟 8 中儲存的值。

```bash
SECRET=<secret value>
```

將 `TENANT` 設定為您在[建立新的租用戶](howto-create-tenant.md#create-a-new-azure-ad-tenant)步驟 7 中儲存的租用戶識別碼值。

```bash
TENANT=<tenant ID>
```

建立叢集的資源群組。 從您用來定義上述變數的相同 Bash 殼層執行下列命令：

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>選擇性：將叢集的虛擬網路連線到現有的虛擬網路

如果您不需透過對等互連將所建立叢集的虛擬網路 (VNET) 連線到現有的 VNET，請略過此步驟。

如果對等互連至預設訂用帳戶外部的網路，則您也需要在該訂用帳戶中，註冊提供者 Microsoft.ContainerService。 若要完成這個操作，請在該訂用帳戶中執行以下命令。 或者，如果您對等互連的 VNET 位於相同訂用帳戶中，可以略過註冊步驟。

`az provider register -n Microsoft.ContainerService --wait`

首先，取得現有 VNET 的識別碼。 識別碼的格式會是：`/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`。

如果您不知道網路名稱或現有 VNET 所屬的資源群組，請移至[虛擬網路刀鋒視窗](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks)，然後按一下您的虛擬網路。 [虛擬網路] 頁面隨即出現，其中會列出網路名稱及其所屬的資源群組。

在 BASH 殼層中使用下列 CLI 命令定義 VNET_ID 變數：

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

例如： `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>選擇性：將叢集連線至 Azure 監視

首先，取得**現有**記錄分析工作區的識別碼。 識別碼的格式會是：

第 1 課：建立 Windows Azure 儲存體物件`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`。

如果您不知道現有記錄分析工作區所屬的記錄分析工作區名稱或資源群組，請移至[記錄分析工作區](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces)，然後按一下您的記錄分析工作區。 記錄分析工作區頁面隨即出現，其中會列出工作區名稱及其所屬的資源群組。

_若要建立記錄分析工作區，請參閱[建立記錄分析工作區](../azure-monitor/learn/quick-create-workspace-cli.md)_

在 BASH 殼層中使用下列 CLI 命令定義 WORKSPACE_ID 變數：

```bash
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>建立叢集

您現在可以開始建立叢集。 下列命令會在指定的 Azure AD 租用戶中建立叢集，並指定要作為安全性主體的 Azure AD 應用程式物件和密碼，以及對叢集具有管理員存取權的成員所屬的安全性群組。

> [!IMPORTANT]
> 在建立叢集之前，請確定您已正確地為 Azure AD 應用程式新增適當的權限，如同[此處所述](howto-aad-app-configuration.md#add-api-permissions)

如果您的叢集**未**與虛擬網路對等互連，或您**不**想要使用 Azure 監視，請使用下列命令：

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

如果您的叢集與虛擬網路**對等互連**，請使用下列加上 `--vnet-peer` 旗標的命令：

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

如果您**想要**將 Azure 監視用於您的叢集，請使用下列命令來新增 `--workspace-id` 旗標：

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> 如果您收到的錯誤表示主機名稱不可用，則可能是因為您的叢集名稱並非唯一。 嘗試刪除原始應用程式註冊，並使用不同的叢集名稱重新執行 [建立新的應用程式註冊](howto-aad-app-configuration.md#create-an-azure-ad-app-registration) 中的步驟 (省略建立新的使用者和安全性群組的步驟)。




`az openshift create` 將在幾分鐘之後完成。

### <a name="get-the-sign-in-url-for-your-cluster"></a>取得叢集的登入 URL

執行下列命令以取得登入叢集的 URL：

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

在輸出中尋找 `publicHostName`，例如：`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

叢集的登入 URL 將是 `https://` 加上尾隨的 `publicHostName` 值。  例如： `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io` 。  您將在下一個步驟中以此 URI 作為應用程式註冊重新導向 URI 的一部分。

## <a name="step-3-update-your-app-registration-redirect-uri"></a>步驟 3：更新您的應用程式註冊重新導向 URI

現在，您已取得叢集的登入 URL，接下來請設定應用程式註冊重新導向 URI：

1. 開啟 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 刀鋒視窗。
2. 按一下您的應用程式註冊物件。
3. 按一下 [新增重新導向 URI]  。
4. 確定 [類型]  為 [Web]  ，並使用下列模式設定 [重新導向 URI]  ：`https://<public host name>/oauth2callback/Azure%20AD`。 例如： `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. 按一下 [儲存] 

## <a name="step-4-sign-in-to-the-openshift-console"></a>步驟 4：登入 OpenShift 主控台

您現在即可登入新叢集的 OpenShift 主控台。 [OpenShift Web 主控台](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html)可讓您以視覺化方式呈現、瀏覽及管理 OpenShift 專案的內容。

您需要全新的瀏覽器執行個體，且該執行個體並未快取您平常用來登入 Azure 入口網站的身分識別。

1. 開啟 *incognito* 視窗 (Chrome) 或 *InPrivate* 視窗 (Microsoft Edge)。
2. 瀏覽至您先前取得的登入 URL，例如：`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

使用您在[建立新的 Azure Active Directory 使用者](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user)的步驟 3 中建立的使用者名稱登入。

[要求的權限]  對話方塊隨即出現。 按一下 [代表您的組織同意]  ，然後按一下 [接受]  。

您現在已登入叢集主控台。

![OpenShift 叢集主控台的螢幕擷取畫面](./media/aro-console.png)

 深入了解如何[使用 OpenShift 主控台](https://docs.openshift.com/aro/getting_started/developers_console.html)，以建立 [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) 文件中的映像。

## <a name="step-5-install-the-openshift-cli"></a>步驟 5：安裝 OpenShift CLI

[OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (或 OC 工具  ) 會提供用於管理應用程式和較低層級公用程式的命令，以便與 OpenShift 叢集的各種元件互動。

在 OpenShift 主控台中，按一下右上角您登入名稱旁邊的問號，然後選取 [命令列工具]  。  請遵循 [最新版本]  連結來下載和安裝 Linux、 MacOS 或 Windows 支援的 oc CLI。

> [!NOTE]
> 如果看不到右上角的問號圖示，請從左上角下拉式清單中選取 [服務目錄]  或 [應用程式主控台]  。
>
> 或者，您可以直接[下載 oc CLI](https://www.okd.io/download.html)。

[命令列工具]  頁面提供 `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>` 形式的命令。  按一下 [複製到剪貼簿]  按鈕來複製此命令。  在終端機視窗中，[設定您的路徑](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli)以包含您本機安裝的 oc 工具。 然後使用您複製的 oc CLI 命令登入叢集。

如果您無法使用上述步驟取得權杖值，請從 `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request` 取得權杖值。

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure Red Hat OpenShift 叢集

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [調整 Azure Red Hat OpenShift 叢集](tutorial-scale-cluster.md)
