---
title: 整合 Microsoft Azure 與 Oracle 雲端基礎結構 |Microsoft Docs
description: 瞭解將在 Microsoft Azure 上執行的 Oracle 應用程式與 Oracle 雲端基礎結構（OCI）中的資料庫整合的解決方案。
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
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: e1249913300be532cc6514f1478bbc6f4183c001
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300548"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>整合 Microsoft Azure 和 Oracle 雲端基礎結構的 oracle 應用程式解決方案（預覽）

Microsoft 和 Oracle 合作提供低延遲、高輸送量的跨雲端連線能力，可讓您充分利用這兩個雲端的優點。 

使用此跨雲端連線，您可以分割多層式應用程式，以在 Oracle 雲端基礎結構（OCI）上執行您的資料庫層，以及 Microsoft Azure 上的應用程式和其他層級。 其體驗類似于在單一雲端中執行整個解決方案堆疊。 

> [!IMPORTANT]
> 這項跨雲端功能目前為預覽狀態，並[適用限制](#region-availability)。 若要建立 Azure 與 OCI 之間的低延遲連線，您的 Azure 訂用帳戶必須先啟用這項功能。 您必須完成這[份簡短問卷](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)，才能在預覽版中註冊。 當訂用帳戶註冊完成之後，您會收到電子郵件。 在收到確認電子郵件後才能使用此功能。 您也可以聯繫 Microsoft 代表，以供此預覽版啟用。 預覽功能的存取權受限於 Microsoft 自行決定的可用性和限制。 填寫問卷並不保證能夠存取。 此預覽版是在沒有服務等級協定的情況下提供，不應用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 Microsoft Azure 預覽的[補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

如果您想要完全在 Azure 基礎結構上部署 Oracle 解決方案，請參閱[Microsoft Azure 上的 ORACLE VM 映射和其部署](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>案例概觀

跨雲端連線可提供解決方案，讓您在 Azure 虛擬機器上執行 Oracle 領先業界的應用程式，以及您自己的自訂應用程式，同時享受 OCI 中託管資料庫服務的優點。 

您可以在跨雲端設定中執行的應用程式包括：

* 電子商務套件
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售應用程式
* Oracle Hyperion 財務管理

下圖是已連線解決方案的高階總覽。 為了簡單起見，此圖表只會顯示應用層和資料層。 根據應用程式架構而定，您的解決方案可以包含其他層級，例如 Azure 中的 web 層。 如需詳細資訊，請參閱下列幾節。

![Azure OCI 解決方案總覽](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>區域可用性 

跨雲端連線能力僅限於下欄區域：
* Azure 美國東部（eastus） & OCI 阿什本（美國東部）
* Azure 英國南部（uksouth） & OCI 倫敦（英國南部）
* Azure 加拿大中部（canadacentral） & OCI 多倫多（加拿大東南部）
* Azure 西歐（westeurope） & OCI 阿姆斯特丹（荷蘭西北部）

## <a name="networking"></a>網路功能

企業客戶通常會選擇在多個雲端上 多樣化和部署工作負載，以因應各種商務和操作的原因。 若要 多樣化，客戶可以使用網際網路、IPSec VPN，或透過內部部署網路使用雲端提供者的直接連線解決方案，來與雲端網路互連。 將雲端網路互連，可能需要大量投資時間、金錢、設計、採購、安裝、測試和作業。 

為了解決這些客戶的挑戰，Oracle 和 Microsoft 已實現整合式的多重雲端體驗。 建立跨雲端網路功能的方式，是將 Microsoft Azure 中的[ExpressRoute](../../../expressroute/expressroute-introduction.md)線路與 OCI 中的[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)線路連接。 如果 Azure ExpressRoute 對等互連位置與 OCI FastConnect 位於相同的對等互連位置，就可能發生這種連線能力。 此設定可在兩個雲端之間進行安全的快速連線，而不需要中繼服務提供者。

使用 ExpressRoute 和 FastConnect，客戶可以將 Azure 中的虛擬網路與 OCI 中的虛擬雲端網路對等互連，前提是私人 IP 位址空間不會重迭。 對等互連這兩個網路可讓虛擬網路中的資源與 OCI 虛擬雲端網路中的資源進行通訊，如同兩者都位於相同的網路中。

## <a name="network-security"></a>網路安全性

網路安全性是任何企業應用程式的重要元件，而且是這個多雲端解決方案的核心。 通過 ExpressRoute 和 FastConnect 的任何流量都會透過私人網路傳遞。 此設定可讓您在 Azure 虛擬網路與 Oracle 虛擬雲端網路之間進行安全通訊。 您不需要提供公用 IP 位址給 Azure 中的任何虛擬機器。 同樣地，您不需要 OCI 中的網際網路閘道。 所有通訊都會透過電腦的私人 IP 位址進行。

此外，您可以在 OCI 虛擬雲端網路和安全性規則（附加至 Azure[網路安全性群組](../../../virtual-network/security-overview.md)）上設定[安全性清單](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)。 使用這些規則來控制在虛擬網路中的電腦之間流動的流量。 網路安全性規則可以在電腦層級、子網層級，以及虛擬網路層級新增。
 
## <a name="identity"></a>身分識別

身分識別是 Microsoft 與 Oracle 之間合作關係的其中一個核心要素。 已完成將[Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) （IDCS）與[Azure Active Directory](../../../active-directory/index.yml) （Azure AD）整合的重要工作。 Azure AD 是 Microsoft 的雲端式身分識別和存取管理服務。 它可協助您的使用者登入及存取各種資源。 Azure AD 也可讓您管理使用者和其許可權。

目前，這項整合可讓您在一個中央位置（也就是 Azure Active Directory）進行管理。 Azure AD 會將目錄中的任何變更與對應的 Oracle 目錄進行同步處理，並用於跨雲端 Oracle 解決方案的單一登入。

## <a name="next-steps"></a>後續步驟

開始使用 Azure 與 OCI 之間的[跨雲端網路](configure-azure-oci-networking.md)。 

如需有關 OCI 的詳細資訊和白皮書，請參閱[Oracle 雲端](https://docs.cloud.oracle.com/iaas/Content/home.htm)檔。
