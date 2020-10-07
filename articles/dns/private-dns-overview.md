---
title: 什麼是 Azure Private DNS？
description: 在本文中，請先參閱 Microsoft Azure 上的私人 DNS 主機服務概觀。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 540c4d6f2dde64cef9b5795d635ac6259bfc69c3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87985626"
---
# <a name="what-is-azure-private-dns"></a>什麼是 Azure Private DNS？

「網域名稱系統」(DNS) 會負責將服務名稱轉譯 (或解析) 為其 IP 位址。  Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構來提供名稱解析。 除了支援網際網路對向 DNS 網域之外，Azure DNS 也支援私人 DNS 區域。

Azure Private DNS 提供一個可靠、安全的 DNS 服務，讓您不必新增自訂 DNS 解決方案，就能管理及解析虛擬網路中的網域名稱。 藉由使用私人 DNS 區域，您就可以使用自己的自訂網域名稱，而不是現今可用的 Azure 提供名稱。 使用自訂網域名稱可協助您量身打造虛擬網路架構，來充分滿足您的組織需求。 它可以為虛擬網路內的虛擬機器 (VM) 及虛擬網路之間提供名稱解析。 此外，您還可以利用水平分割檢視來設定區域名稱，使私人與公用 DNS 區域能夠共用名稱。

若要從您的虛擬網路解析私人 DNS 區域的記錄，您必須將虛擬網路連結至該區域。 連結的虛擬網路具有完整存取權，且可解析在私人區域中發佈的所有 DNS 記錄。 此外，您也可以在虛擬網路連結上啟用自動註冊。 如果您在虛擬網路連結上啟用自動註冊，該虛擬網路上的虛擬機器 DNS 記錄會註冊在私人區域中。 啟用自動註冊時，Azure DNS 也會在每次建立虛擬機器時更新區域記錄，變更其 IP 位址，或者在刪除虛擬機器時更新。

![DNS 概觀](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 最佳做法是請勿為您的私人 DNS 區域使用 *.local* 網域。 並非所有作業系統都支援它。

## <a name="benefits"></a>優點

Azure Private DNS 提供下列優點：

* **不再需要自訂 DNS 解決方案**。 以前，許多客戶建立自訂 DNS 解決方案來管理其虛擬網路中的 DNS 區域。 您現在可以使用原生 Azure 基礎結構來管理 DNS 區域，擺脫了建立和管理自訂 DNS 解決方案的負擔。

* **使用所有常見的 DNS 記錄類型**。 Azure DNS 支援 A、AAAA、CNAME、MX、PTR、SOA、SRV 及 TXT 記錄。

* **自動主機名稱記錄管理**。 除了裝載您的自訂 DNS 記錄之外，Azure 還會自動維護所指定虛擬網路中 VM 的主機名稱記錄。 在此案例中，您無須建立自訂 DNS 解決方案或修改應用程式，即可將您使用的網域名稱最佳化。

* **虛擬網路之間的主機名稱解析**。 不同於 Azure 提供的主機名稱，私人 DNS 區域可以在虛擬網路之間共用。 此功能簡化了跨網路及服務探索案例，例如虛擬網路對等互連。

* **熟悉的工具和使用者體驗**。 為了縮短學習曲線，這個服務使用已經成熟的 Azure DNS 工具 (Azure 入口網站、Azure PowerShell、Azure CLI、Azure Resource Manager 範本和 REST API)。

* **水平分割 DNS 支援**。 Azure DNS 可讓您建立具有相同名稱，但從虛擬網路內與從公用網際網路會解析成不同答案的區域。 典型的水平分割 DNS 案例是提供一個專用的服務版本以供在您的虛擬網路內使用。

* **所有 Azure 區域均可使用**。 Azure DNS 私人區域已在 Azure 公用雲端中的所有 Azure 區域內正式運作。

## <a name="capabilities"></a>功能

Azure DNS 提供以下功能：

* **若虛擬網路連結至已啟用自動註冊的私人區域，為此虛擬網路中的虛擬機器進行自動註冊**。 虛擬機器將會在私人區域中註冊 (新增) 為指向其私人 IP 位址的 A 記錄。 在已啟用自動註冊的虛擬網路連結中虛擬機器遭到刪除時，Azure DNS 也會自動從連結的私人區域中移除對應的 DNS 記錄。

* **針對已連結到私人區域的多個虛擬網路，支援正向 DNS 解析**。 針對跨虛擬網路的 DNS 解析，並沒有虛擬網路彼此間對等互連的明確相依性。 不過，您可能會想在其他案例中 (例如 HTTP 流量) 讓虛擬網路對等互連。

* **在虛擬網路範圍內支援反向 DNS 查閱**。 針對指派到私人區域的虛擬網路內私人 IP 位址的反向 DNS 查閱，將會傳回包含主機/記錄名稱以及用區域名稱作為尾碼的 FQDN。

## <a name="other-considerations"></a>其他考量

Azure DNS 有下列限制：

* 如果已啟用 VM DNS 記錄的自動註冊，則特定虛擬網路只能連結到一個私人區域。 不過您可以將多個虛擬網路連結至單一 DNS 區域。
* 反向 DNS 只適用於已連結虛擬網路中的私人 IP 空間
* 已連結虛擬網路的私人 IP 位址反向 DNS 會傳回 *internal.cloudapp.net* 作為虛擬機器的預設尾碼。 若虛擬網路連結至已啟用自動註冊的私人區域，私人 IP 位址的反向 DNS 會傳回兩個 FQDN，一個具有預設尾碼 *internal.cloudapp.net*，另一個則具有私人區域的尾碼。
* 目前無法原生支援條件式轉送。 若要啟用 Azure 和內部部署網路之間的解析，請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
 
## <a name="pricing"></a>定價

如需價格相關資訊，請參閱 [Azure DNS 定價](https://azure.microsoft.com/pricing/details/dns/)。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。

* 如需了解 DNS 區域和記錄，請瀏覽：[DNS 區域和記錄概觀](dns-zones-records.md)。

* 了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。
