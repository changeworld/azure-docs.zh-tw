---
title: 將微軟 Azure 與 Oracle 雲端基礎架構整合 |微軟文件
description: 瞭解將在 Microsoft Azure 上運行的 Oracle 應用與 Oracle 雲端基礎架構 (OCI) 中的資料庫整合的解決方案。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b5d5ebda850163989e6ef1af94ec623d2d758f07
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878284"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>整合微軟 Azure 和 Oracle 雲端基礎架構的 Oracle 應用程式解決方案

Microsoft 和 Oracle 已合作提供低延遲、高吞吐量的跨雲端連接,使您能夠充分利用這兩種雲。 

使用此跨雲端連接,可以分區多層應用程式,以在Oracle雲端結構 (OCI)以及Microsoft Azure上的應用程式和其他層上運行資料庫層。 體驗類似於在單個雲端中運行整個解決方案堆疊。 

如果您有興趣完全在 Azure 基礎結構上部署 Oracle 解決方案,請參閱[Oracle VM 映像及其在 Microsoft Azure 上的部署](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>案例概觀

跨雲端連接為您提供了在 Azure 虛擬機器上運行 Oracle 產業領先的應用程式和您自己的自訂應用程式的解決方案,同時享受 OCI 中託管資料庫服務的優勢。 

> [!IMPORTANT]
> 在 2020 年 5 月之前,Oracle 將在 Azure/Oracle 雲端互連解決方案中驗證這些應用程式在 Azure 中運行。

可在跨雲端設定中執行的應用程式包括:

* 電子商務套件
* JD Edwards EnterpriseOne
* PeopleSoft
* 甲骨文零售應用程式
* 甲骨文海龍財務管理

下圖是連接的解決方案的高級概述。 為簡單起見,該關係圖僅顯示應用程式層和數據層。 根據應用程式體系結構,解決方案可以包括其他層,如 Azure 中的 Web 層。 如需詳細資訊，請參閱下列幾節。

![Azure OCI 解決方案概述](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>區域可用性 

跨雲連接僅限於以下區域:
* Azure 東美國(東部) & OCI 阿什本 (美國東部)
* Azure UK 南部(英國南部)& OCI 倫敦(英國南部)
* Azure 加拿大中部(加拿大中部)& OCI 多倫多(加拿大東南部)
* Azure 西歐(西歐) & OCI 阿姆斯特丹 (荷蘭西北部)

## <a name="networking"></a>網路功能

出於各種業務和運營原因,企業客戶通常選擇在多個雲上實現工作負載多樣化和部署。 為了實現多樣化,客戶使用互聯網、IPSec VPN 或使用雲端供應商的直接連接解決方案透過您的本地網路互連雲端網路。 互聯雲網路可能需要在時間、資金、設計、採購、安裝、測試和運營方面進行大量投資。 

為了應對這些客戶挑戰,Oracle 和 Microsoft 實現了整合的多雲體驗。 跨雲網路是透過將Microsoft Azure中的[ExpressRoute](../../../expressroute/expressroute-introduction.md)電路與OCI中的[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)電路連接而建立的。 如果 Azure ExpressRoute 對等互連位置靠近或與 OCI FastConnect 位於同一對等位置,則可以進行這種連接。 此設置允許在兩個雲之間實現安全、快速的連接,而無需中間服務提供者。

使用 ExpressRoute 和 FastConnect,客戶可以在 Azure 中與 OCI 中的虛擬雲端網路對等虛擬網路,前提是專用 IP 位址空間不重疊。 對等兩個網路允許虛擬網路中的資源與 OCI 虛擬雲網路中的資源進行通信,就像它們都在同一網路中一樣。

## <a name="network-security"></a>網路安全性

網路安全是任何企業應用程式的關鍵元件,也是此多雲解決方案的核心。 通過 ExpressRoute 和 FastConnect 的任何流量都透過專用網路。 此設定允許在 Azure 虛擬網路和 Oracle 虛擬雲端網路之間進行安全通訊。 不需要向 Azure 中的任何虛擬機器提供公共 IP 位址。 同樣,您不需要 OCI 中的互聯網閘道。 所有通信都通過計算機的專用 IP 位址進行。

此外,您還可以在 OCI 虛擬雲網路和安全規則(附加到 Azure[網路安全組](../../../virtual-network/security-overview.md))上設置[安全清單](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)。 使用這些規則控制虛擬網路中計算機之間的流量。 可以在計算機級別、子網級別以及虛擬網路級別添加網路安全規則。
 
## <a name="identity"></a>身分識別

身份是微軟和甲骨文之間夥伴關係的核心支柱之一。 在將[Oracle 識別雲服務](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html)(IDCS) 與[Azure 活動目錄](../../../active-directory/index.yml)(Azure AD) 整合方面做了大量工作。 Azure AD 是Microsoft基於雲的標識和存取管理服務。 它可以説明您的用戶登錄並訪問各種資源。 Azure AD 還允許您管理使用者及其許可權。

目前,此整合允許您在一個中心位置進行管理,即 Azure 活動目錄。 Azure AD 將目錄中的任何更改與相應的 Oracle 目錄同步,並用於單次登錄到跨雲 Oracle 解決方案。

## <a name="next-steps"></a>後續步驟

開始使用 Azure 和 OCI 之間的[跨雲端網路](configure-azure-oci-networking.md)。 

有關 OCI 的詳細資訊和白皮書,請參閱[Oracle 雲](https://docs.cloud.oracle.com/iaas/Content/home.htm)文檔。
