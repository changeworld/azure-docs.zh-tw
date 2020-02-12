---
title: Azure 上的 SAP HANA (大型執行個體) 的基礎結構和連線 | Microsoft Docs
description: 設定必要的連線基礎結構以使用「Azure 上的 SAP HANA」(大型執行個體)。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36f0b78f563bb4dda76f192259541d1c2b1fa060
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224679"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (大型執行個體) 部署 

本文假設您已向 Microsoft 購買完「Azure 上的 SAP HANA」(大型執行個體)。 閱讀本文之前，如需了解一般背景，請參閱 [HANA 大型執行個體的常見詞彙](hana-know-terms.md)和 [HANA 大型執行個體 SKU](hana-available-skus.md)。


Microsoft 必須有下列資訊，才能部署 HANA 大型執行客體單位：

- 客戶名稱。
- 業務連絡人資訊 (包括電子郵件地址和電話號碼)。
- 技術連絡人資訊 (包括電子郵件地址和電話號碼)。
- 技術網路連絡人資訊 (包括電子郵件地址和電話號碼)。
- Azure 部署區域 (例如「美國西部」、「澳大利亞東部」或「北歐」)。
- Azure 上的 SAP HANA (大型執行個體) SKU (設定)。
- 針對每個 Azure 部署區域：
    - 將 Azure 虛擬網路連線到 HANA 大型執行個體的 ER-P2P 連線 /29 IP 位址範圍。
    - 用於 HANA 大型執行個體伺服器 IP 集區的 /24 CIDR 區塊。
    - 選擇性使用[ExpressRoute Global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)觸達以啟用從內部部署到 Hana 大型實例單位的直接路由，或在不同 Azure 區域中的 Hana 大型實例單位之間進行路由傳送時，您必須保留另一個/29 個 IP 位址範圍。 此特定範圍不能與您之前定義的任何其他 IP 位址範圍重迭。
- 每個連線到 HANA 大型執行個體之 Azure 虛擬網路的虛擬網路位址空間屬性中所使用的 IP 位址範圍值。
- 每個 HANA 大型執行個體系統的資料：
  - 所需的主機名稱 - 最好是完整的網域名稱。
  - 來自伺服器 IP 集區位址範圍的所需 HANA 大型執行個體單位 IP 位址。 (伺服器 IP 集區位址範圍中的前 30 個 IP 位址會保留供 HANA 大型執行個體內部使用)。
  - SAP HANA 執行個體的 SAP HANA SID 名稱 (這是建立必要 SAP HANA 相關磁碟區所需的名稱)。 Microsoft 需要 HANA SID 來為 sidadm 建立 NFS 磁碟區上的權限。 這些磁碟區會連結至 HANA 大型執行個體單位。 HANA SID 也用來作為所掛接磁碟區的其中一個名稱元件。 如果您想要在單位上執行多個 HANA 執行個體，則應該列出多個 HANA SID。 每一個都會分別指派一組磁碟區。
  - 在 Linux OS 中，sidadm 使用者會有群組識別碼。 必須有此識別碼，才能建立必要的 SAP HANA 相關磁碟區。 SAP HANA 安裝通常會建立群組識別碼為 1001 的 sapsys 群組。 sidadm 使用者是該群組的成員。
  - 在 Linux OS 中，sidadm 使用者會有使用者識別碼。 必須有此識別碼，才能建立必要的 SAP HANA 相關磁碟區。 如果您要在單位上執行數個 HANA 執行個體，請列出所有 sidadm 使用者。 
- 「Azure 上的 SAP HANA」HANA 大型執行個體要直接連線之 Azure 訂用帳戶的 Azure 訂用帳戶 ID。 此訂用帳戶 ID 會參考將以 HANA 大型執行個體單位收費的 Azure 訂用帳戶。

在您提供上述資訊之後，Microsoft 就會佈建「Azure 上的 SAP HANA」(大型執行個體)。 Microsoft 會傳送資訊給您，以供您將 Azure 虛擬網路連結至 HANA 大型執行個體。 您也可以存取 HANA 大型執行個體單位。

在 Microsoft 部署好 HANA 大型執行個體之後，請依下列順序連線到這些執行個體：

1. [將 Azure VM 連接到 HANA 大型執行個體](hana-connect-azure-vm-large-instances.md)
2. [將 VNet 連接到 HANA 大型執行個體 ExpressRoute](hana-connect-vnet-express-route.md)
3. [其他網路需求 (選擇性)](hana-additional-network-requirements.md)

