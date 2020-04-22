---
title: 在 Azure 中部署 OpenShift 容器平臺 3.11 自我管理的市場產品
description: 在 Azure 中部署 OpenShift 容器平臺 3.11 自管理應用商店產品。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1cf6c7417aa86d47e59e08786e7807e32c175a25
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759567"
---
# <a name="configure-prerequisites"></a>設定必要條件

在使用應用商店產品/服務在 Azure 中部署自管理的 OpenShift 容器平臺 3.11 群集之前,必須配置一些先決條件。  閱讀[OpenShift 先決條件](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites)一文,瞭解建立 ssh 密鑰(不帶密碼短語)、Azure 密鑰保管庫、密鑰保管庫金鑰金鑰和服務主體的說明。

 
## <a name="deploy-using-the-marketplace-offer"></a>使用應用商店產品/服務進行部署

將自管理的 OpenShift 容器平臺 3.11 群集部署到 Azure 的最簡單方法是使用[Azure 應用商店產品/](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

此選項是最簡單的,但它也具有有限的自定義功能。 應用商店產品部署 OpenShift 容器平臺 3.11.82,包括以下設定選項:

- **主要節點**：具有可設定執行個體類型的三 (3) 個主要節點。
- **基礎節點**：具有可設定執行個體類型的三 (3) 個基礎節點。
- **節點**: 節點數(介於 1 和 9 之間)和實例類型是可配置的。
- **磁碟類型**：使用受控磁碟。
- **網路**:支援新的或現有的網路和自定義 CIDR 範圍。
- **CN**：可以啟用 CN。
- **指標**:可以啟用鷹派指標。
- **記錄記錄**:可以啟用 EFK 紀錄記錄。
- **Azure 雲提供者**:默認情況下啟用,可以禁用。

在 Azure 門戶的左上角,按下 **「創建資源**」,在搜尋框中輸入「開班容器平臺」,然後單擊 Enter。

   ![新資源搜尋](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

結果頁面將在清單中開啟**紅帽 OpenShift 容器平臺 3.11 自我管理**。 

   ![新的資源搜尋結果](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

單擊產品/服務以查看優惠的詳細資訊。 要部署此產品/服務,請單擊"**創建**"。 將顯示要輸入必要參數的 UI。 第一個螢幕是 **「基本」** 邊欄選項卡。

   ![優惠標題頁](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**基本概念**

要獲取有關任何輸入參數的説明,請將滑鼠懸停在參數名稱旁邊的***i***上。

輸入輸入參數的值,然後單擊 **"確定**"。

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| VM 管理員使用者名稱 | 要在所有 VM 實體建立的管理員使用者 |
| 管理者使用者的 SSH 公開金鑰 | 用於登入 VM 的 SSH 公開金鑰 ─不得有密碼 |
| 訂用帳戶 | 要將叢集部署到 |
| 資源群組 | 建立新資源群組或為群組資源選擇現有空資源群組 |
| Location | 要將叢集部署到 |

   ![提供基本刀片](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**基礎設施設定**

輸入輸入參數的值,然後單擊 **"確定**"。

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| OCP 叢集名稱前置字 | 用於配置所有節點的主機名的群集首碼。 1 到 20 個字元之間 |
| 主節點大小 | 接受預設 VM 大小或按下 **「更改大小」** 以選擇其他 VM 大小。  為工作負載選擇適當的 VM 大小 |
| 基礎結構節點大小 | 接受預設 VM 大小或按下 **「更改大小」** 以選擇其他 VM 大小。  為工作負載選擇適當的 VM 大小 |
| 應用程式節點數 | 接受預設 VM 大小或按下 **「更改大小」** 以選擇其他 VM 大小。  為工作負載選擇適當的 VM 大小 |
| 應用程式節點大小 | 接受預設 VM 大小或按下 **「更改大小」** 以選擇其他 VM 大小。  為工作負載選擇適當的 VM 大小 |
| 馬巴塞機大小 | 接受預設 VM 大小或按下 **「更改大小」** 以選擇其他 VM 大小。  為工作負載選擇適當的 VM 大小 |
| 新增或現有虛擬網路 | 建立新的 vNet(預設)或使用現有 vNet |
| 選擇預設 CIDR 設定或自訂 IP 範圍 (CIDR) | 接受預設 CIDR 範圍或選擇**自訂 IP 範圍**並輸入自訂 CIDR 資訊。  預設設定將創建具有 10.0.0.0/14 的 CIDR 的 vNet,使用 10.1.0.0/16 的主子網、包含 10.2.0.0/16 的紅外線網以及 10.3.0.0/16 的計算和 cns 子網 |
| 金鑰保存庫資源群組名稱 | 包含金鑰保存庫的資源群組的名稱 |
| Key Vault 名稱 | 包含 ssh 私密金鑰的機密的金鑰保管庫的名稱。  只允許字母數字字元和破折號,介於 3 到 24 個字元之間 |
| 秘密名稱 | 包含 ssh 私鑰的機密的名稱。  只允許字母數字字元與破折號 |

   ![提供基礎架構刀片](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**改變大小**

要選擇其他 VM 大小,請按下「***更改大小***」 。  將打開 VM 選擇視窗。  選擇所需的 VM 大小,然後按下 **「選擇**」 。

   ![選擇 VM 大小](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**現有虛擬網路**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| 現有虛擬網路名稱 | 現有 vNet 的名稱 |
| 主節點的子網名稱 | 主節點的現有子網的名稱。  需要包含至少 16 個 IP 位址,並遵循 RFC 1918 |
| 紅外線節點的子網名稱 | 現有子網的子網的名稱為紅外線節點。  需要包含至少 32 個 IP 位址,並遵循 RFC 1918 |
| 計算和 cns 節點子網名稱 | 用於計算和 cns 節點的現有子網的名稱。  需要包含至少 32 個 IP 位址,並遵循 RFC 1918 |
| 現有虛擬網路的資源群組 | 包含現有 vNet 的資源群組名稱 |

   ![提供基礎架構現有 vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**自訂 IP 範圍**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| 虛擬網路的位址範圍 | vNet 的自訂 CIDR |
| 引入主節點的子網的位址範圍 | 主子網路的自訂 CIDR |
| 包含基礎結構節點的子網路的位址範圍 | 基礎結構子網路的自訂 CIDR |
| 包含計算節點和 cns 節點的子網的位址範圍 | 計算節點和 cns 節點的自訂 CIDR |

   ![提供基礎架構自訂 IP 範圍](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

輸入輸入參數的值,然後單擊"**確定"**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| 開放移位管理員使用者密碼 | 初始 OpenShift 使用者的密碼。  此使用者也將是群集管理員 |
| 確認開放移位管理員使用者密碼 | 重新鍵入 OpenShift 管理員使用者密碼 |
| 紅帽訂閱管理員使用者名稱 | 訪問紅帽訂閱或組織 ID 的使用者名稱。  此認證用於將 RHEL 實體註冊到您的訂閱,並且不會由 Microsoft 或紅帽儲存 |
| 紅帽訂閱管理員密碼 | 訪問紅帽訂閱或啟動密鑰的密碼。  此認證用於將 RHEL 實體註冊到您的訂閱,並且不會由 Microsoft 或紅帽儲存 |
| 紅帽訂閱管理員開啟移位池 ID | 包含 OpenShift 容器平台授權的池 ID。 確保您有足夠的 OpenShift 容器平台權利來安裝叢集 |
| 紅帽訂閱管理員為經紀商/主節點打開 Shift 池 ID | 包含代理/主節點的 OpenShift 容器平台許可權的池 ID。 確保您有足夠的 OpenShift 容器平臺權利來安裝群集。 如果不使用代理/主池 ID,請輸入應用程式節點的池 ID |
| 設定 Azure 雲提供者 | 將 OpenShift 設定為使用 Azure 雲端提供程式。 如果對持久卷使用 Azure 磁碟附加,則需要。  默認值為"是" |
| Azure AD 服務主客戶端 ID GUID | Azure AD 服務主體客戶端 ID GUID - 也稱為 AppID。 僅當將 Azure 雲提供程式設定為 **「是**」時才需要 |
| Azure AD 服務主體客戶端 ID 金鑰 | Azure AD 服務主體客戶端 ID 金鑰。 僅當將 Azure 雲提供程式設定為 **「是**」時才需要 |
 
   ![提供開放式移位刀片](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**其他設定**

"附加設定"邊欄選項卡允許配置 CNS 用於 glusterfs 儲存、日誌記錄、指標和路由器子域。  默認值不會安裝任何這些選項,並將nip.io用作路由器子域進行測試。 啟用 CNS 將安裝另外三個計算節點,以及另外三個附加磁碟,這些磁碟將承載 glusterfs pod。  

輸入輸入參數的值,然後單擊"**確定"**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| 設定容器本機儲存 (CNS) | 在 OpenShift 群集中安裝 CNS 並將其啟用為存儲。 如果關閉 Azure 提供者,則為預設值 |
| 設定叢集記錄記錄 | 將 EFK 紀錄記錄功能安裝到群集中。  適當調整紅外節點的大小,以承載 EFK pod |
| 設定叢集的指標 | 將 Hawkular 指標安裝到 OpenShift 群集中。  適當調整體形節點的大小,以承載 Hawkular 指標窗格 |
| 預設路由器子域 | 選擇測試或自訂的 nipio 以輸入您自己的子域進行生產 |
 
   ![提供其他刀片](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**附加設定 - 額外參數**

| 輸入參數 | 參數描述 |
|-----------------------|-----------------|
| (中新社)節點大小 | 接受預設節點大小或選擇 **「更改大小」** 以選擇新的 VM 大小 |
| 輸入自訂子網域 | 用於透過 OpenShift 群集上的路由器公開應用程式的自訂路由域。  請務必建立相應的通配符 DNS 條目* |
 
   ![提供額外的 cns 安裝](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**摘要**

在此階段進行驗證,以檢查核心配額足以部署為群集選擇的 VM 總數。  查看輸入的所有參數。  如果輸入可以接受,請單擊 **「確定」** 以繼續。

   ![提供摘要邊欄選項卡](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**買**

確認"購買"頁面上的聯繫資訊,然後按**下 「購買**」以接受 OpenShift 容器平臺群集的使用條款並開始部署。

   ![提供購買刀片](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，請從部署的輸出區段中擷取連線。 使用**OpenShift 主控台網址**使用瀏覽器連接到 OpenShift 主控台。 您也可以 SSH 到堡壘主機。 在以下範例中，管理員使用者名稱是 clusteradmin，而防禦公用 IP DNS FQDN 是 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

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
- [使用 OpenShift 容器平台入門](https://docs.openshift.com)
- 