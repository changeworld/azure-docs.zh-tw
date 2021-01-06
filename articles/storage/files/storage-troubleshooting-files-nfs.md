---
title: 針對 Azure NFS 檔案共用問題進行疑難排解-Azure 檔案儲存體
description: 針對 Azure NFS 檔案共用問題進行疑難排解。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2a37c86268d2424971058021044c60185a25348f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916451"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>針對 Azure NFS 檔案共用進行疑難排解

本文列出一些與 Azure NFS 檔案共用相關的常見問題。 當遇到這些問題時，它會提供潛在的原因和因應措施。

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" 失敗：不正確引數 (22) 

### <a name="cause-1-idmapping-is-not-disabled"></a>原因1：未停用 idmapping
Azure 檔案儲存體不允許英數位元 UID/GID。 因此必須停用 idmapping。 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>原因2： idmapping 已停用，但在遇到錯誤的檔案/目錄名稱之後重新啟用
即使已正確停用 idmapping，在某些情況下也會覆寫停用 idmapping 的設定。 例如，當 Azure 檔案儲存體遇到錯誤的檔案名時，會傳回錯誤。 在看到此特定錯誤碼時，NFS v 4.1 Linux 用戶端決定重新啟用 idmapping，未來的要求會以英數位元 UID/GID 再次傳送。 如需 Azure 檔案儲存體上不支援的字元清單，請參閱這 [篇文章](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#:~:text=The%20Azure%20File%20service%20naming%20rules%20for%20directory,be%20no%20more%20than%20255%20characters%20in%20length)。 冒號是不支援的其中一個字元。 

### <a name="workaround"></a>因應措施
檢查 idmapping 是否已停用，而且沒有重新啟用它，然後執行下列動作：

- 將共用取消掛接
- 使用 # echo Y 停用識別碼對應 >/sys/module/nfs/parameters/nfs4_disable_idmapping
- 掛上共用
- 如果正在執行 rsync，請從沒有任何錯誤 dir/檔案名的目錄中，執行具有 "— numeric id" 引數的 rsync。

## <a name="unable-to-create-an-nfs-share"></a>無法建立 NFS 共用

### <a name="cause-1-subscription-is-not-enabled"></a>原因1：訂用帳戶未啟用

您的訂用帳戶可能尚未註冊 Azure 檔案儲存體 NFS 預覽版。 您將需要從 Cloud Shell 或本機終端機執行幾個額外的 commandlet，以啟用此功能。

> [!NOTE]
> 您可能必須等候最多30分鐘的時間才能完成註冊。


#### <a name="solution"></a>解決方案

使用下列腳本來註冊功能和資源提供者，並 `<yourSubscriptionIDHere>` 在執行腳本之前取代：

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>原因2：不支援的儲存體帳戶設定

NFS 僅適用于具有下列設定的儲存體帳戶：

- 層級-Premium
- 帳戶種類-FileStorage
- 冗余-LRS
- 區域- [支援的區域清單](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-nfs-shares?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>解決方案

遵循本文中的指示： [如何建立 NFS 共用](storage-files-how-to-create-nfs-shares.md)。

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>原因3：儲存體帳戶是在註冊完成之前建立的

為了讓儲存體帳戶使用此功能，必須在訂用帳戶完成 NFS 註冊之後建立。 註冊完成最多可能需要30分鐘的時間。

#### <a name="solution"></a>解決方案

註冊完成後，請遵循本文中的指示： [如何建立 NFS 共用](storage-files-how-to-create-nfs-shares.md)。

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>無法連接或掛接 Azure NFS 檔案共用

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>原因1：要求源自不受信任的網路/不受信任 IP 中的用戶端

與 SMB 不同的是，NFS 沒有以使用者為基礎的驗證。 共用的驗證是以您的網路安全性規則設定為基礎。 基於這個原因，為了確保只會對您的 NFS 共用建立安全的連接，您必須使用服務端點或私人端點。 若要從內部部署存取私人端點以外的共用，您必須設定 VPN 或 ExpressRoute。 系統會忽略新增至儲存體帳戶的防火牆允許清單中的 Ip。 您必須使用下列其中一種方法來設定 NFS 共用的存取權：


- [服務端點](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - 由公用端點存取
    - 僅適用于相同的區域。
    - VNet 對等互連不會授與您共用的存取權。
    - 每個虛擬網路或子網都必須個別新增至允許清單。
    - 針對內部部署存取，服務端點可搭配 ExpressRoute、點對站和站對站 Vpn 使用，但我們建議使用私人端點，因為它比較安全。

下圖描述使用公用端點的連接。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="公用端點連線能力的圖表。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [私人端點](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - 存取比服務端點更安全。
    - 您可以從儲存體帳戶的 Azure 區域內部和外部，透過私人連結存取 NFS 共用 (跨區域、內部部署) 
    - 虛擬網路對等互連與裝載于私人端點的虛擬網路對等互連，可讓 NFS 共用存取對等互連虛擬網路中的用戶端。
    - 私人端點可與 ExpressRoute、點對站和站對站 Vpn 搭配使用。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="私人端點連線能力的圖表。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>原因2：已啟用需要安全傳輸

尚未對 NFS 共用支援雙重加密。 Azure 會使用 MACSec，為 Azure 資料中心之間傳輸中的所有資料提供加密層級。 NFS 共用只能從受信任的虛擬網路，以及透過 VPN 通道存取。 NFS 共用上沒有其他可用的傳輸層加密。

#### <a name="solution"></a>解決方案

停用儲存體帳戶設定分頁中所需的安全傳輸。

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="儲存體帳戶設定分頁的螢幕擷取畫面，需要停用安全傳輸。":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>原因3： nfs-未安裝一般套件
執行掛接命令之前，請執行下列發行版本專用的命令來安裝套件。

若要檢查是否已安裝 NFS 封裝，請執行： `rpm qa | grep nfs-utils`

#### <a name="solution"></a>解決方案

如果未安裝套件，請在您的散發套件上安裝套件。

##### <a name="ubuntu-or-debian"></a>Ubuntu 或 Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora、Red Hat Enterprise Linux 8 +、CentOS 8 +

使用 dnf 套件管理員： `sudo dnf install nfs-common` 。

較舊版本的 Red Hat Enterprise Linux 和 CentOS 使用 yum 套件管理員： `sudo yum install nfs-common` 。

##### <a name="opensuse"></a>openSUSE

使用 zypper 套件管理員： `sudo zypper install-nfscommon` 。

### <a name="cause-4-firewall-blocking-port-2049"></a>原因4：防火牆封鎖埠2049

NFS 通訊協定會透過埠2049與其伺服器通訊，請確定此埠已開放給 (NFS 伺服器) 的儲存體帳戶。

#### <a name="solution"></a>解決方案

藉由執行下列命令，確認已在用戶端上開啟埠2049： `telnet <storageaccountnamehere>.file.core.windows.net 2049` 。 如果埠未開啟，請開啟它。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
