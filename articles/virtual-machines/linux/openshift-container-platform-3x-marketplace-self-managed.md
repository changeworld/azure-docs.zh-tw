---
title: 在 Azure 中部署 OpenShift Container Platform 3.11 自我管理的 Marketplace 供應專案
description: 在 Azure 中部署 OpenShift Container Platform 3.11 自我管理的 Marketplace 供應專案。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 36adf35c5fbfc3e88b7d9af425ebabc852707e69
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374094"
---
# <a name="configure-prerequisites"></a>設定必要條件

在使用 Marketplace 供應專案在 Azure 中部署自我管理的 OpenShift 容器平臺3.11 叢集之前，必須先設定幾個必要條件。  如需建立 ssh 金鑰（不含複雜密碼）、Azure 金鑰保存庫、金鑰保存庫密碼和服務主體的指示，請參閱[OpenShift 必要條件](./openshift-container-platform-3x-prerequisites.md)一文。

 
## <a name="deploy-using-the-marketplace-offer"></a>使用 Marketplace 供應專案進行部署

將自我管理的 OpenShift 容器平臺3.11 叢集部署到 Azure 的最簡單方式，就是使用[Azure Marketplace 的供應](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)專案。

此選項是最簡單的，但它也具有有限的自訂功能。 Marketplace 供應專案會部署 OpenShift 容器平臺3.11.82，並包含下列設定選項：

- **主要節點**：具有可設定執行個體類型的三 (3) 個主要節點。
- **基礎節點**：具有可設定執行個體類型的三 (3) 個基礎節點。
- **節點**：節點數目（介於1到9之間）和實例類型是可設定的。
- **磁碟類型**：使用受控磁碟。
- **網路**功能：支援新的或現有的網路和自訂 CIDR 範圍。
- **CN**：可以啟用 CN。
- **計量**：可以啟用 Hawkular 計量。
- **記錄**：可以啟用 EFK 記錄。
- **Azure 雲端提供者**：預設為啟用，可以停用。

在 Azure 入口網站的左上方，按一下 [**建立資源**]，在搜尋方塊中輸入「openshift 容器平臺」，然後按 enter 鍵。

   ![新增資源搜尋](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

[結果] 頁面隨即開啟，並在清單中以**Red Hat OpenShift 容器平臺3.11 自我管理**。 

   ![新增資源搜尋結果](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

按一下 [供應專案] 以查看供應專案的詳細資料。 若要部署此供應專案，請按一下 [**建立**]。 將會出現輸入必要參數的 UI。 第一個畫面是 [**基本**] 分頁。

   ![供應專案封面頁面](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**基本概念**

若要取得任何輸入參數的說明，請將滑鼠停留在參數名稱旁邊的***i*** 。

輸入輸入參數的值，然後按一下 **[確定]**。

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| VM 系統管理員使用者名稱 | 要在所有 VM 實例上建立的系統管理員使用者 |
| 管理使用者的 SSH 公開金鑰 | 用來登入 VM 的 SSH 公用金鑰-不能有複雜密碼 |
| 訂用帳戶 | 要在其中部署叢集的 Azure 訂用帳戶 |
| 資源群組 | 為叢集資源建立新的資源群組，或選取現有的空白資源群組 |
| 位置 | 要在其中部署叢集的 Azure 區域 |

   ![供應專案基本功能分頁](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**基礎結構設定**

輸入輸入參數的值，然後按一下 **[確定]**。

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| OCP 叢集名稱前置詞 | 用來為所有節點設定主機名稱的叢集首碼。 介於1到20個字元之間 |
| 主要節點大小 | 接受預設的 VM 大小，或按一下 [**變更大小**] 以選取不同的 vm 大小。  針對您的工作負載選取適當的 VM 大小 |
| 基礎結構節點大小 | 接受預設的 VM 大小，或按一下 [**變更大小**] 以選取不同的 vm 大小。  針對您的工作負載選取適當的 VM 大小 |
| 應用程式節點數目 | 接受預設的 VM 大小，或按一下 [**變更大小**] 以選取不同的 vm 大小。  針對您的工作負載選取適當的 VM 大小 |
| 應用程式節點大小 | 接受預設的 VM 大小，或按一下 [**變更大小**] 以選取不同的 vm 大小。  針對您的工作負載選取適當的 VM 大小 |
| 防禦主機大小 | 接受預設的 VM 大小，或按一下 [**變更大小**] 以選取不同的 vm 大小。  針對您的工作負載選取適當的 VM 大小 |
| 新的或現有的虛擬網路 | 建立新的 vNet （預設值）或使用現有的 vNet |
| 選擇預設的 CIDR 設定或自訂 IP 範圍（CIDR） | 接受預設 CIDR 範圍，或選取 [**自訂 IP 範圍**]，然後輸入自訂 CIDR 資訊。  預設設定會建立具有 10.0.0.0/14 的 CIDR、具有 10.1.0.0/16 的主要子網、具有 10.2.0.0/16 的基礎網，以及具有 10.3.0.0/16 的 compute 和 cn 子網的 vNet |
| Key Vault 的資源組名 | 包含 Key Vault 的資源組名 |
| Key Vault 名稱 | 包含具有 ssh 私密金鑰之密碼的 Key Vault 名稱。  只允許英數位元和連字號，並介於3到24個字元之間 |
| 秘密名稱 | 包含 ssh 私密金鑰的密碼名稱。  只允許英數位元和連字號 |

   ![供應專案基礎結構分頁](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**變更大小**

若要選取不同的 VM 大小，請按一下 [***變更大小***]。  VM 選取範圍視窗隨即開啟。  選取您想要的 VM 大小，然後按一下 [**選取**]。

   ![選取 VM 大小](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**現有的虛擬網路**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| 現有的虛擬網路名稱 | 現有 vNet 的名稱 |
| 主要節點的子網名稱 | 主要節點的現有子網名稱。  必須包含至少16個 IP 位址，並遵循 RFC 1918 |
| 基礎節點的子網名稱 | 基礎節點的現有子網名稱。  必須至少包含32個 IP 位址，並遵循 RFC 1918 |
| 計算和 cn 節點的子網名稱 | 計算和 cn 節點的現有子網名稱。  必須至少包含32個 IP 位址，並遵循 RFC 1918 |
| 現有虛擬網路的資源群組 | 包含現有 vNet 的資源組名 |

   ![提供現有的基礎結構 vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**自訂 IP 範圍**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| 虛擬網路的位址範圍 | VNet 的自訂 CIDR |
| 包含主要節點之子網的位址範圍 | 主要子網的自訂 CIDR |
| 包含基礎結構節點之子網的位址範圍 | 基礎結構子網的自訂 CIDR |
| 包含 [計算] 和 [cn] 節點之子網的位址範圍 | 適用于計算和 cn 節點的自訂 CIDR |

   ![供應專案基礎結構自訂 IP 範圍](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

輸入輸入參數的值，然後按一下 **[確定]**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| OpenShift 管理使用者密碼 | 初始 OpenShift 使用者的密碼。  此使用者也會是叢集系統管理員 |
| 確認 OpenShift 管理使用者密碼 | 重新輸入 OpenShift 系統管理員使用者密碼 |
| Red Hat 訂用帳戶管理員使用者名稱 | 用來存取 Red Hat 訂用帳戶或組織識別碼的使用者名稱。  此認證可用來將 RHEL 實例註冊到您的訂用帳戶，且不會由 Microsoft 或 Red Hat 儲存 |
| Red Hat 訂用帳戶管理員使用者密碼 | 用來存取 Red Hat 訂用帳戶或啟用金鑰的密碼。  此認證可用來將 RHEL 實例註冊到您的訂用帳戶，且不會由 Microsoft 或 Red Hat 儲存 |
| Red Hat 訂用帳戶管理員 OpenShift 集區識別碼 | 包含 OpenShift 容器平臺權利的集區識別碼。 確定您有足夠的 OpenShift 容器平臺權利可以安裝叢集 |
| 適用于 Broker/主要節點的 Red Hat 訂用帳戶管理員 OpenShift 集區識別碼 | 包含 Broker/Master 節點之 OpenShift 容器平臺權利的集區識別碼。 請確定您有足夠的 OpenShift 容器平臺權利，可用於安裝叢集。 如果未使用 broker/主要集區識別碼，請輸入應用程式節點的集區識別碼 |
| 設定 Azure 雲端提供者 | 將 OpenShift 設定為使用 Azure 雲端提供者。 如果為永久磁片區使用 Azure 磁片連接，則為必要。  預設值為 Yes |
| Azure AD 服務主體用戶端識別碼 GUID | Azure AD 服務主體用戶端識別碼 GUID （也稱為 AppID）。 只有在將 Azure 雲端提供者設定為 **[是]** 時才需要 |
| Azure AD 服務主體用戶端識別碼密碼 | Azure AD 服務主體用戶端識別碼密碼。 只有在將 Azure 雲端提供者設定為 **[是]** 時才需要 |
 
   ![供應專案 OpenShift 分頁](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**其他設定**

[其他設定] 分頁可讓您設定適用于 glusterfs 儲存體、記錄、計量和路由器子網域的 CN。  預設不會安裝任何這些選項，而且會使用 nip.io 做為測試用途的路由器子網域。 啟用 CN 會安裝三個額外的計算節點，其中包含三個額外的連接磁片，將裝載 glusterfs pod。  

輸入輸入參數的值，然後按一下 **[確定]**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| 設定容器原生儲存體（CN） | 在 OpenShift 叢集中安裝 CN，並將其啟用為存放裝置。 如果 Azure 提供者已停用，將會是預設值 |
| 設定叢集記錄 | 將 EFK 記錄功能安裝到叢集中。  適當地調整基礎節點的大小以裝載 EFK pod |
| 設定叢集的計量 | 將 Hawkular 計量安裝到 OpenShift 叢集中。  適當地調整基礎節點的大小以裝載 Hawkular 計量 pod |
| 預設路由器子域 | 選取 [nipio] 進行測試或 [自訂]，以輸入您的專屬子域以用於生產環境 |
 
   ![提供額外的分頁](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**其他設定-額外的參數**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| CN節點大小 | 接受預設節點大小，或選取 [**變更大小**] 以選取新的 VM 大小 |
| 輸入您的自訂子域 | 自訂路由網域，用於透過 OpenShift 叢集上的路由器公開應用程式。  請務必建立適當的萬用字元 DNS 專案] |
 
   ![提供額外的 cn 安裝](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**總結**

此階段會進行驗證，以檢查核心配額是否足以部署為叢集選取的 Vm 總數。  檢查所有輸入的參數。  如果可以接受輸入，請按一下 **[確定]** 繼續進行。

   ![供應專案摘要分頁](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**買賣**

確認 [購買] 頁面上的連絡人資訊，然後按一下 [**購買**] 以接受使用規定，並開始部署 OpenShift 容器平臺叢集。

   ![供應專案購買分頁](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，請從部署的輸出區段中擷取連線。 使用**OpenShift 主控台 URL**，透過您的瀏覽器連線至 OpenShift 主控台。 您也可以透過 SSH 連線到防禦主機。 在以下範例中，管理員使用者名稱是 clusteradmin，而防禦公用 IP DNS FQDN 是 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，您可以使用 [az group delete](/cli/azure/group) 命令，移除資源群組、OpenShift 叢集和所有相關資源。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>後續步驟

- [部署後工作](./openshift-container-platform-3x-post-deployment.md)
- [針對 Azure 中的 OpenShift 部署進行疑難排解](./openshift-container-platform-3x-troubleshooting.md)
- [開始使用 OpenShift 容器平臺](https://docs.openshift.com)
- 
