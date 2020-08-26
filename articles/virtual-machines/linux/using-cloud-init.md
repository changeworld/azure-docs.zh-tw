---
title: Azure 中適用於 Linux VM 的 cloud-init 支援概觀
description: 在 Azure 中於佈建階段設定 VM 的 cloud-init 功能概觀。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/15/2020
ms.author: danis
ms.openlocfilehash: 7ddbb48f3598780988feb25a11729a5086d31fde
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869264"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure 中適用於虛擬機器的 cloud-init 支援
此文章說明針對 [cloud-init](https://cloudinit.readthedocs.io) \(英文\) 存在的支援，以便在 Azure 中於佈建階段設定虛擬機器 (VM) 或虛擬機器擴展集。 一旦 Azure 佈建資源，這些 cloud-init 設定就會在初次開機時執行。  

VM 佈建是 Azure 向下傳遞您的 VM Creater 參數值 (例如主機名稱、使用者名稱、密碼等)，並在 VM 開機時為其加以提供的程序。 「佈建代理程式」將會使用那些值，設定 VM，然後在完成時回報。 

Azure 支援兩種佈建代理程式，[cloud-init](https://cloudinit.readthedocs.io) \(英文\) 和 [Azure Linux 代理程式 (WALA)](../extensions/agent-linux.md) \(部分機器翻譯\)。

## <a name="cloud-init-overview"></a>cloud-init 概觀
[cloud-init](https://cloudinit.readthedocs.io) \(英文\) 時常用來對初次開機的 Linux VM 進行自訂。 您可以使用 cloud-init 來安裝封裝和寫入檔案，或者設定使用者和安全性。 因為在初次開機程序期間時會呼叫 Cloud-init，因此不需要使用任何額外的步驟或必要的代理程式，就可以套用您的設定。  如需如何正確地設定 `#cloud-config` 檔案或其他輸入之格式的詳細資訊，請參閱 [cloud-init 文件網站](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data) \(英文\)。  `#cloud-config` 檔案是以 base64 編碼的文字檔。

cloud-init 也可在不同的發行版本上運作。 例如，您不使用 **apt-get install** 或 **yum install** 來安裝套件。 您可以改為定義要安裝的套件清單。 cloud-init 會針對您選取的發行版本，自動使用原生的套件管理工具。

我們正積極與我們背書的 Linux 發行版本合作夥伴合作，在 Azure Marketplace 中提供已啟用雲端初始化的映射。 這些映像會讓您的 Cloud-init 部署和設定順暢地與 VM 和虛擬機器擴展集運作。 一開始，我們會與經過背書的 Linux 發行版本合作夥伴及上游合作，以確保 cloud-init 能在 Azure 上與 OS 搭配運作，然後便會更新套件，並在發行版本套件存放庫中公開提供。 

在 Azure 上於經過背書的 Linux 發行版本 OS 上提供 cloud-init 有兩個階段：套件支援，然後映像支援：
* 「Azure 上的 cloud-init 套件支援」會記載接下來有哪些 cloud-init 套件會受到支援或處於預覽狀態，讓您可以在自訂映像中將這些套件與 OS 搭配使用。
* 「映像 cloud-init 就緒」會記載映像是否已設定為可以使用 cloud-init。


### <a name="canonical"></a>Canonical
| 發行者 / 版本| 供應項目 | SKU | 版本 | 映像 cloud-init 就緒 | Azure 上的 cloud-init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|Canonical 18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|Canonical 16.04|UbuntuServer |16.04-LTS |最新 |是 | 是 |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |最新 |是 | 是 |

### <a name="rhel"></a>RHEL
| 發行者 / 版本 | 供應項目 | SKU | 版本 | 映像 cloud-init 就緒 | Azure 上的 cloud-init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 | 是 - 從下列套件版本開始支援：*18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 是 (注意：這是預覽映射 **，不能再使用** ，這將于2020年9月1日移除)  | N/A |
|RedHat 7.7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | 是 | 是 - 從下列套件版本開始支援：*18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | 是 | 是 - 從下列套件版本開始支援：*18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7-LVM | 7.7.2020051921 | 是 | 是 - 從下列套件版本開始支援：*18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | 是 | 是 - 從下列套件版本開始支援：*18.5-6.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | 是  | 是 - 從下列套件版本開始支援：*18.5-6.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | 是 (注意：這是預覽映射，一旦所有 RHEL 8.1 映射都支援 cloud init 之後，這將會在 8 2020 月1日之前移除)  | 否，預計於 2020 年 6 月完整支援|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | 是 (注意：這是預覽映射，一旦所有 RHEL 8.1 映射都支援 cloud init 之後，這將會在 8 2020 月1日之前移除)  | 否，預計於 2020 年 6 月完整支援 |

* 所有 RedHat： RHEL 7.8 和 8.2 (Gen1 和 Gen2) 映射都會使用雲端初始化進行布建。

### <a name="centos"></a>CentOS

| 發行者 / 版本 | 供應項目 | SKU | 版本 | 映像 cloud-init 就緒 | Azure 上的 cloud-init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |是 (注意：這是預覽映射 **，不能再使用** ，這將于2020年9月1日移除)  | N/A |
|OpenLogic 7.7 |CentOS | 7.7 |7.7.2020062400 |是 | 是-套件版本的支援： `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2)  |CentOS | 7_7-gen2 |7.7.2020062401 |是 | 是-套件版本的支援： `18.5-6.el7.centos.5`|
|OpenLogic 7.7 |CentOS-HPC | 7.7 |7.6.2020062600 |是 | 是-套件版本的支援： `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2)  |CentOS-HPC | 7_7-gen2 |7.6.2020062601 |是 | 是-套件版本的支援： `18.5-6.el7.centos.5`|
|OpenLogic 8。1 |CentOS | 8_1 |8.1.2020062400 |是 | 是-套件版本的支援： `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2)  |CentOS | 8_1-gen2 |8.1.2020062401 |是 | 是-套件版本的支援： `18.5-7.el8_1.1`|
|OpenLogic 8。1 |CentOS-HPC | 8_1 |8.1.2020062400 |是 | 是-套件版本的支援： `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2)  |CentOS-HPC： 8_1-gen2 | 8_1-gen2 |8.1.2020062401 |是 | 是-套件版本的支援： `18.5-7.el8_1.1`|

* 所有的 OpenLogic： CentOS 7.8 和 8.2 (Gen1 和 Gen2) 映射都會使用雲端初始化進行布建。

### <a name="oracle"></a>Oracle

| 發行者 / 版本 | 供應項目 | SKU | 版本 | 映像 cloud-init 就緒 | Azure 上的 cloud-init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| 預覽影像 (注意：這是預覽映射，一旦所有 Oracle 7.7 映射都支援 cloud init 之後，這將會在2020年中移除，並會提供通知)  | 否，處於預覽狀態，套件為：*18.5-3.0.1.el7*

### <a name="suse-sles"></a>SUSE SLES
這些 SLES 映射已更新為使用雲端初始化進行布建，Gen2 映射變體也已更新。
* suse： sles-15-sp1-{basic/byos/hpc/hpc-byos/chost-byos}： gen1：2020.06.10
* suse： sles-sap-15-sp1： gen1：2020.06.10
* suse： sles-sap-15-sp1-byos： gen1：2020.06.10
* suse： manager-proxy-4-byos： gen1：2020.06.10
* suse： manager-伺服器-4-byos： gen1：2020.06.10
* suse： sles-{byos/sap/sap-byos}：15：2020.06.10
* suse： sles-12-sp5： gen1：2020.06.10
* suse： sles-12-sp5 {-byos/basic/hpc-byos/hpc}： gen1：2020.06.10
* suse： sles-{byos/sap/sap-byos}： 12-sp4：2020.06.10
* suse： sles-{byos/sap/sap-byos}： 12-sp3：2020.06.10
* suse： sles-{byos/sap/sap-byos}： 12-sp2：2020.06.10


### <a name="debian"></a>Debian
| 發行者 / 版本 | 供應項目 | SKU | 版本 | 映像 cloud-init 就緒 | Azure 上的 cloud-init 套件支援|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1)  |debian-10 | 10-于 cloudinit |cloud-init-preview| 是 (僅限預覽)  | 否，處於預覽狀態。 |
| debian (Gen2)  |debian-10 | 10-于 cloudinit-gen2 |cloud-init-preview| 是 (僅限預覽)  | 否，處於預覽狀態。 |




目前 Azure Stack 將會支援已啟用 cloud-init 之映像的佈建。

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理程式 (WALA) 之間有哪些差異？
WALA 是 Azure 平台專屬的代理程式，用於佈建及設定 VM，以及處理 [Azure 延伸模組](../extensions/features-linux.md) \(部分機器翻譯\)。 

我們正在加強設定 VM 以使用 cloud-init 取代 Linux 代理程式的工作，以讓現有的 cloud-init 客戶使用其目前的 cloud-init 指令碼，或是讓新客戶利用豐富的 cloud-init 設定功能。 如果您已經對用於設定 Linux 系統所的 cloud-init 指令碼有所投入，則您**不需要其他任何設定**就可以讓 cloud-init 加以處理。 

由於 cloud-init 無法處理 Azure 延伸模組，因此映像中仍須 WALA 來處理延伸模組，但必須停用其佈建程式碼；針對正在轉換為透過 cloud-init 佈建之經過背書的 Linux 發行版本映像，其將已安裝 WALA 且正確設定。

建立 VM 時，如果您在佈建階段未包含 Azure CLI `--custom-data` 參數，cloud-init 或 WALA 會採用所需的最低限度 VM 佈建參數來佈建 VM，並使用預設值完成部署。  如果您使用 `--custom-data` 參數來參考 cloud-init 設定，您自訂資料中所包含的所有內容都會在 VM 開機時提供給 cloud-init 使用。

套用到 VM 的 cloud-init 設定沒有時間限制，且不會因為逾時而導致部署失敗。WALA 並非如此，如果您變更 WALA 預設值以處理自訂資料，其不能超過 40 分鐘的總 VM 佈建時間額度；如果超過，VM Create 將會失敗。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署支援 cloud-init 的虛擬機器
部署支援 cloud-init 的虛擬機器就像在部署期間參考支援 cloud-init 的發行版本一樣簡單。  Linux 發行版本維護者必須選擇啟用，並將 cloud-init 整合到其基礎 Azure 發行映像。 一旦確認您想要部署的映像支援 cloud-init，您就可以使用 Azure CLI 來部署映像。 

部署此映像的第一個步驟是使用 [az group create](/cli/azure/group) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

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

下列範例會建立名為 *centos74* 的 VM，並建立 SSH 金鑰 (如果它們不存在於預設金鑰位置)。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。  使用 `--custom-data` 參數以傳入 cloud-init 組態檔。 如果您將檔案儲存於目前工作目錄之外的位置，請提供 cloud-init.txt  組態的完整路徑。 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

建立 VM 後，Azure CLI 會顯示您部署專屬的資訊。 記下 `publicIpAddress`。 此位址用來存取 VM。  系統需要花一些時間建立 VM、安裝套件以及啟動應用程式。 在 Azure CLI 將您返回提示字元之後，背景工作會繼續執行。 您可以透過 SSH 連線到 VM，並使用「疑難排解」一節中所述的步驟來檢視 cloud-init 記錄。 

您也可以 [在 ARM 範本中傳遞參數](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli#inline-parameters)，以部署已啟用雲端初始化的 VM。

## <a name="troubleshooting-cloud-init"></a>針對 cloud-init 進行疑難排解
一旦佈建 VM，Cloud-init 將會執行 `--custom-data` 中定義的所有模組和指令碼，以設定 VM。  如果您需要針對設定中的任何錯誤或遺漏進行疑難排解，則需要在 cloud-init 記錄 (位於 **/var/log/cloud-init.log**) 中搜尋模組名稱 (例如 `disk_setup` 或 `runcmd`)。

> [!NOTE]
> 並非每個模組失敗都會導致嚴重的 cloud-init 整體設定失敗。 例如，使用 `runcmd` 模組時，如果指令碼失敗，cloud-init 會因為執行了 runcmd 模組而仍然報告佈建成功，。

如需 cloud-init 記錄的詳細資訊，請參閱 [cloud-init 文件](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) \(英文\) 

## <a name="next-steps"></a>後續步驟

針對[雲端初始化的問題進行疑難排解](cloud-init-troubleshooting.md)。


如需設定變更的 cloud-init 範例，請參閱下列文件：
 
- [將其他 Linux 使用者新增至虛擬機器](cloudinit-add-user.md)
- [執行套件管理員以便在初次開機時更新現有的套件](cloudinit-update-vm.md)
- [變更虛擬機器本機的主機名稱](cloudinit-update-vm-hostname.md) 
- [安裝應用程式套件、更新組態檔案，以及插入金鑰](tutorial-automate-vm-deployment.md)
 
