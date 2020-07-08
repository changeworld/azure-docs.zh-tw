---
title: 虛擬機器認證 - 問題與解決方案
description: 本文說明 VM 映射的常見錯誤訊息。 它也會討論相關的解決方案
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: 7bd3f1a5b242ee5196e92456cb3fc8c97f8f5b27
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958526"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>虛擬機器認證 - 問題與解決方案

將虛擬機器（VM）映射發佈至 Azure Marketplace 時，Azure 小組會驗證 VM 映射，以確保其 bootability、安全性及 Azure 相容性。 如果有任何高品質的測試失敗，則發行將會失敗，並顯示包含錯誤的訊息。

本文說明 VM 映射的常見錯誤訊息。 它也會討論相關的解決方案：

> [!NOTE]
> 如果您有改善的問題或意見反應，請聯絡[合作夥伴中心支援](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="approved-base-image"></a>已核准的基底映射

當您提交要求以重新發佈包含更新的映射時，部分編號驗證測試案例可能會失敗。 在此情況下，您的映射將不會被核准。

當您使用的基底映射屬於另一個發行者，而且您已更新映射時，就會發生此失敗。 在此情況下，將不會允許您發佈映射。

若要修正此問題，請從 Azure Marketplace 取出您最新的映射，並對該映射進行變更。 請參閱下列內容以觀看已核准的基底映射，您可以在其中搜尋您的映射：

- [Linux-映射](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-影像](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)）

## <a name="vm-extension-failure"></a>VM 擴充功能失敗

若要檢查您的映射是否支援 VM 延伸模組，請遵循下列步驟：

啟用 VM 擴充功能：

1. 選取 Linux VM。
2. 移至 [**診斷設定**]。
3. 藉由更新**儲存體帳戶**來啟用基底矩陣。
4. 選取 [儲存]。

   ![啟用來賓層級監視](./media/vm-certification-issues-solutions-1.png)

檢查是否已正確啟用 VM 擴充功能：

5. 移至 VM 的 [ **vm 擴充**功能] 索引標籤，並確認**Linux 診斷延伸**模組。
6. 如果狀態為 [布建**成功**]，則已通過延伸模組測試案例。
7. 如果狀態為 [布建**失敗**]，則延伸模組測試案例失敗，而且您需要設定強化的旗標。

   ![布建成功](./media/vm-certification-issues-solutions-2.png)

   如果 VM 延伸模組失敗，請移至[使用 Linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md)以加以啟用。 如果您不想要啟用 VM 延伸模組，請洽詢支援小組，並要求他們停用此延伸模組。

## <a name="virtual-machine-provisioning-issue"></a>虛擬機器布建問題

在提交您的供應專案之前，請先針對 VM 檢查布建程式是否經過嚴格的處理。 若要查看布建 VM 的 json 格式，請移至[Azure 虛擬機器（VM）映射認證](azure-vm-image-certification.md)。

布建問題可能包括下列失敗案例：

|S.NO|錯誤|reason|解決方案|
|---|---|---|---|
|1|不正確虛擬硬碟（VHD）|如果 VHD 頁尾中的指定 cookie 值不正確，則會將 VHD 視為無效。|重新建立映射並提交要求。|
|2|不正確 Blob 類型|VM 布建失敗，因為使用的區塊是 blob 類型，而不是頁面類型。|重新建立映射並提交要求。|
|3|布建超時或未正確一般化|VM 一般化發生問題。|使用一般化重新建立映射並提交要求。|

> [!NOTE]
> 針對與 VM 一般化相關的檔，請遵循下列連結：
> - [Linux](create-azure-vm-technical-asset.md#generalize-the-image)）
> - [Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)）

## <a name="software-compliance-for-windows"></a>Windows 的軟體合規性

如果您的 windows 映射要求因為軟體相容性而遭到拒絕，則您可能已建立已安裝 SQL server 的 Windows 映像，而不是從 Azure Marketplace 取得相關的 SQL 版本基底映射。

請勿使用安裝的 SQL server 建立自己的 windows 映射。 相反地，請使用來自 Azure Marketplace 的已核准 SQL 基底映射（Enterprise/Standard/web）。

如果您嘗試安裝 Visual studio 或任何 office 授權產品，請洽詢支援小組以進行先前的核准。

如需詳細資訊，請造訪[建立您的 Azure 虛擬機器技術資產](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)）以選取已核准的基底。

## <a name="tool-kit-test-case-execution-failed"></a>工具套件測試案例執行失敗

Microsoft 認證工具組將協助您執行測試案例，確認您的 VHD/映射與 Azure 環境相容。

下載[Microsoft 認證工具](azure-vm-image-certification.md)組。

## <a name="linux-test-cases"></a>Linux 測試案例

以下是工具組將會執行的 Linux 測試案例。 [描述] 中會指出測試驗證。

|S.No|測試案例|description|
|---|---|---|
|1|Bash 歷程記錄|建立 VM 映射之前，應該先清除 Bash 歷程記錄檔案。|
|2|Linux 代理程式版本|應該安裝 Azure Linux 代理程式2.2.41 和以上版本。|
|3|必要的核心參數|確認已設定下列核心參數： <br>主控台 = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|在 OS 磁片上交換磁碟分割|確認不會在 OS 磁片上建立交換資料分割。|
|5|OS 磁片上的根磁碟分割|為 OS 磁片建立單一根磁碟分割。|
|6|OpenSSL 版本|OpenSSL 版本應大於或等於 v 0.9.8。|
|7|Python 版本|強烈建議使用 Python 2.6 + 版。|
|8|用戶端存留時間間隔|將 ClientAliveInterval 設定為180。 在應用程式需要時，可以設定30到235之間。 如果您要為使用者啟用 SSH，必須依照說明設定此值。|
|9|OS 架構|僅支援 64 位元作業系統。|
|10|自動更新|識別 Linux 代理程式自動更新是否已啟用。|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>執行先前的測試案例時，發現常見的錯誤

執行先前的測試案例時，發現常見的錯誤。
 
|S.NO|測試案例|錯誤|解決方案|
|---|---|---|---|
|1|Linux 代理程式版本測試案例|最低 Linux 代理程式版本為2.241 或更高版本。 自2020年5月1日起，這項需求是強制的|映射必須以所需的版本更新，才能[提交要求](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。|
|2|Bash 歷程記錄測試案例|如果您提交之映射中的 bash 歷程記錄大小超過 1 KB，您就會看到錯誤。 大小限制為 1 KB，以確保您的 bash 歷程記錄檔案中不會捕捉到任何可能的機密資訊。|若要解決此問題，請將 VHD 掛接到任何其他工作 VM，並進行任何變更（例如，刪除歷程記錄檔），以將 `.bash` 大小縮減到小於或等於 1 KB。|
|3|必要的核心參數測試案例|當 [**主控台**] 的值未設定為 [ **ttyS0**] 時，您會收到這個錯誤。 執行下列命令來檢查：<br>`cat /proc/cmdline`|將 [**主控台**] 的值設定為**ttyS0** ，然後重新提交要求。|
|4|ClientAlive Interval 測試案例|如果工具組結果提供此測試案例的失敗結果，則**ClientAliveInterval**會有不適當的值。|將**ClientAliveInterval**的值設定為小於或等於235，然後重新提交要求。|

### <a name="windows-test-cases"></a>Windows 測試案例

以下是工具組將會執行的 Windows 測試案例。 [描述] 中會指出測試驗證。

|S.No|測試案例|description|
|---|---|---|---|
|1|OS 架構|Azure 僅支援64位的作業系統。|
|2|使用者帳戶相關性|應用程式執行不應與系統管理員帳戶相依。|
|3|容錯移轉叢集|尚未支援 Windows Server 容錯移轉叢集功能。 應用程式不應該相依于這項功能。|
|4|IPV6|Azure 環境中尚未支援 IPv6。 應用程式不應該相依于這項功能。|
|5|DHCP|尚未支援動態主機設定通訊協定伺服器角色。 應用程式不應該相依于這項功能。|
|6|Hyper-V|尚不支援 hyper-v 伺服器角色。 應用程式不應該相依于這項功能。|
|7|遠端存取|尚不支援遠端存取（直接存取）伺服器角色。 應用程式不應該相依于這項功能。|
|8|Rights Management Services|Rights Management 服務。 尚不支援伺服器角色。 應用程式不應該相依于這項功能。|
|9|Windows Deployment Services|Windows 部署服務。 尚不支援伺服器角色。 應用程式不應該相依于這項功能。|
|10|BitLocker 磁碟機加密|作業系統硬碟不支援 BitLocker 磁碟機加密，但可用於資料磁片。|
|11|Internet Storage Name Server|尚不支援網際網路儲存名稱伺服器功能。 應用程式不應該相依于這項功能。|
|12|多重路徑 I/O|多重路徑 i/o。 尚不支援此伺服器功能。 應用程式不應該相依于這項功能。|
|13|Network Load Balancing|網路負載平衡。 尚不支援此伺服器功能。 應用程式不應該相依于這項功能。|
|14|對等名稱解析通訊協定|對等名稱解析通訊協定。 尚不支援此伺服器功能。 應用程式不應該相依于這項功能。|
|15|SNMP 服務|尚未支援 SNMP 服務功能。 應用程式不應該相依于這項功能。|
|16|Windows 網際網路名稱服務|Windows 網際網路名稱服務。 尚不支援此伺服器功能。 應用程式不應該相依于這項功能。|
|17|無線區域網路服務|無線局域網路服務。 尚不支援此伺服器功能。 應用程式不應該相依于這項功能。|

如果您在上述測試案例中遇到任何失敗，請參閱上表中的 [**描述**] 資料行以取得解決方案。 如果您需要詳細資訊，請聯絡支援小組。 

## <a name="data-disk-size-verification"></a>資料磁片大小驗證

如果使用資料磁片提交的任何要求大小大於 1023 GB，則不會核准該要求。 此規則適用于 Linux & Windows。

以小於或等於 1023 GB 的大小重新提交要求。

## <a name="os-disk-size-validation"></a>OS 磁片大小驗證

如需 OS 磁片大小的限制，請參閱下列規則。 當您提交任何要求時，請確認作業系統磁片大小在 Linux 或 Windows 的限制範圍內。

|OS|建議的 VHD 大小|
|---|---|
|Linux|30 GB 到 1023 GB|
|Windows|30 GB 到 250 GB|

當 Vm 允許存取基礎作業系統時，請確定 VHD 大小夠大。 因為無法在沒有停機時間的情況下擴展磁片，所以請使用介於30到 50 GB 的磁片大小。

|VHD 大小|實際佔用的大小|解決方案|
|---|---|---|
|>500 TiB|n/a|請洽詢支援小組以取得例外狀況核准。|
|250-500 TiB|>200 GiB 與 blob 大小不同|請洽詢支援小組以取得例外狀況核准。|

> [!NOTE]
> 較大的磁片大小會產生較高的成本，並會在布建和複寫步驟期間產生延遲。 由於這項延遲和成本的增加，支援小組可能會搜尋例外狀況核准的理由。

## <a name="wannacry-patch-verification-test-for-windows"></a>適用于 Windows 的 WannaCry 修補程式驗證測試

若要避免與 WannaCry 病毒相關的潛在攻擊，請確定所有 windows 映射要求都會以最新的修補程式來更新。

若要檢查 Windows Server 修補後的版本，請參閱下表中的 OS 詳細資料及其支援的最低版本。 

您可以從或驗證影像檔版本 `C:\windows\system32\drivers\srv.sys` `srv2.sys` 。

> [!NOTE]
> WindowsServer2019 沒有任何強制版本需求。

|OS|version|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|NA|

## <a name="sack-vulnerability-patch-verification"></a>SACK 弱點修補程式驗證

當您提交 Linux 映射時，您的要求可能會因為核心版本問題而遭到拒絕。

以核准的版本更新核心，然後重新送出要求。 您可以在下表中找到已核准的核心版本。 版本號碼應大於或等於以下所列的數目。

如果您的映射未與下列其中一個核心版本一起安裝，請使用正確的修補程式更新您的映射。 您可以從下列連結找到更多資訊。 在使用下列必要的修補程式更新映射之後，向支援小組要求必要的核准：

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|作業系統系列|version|核心 (kernel)|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049-*-azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18.10|4.18.0-1023|
||19.04|5.0.0-1010|
||19.04|5.3.0-1004|
|RHEL 和分幣 OS|6.10|2.6.32-754.15。3|
||7.2|3.10.0-327.79。2|
||7.3|3.10.0-514.66。2|
||7.4|3.10.0-693.50。3|
||7.5|3.10.0-862.34。2|
||7.6|3.10.0-957.21。3|
||7.7|3.10.0-1062.1。1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||「7-原始」（7.6）||
||"7-LVM" （7.6）|3.10.0-957.21。3|
||RHEL-SAP 7。4|TBD|
||RHEL-SAP 7。5|TBD|
|SLES|SLES11SP4 （包括 SAP）|3.0.101-108.95.2|
||適用于 SAP 的 SLES12SP1|3.12.74-60.64.115。1|
||適用于 SAP 的 SLES12SP2|4.4.121-92.114。1|
||SLES12SP3|4.4180-4.31.1 （核心-azure）|
||適用于 SAP 的 SLES12SP3|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 （核心-azure）|
||適用于 SAP 的 SLES12SP4|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 （核心-azure）|
||適用于 SAP 的 SLES15|4.12.14-5.30.1 （核心-azure）|
||SLES15SP1|4.12.14-5.30.1 （核心-azure）|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35。2<br>RHCK 2.6.32-754.15。3 
||7.0-7。5|UEK3 3.8.13-118.35。2<br>UEK4 4.1.12-124.28。3<br>RHCK 遵循 RHEL|
||7.6|RHCK 3.10.0-957.21。3<br>UEK5 4.14.35-1902.2.0|
|CoreOS 穩定2079.6。0|4.19.43*|
||Beta 2135.3。1|4.19.50*|
||Alpha 2163.2。1|4.19.50*|
|Debian|jessie （安全性）|3.16.68-2|
||jessie 反向移植|4.9.168-1 + deb9u3|
||stretch （安全性）|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 （buster）|Debian 6.3.0-18 + deb9u1|
||buster、sid （stretch 反向移植）|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>映射大小應為 mb 的倍數

Azure 上的所有 Vhd 都必須將虛擬大小調整為 1 MB 的倍數。 如果您的 VHD 不符合建議的虛擬大小，您的要求可能會遭到拒絕。

當您從未經處理的磁片轉換成 VHD 時，請遵循指導方針，並且必須確定原始磁片大小是 1 MB 的倍數。 如需詳細資訊，請參閱[非背書散發套件的資訊](../../virtual-machines/linux/create-upload-generic.md)

## <a name="vm-access-denied"></a>拒絕存取 VM

如果您在執行 VM 上的測試案例時遇到拒絕存取的問題，可能是因為許可權不足而無法執行測試案例。

檢查是否已針對執行自我測試案例的帳戶啟用適當的存取權。 如果不是，請啟用 [存取] 來執行測試案例。 如果您不想要啟用存取權，您可以與支援小組共用自我測試案例結果。

## <a name="download-failure"></a>下載失敗
    
當您使用 SAS URL 下載 VM 映射時，請參閱下表中的任何問題。

|S.NO|錯誤|reason|解決方案|
|---|---|---|---|
|1|找不到 Blob|可以從指定的位置刪除或移動 VHD|| 
|2|使用中的 Blob|VHD 正由另一個內部進程使用中|使用 SAS URL 下載時，VHD 應處於使用中狀態。|
|3|SAS URL 無效|該 VHD 的相關聯 SAS URL 不正確。|取得正確的 SAS URL。|
|4|無效的簽章|該 VHD 的相關聯 SAS URL 不正確。|取得正確的 SAS URL。|
|6|HTTP 條件式標頭|不正確 SAS URL。|取得正確的 SAS URL。|
|7|不正確 VHD 名稱|檢查 VHD 名稱中是否有任何特殊字元（例如 **%** 或 **""** ）|移除特殊字元以重新命名 VHD 檔案|

## <a name="first-1-mb-partition"></a>前 1 MB 的分割區

提交 VHD 時，請確定 VHD 的前 1 MB 磁碟分割是空的。 否則，您的要求將會失敗。

## <a name="default-credentials"></a>預設認證

請務必確定預設認證不會隨提交的 VHD 一起傳送。 新增預設認證可讓 VHD 更容易受到安全性威脅的攻擊。 請改為在提交 VHD 時建立您自己的認證。
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk 對應錯誤

使用多個資料磁片提交要求時，但其順序不會依序排列，這會被視為對應問題。 例如，如果有三個數據磁片，編號順序必須是**0、1、2**。 任何其他訂單都會被視為對應問題。

以適當的資料磁片排序來重新提交要求。

## <a name="incorrect-os-mapping"></a>不正確的 OS 對應

建立映射時，它可能會對應或指派給錯誤的 OS 標籤。 例如，當您在建立映射時選取**Windows**做為 OS 名稱的一部分時，OS 磁片應該只會與 Windows 一起安裝。 同樣適用于 Linux。

## <a name="vm-not-generalized"></a>VM 未一般化

如果從 Azure Marketplace 取得的所有映射都要重複使用，則必須將作業系統 VHD 一般化。

Linux：

下列程序會將 Linux VM 一般化，並重新部署為個別的 VM。

在 [SSH] 視窗中，輸入下列命令：`sudo waagent -deprovision+user`

Windows：

Windows 映像是以一般化 `sysreptool` 。

您可以在[Sysprep （系統準備）總覽]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)找到有關此工具的詳細資訊。

## <a name="datadisk-error"></a>DataDisk 錯誤

請參閱下表，以取得與資料磁片相關的錯誤解決方案。

|錯誤|reason|解決方案|
|---|---|---|
|`DataDisk- InvalidUrl:`|此錯誤可能是因為在提交供應專案時，為 LUN 指定的號碼無效。|確認資料磁片的 LUN 編號順序位於 [合作夥伴中心]。|
|`DataDisk- NotFound:`|此錯誤可能是因為資料磁片不位於指定的 SAS URL|確認資料磁片位於要求中指定的 SAS URL。|

## <a name="remote-access-issue"></a>遠端存取問題

如果 Windows 映像未啟用 RDP 選項，您將會收到此錯誤。 

在提交之前啟用 Windows 映像的 RDP 存取。

## <a name="next-steps"></a>後續步驟

如果您有改善的問題或意見反應，請聯絡[合作夥伴中心支援](https://partner.microsoft.com/support/v2/?stage=1)。
