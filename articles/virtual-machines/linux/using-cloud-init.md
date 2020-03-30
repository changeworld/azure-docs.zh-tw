---
title: Azure 中對 Linux VM 的雲 init 支援概述
description: 在 Azure 中的預配時間配置 VM 的雲 init 功能概述。
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465034"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>對 Azure 中的虛擬機器的雲 init 支援
本文介紹[雲 init](https://cloudinit.readthedocs.io)在 Azure 中的預配時間配置虛擬機器 （VM） 或虛擬機器縮放集的支援。 Azure 預配資源後，這些雲 init 配置將首先啟動。  

VM 預配是 Azure 將傳遞 VM Create 參數值（如主機名稱、使用者名、密碼等）的過程，並在啟動時將其提供給 VM。 "預配代理"將使用這些值、配置 VM 並在完成後報告。 

Azure 支援兩個預配代理[雲 init](https://cloudinit.readthedocs.io)和[Azure Linux 代理 （WALA）。](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)

## <a name="cloud-init-overview"></a>雲 init 概述
[雲init](https://cloudinit.readthedocs.io)是一種廣泛使用的方法，用於自訂Linux VM，因為它第一次啟動。 您可以使用 cloud-init 來安裝封裝和寫入檔案，或者設定使用者和安全性。 因為在初次開機程序期間時會呼叫 Cloud-init，因此不需要使用任何額外的步驟或必要的代理程式，就可以套用您的設定。  有關如何正確設置`#cloud-config`檔或其他輸入的格式的詳細資訊，請參閱[雲 init 文檔網站](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 檔案是以 base64 編碼的文字檔。

雲 init 還跨發行版本工作。 例如，您不使用 **apt-get install** 或 **yum install** 來安裝套件。 您可以改為定義要安裝的套件清單。 雲 init 會自動使用本機包管理工具進行您選擇的發行版本。

我們一直積極地與背書的 Linux 發行版本合作夥伴合作，以便在 Azure Marketplace 中提供支援 Cloud-init 的映像。 這些映像會讓您的 Cloud-init 部署和設定順暢地與 VM 和虛擬機器擴展集運作。 最初，我們與認可的 Linux 分型合作夥伴和上游協作，以確保在 Azure 上的作業系統中使用雲 init 函數，然後更新包並在分眾包存儲庫中公開提供。 

在 Azure 上向支援的 Linux 發行版本作業系統提供雲 init 分為兩個階段，包括包支援，然後是映射支援：
* 支援或預覽中的雲 init 包在 Azure 文檔上的"雲 init 包支援"，因此可以在自訂映射中將這些包與作業系統一起使用。
* 如果映射已配置為使用雲 init，則"映射雲 init 就緒"文檔。


### <a name="canonical"></a>Canonical
| 發行者/版本| 供應項目 | SKU | 版本 | 圖像雲 init 就緒 | Azure 上的雲 init 包支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|規範 18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|規範 16.04|UbuntuServer |16.04-LTS |最新 |是 | 是 |
|規範 14.04|UbuntuServer |14.04.5-LTS |最新 |是 | 是 |

### <a name="rhel"></a>RHEL
| 發行者/版本 | 供應項目 | SKU | 版本 | 圖像雲 init 就緒 | Azure 上的雲 init 包支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 | 是 - 來自包版本的支援 *：18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 是的（請注意，這是一個預覽圖像，一旦所有 RHEL 7.7 圖像都支援雲 init，這將在 2020 年年中刪除，將發出通知） | 是 - 包版本支援： *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-RAW | n/a| 否 - 2020 年 4 月底完成的圖像更新| 是 - 包版本支援： *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | n/a| 否 - 圖像更新，于 4 月底完成| 是 - 包版本支援： *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | n/a| 否 - 圖像更新，于 4 月底完成 | 是 - 包版本支援： *18.5-3.el7*|
|RedHat 7.7 |雷爾-比奧斯 | rhel-lvm77 | n/a|否 - 圖像更新，于 4 月底完成  | 是 - 包版本支援： *18.5-3.el7*|

### <a name="centos"></a>CentOS

| 發行者/版本 | 供應項目 | SKU | 版本 | 圖像雲 init 就緒 | Azure 上的雲 init 包支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|開放邏輯 7.7 |CentOS |7-CI |7.7.20190920 |是的（請注意，這是一個預覽圖像，一旦所有 CentOS 7.7 圖像都支援雲 init，這將在 2020 年年中刪除，將發出通知） | 是 - 從包版本支援： *18.5-3.el7.centos*|

* 2020 年 3 月將在此處更新將啟用雲的 CentOS 7.7 圖像 

### <a name="oracle"></a>Oracle

| 發行者/版本 | 供應項目 | SKU | 版本 | 圖像雲 init 就緒 | Azure 上的雲 init 包支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|甲骨文 7.7 |Oracle-Linux |77-ci |7.7.01| 預覽圖像（請注意，這是預覽圖像，一旦所有 Oracle 7.7 圖像都支援雲 init，這將在 2020 年年中刪除，將發出通知） | 否，在預覽中，包是： *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>debian & 蘇塞 SLES
我們目前正在努力預覽支援，預計在 2020 年 2 月和 3 月更新。

目前，Azure 堆疊將支援預配啟用雲的映射。


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理程式 (WALA) 之間有哪些差異？
WALA 是特定于 Azure 的平臺代理，用於預配和配置 VM 以及處理[Azure 擴展](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。 

我們正在加強將 VM 配置為使用雲 init 而不是 Linux 代理的任務，以便允許現有雲 init 客戶使用其當前的雲 init 腳本，或者新客戶利用豐富的雲 init 配置功能。 如果您對用於配置 Linux 系統的雲 init 腳本進行了現有投資，則**無需其他設置**即可啟用雲 init 處理它們。 

雲 init 無法處理 Azure 擴展，因此映射中仍需要 WALA 來處理擴展，但需要禁用其預配代碼，對於被雲 init 轉換為預配的支援的 Linux 發行版本映射，它們將具有 WALA安裝並正確設置。

創建 VM 時，如果在預配時未包括 Azure `--custom-data` CLI 交換器，則雲 init 或 WALA 會採用預配 VM 和完成預設部署所需的最少 VM 預配參數。  如果使用`--custom-data`交換器引用雲 init 配置，則自訂資料中包含的任何內容都將在 VM 啟動時可供雲 init 使用。

應用於 VM 的雲 init 配置沒有時間限制，也不會通過超時導致部署失敗。WALA 的情況並非如此，如果將 WALA 預設值更改為處理自訂資料，則不能超過 40 分鐘的總 VM 預配時間余量，如果是，VM Create 將失敗。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署支援 cloud-init 的虛擬機器
部署支援 cloud-init 的虛擬機器就像在部署期間參考支援 cloud-init 的發行版本一樣簡單。  Linux 發行版本維護者必須選擇啟用，並將 cloud-init 整合到其基礎 Azure 發行映像。 一旦確認您想要部署的映像支援 cloud-init，您就可以使用 Azure CLI 來部署映像。 

部署此映像的第一個步驟是使用 [az group create](/cli/azure/group) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下面的示例在*東部*位置創建名為*myResourceGroup*的資源組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
下一個步驟是在您目前的殼層中建立名為 *cloud-init.txt* 的檔案，並貼上下列設定。 針對此案例，在 Cloud Shell 中 (而不是本機電腦上) 建立該檔案。 您可以使用任何您想要的編輯器。 輸入 `sensible-editor cloud-init.txt` 可建立檔案，並查看可用的編輯器清單。 建議首先選擇使用 **nano** 編輯器。 請確定已正確複製整個 cloud-init 檔案，特別是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
按下 `ctrl-X` 以結束檔案、輸入 `y` 以儲存檔案，然後按下 `enter` 以在結束時確認檔案名稱。

最後一個步驟是使用 [az vm create](/cli/azure/vm) 命令來建立 VM。 

下列範例會建立名為 *centos74* 的 VM，並建立 SSH 金鑰 (如果它們不存在於預設金鑰位置)。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。  使用 `--custom-data` 參數以傳入 cloud-init 組態檔。 如果您將檔案儲存於目前工作目錄之外的位置，請提供 cloud-init.txt** 組態的完整路徑。 下列範例會建立名為 *centos74* 的 VM：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

建立 VM 後，Azure CLI 會顯示您部署專屬的資訊。 記下 `publicIpAddress`。 此位址用來存取 VM。  系統需要花一些時間建立 VM、安裝套件以及啟動應用程式。 在 Azure CLI 將您返回提示字元之後，背景工作會繼續執行。 您可以透過 SSH 連線到 VM，並使用「疑難排解」一節中所述的步驟來檢視 cloud-init 記錄。 

## <a name="troubleshooting-cloud-init"></a>針對 cloud-init 進行疑難排解
一旦佈建 VM，Cloud-init 將會執行 `--custom-data` 中定義的所有模組和指令碼，以設定 VM。  如果您需要針對設定中的任何錯誤或遺漏進行疑難排解，則需要在 cloud-init 記錄 (位於 **/var/log/cloud-init.log**) 中搜尋模組名稱 (例如 `disk_setup` 或 `runcmd`)。

> [!NOTE]
> 並非每個模組失敗都會導致嚴重的 cloud-init 整體設定失敗。 例如，使用 `runcmd` 模組時，如果指令碼失敗，cloud-init 會因為執行了 runcmd 模組而仍然報告佈建成功，。

如需 cloud-init 記錄的詳細資訊，請參閱 [cloud-init 文件](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) \(英文\) 

## <a name="next-steps"></a>後續步驟
如需設定變更的 cloud-init 範例，請參閱下列文件：
 
- [將其他 Linux 使用者新增至虛擬機器](cloudinit-add-user.md)
- [執行套件管理員以便在初次開機時更新現有的套件](cloudinit-update-vm.md)
- [變更虛擬機器本機的主機名稱](cloudinit-update-vm-hostname.md) 
- [安裝應用程式套件、更新組態檔案，以及插入金鑰](tutorial-automate-vm-deployment.md)
 
