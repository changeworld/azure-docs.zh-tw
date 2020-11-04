---
title: Azure Marketplace 的 VM 認證疑難排解
description: 本文涵蓋針對 Azure Marketplace 測試和認證 VM 映射常見的疑難排解主題。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: f065b1bc98eab86542ecff73e1471e4d90cd4182
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339528"
---
# <a name="vm-certification-troubleshooting"></a>VM 認證疑難排解

當您將虛擬機器 (VM) 映射發佈到 Azure Marketplace 時，Azure 小組會進行驗證以確保其 bootability、安全性和 Azure 相容性。 如果有任何高品質的測試失敗，發佈將會失敗，而且您會收到描述問題的錯誤訊息。

本文說明在 VM 映射發佈期間常見的錯誤訊息，以及相關的解決方案。

> [!NOTE]
> 如果您有任何問題或意見反應要改進，請聯絡合作夥伴中心 [支援服務](https://aka.ms/marketplacepublishersupport)。

## <a name="approved-base-image"></a>核准的基底映射

當您提交以更新重新發佈映射的要求時，元件編號驗證測試案例可能會失敗。 如果失敗，您的映射將不會被核准。

當您使用屬於另一個發行者的基底映射，而且已更新映射時，就會發生此失敗。 在此情況下，將不會允許您發佈映射。

若要修正此問題，請從 Azure Marketplace 取出映射，並對其進行變更。 如需詳細資訊，請參閱下列文章：

- [Linux 映像](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows 映像](azure-vm-create-using-approved-base.md)

> [!Note]
> 如果您使用的是不是從 Azure Marketplace 取得的 Linux 基底映射，您可以將第一個磁碟分割的 2048 KB 位移。 這可讓您使用未格式化的空間來新增計費資訊，並允許 Azure 將您的 VM 發佈至 Azure Marketplace。  

> [!Note]
> 如果您使用的是不是從 Marketplace 取得的 Linux 基底映射，您可以使用 2048 KB 來位移第一個磁碟分割。 這可讓您使用未格式化的空間來新增計費資訊，並允許 Azure 將您的 VM 發佈至 Marketplace。  

## <a name="vm-extension-failure"></a>VM 延伸模組失敗

查看您的映射是否支援 VM 擴充功能。

若要啟用 VM 擴充功能，請執行下列動作：

1. 選取您的 Linux VM。
1. 移至 [ **診斷設定** ]。
1. 藉由更新 **儲存體帳戶** 來啟用基底矩陣。
1. 選取 [儲存]。

   ![啟用來賓層級監視](./media/create-vm/vm-certification-issues-solutions-1.png)

若要確認是否已正確啟用 VM 擴充功能，請執行下列動作：

1. 在 VM 中，選取 [ **VM 擴充** 功能] 索引標籤，然後確認 **Linux 診斷擴充** 功能的狀態。
1. 
    * 如果狀態為「布建 *成功* 」，則會傳遞延伸模組測試案例。  
    * 如果狀態為「布建 *失敗* 」，擴充功能測試案例會失敗，而您必須設定強化旗標。

      ![顯示布建成功的螢幕擷取畫面](./media/create-vm/vm-certification-issues-solutions-2.png)

      如果 VM 擴充功能失敗，請參閱 [使用 Linux 診斷擴充功能來監視計量和記錄](../virtual-machines/extensions/diagnostics-linux.md) 以加以啟用。 如果您不想要啟用 VM 擴充功能，請洽詢支援小組，並要求他們停用它。

## <a name="vm-provisioning-issue"></a>VM 布建問題

請檢查以確定您已遵循 VM 布建程式，再提交您的供應專案。 若要查看提供 VM 的 JSON 格式，請參閱 [測試虛擬機器映射](azure-vm-image-test.md)。

布建問題可能包含下列失敗案例：

|狀況|錯誤|原因|解決方法|
|---|---|---|---|
|1| (VHD) 的虛擬硬碟無效|如果 VHD 頁尾中指定的 cookie 值不正確，VHD 將會視為無效。|重新建立映射並提交要求。|
|2|不正確 blob 類型|VM 布建失敗，因為使用的區塊是 blob 類型，而不是頁面類型。|重新建立映射並提交要求。|
|3|布建超時或未正確一般化|VM 一般化有問題。|使用一般化重新建立映射並提交要求。|
|

> [!NOTE]
> 如需 VM 一般化的詳細資訊，請參閱：
> - [Linux 檔](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows 檔](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>VHD 規格

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie 和其他 VHD 規格
' Conectix ' 字串是 VHD 規格的一部分，在下面的 VHD 頁尾中定義為8位元組 ' cookie '，以識別檔案建立者。 Microsoft 建立的所有 vhd 檔案都有此 cookie。 

VHD 格式化的 blob 應該有512位元組頁尾;這是 VHD 頁尾格式：

|硬碟頁尾欄位|大小 (位元組)|
|---|---|
Cookie|8
特性|4
檔案格式版本|4
資料位移|8
時間戳記|4
建立者應用程式|4
Creator 版本|4
Creator 主機 OS|4
原始大小|8
目前大小|8
磁片幾何|4
磁碟類型|4
總和檢查碼|4
唯一識別碼|16
儲存狀態|1
保留|427


### <a name="vhd-specifications"></a>VHD 規格
若要確保順暢的發佈體驗，請確定 **VHD 符合下列準則：**
* Cookie 必須包含 "conectix" 字串
* 必須修正磁片類型
* VHD 的虛擬大小至少為20MB
* VHD 會對齊 (亦即虛擬大小必須是 1 MB 的倍數) 
* VHD blob 長度 = 虛擬大小 + VHD 頁尾長度 (512) 

您可以在這裡下載 VHD 規格 [。](https://www.microsoft.com/download/details.aspx?id=23850)


## <a name="software-compliance-for-windows"></a>Windows 的軟體合規性

如果您的 Windows 映像要求因為軟體合規性問題而遭到拒絕，您可能已使用已安裝的 SQL server 實例建立 Windows 映像，而不是從 Azure Marketplace 取得相關的 SQL 版本基礎映射。

請勿建立您自己的 Windows 映像並安裝 SQL server。 相反地，請使用已核准的 SQL 基礎映射 (Enterprise/Standard/web) Azure Marketplace。

如果您嘗試安裝 Visual Studio 或任何 Office 授權的產品，請洽詢支援小組以取得先前的核准。

如需選取核准的基礎的詳細資訊，請參閱 [從核准的基底建立虛擬機器](azure-vm-create-using-approved-base.md)。

## <a name="tool-kit-test-case-execution-failed"></a>工具套件測試案例執行失敗

Microsoft 認證工具組可協助您執行測試案例，並確認您的 VHD 或映射與 Azure 環境相容。

下載 [Microsoft 認證工具](azure-vm-image-test.md)組。

## <a name="linux-test-cases"></a>Linux 測試案例

下表列出工具組將執行的 Linux 測試案例。 [描述] 中會陳述測試驗證。

|狀況|測試案例|描述|
|---|---|---|
|1|Bash 歷程記錄|您應先清除 Bash 記錄檔，再建立 VM 映射。|
|2|Linux 代理程式版本|應安裝 Azure Linux 代理程式2.2.41 或更新版本。|
|3|必要的核心參數|確認已設定下列核心參數： <br>主控台 = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|在 OS 磁片上交換磁碟分割|確認未在作業系統磁片上建立交換磁碟分割。|
|5|OS 磁片上的根磁碟分割|為 OS 磁片建立單一根磁碟分割。|
|6|OpenSSL 版本|OpenSSL 版本應為 v 0.9.8 或更新版本。|
|7|Python 版本|強烈建議使用 Python 2.6 版或更新版本。|
|8|用戶端存活間隔|將 ClientAliveInterval 設定為180。 在應用程式需要時，可以將它設定為30到235。 如果您要為終端使用者啟用 SSH，則必須設定此值，如所述。|
|9|OS 架構|僅支援 64 位元作業系統。|
|10|自動更新|識別 Linux 代理程式自動更新是否已啟用。|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>執行先前的測試案例時發現常見的錯誤

下表列出執行先前的測試案例時所發現的常見錯誤：
 
|狀況|測試案例|錯誤|解決方法|
|---|---|---|---|
|1|Linux 代理程式版本測試案例|Linux 代理程式的最低版本是2.2.41 或更新版本。 自2020年5月1日起，這項需求是強制的。|請更新 Linux 代理程式版本，其應為2.241 或更新版本。 如需詳細資訊，您可以造訪 [Linux 代理程式版本更新頁面](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。|
|2|Bash 歷程記錄測試案例|如果您提交的映射中 bash 歷程記錄的大小超過 1 kb (KB) ，您將會看到錯誤。 大小限制為 1 KB，以確保不會在 bash 記錄檔中捕捉任何可能的機密資訊。|若要解決此問題，請將 VHD 掛接到任何其他工作中的 VM，並進行您想要的任何變更 (例如，刪除 *bash* 歷程記錄檔) ，將大小減少為小於或等於 1 KB。|
|3|必要的核心參數測試案例|當 **主控台** 的值未設為 **ttyS0** 時，您會收到此錯誤。 執行下列命令來進行檢查：<br>`cat /proc/cmdline`|將 [ **主控台** ] 的值設定為 [ **ttyS0** ]，然後重新提交要求。|
|4|ClientAlive 間隔測試案例|如果工具組的結果會在此測試案例中提供失敗的結果，則會有不適當的 **ClientAliveInterval** 值。|將 **ClientAliveInterval** 的值設定為小於或等於235，然後重新提交要求。|

### <a name="windows-test-cases"></a>Windows 測試案例

下表列出工具組將執行的 Windows 測試案例，以及測試驗證的描述：

|狀況 |測試案例|描述|
|---|---|---|---|
|1|OS 架構|Azure 僅支援64位作業系統。|
|2|使用者帳戶相依性|應用程式執行不應依存于系統管理員帳戶。|
|3|容錯移轉叢集|尚未支援 Windows Server 容錯移轉叢集功能。 應用程式不應該相依于這項功能。|
|4|IPV6|Azure 環境中尚未支援 IPv6。 應用程式不應該相依于這項功能。|
|5|DHCP|尚未支援動態主機設定通訊協定伺服器角色。 應用程式不應該相依于這項功能。|
|6|Hyper-V|尚未支援 hyper-v 伺服器角色。 應用程式不應該相依于這項功能。|
|7|遠端存取|尚未支援遠端存取 (直接存取) 伺服器角色。 應用程式不應該相依于這項功能。|
|8|Rights Management Services|Rights Management 服務。 尚不支援伺服器角色。 應用程式不應該相依于這項功能。|
|9|Windows Deployment Services|Windows 部署服務。 尚不支援伺服器角色。 應用程式不應該相依于這項功能。|
|10|BitLocker 磁碟機加密|作業系統硬碟不支援 BitLocker 磁碟機加密，但它可能會用於資料磁片。|
|11|Internet Storage Name Server|尚未支援網際網路存放裝置名稱伺服器功能。 應用程式不應該相依于這項功能。|
|12|多重路徑 I/O|多重路徑 i/o。 尚未支援此伺服器功能。 應用程式不應該相依于這項功能。|
|13|Network Load Balancing|網路負載平衡。 尚未支援此伺服器功能。 應用程式不應該相依于這項功能。|
|14|對等名稱解析通訊協定|對等名稱解析通訊協定。 尚未支援此伺服器功能。 應用程式不應該相依于這項功能。|
|15|SNMP 服務|尚未支援簡易網路管理通訊協定 (SNMP) 服務功能。 應用程式不應該相依于這項功能。|
|16|Windows 網際網路名稱服務|Windows 網際網路名稱服務。 尚未支援此伺服器功能。 應用程式不應該相依于這項功能。|
|17|無線區域網路服務|無線局域網路服務。 尚未支援此伺服器功能。 應用程式不應該相依于這項功能。|
|

如果您在上述測試案例中遇到任何失敗，請參閱此方案之表格中的 [ **描述** ] 資料行。 如果您需要詳細資訊，請洽詢支援小組。 

## <a name="data-disk-size-verification"></a>資料磁片大小驗證

如果任何提交資料磁片的要求大小超過 1023 gb (GB) ，則不會核准要求。 這項規則適用于 Linux 和 Windows。

重新提交大小小於或等於 1023 GB 的要求。

## <a name="os-disk-size-validation"></a>作業系統磁片大小驗證

請參閱下列規則，以瞭解 OS 磁片大小的限制。 當您提交任何要求時，請確認作業系統磁片大小在 Linux 或 Windows 的限制內。

|OS|建議的 VHD 大小|
|---|---|
|Linux|30 GB 至 1023 GB|
|Windows|30 GB 至 250 GB|
|

當 Vm 允許存取基礎作業系統時，請確定 VHD 大小對 VHD 而言夠大。 因為磁片無法在沒有停機的情況下展開，所以請使用 30 GB 到 50 GB 的磁片大小。

|VHD 大小|實際佔用的大小|解決方法|
|---|---|---|
|>500 tib (TiB) |n/a|請洽詢支援小組以取得例外狀況核准。|
|250-500 TiB|>200 32,767 gib (GiB) 與 blob 大小的差異|請洽詢支援小組以取得例外狀況核准。|
|

> [!NOTE]
> 較大的磁片大小會產生較高的成本，並會在安裝和複寫過程中產生延遲。 由於此延遲和成本，支援小組可能會尋求例外狀況核准的理由。

## <a name="wannacry-patch-verification-test-for-windows"></a>適用于 Windows 的 WannaCry patch 驗證測試

若要避免與 WannaCry 病毒相關的潛在攻擊，請確定所有 Windows 映像要求都會以最新的修補程式更新。

若要檢查 Windows Server 修補版本的 OS 詳細資料，以及它所支援的最低版本，請參閱下表： 

影像檔案版本可以從或進行 `C:\windows\system32\drivers\srv.sys` 驗證 `srv2.sys` 。

> [!NOTE]
> Windows Server 2019 沒有任何強制的版本需求。

|OS|版本|
|---|---|
|Windows 服務 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|
|

## <a name="sack-vulnerability-patch-verification"></a>SACK 弱點修補程式驗證

當您提交 Linux 映射時，因為發生核心版本問題，所以可能會拒絕您的要求。

以核准的版本更新核心，然後重新提交要求。 您可以在下表中找到已核准的核心版本。 版本號碼應等於或大於此處所列的號碼。

如果您的映射未安裝下列其中一個核心版本，請使用正確的修補程式加以更新。 使用這些必要的修補程式更新映射之後，請向支援小組要求所需的核准：

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|作業系統系列|版本|核心|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049-*-azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18.10|4.18.0-1023|
||19.04|5.0.0-1010|
||19.04|5.3.0-1004|
|RHEL 和美分 OS|6.10|2.6.32-754.15。3|
||7.2|3.10.0-327.79。2|
||7.3|3.10.0-514.66。2|
||7.4|3.10.0-693.50。3|
||7.5|3.10.0-862.34。2|
||7.6|3.10.0-957.21。3|
||7.7|3.10.0-1062.1。1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7.6) ||
||"7-LVM" (7.6) |3.10.0-957.21。3|
||RHEL-SAP 7。4|TBD|
||RHEL-SAP 7。5|TBD|
|SLES|SLES11SP4 (包括 SAP) |3.0.101-108.95.2|
||適用于 SAP 的 SLES12SP1|3.12.74-60.64.115。1|
||適用于 SAP 的 SLES12SP2|4.4.121-92.114。1|
||SLES12SP3|4.4180-4.31.1 (核心-azure) |
||適用于 SAP 的 SLES12SP3|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (核心-azure) |
||適用于 SAP 的 SLES12SP4|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (核心-azure) |
||適用于 SAP 的 SLES15|4.12.14-5.30.1 (核心-azure) |
||SLES15SP1|4.12.14-5.30.1 (核心-azure) |
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35。2<br>RHCK 2.6.32-754.15。3 
||7.0-7。5|UEK3 3.8.13-118.35。2<br>UEK4 4.1.12-124.28。3<br>RHCK 遵循 RHEL|
||7.6|RHCK 3.10.0-957.21。3<br>UEK5 4.14.35-1902.2.0|
|CoreOS 穩定2079.6。0|4.19.43*|
||Beta 2135.3。1|4.19.50*|
||Alpha 2163.2。1|4.19.50*|
|Debian|jessie (安全性) |3.16.68-2|
||jessie 反向移植|4.9.168-1 + deb9u3|
||stretch (安全性) |4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (buster) |Debian 6.3.0-18 + deb9u1|
||buster、sid (stretch 反向移植) |4.19.37-5|
|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>影像大小應為 mb 的倍數

Azure 上的所有 Vhd 必須具有與 1 mb (MB) 的倍數相符的虛擬大小。 如果您的 VHD 未遵守建議的虛擬大小，您的要求可能會遭到拒絕。

當您從原始磁片轉換成 VHD 時，請遵循指導方針，並確定原始磁片大小是 1 MB 的倍數。 如需詳細資訊，請參閱 [非背書發行](../virtual-machines/linux/create-upload-generic.md)版的資訊。

## <a name="vm-access-denied"></a>拒絕存取 VM

如果您在 VM 上執行測試案例時遇到拒絕存取的問題，可能是因為許可權不足而無法執行測試案例。

檢查是否已啟用執行自我測試案例的帳戶是否有適當的存取權。 如果未啟用存取，請啟用它來執行測試案例。 如果您不想要啟用存取權，您可能會與支援小組共用自我測試案例的結果。

如果您想要使用 SSH 停用的映射來提交認證程式的要求，請遵循下列步驟

1. 在您的映射上執行 Azure 工具組。  (請下載[最新的工具](https://aka.ms/AzureCertificationTestTool)組

2. 提出 [支援票證](https://aka.ms/marketplacepublishersupport)、附加工具組報表，並提供供應專案詳細資料-供應專案名稱、發行者名稱、方案識別碼/SKU 和版本。

3. 請重新提交您的認證要求。


## <a name="download-failure"></a>下載失敗
    
請參閱下表，以瞭解當您使用共用存取簽章 (SAS) URL 下載 VM 映射時所發生的任何問題。

|狀況|錯誤|原因|解決方法|
|---|---|---|---|
|1|找不到 Blob|VHD 可能會從指定的位置刪除或移動。|| 
|2|使用中的 Blob|VHD 會由另一個內部進程使用。|當您使用 SAS URL 下載 VHD 時，該 VHD 應該會處於使用中狀態。|
|3|不正確 SAS URL|VHD 的相關聯 SAS URL 不正確。|取得正確的 SAS URL。|
|4|簽章無效|VHD 的相關聯 SAS URL 不正確。|取得正確的 SAS URL。|
|6|HTTP 條件式標頭|SAS URL 無效。|取得正確的 SAS URL。|
|7|VHD 名稱無效|查看 VHD 名稱中是否有任何特殊字元，例如% sign (% ) 或引號 ( ") 。|藉由移除特殊字元來重新命名 VHD 檔案。|
|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>第一個 MB (2048 KB) 磁碟分割 (僅適用于 Linux) 

當您提交 VHD 時，請確定 VHD 的前 2048 KB 是空的。 否則，您的要求將會失敗 *。

>[!NOTE]
>* 針對某些特殊映射（例如建置於 Azure Marketplace 的 Azure Windows 基礎映射之上），我們會檢查帳單標記，並在帳單標記存在且符合我們的內部可用值時，忽略 MB 的磁碟分割。


## <a name="steps-for-creating-first-mb-2048-kb-partition-only-for-linux-on-an-empty-vhd"></a>建立第一個 MB (2048 KB) 分割 (的步驟，僅適用于空白 VHD 上的 Linux) 

步驟1：建立任何類型的 VM (範例： Ubuntu、分幣 OS 等) 。 填寫必要欄位，然後按一下 [下一步：磁片>] \
![下一步：磁片命令](./media/create-vm/vm-certification-issues-solutions-15.png)

步驟2：為上述 VM 建立非受控磁片。
![建立非受控磁片](./media/create-vm/vm-certification-issues-solutions-16.png)

請注意，您可以使用預設值，或為 NIC、NSG 和公用 IP 等欄位指定任意值。

步驟3：建立 VM 之後，請按一下左側的 [磁片]，如下所示， ![ 按一下 [磁片]](./media/create-vm/vm-certification-issues-solutions-17.png)

步驟4：請將您的 VHD 做為資料磁片連結至上述 VM，以如下所示建立分割區資料表。
![附加您的 VHD](./media/create-vm/vm-certification-issues-solutions-18.png)

按一下 [新增 >datadisk-> 現有的 Blob-> 流覽您的 VHD 儲存體帳戶-> 容器-> 選取 VHD-> 按一下 [確定]，如下所示 \
![選取 VHD](./media/create-vm/vm-certification-issues-solutions-19.png)

您的 VHD 將新增為數據磁片 LUN 0，請在新增磁片之後重新開機 VM

步驟5：重新開機 VM 之後，使用 Putty (或任何其他用戶端) 登入 VM，然後執行 "sudo-i" 命令以取得根存取權。

![登入 VM](./media/create-vm/vm-certification-issues-solutions-20.png)

步驟6：依照下列步驟在您的 VHD 上建立磁碟分割。

) 類型 fdisk/dev/sdb 命令

b) 若要從您的 VHD 查看現有的資料分割清單，請輸入 p

c) 類型 d 來刪除 VHD 中所有現有的磁碟分割 (您可以略過此步驟（如果不需要的話）) ![ 刪除所有現有的磁碟分割](./media/create-vm/vm-certification-issues-solutions-21.png)

d) 輸入 n 以建立新的分割區，然後選取 p 作為 (主要磁碟分割) 。

e) 請輸入2048作為「第一個磁區」值，您可以保留「最後一個磁區」，因為它會接受預設值。請注意，在 2048 KB 之前，將會清除所有資料。
           
>[!NOTE]
>* 請注意，建立磁碟分割之後，任何現有的資料都會被清除到 2048 KB 為止，因此建議您先備份 VHD，再執行上述命令。

請參閱下列螢幕擷取畫面，以取得您的參考。
![清除的資料](./media/create-vm/vm-certification-issues-solutions-22.png)

f) 輸入 w 來確認建立資料分割。 

![建立分割區](./media/create-vm/vm-certification-issues-solutions-23.png)

g) 您可以藉由執行命令 n fdisk/dev/sdb 並輸入 p 來驗證資料分割資料表，然後您可以看到如下所示的資料分割是以 2048 offset 值建立。 

 ![2048位移](./media/create-vm/vm-certification-issues-solutions-24.png)

步驟7：請將 VHD 從 VM 卸離，並刪除 VM。

         
## <a name="steps-for-creating-first-mb-2048-kb-partition-only-for-linux-by-moving-the-existing-data-on-vhd"></a>建立第一個 MB (2048 KB)  (分割區的步驟，僅適用于 Linux) ，藉由在 VHD 上移動現有資料

步驟1：建立任何類型的 VM (範例： Ubuntu、分幣 OS 等) 。 填寫必要欄位，然後按一下 [下一步：磁片>] \
![按一下 [下一步：磁片>]](./media/create-vm/vm-certification-issues-solutions-15.png)

步驟2：為上述 VM 建立非受控磁片。
![建立非受控磁片](./media/create-vm/vm-certification-issues-solutions-16.png)

請注意，您可以使用預設值，或為 NIC、NSG 和公用 IP 等欄位指定任意值。

步驟3：建立 VM 之後，請按一下左側的 [磁片]，如下所示， ![ 按一下 [磁片]](./media/create-vm/vm-certification-issues-solutions-17.png)

步驟4：請將您的 VHD 做為資料磁片連結至上述 VM，以如下所示建立分割區資料表。
![分割區資料表](./media/create-vm/vm-certification-issues-solutions-18.png)

按一下 [新增 >datadisk-> 現有的 Blob-> 流覽您的 VHD 儲存體帳戶-> 容器-> 選取 VHD-> 按一下 [確定]，如下所示 \
![選取 VHD](./media/create-vm/vm-certification-issues-solutions-19.png)

您的 VHD 將新增為數據磁片 LUN 0，請在新增磁片之後重新開機 VM

步驟5：重新開機 VM 之後，使用 Putty 登入 VM，然後執行 "sudo-i" 命令以取得根存取權。 \
![重新開機後登入](./media/create-vm/vm-certification-issues-solutions-20.png)

步驟6：請 excute 命令 echo ' + 1M，' |sfdisk--move-資料/dev/sdc-N 1 ![ Execute 命令](./media/create-vm/vm-certification-issues-solutions-25.png)

>[!NOTE]
>* 請注意，上述命令可能需要更多時間才能完成，因為這取決於磁片的大小。

步驟7：請將 VHD 從 VM 卸離，並刪除 VM。


## <a name="default-credentials"></a>預設認證

一律確定預設認證不會與提交的 VHD 一起傳送。 新增預設認證會使 VHD 更容易遭受安全性威脅。 當您提交 VHD 時，請改為建立您自己的認證。
  
## <a name="datadisk-mapped-incorrectly"></a>>datadisk 對應不正確

使用多個資料磁片提交要求，但其訂單未依序，這會被視為對應問題。 例如，如果有三個數據磁片，編號順序必須為 *0、1、2* 。 任何其他訂單都會被視為對應問題。

使用適當的資料磁片排序來重新提交要求。

## <a name="incorrect-os-mapping"></a>作業系統對應不正確

建立映射時，它可能會對應至或指派錯誤的 OS 標籤。 例如，當您在建立映射時選取 [ **Windows** ] 做為作業系統名稱的一部分時，作業系統磁片應該只安裝在 windows 上。 相同需求適用于 Linux。

## <a name="vm-not-generalized"></a>VM 未一般化

如果要重複使用從 Azure Marketplace 取得的所有映射，則必須將作業系統 VHD 一般化。

* 針對 **linux** ，下列程式會一般化 Linux vm，並將它重新部署為個別的 vm。

  在 SSH 視窗中，輸入下列命令： `sudo waagent -deprovision+user`

* 針對 **windows** ，您可以使用來一般化 windows 映射 `sysreptool` 。

如需此工具的詳細資訊，請參閱 [系統準備 (Sysprep) 總覽]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

## <a name="datadisk-errors"></a>>datadisk 錯誤

如需與資料磁片相關之錯誤的解決方案，請使用下表：

|錯誤|原因|解決方法|
|---|---|---|
|`DataDisk- InvalidUrl:`|此錯誤的發生原因可能是在提交供應專案時，為邏輯單元編號 (LUN) 指定的號碼無效。|確認資料磁片的 LUN 編號順序合作夥伴中心。|
|`DataDisk- NotFound:`|發生此錯誤的原因可能是資料磁片不是位於指定的 SAS URL。|確認資料磁片位於要求中所指定的 SAS URL。|
|

## <a name="remote-access-issue"></a>遠端存取問題

如果未啟用 Windows 映像的遠端桌面通訊協定 (RDP) 選項，您將會收到此錯誤。 

請先啟用 Windows 映像的 RDP 存取，然後再提交。

## <a name="bash-history-failed"></a>Bash 歷程記錄失敗

如果您提交的映射中 bash 歷程記錄的大小超過 1 kb (KB) ，您將會看到此錯誤。 大小限制為 1 KB，以確保不會在 bash 記錄檔中捕捉任何可能的機密資訊。

以下是刪除「Bash 歷程記錄」的步驟。

步驟 1： 部署 VM，並按一下 Azure 入口網站上的 [執行命令] 選項。
![Azure 入口網站上的 Run 命令](./media/create-vm/vm-certification-issues-solutions-3.png)

步驟 2： 選取第一個選項 "RunShellScript"，然後執行下列命令。

命令： "貓/dev/null > ~/.bash_history && 歷程記錄-c" ![ Azure 入口網站上的 bash 歷程記錄命令](./media/create-vm/vm-certification-issues-solutions-4.png)

步驟 3： 成功執行命令之後，請重新開機 VM。

步驟 4： 將 VM 一般化，取得映射 VHD 並停止 VM。

步驟 5。 Re-Submit 一般化映射。

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a> (自訂範本) 在選擇性測試的 VM 映射上要求例外狀況

發行者可以針對 VM 認證期間執行的少數測試要求例外狀況。 在極少數的情況下，當發行者提供證據來支援要求時，會提供例外狀況。 認證小組保留隨時拒絕或核准例外狀況的權利。

在下列各節中，我們將討論要求例外狀況的主要案例，以及如何要求例外狀況。

### <a name="scenarios-for-exception"></a>例外狀況的案例

通常在三個案例/案例中，發行者會要求例外狀況。

- **一或多個測試案例的例外** 狀況-發行者聯絡合作夥伴中心 [支援人員](https://aka.ms/marketplacepublishersupport) 要求測試案例的例外狀況。

- **鎖定的 vm/無根目錄存取** –少數的發行者會有 vm 需要鎖定的案例，因為 vm 上已安裝防火牆等軟體。 在此情況下，發行者可以下載 [經認證的測試控管](https://aka.ms/AzureCertificationTestTool) ，並在合作夥伴中心 [支援服務](https://aka.ms/marketplacepublishersupport)上提交報告。

- **自訂範本** -某些發行者會發佈需要自訂 ARM 範本的 vm 映射來部署 vm。 在此情況下，發行者應該在合作夥伴中心 [支援](https://aka.ms/marketplacepublishersupport) 中提交自訂範本，讓認證小組可以使用相同的驗證。

### <a name="information-to-provide-for-exception-scenarios"></a>針對例外狀況案例提供的資訊

發行者應聯絡合作夥伴中心 [支援](https://aka.ms/marketplacepublishersupport) ，以在上述案例中要求例外狀況，並提供額外的下列資訊：

   1. 發行者識別碼–合作夥伴中心入口網站上的發行者識別碼
   2. 供應專案識別碼/名稱–要求例外狀況的供應專案識別碼/名稱
   3. SKU/方案識別碼–要求例外狀況的 VM 供應專案的方案識別碼/sku
   4. 版本–要求例外狀況的 VM 供應專案版本
   5. 例外狀況類型–測試、鎖定的 VM、自訂範本
   6. 要求的原因-此例外狀況的原因，以及要豁免之測試的相關資訊
   7. 時間軸-已要求這個例外狀況的日期
   8. 附件-附加任何重要的辨識項檔。 針對鎖定的 Vm，請附加測試報告和自訂範本，提供自訂的 ARM 範本作為附件。 無法連接已鎖定 Vm 的報告，以及自訂範本的自訂 ARM 範本，將會導致拒絕要求

## <a name="address-a-vulnerability-or-exploit-in-a-vm-offer"></a>解決 VM 供應專案中的弱點或惡意探索

本節說明當您使用其中一個 VM 映射發現弱點或惡意探索時，如何提供新的 VM 映射。 這僅適用于發佈至 Azure Marketplace 的 Azure 虛擬機器供應專案。

> [!NOTE]
> 您無法從方案中移除最後一個 VM 映射，或停止銷售供應專案的最後一個方案。

請執行下列其中一項：

- 如果您有新的 VM 映射來取代易受攻擊的 VM 映射，請參閱下方 [提供的固定 vm 映射](#provide-a-fixed-vm-image) 。
- 如果您沒有新的 VM 映射來取代方案中的唯一 VM 映射，或您已完成此計畫，請 [停止銷售方案](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)。
- 如果您不打算更換供應專案中的唯一 VM 映射，建議您 [停止銷售供應](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)專案。

### <a name="provide-a-fixed-vm-image"></a>提供固定的 VM 映射

若要提供固定的 VM 映射來取代有弱點或惡意探索的 VM 映射，請執行下列動作：

1. 提供新的 VM 映射來解決安全性弱點或惡意探索。
2. 移除具有安全性弱點或惡意探索的 VM 映射。
3. 重新發佈供應專案。

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>提供新的 VM 映射來解決安全性弱點或惡意探索

若要完成這些步驟，您必須為想要新增的 VM 映射準備技術資產。 如需詳細資訊，請參閱使用 [已核准的基底建立虛擬機器](azure-vm-create-using-approved-base.md) ，或 [使用您自己的映射建立虛擬機器](azure-vm-create-using-own-image.md)，並 [產生 VM 映射的 SAS URI](azure-vm-get-sas-uri.md)。

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [ **商業 Marketplace**  >  **總覽** ]。
3. 在 [ **供應專案別名** ] 資料行中，選取供應專案。
4. 在 [ **計畫總覽** ] 索引標籤的 [ **名稱** ] 欄中，選取您要新增 VM 的方案。
5. 在 [ **技術** 設定] 索引標籤的 [ **VM 映射** ] 底下，選取 [ **+ 新增 VM 映射** ]。

> [!NOTE]
> 您一次只能將一個 VM 映射新增至一個方案。 若要新增多個 VM 映射，請在新增下一個 VM 映射之前，先將第一個映射發佈。

6. 在出現的方塊中，提供新的磁片版本和虛擬機器映射。
7. 選取 [儲存草稿]。

繼續進行下一節，以移除具有安全性弱點的 VM 映射。

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>移除具有安全性弱點或惡意探索的 VM 映射

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [ **商業 Marketplace**  >  **總覽** ]。
3. 在 [ **供應專案別名** ] 資料行中，選取供應專案。
4. 在 [ **計畫總覽** ] 索引標籤的 [ **名稱** ] 欄中，選取您要移除 VM 的方案。
5. 在 [ **技術** 設定] 索引標籤的 [ **vm 映射** ] 底下，選取您要移除的 vm 映射旁的 [ **移除 vm 映射** ]。
6. 在出現的對話方塊中，選取 [ **繼續** ]。
7. 選取 [儲存草稿]。

繼續進行下一節以重新發佈供應專案。

#### <a name="republish-the-offer"></a>重新發佈供應專案

1. 選取 [ **審核併發布** ]。
2. 如果您需要向認證小組提供任何資訊，請將其新增至 [憑證 **的附注** ] 方塊。
3. 選取 [發佈]  。

若要完成發佈程式，請參閱 [審核和發佈](review-publish-offer.md)供應專案。

## <a name="next-steps"></a>後續步驟

- [設定 VM 供應項目屬性](azure-vm-create-properties.md)
- [主動式 marketplace 獎勵](partner-center-portal/marketplace-rewards.md)
- 如果您有任何問題或意見反應要改進，請聯絡合作夥伴中心 [支援服務](https://aka.ms/marketplacepublishersupport)。
