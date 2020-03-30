---
title: Azure VMware 解決方案（按雲簡單 ） - 雲簡單服務的安全性
description: 描述雲簡單服務安全性的共用責任模型
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024988"
---
# <a name="cloudsimple-security-overview"></a>雲簡單安全概述

本文概述了如何通過雲簡單服務、基礎結構和資料中心在 Azure VMware 解決方案上實現安全性。 瞭解資料保護和安全、網路安全以及如何管理漏洞和修補程式。

## <a name="shared-responsibility"></a>共同責任

雲簡單 Azure VMware 解決方案使用共用責任模型進行安全性保護。 雲中值得信賴的安全性是通過客戶和 Microsoft 作為服務提供者的共同責任實現的。 這種責任矩陣提供了更高的安全性，並消除了單點故障。

## <a name="azure-infrastructure"></a>Azure 基礎結構

Azure 基礎結構安全注意事項包括資料中心和設備位置。

### <a name="datacenter-security"></a>資料中心安全性

Microsoft 的整個部門都致力於設計、構建和操作支援 Azure 的物理設施。 這個團隊致力於維護最新的實體安全性。 有關物理安全的詳細資訊，請參閱[Azure 設施、場所和物理安全](../security/azure-physical-security.md)。

### <a name="equipment-location"></a>設備位置

運行私有雲的裸機硬體設備託管在 Azure 資料中心位置。  該設備所在的籠子需要基於生物識別的雙因素認證才能獲得訪問。

## <a name="dedicated-hardware"></a>專用硬體

作為 CloudSimple 服務的一部分，所有 CloudSimple 客戶都獲得專用裸機主機，這些主機具有本地連接的磁片，這些磁片與其他租戶硬體物理隔離。 具有 vSAN 的 ESXi 虛擬機器管理程式在每個節點上運行。 節點通過客戶專用 VMware vCenter 和 NSX 進行管理。 租戶之間不共用硬體可提供額外的隔離和安全保護層。

## <a name="data-security"></a>資料安全性

客戶保持對其資料的控制和擁有權。 客戶資料的資料管理是客戶的責任。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>內部網路中靜止資料和動態資料的資料保護

對於私有雲環境中靜態的資料，可以使用 vSAN 加密。 vSAN 加密適用于您自己的虛擬網路或本地的 VMware 認證外部金鑰管理伺服器 （KMS）。  您可以自己控制資料加密金鑰。 對於私有雲中正在啟動的資料，vSphere 支援對所有 vmkernel 流量（包括 vMotion 流量）通過線路加密資料。

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>資料資料保護，用於在公共網路中移動所需的資料

為了保護通過公共網路傳輸的資料，可以為私有雲創建 IPsec 和 SSL VPN 隧道。 支援通用加密方法，包括 128 位元組和 256 位元組 AES。 傳輸中的資料（包括身份驗證、管理訪問和客戶資料）通過標準加密機制（SSH、TLS 1.2 和安全 RDP）進行加密。 傳輸敏感資訊的通信使用標準加密機制。

### <a name="secure-disposal"></a>安全處置

如果您的 CloudSimple 服務過期或終止，您有責任刪除或刪除您的資料。 CloudSimple 將與您合作，刪除或退回客戶協定中提供的所有客戶資料，除非適用法律要求 CloudSimple 保留部分或全部個人資料。 如果需要保留任何個人資料，CloudSimple 將存檔資料並實施合理措施，以防止客戶資料進一步處理。

### <a name="data-location"></a>資料位置

設置私有雲時，請選擇將部署雲的 Azure 區域。 除非您執行資料移轉或異地資料備份，否則 VMware 虛擬機器資料不會從該物理資料中心移動。 如果需要，還可以在多個 Azure 區域中託管工作負荷和將資料存儲。

如果沒有租戶管理員的顯式操作，駐留在私有雲超融合節點的客戶資料不會遍歷位置。 您有責任以高度可用的方式實現工作負載。

### <a name="data-backups"></a>資料備份

雲簡單不會備份或存檔客戶資料。 CloudSimple 會定期備份 vCenter 和 NSX 資料，以提供高可用性的管理伺服器。 在備份之前，所有資料都使用 VMware API 在 vCenter 源進行加密。 加密資料在 Azure Blob 中傳輸和存儲。 備份的加密金鑰存儲在 Azure 中的 CloudSimple 虛擬網路中運行的高度安全的 CloudSimple 託管保存庫中。

## <a name="network-security"></a>網路安全性

CloudSimple 解決方案依賴于網路安全層。

### <a name="azure-edge-security"></a>Azure 邊緣安全性

CloudSimple 服務構建在 Azure 提供的基本網路安全之上。 Azure 應用縱深防禦技術來檢測並及時回應與異常入侵或出口流量模式和分散式阻斷服務 （DDoS） 攻擊相關的基於網路的攻擊。 此安全控制適用于私有雲環境和 CloudSimple 開發的控制平面軟體。

### <a name="segmentation"></a>分割

CloudSimple 服務具有邏輯上獨立的第 2 層網路，這些網路限制對私有雲環境中您自己的私人網路絡的訪問。 您可以使用防火牆進一步保護您的私有雲網路。 CloudSimple 門戶允許您為所有網路流量定義 EW 和 NS 網路流量控制規則，包括私有雲內部流量、私有雲間流量、Internet 的普通流量以及通過 IPsec VPN 或快速路由連接。

## <a name="vulnerability-and-patch-management"></a>漏洞和修補程式管理

CloudSimple 負責定期對託管 VMware 軟體（ESXi、vCenter 和 NSX）進行安全修補。

## <a name="identity-and-access-management"></a>身分識別和存取管理

客戶可以使用多重要素驗證或首選 SSO 對其 Azure 帳戶（Azure AD 中）進行身份驗證。 在 Azure 門戶中，無需重新輸入憑據即可啟動 CloudSimple 門戶。

CloudSimple 支援私有雲 vCenter 標識源的可選配置。 可以使用[本地標識源](set-vcenter-identity.md)、私有雲的新標識源或[Azure AD](azure-ad.md)。

預設情況下，客戶將獲得在私有雲中進行 vCenter 日常操作所需的許可權。 此權限等級不包括對 vCenter 的管理存取權限。 如果臨時需要管理存取權限，則可以在完成管理工作時在有限時間內[升級許可權](escalate-private-cloud-privileges.md)。
