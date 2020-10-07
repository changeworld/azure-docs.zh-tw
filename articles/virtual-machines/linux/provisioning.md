---
title: Linux 佈建概觀
description: 如何帶入您的 Linux VM 映像或建立新映像以在 Azure 中使用的概觀。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a7d9aa7de8bb75a22acc85c77924765eaa1b6b3b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87080143"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux VM 佈建
當您從一般化映像 (共用映像庫或受控映像) 建立 VM 時，控制平面可讓您建立 VM，並將參數和設定傳遞至 VM。 這稱為 VM「佈建」。 在佈建期間，平台會在虛擬機器啟動時，讓必要的 VM Create 參數值 (主機名稱、使用者名稱、密碼、SSH 金鑰、customData) 可供 VM 使用。 

內建在映像內的佈建代理程式會與平台連接，連線到多個獨立的佈建介面，將屬性和訊號設定至已完成的平台。 

佈建代理程式可以是 [Azure Linux 代理程式](../extensions/agent-linux.md) 或 [cloud-init](./using-cloud-init.md)。 這些是建立一般化映像的[必要條件](create-upload-generic.md)。

佈建代理程式會為所有經過背書的 [Azure Linux 散發套件](./endorsed-distros.md)提供支援，而且在許多情況下，您會發現經過背書的散發版本映像會隨附於 cloud-init 和 Linux 代理程式。 這可讓您選擇讓 cloud-init 處理佈建，然後 Linux 代理程式將提供處理 [Azure 擴充功能](../extensions/features-windows.md)的支援。 提供擴充功能的支援表示 VM 接著有資格支援其他 Azure 服務，例如 VM 密碼重設、Azure 監視、Azure 備份、Azure 磁碟加密等。

佈建完成之後，就會在每次開機時執行 cloud-init。 cloud-init 會監視 VM 的變更，例如網路變更、掛接和格式化暫時磁碟，以及啟動 Linux 代理程式。 Linux 代理程式會持續在伺服器上執行，並從 Azure 平台尋找「目標狀態」(新的設定)，因此每當您安裝擴充功能時，代理程式就能夠進行處理。

雖然目前有兩個佈建代理程式，但是您應該選擇 cloud-init 作為佈建代理程式，應該安裝 Linux 代理程式以提供擴充功能支援。 這可讓您充分利用平台最佳化，並且可讓您停用/移除 Linux 代理程式，如需有關如何在沒有代理程式的情況下建立映像，以及如何移除的詳細資訊，請參閱這份[文件](disable-provisioning.md)。

如果您有無法支援執行任一代理程式的 Linux 核心，但是想要設定一些 VM Create 屬性 (例如 hostname、customData、userName、password、ssh 金鑰)，本文件中會討論如何[在沒有代理程式的情況下建立一般化映像](no-agent.md)，並且符合平台需求。


## <a name="provisioning-agent-responsibilities"></a>佈建代理程式責任

**映像佈建**
  
- 建立使用者帳戶
- 設定 SSH 驗證類型
- 部署 SSH 公開金鑰和金鑰組
- 設定主機名稱
- 將主機名稱發佈至平台 DNS
- 將 SSH 主機金鑰和指紋報告給平台
- 資源磁碟管理
- 格式化和掛接資源磁碟
- 使用和處理 `customData`
 
**網路功能**
  
- 管理路由以提高平台 DHCP 伺服器的相容性
- 確保網路介面名稱的穩定性

**核心**
  
- 設定虛擬 NUMA (核心 < `2.6.37` 時停用)
- 針對 `/dev/random` 使用 Hyper-V 熵
- 設定根裝置 (可能在遠端) 的 SCSI 逾時

**診斷**
  
- 將主控台重新導向至序列埠

## <a name="communication"></a>通訊
資訊經由兩個管道從平台流向代理程式：

- 在 IaaS 部署中，開機時連接的 DVD。 DVD 包含 OVF 相容組態檔，內含實際 SSH 金鑰組以外的所有佈建資訊。
- TCP 端點，公開可用來取得部署和拓撲組態的 REST API。


## <a name="azure-provisioning-agent-requirements"></a>Azure 佈建代理程式需求
Linux 代理程式和 cloud-init 需要一些系統套件才能正確運作：
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- 檔案系統公用程式：`sfdisk`、`fdisk`、`mkfs`、`parted`
- 密碼工具：chpasswd、sudo
- 文字處理工具：sed、grep
- 網路工具：ip-route
- 掛接 UDF 檔案系統的核心支援。

## <a name="next-steps"></a>後續步驟

如有需要，您可以[停用佈建並且移除 Linux 代理程式](disable-provisioning.md)。
