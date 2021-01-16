---
title: Azure Marketplace 的虛擬機器 (VM) 認證疑難排解
description: 針對 Azure Marketplace 的測試和驗證虛擬機器 (VM) 映射的常見問題進行疑難排解。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 921c05b76640935a1bd9e65d556933c23093e5b2
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251432"
---
# <a name="troubleshoot-virtual-machine-certification"></a>針對虛擬機器認證進行疑難排解

當您將虛擬機器 (VM) 映射發佈到 Azure Marketplace 時，Azure 小組會進行驗證，以確保其可開機、安全且與 Azure 相容。 如果您的 VM 映射未通過任何高品質的測試，則不會發佈。 您將會收到描述問題的錯誤訊息。

本文說明在 VM 映射發佈期間常見的錯誤訊息，以及相關的解決方案。

> [!NOTE]
> 如果您對本文有任何問題或改進建議，請聯絡 [合作夥伴中心支援服務](https://aka.ms/marketplacepublishersupport)。


## <a name="vm-extension-failure"></a>VM 延伸模組失敗

查看您的映射是否支援 VM 擴充功能。

若要啟用 VM 擴充功能：

1. 選取您的 Linux VM。
1. 移至 [ **診斷設定**]。
1. 藉由更新 **儲存體帳戶** 來啟用基底矩陣。
1. 選取 [儲存]。

   ![顯示如何啟用來賓層級監視的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-1.png)

若要確認 VM 擴充功能是否已正確啟用：

1. 在 VM 中，選取 [ **VM 擴充** 功能] 索引標籤，然後確認 **Linux 診斷擴充** 功能的狀態。
1. 檢查布建狀態。

   - 如果狀態為「布建 *成功*」，則會傳遞延伸模組測試案例。  
   - 如果狀態為「布建 *失敗*」，擴充功能測試案例會失敗，而您必須設定強化旗標。

   ![顯示布建成功的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-2.png)

   如果 VM 擴充功能失敗，請參閱 [使用 Linux 診斷擴充功能來監視計量和記錄](../virtual-machines/extensions/diagnostics-linux.md) 以加以啟用。 如果您不想要啟用 VM 擴充功能，請洽詢支援小組，並要求他們停用它。

## <a name="vm-provisioning-issue"></a>VM 布建問題

請檢查以確定您已遵循 VM 布建程式，再提交您的供應專案。 若要查看提供 VM 的 JSON 格式，請參閱 [測試虛擬機器映射](azure-vm-image-test.md)。

布建問題可能包含下列失敗案例：

|狀況|錯誤|原因|解決方案|
|---|---|---|---|
|1| (VHD) 的虛擬硬碟無效|如果 VHD 頁尾中指定的 cookie 值不正確，VHD 將會視為無效。|重新建立映射並提交要求。|
|2|不正確 blob 類型|VM 布建失敗，因為使用的區塊是 blob 類型，而不是頁面類型。|重新建立映射並提交要求。|
|3|布建超時或未正確一般化|VM 一般化有問題。|使用一般化重新建立映射並提交要求。|

> [!NOTE]
> 如需 VM 一般化的詳細資訊，請參閱：
> - [Linux 檔](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows 檔](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>VHD 規格

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie 和其他 VHD 規格

' Conectix ' 字串是 VHD 規格的一部分。 它會在 VHD 頁尾中定義為8位元組的 cookie，以識別檔案建立者。 Microsoft 建立的所有 VHD 檔案都有此 cookie。 

VHD 格式化 blob 的格式應為512位元組頁尾：

|硬碟頁尾欄位|大小 (位元組)|
|---|---|
Cookie|8
功能|4
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

為了確保順利發佈體驗，請確定您的 VHD 符合下列準則：

- Cookie 包含字串 ' conectix '。
- 磁片類型是固定的。
- VHD 的虛擬大小至少為 20 MB。
- VHD 已對齊。 虛擬大小必須是 1 MB 的倍數。
- VHD blob 長度等於虛擬大小，加上 VHD 頁尾長度 (512) 。

下載 [VHD 規格](https://www.microsoft.com/download/details.aspx?id=23850)。

## <a name="software-compliance-for-windows"></a>Windows 的軟體合規性

如果您的 Windows 映像要求因為軟體合規性問題而遭到拒絕，您可能已使用安裝的 SQL Server 實例建立 Windows 映像。 相反地，您必須從 Azure Marketplace 取得相關的 SQL Server 版本基礎映射。

請勿建立您自己的 Windows 映像，並在其中安裝 SQL Server。 使用已核准的 SQL Server 基礎映射， (企業/標準/web) 的 Azure Marketplace。

如果您嘗試安裝 Visual Studio 或任何 Office 授權的產品，請洽詢支援小組以取得先前的核准。

如需選取核准的基礎的詳細資訊，請參閱 [從核准的基底建立虛擬機器](azure-vm-create-using-approved-base.md)。

## <a name="toolkit-test-case-execution-failed"></a>工具組測試案例執行失敗

Microsoft 認證工具組可協助您執行測試案例，並確認您的 VHD 或映射與 Azure 環境相容。

下載 [Microsoft 認證工具](azure-vm-image-test.md)組。

### <a name="linux-test-cases"></a>Linux 測試案例

下表列出工具組將執行的 Linux 測試案例。 [描述] 中會陳述測試驗證。

|狀況|測試案例|說明|
|---|---|---|
|1|Bash 歷程記錄|您應先清除 Bash 記錄檔，再建立 VM 映射。|
|2|Linux 代理程式版本|應安裝 Azure Linux 代理程式2.2.41 或更新版本。|
|3|必要的核心參數|確認已設定下列核心參數： <br>主控台 = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|在 OS 磁片上交換磁碟分割|確認未在作業系統磁片上建立交換磁碟分割。|
|5|OS 磁片上的根磁碟分割|為 OS 磁片建立單一根磁碟分割。|
|6|OpenSSL 版本|OpenSSL 版本應為 v 0.9.8 或更新版本。|
|7|Python 版本|強烈建議使用 Python 2.6 版或更新版本。|
|8|用戶端存活間隔|將 ClientAliveInterval 設定為180。 在應用程式需要時，可以將它設定為30到235。 如果您要為終端使用者啟用 SSH，則必須設定此值，如所述。|
|9|作業系統架構|僅支援 64 位元作業系統。|
|10|自動更新|識別 Linux 代理程式自動更新是否已啟用。|

### <a name="common-test-case-errors"></a>常見的測試案例錯誤

請參閱下表，以瞭解執行測試案例時可能會看到的常見錯誤：

| 狀況 | 測試案例 | 錯誤 | 解決方法 |
| --- | --- | --- | --- |
| 1 | Linux 代理程式版本測試案例 | Linux 代理程式的最低版本是2.2.41 或更新版本。 自2020年5月1日起，這項需求是強制的。 | 更新 Linux 代理程式版本。 它應該是2.241 或更新版本。 如需詳細資訊，請造訪 [Linux 代理程式版本更新頁面](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。 |
| 2 | Bash 歷程記錄測試案例 | 如果您提交的映射中 Bash 歷程記錄的大小超過 1 kb (KB) ，就會發生錯誤。 大小會限制為 1 KB，以確保您的 Bash 歷程記錄檔不會包含任何可能的機密資訊。 | 解決方法是將 VHD 掛接到另一個工作中的 VM，並進行變更，將大小縮減為 1 KB 或更少。 例如，刪除記錄檔 `.bash` 。 |
| 3 | 必要的核心參數測試案例 | 當的值未設為時，您將會收到這個錯誤 `console` `ttyS0` 。 執行下列命令來進行檢查： <br /> `cat /proc/cmdline` | 將的值設定 `console` 為 `ttyS0` ，然後重新提交要求。 |
| 4 | ClientAlive 間隔測試案例 | 如果工具組在此測試案例中提供您失敗的結果，則會有不適當的值 `ClientAliveInterval` 。 | 將的值設定 `ClientAliveInterval` 為小於或等於235，然後重新提交要求。 |


### <a name="windows-test-cases"></a>Windows 測試案例

下表列出工具組將執行的 Windows 測試案例，以及測試驗證的描述：

|狀況 |測試案例|說明|
|---|---|---|
|1|作業系統架構|Azure 僅支援64位作業系統。|
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

如果您在上述測試案例中遇到任何失敗，請參閱此方案之表格中的 [ **描述** ] 資料行。 如需詳細資訊，請洽詢支援小組。 

## <a name="data-disk-size-verification"></a>資料磁片大小驗證

大小大於 1023 gb 的資料磁片要求 (GB) 將不會獲得核准。 這項規則適用于 Linux 和 Windows。

重新提交大小小於或等於 1023 GB 的要求。

## <a name="os-disk-size-validation"></a>作業系統磁片大小驗證

請參閱下列規則，以瞭解 OS 磁片大小的限制。 當您提交任何要求時，請確認作業系統磁片大小在 Linux 或 Windows 的限制內。

|OS|建議的 VHD 大小|
|---|---|
|Linux|1 GB 至 1023 GB|
|Windows|30 GB 至 250 GB|

由於 Vm 允許存取基礎作業系統，因此請確定 VHD 大小對 VHD 而言夠大。 磁片無法展開而不需要停機。 使用從 30 GB 到 50 GB 的磁片大小。

|VHD 大小|實際佔用的大小|解決方案|
|---|---|---|
|>500 tib (TiB) |n/a|請洽詢支援小組以取得例外狀況核准。|
|250-500 TiB|>200 32,767 gib (GiB) 與 blob 大小的差異|請洽詢支援小組以取得例外狀況核准。|

> [!NOTE]
> 較大的磁片大小會產生較高的成本，並會在安裝和複寫過程中造成延遲。 由於此延遲和成本，支援小組可能會尋求例外狀況核准的理由。

## <a name="wannacry-patch-verification-test-for-windows"></a>適用于 Windows 的 WannaCry patch 驗證測試

若要避免與 WannaCry 病毒相關的潛在攻擊，請確定所有 Windows 映像要求都會以最新的修補程式更新。

您可以從或確認影像檔案 `C:\windows\system32\drivers\srv.sys` 版本 `srv2.sys` 。

下表顯示 Windows Server 的最小修補版本： 

|OS|版本|
|---|---|
|Windows 服務 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|

> [!NOTE]
> Windows Server 2019 沒有任何強制的版本需求。

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
||14.04 LTS|4.15.0-1049- \* -azure|
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
|CoreOS 穩定2079.6。0|4.19.43\*|
||Beta 2135.3。1|4.19.50\*|
||Alpha 2163.2。1|4.19.50\*|
|Debian|jessie (安全性) |3.16.68-2|
||jessie 反向移植|4.9.168-1 + deb9u3|
||stretch (安全性) |4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (buster) |Debian 6.3.0-18 + deb9u1|
||buster、sid (stretch 反向移植) |4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>影像大小應為 mb 的倍數

Azure 上的所有 Vhd 必須具有與 1 mb (MB) 的倍數相符的虛擬大小。 如果您的 VHD 未遵守建議的虛擬大小，您的要求可能會遭到拒絕。

當您從原始磁片轉換成 VHD 時，請遵循指導方針。 確定原始磁片大小是 1 MB 的倍數。 如需詳細資訊，請參閱 [非背書發行](../virtual-machines/linux/create-upload-generic.md)版的資訊。

## <a name="vm-access-denied"></a>拒絕存取 VM

在 VM 上執行測試案例的 _拒絕存取_ 問題可能是因為許可權不足所造成。

檢查您是否已針對執行自我測試案例的帳戶啟用適當的存取權。 如果未啟用，請啟用執行測試案例的存取權。 如果您不想要啟用存取權，您可能會與支援小組共用自我測試案例的結果。

若要以 SSH 停用的映射提交要求以進行認證程式：

1. 針對您映射上的 [Azure vm 執行最新的認證測試控管](https://aka.ms/AzureCertificationTestTool) 。

2. 提出 [支援票證](https://aka.ms/marketplacepublishersupport)。 請務必附加工具組報表，並提供供應專案詳細資料：
   - 供應項目名稱
   - 發行者名稱
   - 方案識別碼/SKU 和版本

3. 重新提交您的認證要求。

## <a name="download-failure"></a>下載失敗
    
請參閱下表，以瞭解當您下載具有共用存取簽章 (SAS) URL 的 VM 映射時所發生的任何問題。

|狀況|錯誤|原因|解決方案|
|---|---|---|---|
|1|找不到 Blob|VHD 可能會從指定的位置刪除或移動。|| 
|2|使用中的 Blob|VHD 會由另一個內部進程使用。|當您使用 SAS URL 下載 VHD 時，該 VHD 應該會處於使用中狀態。|
|3|不正確 SAS URL|VHD 的相關聯 SAS URL 不正確。|取得正確的 SAS URL。|
|4|簽章無效|VHD 的相關聯 SAS URL 不正確。|取得正確的 SAS URL。|
|6|HTTP 條件式標頭|SAS URL 無效。|取得正確的 SAS URL。|
|7|VHD 名稱無效|查看 VHD 名稱中是否有任何特殊字元，例如百分比符號 `%` 或引號 `"` 。|藉由移除特殊字元來重新命名 VHD 檔案。|

## <a name="first-1-mb-partition-2048-sectors-each-sector-of-512-bytes"></a>前 1 MB 分割區 (2048 磁區，每個512位元組的磁區) 

如果您要 [建立自己的映射](azure-vm-create-using-own-image.md)，請確定作業系統磁片的前2048個磁區 (1 MB) 是空的。 否則，您的發行將會失敗。 這項需求只適用于 OS 磁片， (不會) 資料磁片。 如果您是 [從已核准的基底](azure-vm-create-using-approved-base.md)建立映射，則可以略過這項需求。 

### <a name="create-a-1-mb-partition-2048-sectors-each-sector-of-512-bytes-on-an-empty-vhd-linux-only-steps"></a>建立 1 MB 的磁碟分割 (2048 個磁區，每個512位元組的磁區) 于空白 VHD (僅限 Linux 步驟) 

這些步驟僅適用于 Linux。

1. 建立任何類型的 Linux VM，例如 Ubuntu、分幣 OS 或其他。 填寫必要的欄位，然後選取 **[下一步：磁片 >]**。

   ![顯示 [建立虛擬機器] 頁面的螢幕擷取畫面，其中已醒目提示 [下一步：磁片] 命令按鈕。](./media/create-vm/vm-certification-issues-solutions-15.png)

1. 為您的 VM 建立非受控磁片。

   您可以使用預設值，或為 NIC、NSG 和公用 IP 等欄位指定任意值。

   ![建立虛擬機器流程中 [資料磁片] 頁面的螢幕擷取畫面影像。](./media/create-vm/vm-certification-issues-solutions-16.png)

1. 建立 VM 之後，請在左窗格中選取 [ **磁片** ]。

   ![顯示如何選取 VM 磁片的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-17.png)

1. 將 VHD 以資料磁片的形式連結至 VM，以建立磁碟分割資料表。

   1. 選取 [**新增 >datadisk**  >  的 **現有 Blob**]。

      ![顯示如何將資料磁片新增至 VHD 的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. 尋找您的 VHD 儲存體帳戶。
   1. 選取 [ **容器** ]，然後選取您的 VHD。
   1. 選取 [確定]。

      ![[連接非受控磁片] 頁面的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-19.png)

      您的 VHD 將會新增為數據磁片 LUN 0。

   1. 重新啟動 VM。

1. 在您重新開機 VM 之後，請使用 Putty 或其他用戶端登入 VM，然後執行 `sudo  -i` 命令以取得根存取權。

   ![顯示 sudo-i 命令的 Putty 用戶端命令列螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-20.png)

1. 在您的 VHD 上建立磁碟分割。

   1. 輸入 `fdisk /dev/sdb` 命令。
   1. 若要從 VHD 查看現有的資料分割清單，請輸入 `p` 。
   1. 輸入 `d` 以刪除 VHD 中所有可用的現有磁碟分割。 如果不需要，您可以略過此步驟。

      ![Putty 用戶端命令列螢幕擷取畫面，其中顯示用來刪除現有磁碟分割的命令。](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. 輸入 `n` 以建立新的磁碟分割，然後選取做 `p` 為 (主要分割區) 。

   1. 輸入2048作為 _第一個磁區_ 的值。 您可以將 _最後一個磁區_ 保留為預設值。

      >[!IMPORTANT]
      >任何現有的資料將會清除到2048個磁區 (每個512位元組) 的磁區為止。 建立新的分割區之前，請先備份 VHD。

      ![Putty 用戶端命令列螢幕擷取畫面，其中顯示已清除資料的命令和輸出。](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. 輸入 `w` 以確認磁碟分割的建立。 

      ![Putty 用戶端命令列螢幕擷取畫面，其中顯示用來建立磁碟分割的命令。](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. 您可以藉由執行命令並輸入，來驗證磁碟分割資料表 `n fdisk /dev/sdb` `p` 。 您會看到資料分割是以2048位移值所建立。 

      ![Putty 用戶端命令列螢幕擷取畫面，其中顯示用來建立2048位移的命令。](./media/create-vm/vm-certification-issues-solutions-24.png)

1. 從 VM 卸離 VHD 並刪除 VM。

### <a name="create-a-first-1-mb-partition-2048-sectors-each-sector-of-512-bytes-by-moving-existing-data-on-vhd"></a>藉由在 VHD 上移動現有資料，建立前 1 MB 的磁碟分割 (2048 個磁區，每個512位元組的磁區) 

這些步驟僅適用于 Linux。

1. 建立任何類型的 Linux VM，例如 Ubuntu、分幣 OS 或其他。 填寫必要的欄位，然後選取 **[下一步：磁片 >]**。

   ![顯示 [建立虛擬機器] 頁面的螢幕擷取畫面，其中已醒目提示 [下一步：磁片] 命令按鈕。](./media/create-vm/vm-certification-issues-solutions-15.png)

1. 為您的 VM 建立非受控磁片。

   ![建立虛擬機器流程中 [資料磁片] 頁面的螢幕擷取畫面影像。](./media/create-vm/vm-certification-issues-solutions-16.png)

   您可以使用預設值，或為 NIC、NSG 和公用 IP 等欄位指定任意值。

1. 建立 VM 之後，請在左窗格中選取 [ **磁片** ]。

   ![顯示如何選取 VM 磁片的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-17.png)

1. 將 VHD 以資料磁片的形式連結至 VM，以建立磁碟分割資料表。

   1. 將 VHD 以資料磁片的形式連結至 VM，以建立磁碟分割資料表。

   1. 選取 [**新增 >datadisk**  >  的 **現有 Blob**]。

      ![顯示如何將資料磁片新增至 VHD 的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. 尋找您的 VHD 儲存體帳戶。
   1. 選取 [ **容器** ]，然後選取您的 VHD。
   1. 選取 [確定]。

      ![[連接非受控磁片] 頁面的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-19.png)

      您的 VHD 將會新增為數據磁片 LUN 0。

   1. 重新啟動 VM。

1. 使用 Putty 或其他用戶端登入 VM，然後執行 `sudo  -i` 命令以取得根存取權。

   ![Putty 用戶端命令列螢幕擷取畫面，其中顯示 [登入] 和 [sudo-i] 命令。](./media/create-vm/vm-certification-issues-solutions-20.png)

1. 執行命令 `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`。

   ![Putty 用戶端命令列螢幕擷取畫面，其中顯示命令的執行。](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >此命令可能需要一些時間才能完成，因為它相依于磁片的大小。

1. 從 VM 卸離 VHD 並刪除 VM。


## <a name="default-credentials"></a>預設認證

絕不會使用提交的 VHD 傳送預設認證。 新增預設認證會使 VHD 更容易遭受安全性威脅。 當您提交 VHD 時，請改為建立您自己的認證。
  
## <a name="datadisk-mapped-incorrectly"></a>>datadisk 對應不正確

當提交的多個資料磁片未依序送出要求時，可能會發生對應問題。 例如，三個數據磁片的編號順序必須為 *0、1、2*。 任何其他訂單都會被視為對應問題。

使用適當的資料磁片排序來重新提交要求。

## <a name="incorrect-os-mapping"></a>作業系統對應不正確

建立映射時，它可能會對應至或指派錯誤的 OS 標籤。 例如，當您在建立映射時選取 [ **Windows** ] 做為作業系統名稱的一部分時，作業系統磁片應該只安裝在 windows 上。 相同需求適用于 Linux。

## <a name="vm-not-generalized"></a>VM 未一般化

如果要重複使用從 Azure Marketplace 取得的所有映射，則必須將作業系統 VHD 一般化。

* 針對 **linux**，下列程式會一般化 Linux vm，並將它重新部署為個別的 vm。

  在 SSH 視窗中，輸入下列命令：`sudo waagent -deprovision+user`。

* 針對 **windows**，您可以使用來一般化 windows 映射 `sysreptool` 。

  如需此工具的詳細資訊 `sysreptool` ，請參閱 [系統準備 (Sysprep) 總覽](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

## <a name="datadisk-errors"></a>>datadisk 錯誤

如需與資料磁片相關之錯誤的解決方案，請使用下表：

|錯誤|原因|解決方案|
|---|---|---|
|`DataDisk- InvalidUrl:`|發生此錯誤的原因可能是在提交供應專案時， (LUN) 不正確邏輯單元編號。|確認資料磁片的 LUN 編號順序合作夥伴中心。|
|`DataDisk- NotFound:`|發生此錯誤的原因可能是資料磁片不是位於指定的 SAS URL。|確認資料磁片位於指定的 SAS URL。|

## <a name="remote-access-issue"></a>遠端存取問題

如果未啟用 Windows 映像的遠端桌面通訊協定 (RDP) 選項，您將會收到此錯誤。

請先啟用 Windows 映像的 RDP 存取，然後再提交。

## <a name="bash-history-failed"></a>Bash 歷程記錄失敗

如果您提交的映射中 Bash 歷程記錄的大小超過 1 kb (KB) ，您將會看到此錯誤。 大小限制為 1 KB，以限制檔案不包含可能的機密資訊。

若要刪除 Bash 記錄：

1. 部署 VM，並選取 Azure 入口網站上的 **執行命令** 選項。

   ![左窗格中 [執行命令] 選項 Azure 入口網站的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-3.png)

1. 選取第一個選項 **RunShellScript** ，然後執行命令： `cat /dev/null > ~/.bash_history && history -c` 。

   ![Azure 入口網站上 [執行命令腳本] 頁面的螢幕擷取畫面。](./media/create-vm/vm-certification-issues-solutions-4.png)

1. 順利執行命令之後，請重新開機 VM。

1. 將 VM 一般化、取得映射 VHD，然後停止 VM。

1. 重新提交一般化映射。

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>針對選取的測試在 VM 映射上要求例外狀況

發行者可以針對在 VM 認證期間執行的幾個測試要求例外狀況。 當發行者提供辨識項以支援要求時，會在罕見的情況下提供例外狀況。 認證小組保留隨時拒絕或核准例外狀況的權利。

本節說明發行者要求例外狀況的一般案例，以及如何要求例外狀況。

### <a name="scenarios-for-exception"></a>例外狀況的案例

發行者通常會在下列情況下要求例外狀況：

- **一或多個測試案例的例外** 狀況。 請聯絡 [合作夥伴中心支援人員](https://aka.ms/marketplacepublishersupport) ，要求測試案例的例外狀況。

- **鎖定的 vm/無根目錄存取**。 有些發行者的案例是因為 vm 上已安裝防火牆等軟體，所以需要鎖定 Vm。 在此情況下，請下載 [經認證的測試控管](https://aka.ms/AzureCertificationTestTool) ，並在 [合作夥伴中心支援服務](https://aka.ms/marketplacepublishersupport)上提交報告。

- **自訂範本**。 某些發行者會發佈需要自訂 Azure Resource Manager (ARM) 範本的 VM 映射來部署 Vm。 在此情況下，請在 [合作夥伴中心支援](https://aka.ms/marketplacepublishersupport) 中提交自訂範本，讓認證小組可以使用它來進行驗證。

### <a name="information-to-provide-for-exception-scenarios"></a>針對例外狀況案例提供的資訊

請聯絡 [合作夥伴中心支援人員](https://aka.ms/marketplacepublishersupport) 以要求其中一個案例的例外狀況，並包含下列資訊：

- **發行者識別碼**。 輸入您的合作夥伴中心入口網站發行者識別碼。
- **供應專案識別碼/名稱**。 輸入供應專案識別碼或名稱。
- **SKU/方案識別碼**。 輸入 VM 供應專案方案識別碼或 SKU。
- [版本]。 輸入需要例外狀況的 VM 供應專案版本。
- **例外狀況類型**。 選擇測試、鎖定的 VM 或自訂範本。
- **要求的原因**。 包括例外狀況要求的原因，以及有關測試豁免的任何資訊。
- **時間軸**。 輸入例外狀況的結束日期。
- **附件**。 附加重要的辨識項檔：

  - 針對鎖定的 Vm，請附加測試報告。
  - 針對自訂範本，請提供自訂 ARM 範本作為附件。

  如果您無法包含這些附件，將會拒絕您的要求。

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>解決 VM 供應專案中的弱點或惡意探索

本節說明當您使用其中一個 VM 映射發現弱點或惡意探索時，如何提供新的 VM 映射。 它只適用于發佈至 Azure Marketplace 的 Azure VM 供應專案。

> [!NOTE]
> 您無法從方案中移除最後一個 VM 映射，或停止銷售供應專案的最後一個方案。

執行下列其中一個動作：

- 如果您有新的 VM 映射來取代易受攻擊的 VM 映射，請參閱 [提供固定的 vm 映射](#provide-a-fixed-vm-image)。
- 如果您沒有新的 VM 映射來取代方案中的唯一 VM 映射，或您已完成此計畫，請 [停止銷售方案](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)。
- 如果您不打算更換供應專案中的唯一 VM 映射，建議您 [停止銷售供應](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)專案。

### <a name="provide-a-fixed-vm-image"></a>提供固定的 VM 映射

若要提供固定的 VM 映射來取代具有弱點或惡意探索的 VM 映射：

1. 提供新的 VM 映射來解決安全性弱點或惡意探索。
1. 移除具有安全性弱點或惡意探索的 VM 映射。
1. 重新發佈供應專案。

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>提供新的 VM 映射來解決安全性弱點或惡意探索

若要完成這些步驟，請為您要新增的 VM 映射準備技術資產。 如需詳細資訊，請參閱使用 [已核准的基底建立虛擬機器](azure-vm-create-using-approved-base.md)，或 [使用您自己的映射建立虛擬機器](azure-vm-create-using-own-image.md) ，並 [產生 VM 映射的 SAS URI](azure-vm-get-sas-uri.md)。

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左窗格中，選取 [**商業 Marketplace**  >  **總覽**]。
1. 在 [ **供應專案別名** ] 資料行中，選取供應專案。
1. 在 [ **計畫總覽** ] 索引標籤的 [ **名稱** ] 欄中，選取適當的計畫。
1. 在 [ **技術** 設定] 索引標籤的 [ **VM 映射**] 底下，選取 [ **+ 新增 VM 映射**]。

   > [!NOTE]
   > 您一次只能將一個 VM 映射新增至一個方案。 若要新增多個 VM 映射，請在新增下一個 VM 映射之前先發佈第一個映射。

1. 在出現的方塊中，提供新的磁片版本和虛擬機器映射。
1. 選取 [儲存草稿]。

接下來，移除具有安全性弱點的 VM 映射。

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>移除具有安全性弱點或惡意探索的 VM 映射

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左窗格中，選取 [**商業 Marketplace**  >  **總覽**]。
3. 在 [ **供應專案別名** ] 資料行中，選取供應專案。
4. 在 [ **計畫總覽** ] 索引標籤的 [ **名稱** ] 欄中，選取適當的計畫。
5. 在 [ **技術** 設定] 索引標籤的 [ **vm 映射**] 底下，選取您要移除的 vm 映射旁的 [ **移除 vm 映射**]。
6. 在對話方塊中，選取 [ **繼續**]。
7. 選取 [儲存草稿]。

接下來，重新發佈供應專案。

#### <a name="republish-the-offer"></a>重新發佈供應專案

1. 選取 [ **審核併發布**]。
2. 如果您需要向認證小組提供任何資訊，請將其新增至 [憑證 **的附注** ] 方塊。
3. 選取 [發佈]。

若要完成發佈程式，請參閱 [審核和發佈](review-publish-offer.md)供應專案。

## <a name="next-steps"></a>後續步驟

- [設定 VM 供應項目屬性](azure-vm-create-properties.md)
- [主動式 marketplace 獎勵](partner-center-portal/marketplace-rewards.md)
- 如果您有任何問題或意見反應要改進，請聯絡 [合作夥伴中心支援服務](https://aka.ms/marketplacepublishersupport)。
