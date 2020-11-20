---
title: 整合 Microsoft Azure 與 Oracle Cloud Infrastructure | Microsoft Docs
description: 了解用於整合 Microsoft Azure 上所執行 Oracle 應用程式與 Oracle Cloud Infrastructure (OCI) 中資料庫的解決方案。
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: e8e9bf23c236f805135d7d46b969c564975448ac
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965403"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>用於整合 Microsoft Azure 與 Oracle Cloud Infrastructure 的 Oracle 應用程式解決方案

Microsoft 與 Oracle 合作提供高輸送量的低延遲跨雲端連線，以供充分利用這兩個雲端的優點。 

使用此跨雲端連線，您可分割多層式應用程式，在 Oracle Cloud Infrastructure (OCI) 上執行資料庫層，並在 Microsoft Azure 上執行應用程式層及其他層級。 其體驗類似於在單一雲端中執行整個解決方案堆疊。 

如果想要在 Azure 基礎結構上執行中介軟體 (包括 WebLogic Server)，但想要在 OCI 內執行 Oracle 資料庫，請參閱 [WebLogic Server Azure 應用程式](oracle-weblogic.md)。

如果想要將 Oracle 解決方案完全部署在 Azure 基礎結構上，請參閱 [Microsoft Azure 上的 Oracle VM 映像及其部署](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>案例概觀

跨雲端連線提供一個解決方案，以供在 Azure 虛擬機器上執行 Oracle 領先業界的應用程式，以及自有的自訂應用程式，同時享有在 OCI 中託管資料庫服務的優點。 

自2020到5月為止，下列應用程式已通過跨雲端設定的認證：

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail 應用程式
* Oracle Hyperion Financial Management

下列圖表是已連線解決方案的高階概觀。 為簡單起見，此圖表只會顯示應用程式層和資料層。 視應用程式架構而定，解決方案可能會包含其他層級，例如 Azure 中的 WebLogic Server 叢集或 Web 層。 如需詳細資訊，請參閱下列幾節。

![Azure OCI 解決方案概觀](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>區域可用性 

跨雲端連線僅限於下列區域：
* Azure 美國東部 (EastUS) 與 OCI 維吉尼亞州阿什本 (美國東部)
* Azure 英國南部 (UKSouth) 與 OCI 倫敦 (英國南部)
* Azure 加拿大中部 (CanadaCentral) 與 OCI 多倫多 (加拿大東南部)
* Azure 西歐 (WestEurope) & OCI 阿姆斯特丹 (荷蘭西北部)
* Azure 日本東部 (JapanEast) 與 OCI 東京 (日本東部)
* Azure 美國西部 (WestUS) & OCI 聖馬利諾 (美國西部) 

## <a name="networking"></a>網路功能

企業客戶通常會基於各種商務和營運理由，選擇將工作負載多樣化並部署到多個雲端。 為多樣化經營，客戶會使用網際網路、IPSec VPN，或透過內部部署網路使用雲端提供者的直接連線解決方案來與雲端網路互連。 與雲端網路互連需要投入大量的時間、金錢、設計、採購、安裝、測試和作業。 

為解決這些客戶挑戰，Oracle 與 Microsoft 提供了一個整合式多重雲端體驗。 跨雲端網路的建立方式是將 Microsoft Azure 中的 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 線路與 OCI 中的 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) 線路連線。 如果 Azure ExpressRoute 對等互連位置與 OCI FastConnect 對等互連位置很接近或相同，則可進行此連線。 此設定可供在兩個雲端之間進行安全快速的連線，而不需要中繼服務提供者。

使用 ExpressRoute 和 FastConnect，只要私人 IP 位址空間未重疊，客戶即可將 Azure 中虛擬網路與 OCI 中的虛擬雲端網路對等互連。 將這兩個網路對等互連可供虛擬網路中資源與 OCI 虛擬雲端網路中的資源進行通訊，就像是兩者位於相同的網路一樣。

## <a name="network-security"></a>網路安全性

網路安全性是任何企業應用程式的重要元件，且是這個多重雲端解決方案的核心。 通過 ExpressRoute 和 FastConnect 的任何流量都會透過私人網路傳遞。 此設定可供在 Azure 虛擬網路與 Oracle 虛擬雲端網路之間進行安全通訊。 您不需要為 Azure 中的任何虛擬機器提供公用 IP 位址。 同樣地，OCI 中不需要網際網路閘道。 所有通訊都會透過電腦的私人 IP 位址進行。

此外，您可設定有關 OCI 虛擬雲端網路的[安全性清單](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)以及安全性規則 (已附加至 Azure [網路安全性群組](../../../virtual-network/network-security-groups-overview.md))。 使用這些規則可控制虛擬網路中電腦之間流動的流量。 您可在電腦層級、子網路層級及虛擬網路層級新增網路安全性規則。

[WebLogic Server Azure 應用程式](oracle-weblogic.md)會各建立一個網路安全性群組，已預先設定為使用 WebLogic Server 的連接埠設定。
 
## <a name="identity"></a>身分識別

身分識別是 Microsoft 與 Oracle 之間合作關係的其中一個核心要素。 已完成整合 [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) 與 [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) 的重要工作。 Azure AD 是 Microsoft 的雲端式身分識別和存取管理服務。 使用者可透過 Azure AD 的協助來登入及存取各種資源。 Azure AD 也可供管理使用者及其權限。

目前，這項整合可供在一個中央位置 (也就是 Azure Active Directory) 進行管理。 Azure AD 會將目錄中的任何變更與對應的 Oracle 目錄進行同步，並用於對跨雲端 Oracle 解決方案進行單一登入。

## <a name="next-steps"></a>後續步驟

開始使用 Azure 與 OCI 之間的[跨雲端網路](configure-azure-oci-networking.md)。 

如需 OCI 的詳細資訊和技術白皮書，請參閱 [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) 文件。
