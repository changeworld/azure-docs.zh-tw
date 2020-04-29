---
title: Azure 中 Linux Vm 的雲端 init 支援總覽
description: 在 Azure 中布建階段設定 VM 的雲端 init 功能總覽。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79465034"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure 中虛擬機器的雲端初始化支援
本文說明在 Azure 中布建時， [cloud init](https://cloudinit.readthedocs.io)用來設定虛擬機器（VM）或虛擬機器擴展集的支援。 一旦 Azure 布建資源之後，就會在第一次開機時執行這些雲端 init 設定。  

VM 布建是 Azure 會將您的 VM 建立參數值（例如主機名稱、使用者名稱、密碼等），並在啟動時提供給 VM 使用的程式。 「布建代理程式」將會使用這些值、設定 VM，並在完成時回報回來。 

Azure 支援兩個布建代理程式：[雲端 init](https://cloudinit.readthedocs.io)和[Azure Linux 代理程式（WALA）](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)。

## <a name="cloud-init-overview"></a>雲端-init 總覽
[雲端 init](https://cloudinit.readthedocs.io)是一種廣泛使用的方法，可在第一次啟動 Linux VM 時進行自訂。 您可以使用 cloud-init 來安裝封裝和寫入檔案，或者設定使用者和安全性。 因為在初次開機程序期間時會呼叫 Cloud-init，因此不需要使用任何額外的步驟或必要的代理程式，就可以套用您的設定。  如需如何正確格式化`#cloud-config`檔案或其他輸入的詳細資訊，請參閱[雲端初始化檔網站](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 檔案是以 base64 編碼的文字檔。

雲端 init 也可以跨散發套件運作。 例如，您不使用 **apt-get install** 或 **yum install** 來安裝套件。 您可以改為定義要安裝的套件清單。 雲端初始化會自動針對您選取的散發版本使用原生封裝管理工具。

我們一直積極地與背書的 Linux 發行版本合作夥伴合作，以便在 Azure Marketplace 中提供支援 Cloud-init 的映像。 這些映像會讓您的 Cloud-init 部署和設定順暢地與 VM 和虛擬機器擴展集運作。 一開始，我們會與背書的 Linux 散發版本合作夥伴和上游共同作業，以確保 Azure 上的 OS 能夠進行雲端 init 功能，然後更新套件並在散發版本套件存放庫中公開提供。 

有兩個階段可以讓已背書的 Linux 散發版本作業系統在 Azure 上使用，套件支援，然後支援映射：
* 「Azure 上的雲端初始化套件支援」記載哪些雲端 init 封裝已開始支援或處於預覽狀態，因此您可以在自訂映射中搭配 OS 使用這些套件。
* 如果映射已設定為使用雲端 init，則為「映射雲端初始化就緒」檔。


### <a name="canonical"></a>Canonical
| 發行者/版本| 產品 | SKU | 版本 | 映射雲端-初始化就緒 | Azure 上的雲端 init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|標準18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|標準16.04|UbuntuServer |16.04-LTS |最新 |是 | 是 |
|標準14.04|UbuntuServer |14.04.5-LTS |最新 |是 | 是 |

### <a name="rhel"></a>RHEL
| 發行者/版本 | 產品 | SKU | 版本 | 映射雲端-初始化就緒 | Azure 上的雲端 init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 | 是-封裝版本的支援： *18.2-1。 el7_6。 2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 是（請注意，這是預覽映射，一旦所有 RHEL 7.7 映射都支援雲端 init 之後，這將會在2020中移除，並會提供通知） | 是-封裝版本的支援： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7-RAW | n/a| 無-在2020年4月底結束的映射更新| 是-封裝版本的支援： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7-LVM | n/a| 無-在4月底完成的映射更新| 是-封裝版本的支援： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7.7 | n/a| 無-在4月底完成的映射更新 | 是-封裝版本的支援： *18.5-3. el7*|
|RedHat 7.7 |rhel-byos | rhel-lvm77 | n/a|無-在4月底完成的映射更新  | 是-封裝版本的支援： *18.5-3. el7*|

### <a name="centos"></a>CentOS

| 發行者/版本 | 產品 | SKU | 版本 | 映射雲端-初始化就緒 | Azure 上的雲端 init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7。7 |CentOS |7-CI |7.7.20190920 |是（請注意，這是預覽映射，一旦所有 CentOS 7.7 映射都支援雲端 init 之後，這將會在2020中移除，並會提供通知） | 是-封裝版本的支援： *18.5-3. el7. centos*|

* 將啟用雲端 init 的 CentOS 7.7 映射將于3月2020日更新 

### <a name="oracle"></a>Oracle

| 發行者/版本 | 產品 | SKU | 版本 | 映射雲端-初始化就緒 | Azure 上的雲端 init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7。7 |Oracle-Linux |77-ci |7.7.01| 預覽影像（請注意，這是預覽影像，一旦所有 Oracle 7.7 映射都支援雲端 init，這將會在2020中移除，並會提供通知） | 否，在預覽中，套件為： *18.5-3.0.1. el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
我們目前正致力於預覽支援，預計會在2月和3月2020更新。

目前 Azure Stack 將支援布建已啟用雲端初始化的映射。


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理程式 (WALA) 之間有哪些差異？
WALA 是 Azure 平臺專屬的代理程式，可用來布建和設定 Vm，以及處理[azure 擴充](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)功能。 

我們正在增強設定 Vm 以使用雲端 init （而不是 Linux 代理程式）的工作，以允許現有的雲端 init 客戶使用其目前的雲端 init 腳本，或新的客戶利用豐富的雲端 init 設定功能。 如果您目前已投資用於設定 Linux 系統的雲端初始化腳本，則**不需要進行其他設定**，就能啟用雲端初始化處理。 

雲端 init 無法處理 Azure 擴充功能，因此映射中仍需要 WALA 來處理延伸模組，但需要停用其布建程式碼，針對轉換成由雲端 init 布建的背書 Linux 散發版本映射，它們會安裝 WALA 並正確設定。

建立 VM 時，如果您在布建時未包含`--custom-data` Azure CLI 參數，則雲端 INIT 或 WALA 會採用布建 vm 所需的最低 VM 布建參數，並使用預設值完成部署。  如果您使用`--custom-data`參數來參考雲端 init 設定，則當 VM 開機時，您的自訂資料中包含的任何內容都會提供給雲端初始化使用。

套用至 Vm 的雲端 init 設定沒有時間限制，且不會因超時而導致部署失敗。這不是 WALA 的情況，如果您將 WALA 預設值變更為處理自訂資料，它就不能超過 VM 布建的總布建時間額度40mins （如果有的話），VM 建立將會失敗。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署支援 cloud-init 的虛擬機器
部署支援 cloud-init 的虛擬機器就像在部署期間參考支援 cloud-init 的發行版本一樣簡單。  Linux 發行版本維護者必須選擇啟用，並將 cloud-init 整合到其基礎 Azure 發行映像。 一旦確認您想要部署的映像支援 cloud-init，您就可以使用 Azure CLI 來部署映像。 

部署此映像的第一個步驟是使用 [az group create](/cli/azure/group) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 eastus  位置建立名為 myResourceGroup  的資源群組。

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
 
