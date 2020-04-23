---
title: 透過雲端從本地輕鬆存取 Azure VMware 解決方案
titleSuffix: Azure VMware Solution by CloudSimple
description: 透過防火牆透過雲端簡單從本地網路存取 Azure VMware 解決方案
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 539665c4756a7dc87078922421b45a88404f58f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868149"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>從本地存取雲簡單私有雲端環境和應用程式

可以使用 Azure ExpressRoute 或網站到網站 VPN 從本地網路連接到雲端簡單連接。  訪問雲簡單私有雲 vCenter 以及您使用連接在私有雲上運行的任何工作負載。  您可以使用本地網路中的防火牆控制連接上打開的埠。  本文討論了一些典型的應用程式埠要求。  對於任何其他應用程式,請參閱有關埠要求的應用程式文檔。

## <a name="ports-required-for-accessing-vcenter"></a>存取 vCenter 所需的連接埠

要造訪私有雲 vCenter 和 NSX-T 管理員,必須在本地防火牆上打開下表中定義的埠。  

| 連接埠       | 來源                           | Destination                      | 目的                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | 內部部署 DNS 伺服器          | 私有雲 DNS 伺服器        | 需要將*az.cloudsimple.io*的 DNS 查找從本地網路轉發到私有雲 DNS 伺服器。       |
| 53 (UDP)   | 私有雲 DNS 伺服器        | 內部部署 DNS 伺服器          | 需要將 DNS 從私有雲 vCenter 到本地 DNS 伺服器的本地域名進行查找。 |
| 80 (TCP)   | 內部部署網路              | 私有雲管理網路 | 需要將 vCenter 網址 從*HTTPs*。*http*                                                           |
| 443 (TCP)  | 內部部署網路              | 私有雲管理網路 | 從本地網路訪問 vCenter 和 NSX-T 管理員是必需的。                                             |
| 8000 (TCP) | 內部部署網路              | 私有雲管理網路 | 虛擬機從本地到私有雲的 vMotion 需要。                                            |
| 8000 (TCP) | 私有雲管理網路 | 內部部署網路              | 虛擬機從私有雲到本地的 vMotion 需要。                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>將本地端目錄用作識別來源所需的連接埠

要將本地活動目錄配置為私有雲 vCenter 上的標識源,必須打開表中定義的埠。  有關設定步驟[,請參閱在雲端簡單私有雲上使用 Azure AD 作為 vCenter 的識別提供者](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-ad/)。

| 連接埠         | 來源                           | Destination                                         | 目的                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | 私有雲 DNS 伺服器        | 內部部署 DNS 伺服器                             | 需要將 DNS 從私有雲 vCenter 到本地 DNS 伺服器的本地活動目錄功能變數名稱進行查找。          |
| 389 (TCP/UDP) | 私有雲管理網路 | 本地活動目錄域控制器     | LDAP 通訊從私有雲 vCenter 伺服器到活動目錄域控制器進行使用者身份驗證所需的。                |
| 636 (TCP)     | 私有雲管理網路 | 本地活動目錄域控制器     | 從私有雲 vCenter 伺服器到活動目錄域控制器的安全 LDAP (LDAPS) 通信需要,以便進行使用者身份驗證。 |
| 3268 (TCP)    | 私有雲管理網路 | 本地活動目錄全域目錄伺服器 | 多域控制器部署中 LDAP 通訊是必需的。                                                                        |
| 3269 (TCP)    | 私有雲管理網路 | 本地活動目錄全域目錄伺服器 | 多域控制器部署中 LDAPS 通訊是必需的。                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>存取工作負載虛擬機器所需的通用埠

訪問在私有雲上運行的工作負載虛擬機需要在本地防火牆上打開埠。  下表顯示了所需的一些常見埠及其用途。  對於任何特定於應用程式的埠要求,請參閱應用程式文檔。

| 連接埠         | 來源                         | Destination                          | 目的                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | 內部部署網路            | 私有雲工作負載網路       | 安全訪問在私有雲上運行的 Linux 虛擬機器。              |
| 3389 (TCP)    | 內部部署網路            | 私有雲工作負載網路       | 遠端桌面到在私有雲端上運行的視窗虛擬機器。                 |
| 80 (TCP)      | 內部部署網路            | 私有雲工作負載網路       | 訪問部署在私有雲端上運行的虛擬機器上的任何 Web 伺服器。        |
| 443 (TCP)     | 內部部署網路            | 私有雲工作負載網路       | 訪問部署在私有雲端上運行的虛擬機器上的任何安全 Web 伺服器。 |
| 389 (TCP/UDP) | 私有雲工作負載網路 | 本地活動目錄網路 | 將 Windows 工作負載虛擬機加入本地活動目錄域。       |
| 53 (UDP)      | 私有雲工作負載網路 | 內部部署網路                  | 工作負載虛擬機對本地 DNS 伺服器的 DNS 服務訪問。         |

## <a name="next-steps"></a>後續步驟

* [建立與管理 VLAN 與子網路](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/)
* [使用 Azure 快速路由連接到本地網路](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-connection/)
* [從本地設定網站到網站 VPN](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway/)
