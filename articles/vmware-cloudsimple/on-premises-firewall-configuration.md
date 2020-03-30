---
title: 通過雲從本地輕鬆訪問 Azure VMware 解決方案
titleSuffix: Azure VMware Solution by CloudSimple
description: 通過防火牆通過雲簡單從本地網路訪問 Azure VMware 解決方案
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df4c51953c6f50e30ba61b993cdb35856fcb8e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082963"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>從本地訪問雲簡單私有雲環境和應用程式

可以使用 Azure ExpressRoute 或網站到網站 VPN 從本地網路連接到雲簡單連接。  訪問雲簡單私有雲 vCenter 以及您使用連接在私有雲上運行的任何工作負載。  您可以使用本地網路中的防火牆控制連接上打開的埠。  本文討論了一些典型的應用程式埠要求。  對於任何其他應用程式，請參閱有關埠要求的應用程式文檔。

## <a name="ports-required-for-accessing-vcenter"></a>訪問 vCenter 所需的埠

要訪問私有雲 vCenter 和 NSX-T 管理器，必須在本地防火牆上打開下表中定義的埠。  

| 連接埠       | 來源                           | Destination                      | 目的                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 （UDP）   | 內部部署 DNS 伺服器          | 私有雲 DNS 伺服器        | 需要將*az.cloudsimple.io*的 DNS 查找從本地網路轉發到私有雲 DNS 伺服器。       |
| 53 （UDP）   | 私有雲 DNS 伺服器        | 內部部署 DNS 伺服器          | 需要將 DNS 從私有雲 vCenter 到本地 DNS 伺服器的本地功能變數名稱進行查找。 |
| 80 （TCP）   | 內部部署網路              | 私有雲管理網路 | 需要將 vCenter URL 從*HTTP*重定向到*HTTPs*。                                                           |
| 443 （TCP）  | 內部部署網路              | 私有雲管理網路 | 從本地網路訪問 vCenter 和 NSX-T 管理器是必需的。                                             |
| 8000 （TCP） | 內部部署網路              | 私有雲管理網路 | 虛擬機器從本地到私有雲的 vMotion 需要。                                            |
| 8000 （TCP） | 私有雲管理網路 | 內部部署網路              | 虛擬機器從私有雲到本地的 vMotion 需要。                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>將本地活動目錄用作標識源所需的埠

要將本地活動目錄配置為私有雲 vCenter 上的標識源，必須打開表中定義的埠。  有關配置步驟[，請參閱在雲簡單私有雲上使用 Azure AD 作為 vCenter 的標識提供程式](https://docs.azure.cloudsimple.com/azure-ad/)。

| 連接埠         | 來源                           | Destination                                         | 目的                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 （UDP）      | 私有雲 DNS 伺服器        | 內部部署 DNS 伺服器                             | 需要將 DNS 從私有雲 vCenter 到本地 DNS 伺服器的本地活動目錄功能變數名稱進行查找。          |
| 389 (TCP/UDP) | 私有雲管理網路 | 本地活動目錄網域控制站     | LDAP 通信從私有雲 vCenter 伺服器到活動目錄網域控制站進行使用者身份驗證所需的。                |
| 636 （TCP）     | 私有雲管理網路 | 本地活動目錄網域控制站     | 從私有雲 vCenter 伺服器到活動目錄網域控制站的安全 LDAP （LDAPS） 通信需要，以便進行使用者身份驗證。 |
| 3268 （TCP）    | 私有雲管理網路 | 本地活動目錄全域目錄伺服器 | 多網域控制站部署中 LDAP 通信是必需的。                                                                        |
| 3269 （TCP）    | 私有雲管理網路 | 本地活動目錄全域目錄伺服器 | 多網域控制站部署中 LDAPS 通信是必需的。                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>訪問工作負載虛擬機器所需的通用埠

訪問在私有雲上運行的工作負載虛擬機器需要在本地防火牆上打開端口。  下表顯示了所需的一些常見埠及其用途。  對於任何特定于應用程式的埠要求，請參閱應用程式文檔。

| 連接埠         | 來源                         | Destination                          | 目的                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 （TCP）      | 內部部署網路            | 私有雲工作負載網路       | 安全訪問在私有雲上運行的 Linux 虛擬機器。              |
| 3389 （TCP）    | 內部部署網路            | 私有雲工作負載網路       | 遠端桌面到在私有雲上運行的視窗虛擬機器。                 |
| 80 （TCP）      | 內部部署網路            | 私有雲工作負載網路       | 訪問部署在私有雲上運行的虛擬機器上的任何 Web 服務器。        |
| 443 （TCP）     | 內部部署網路            | 私有雲工作負載網路       | 訪問部署在私有雲上運行的虛擬機器上的任何安全 Web 服務器。 |
| 389 (TCP/UDP) | 私有雲工作負載網路 | 本地活動目錄網路 | 將 Windows 工作負載虛擬機器加入本地活動目錄域。       |
| 53 （UDP）      | 私有雲工作負載網路 | 內部部署網路                  | 工作負載虛擬機器對本地 DNS 伺服器的 DNS 服務訪問。         |

## <a name="next-steps"></a>後續步驟

* [創建和管理 VLAN 和子網](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [使用 Azure 快速路由連接到本地網路](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [從本地佈建網站到網站 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)
