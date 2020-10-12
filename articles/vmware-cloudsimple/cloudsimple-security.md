---
title: 適用于 CloudSimple 服務的 Azure VMware Solution by CloudSimple-Security
description: 描述 CloudSimple 服務安全性的共用責任模型
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7310c037511fdf1a7d6f3763fd61d292078ea83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86998882"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple 安全性總覽

本文概述 CloudSimple 服務、基礎結構和資料中心如何在 Azure VMware 解決方案上實行安全性。 您將瞭解資料保護和安全性、網路安全性，以及弱點和修補程式的管理方式。

## <a name="shared-responsibility"></a>共同責任

Azure VMware Solution by CloudSimple 使用共同責任模型來進行安全性。 雲端中受信任的安全性是透過客戶和 Microsoft 即服務提供者的共同責任來達成。 此責任的矩陣可提供較高的安全性，並消除單一失敗點。

## <a name="azure-infrastructure"></a>Azure 基礎結構

Azure 基礎結構安全性考慮包括資料中心和設備位置。

### <a name="datacenter-security"></a>資料中心安全性

Microsoft 擁有整個部門，專門用來設計、建立和操作支援 Azure 的實體設備。 這個團隊致力於維護最新的實體安全性。 如需實體安全性的詳細資訊，請參閱 [Azure 設施、內部部署和實體安全性](../security/fundamentals/physical-security.md)。

### <a name="equipment-location"></a>設備位置

執行私用雲端的裸機硬體設備會裝載于 Azure 資料中心位置。  該設備所在的網架需要生物特徵辨識式雙因素驗證才能取得存取權。

## <a name="dedicated-hardware"></a>專用硬體

在 CloudSimple 服務中，所有 CloudSimple 客戶都會取得專用的裸機主機，以及與其他租使用者硬體實際隔離的本機連接磁片。 具有 vSAN 的 ESXi 虛擬程式會在每個節點上執行。 節點是透過客戶專用的 VMware vCenter 和 NSX 來管理。 在租使用者之間共用硬體時，不會提供額外的隔離和安全性保護層。

## <a name="data-security"></a>資料安全性

客戶會保有其資料的控制權和擁有權。 客戶資料的資料監管是客戶的責任。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>待用資料的資料保護，以及內部網路內的移動中資料

若為私用雲端環境中的待用資料，您可以使用 vSAN 加密。 vSAN 加密可搭配在您自己的虛擬網路或內部部署 (KMS) 的 VMware 認證外部金鑰管理伺服器使用。  您可以自行控制資料加密金鑰。 針對在私用雲端內移動的資料，vSphere 支援透過網路對所有 vmkernel 流量進行資料加密， (包括 vMotion 流量) 。

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>透過公用網路移動所需資料的資料保護

若要保護透過公用網路移動的資料，您可以為您的私人雲端建立 IPsec 和 TLS VPN 通道。 支援一般加密方法，包括128位元組和256位元組 AES。 傳輸中的資料 (包括驗證、系統管理存取權和客戶資料) 會使用標準加密機制進行加密， (SSH、TLS 1.2 及安全的 RDP) 。 傳輸機密資訊的通訊會使用標準加密機制。

### <a name="secure-disposal"></a>安全處置

如果您的 CloudSimple 服務已過期或已終止，您必須負責移除或刪除您的資料。 CloudSimple 會與您合作，以刪除或傳回客戶合約中提供的所有客戶資料，除非相關法律需要 CloudSimple 的範圍，才能保留部分或所有的個人資料。 如果需要保留任何個人資料，CloudSimple 會封存資料並執行合理的量值，以防止客戶資料進行任何進一步的處理。

### <a name="data-location"></a>資料位置

設定私人雲端時，您可以選擇將在其中部署的 Azure 區域。 除非您執行資料移轉或異地資料備份，否則 VMware 虛擬機器資料不會從該實體資料中心移動。 您也可以視需要，在多個 Azure 區域中裝載工作負載和儲存資料。

位於私用雲端超交集節點的客戶資料不會在沒有租使用者系統管理員明確動作的情況下，不會跨越位置。 您必須負責以高可用性的方式來執行您的工作負載。

### <a name="data-backups"></a>資料備份

CloudSimple 不會備份或封存客戶資料。 CloudSimple 會執行 vCenter 和 NSX 資料的定期備份，以提供管理伺服器的高可用性。 在進行備份之前，會使用 VMware Api 在 vCenter 來源加密所有資料。 加密的資料會傳輸並儲存在 Azure blob 中。 備份的加密金鑰會儲存在 Azure 中 CloudSimple 虛擬網路中執行的高度安全 CloudSimple 受控保存庫中。

## <a name="network-security"></a>網路安全性

CloudSimple 解決方案依賴網路安全性層級。

### <a name="azure-edge-security"></a>Azure edge 安全性

CloudSimple 服務建置於 Azure 所提供的基本網路安全性之上。 Azure 針對與異常輸入或輸出流量模式以及分散式阻絕服務 (DDoS) 攻擊相關的網路型攻擊，套用深度防禦技術來偵測和及時回應。 此安全性控制適用于私用雲端環境，以及 CloudSimple 所開發的控制平面軟體。

### <a name="segmentation"></a>分割

CloudSimple 服務以邏輯方式分隔第2層網路，以限制在私人雲端環境中存取您自己的私人網路。 您可以使用防火牆進一步保護私用雲端網路。 CloudSimple 入口網站可讓您定義適用于所有網路流量的一或多個網路流量控制規則，包括私人雲端流量、內部私人雲端流量、網際網路的一般流量，以及透過 IPsec VPN 或 ExpressRoute 連線到內部部署的網路流量。

## <a name="vulnerability-and-patch-management"></a>弱點和修補程式管理

CloudSimple 負責定期進行受管理 VMware 軟體的安全性修補， (ESXi、vCenter 和 NSX) 。

## <a name="identity-and-access-management"></a>身分識別和存取管理

客戶可以使用多重要素驗證或 SSO 作為慣用的方式，在 Azure AD) 中驗證其 Azure 帳戶 (。 您可以從 Azure 入口網站啟動 CloudSimple 入口網站，而不需重新進入認證。

CloudSimple 支援針對私人雲端 vCenter 進行身分識別來源的選擇性設定。 您可以使用內部 [部署身分識別來源](set-vcenter-identity.md)、私人雲端的新身分識別來源或 [Azure AD](azure-ad.md)。

根據預設，客戶會獲得私人雲端內 vCenter 日常作業所需的許可權。 此許可權層級不包含 vCenter 的系統管理存取權。 如果暫時需要系統管理存取權，您可以在完成系統管理工作時，將 [您的許可權提升](escalate-private-cloud-privileges.md) 到一段有限的時間。
