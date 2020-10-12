---
title: 從內部部署存取 Azure VMware Solution by CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: 從內部部署網路透過防火牆 CloudSimple 存取您的 Azure VMware 解決方案
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6ff057d99e29c7c6fe30e77f38a0bff265dbe7bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86998881"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>從內部部署存取您的 CloudSimple 私用雲端環境和應用程式

您可以使用 Azure ExpressRoute 或站對站 VPN，設定從內部部署網路到 CloudSimple 的連線。  使用連線，存取您的 CloudSimple 私用雲端 vCenter 和您在私人雲端上執行的任何工作負載。  您可以使用內部部署網路中的防火牆，控制在連線上開啟的埠。  本文討論一些一般的應用程式埠需求。  針對任何其他應用程式，請參閱應用程式檔中的埠需求。

## <a name="ports-required-for-accessing-vcenter"></a>存取 vCenter 所需的埠

若要存取您的私人雲端 vCenter 和 NSX-T 管理員，您必須在內部部署防火牆上開啟下表中定義的埠。  

| Port       | 來源                           | Destination                      | 目的                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)    | 內部部署 DNS 伺服器          | 私人雲端 DNS 伺服器        | 從內部部署網路將 *az.cloudsimple.io* 的 DNS 查閱轉送至私人雲端 DNS 伺服器的必要項。       |
| 53 (UDP)    | 私人雲端 DNS 伺服器        | 內部部署 DNS 伺服器          | 將 DNS 從私人雲端 vCenter 的內部部署功能變數名稱查閱至內部部署 DNS 伺服器時，需要用到。 |
| 80 (TCP)    | 內部部署網路              | 私人雲端管理網路 | 將 vCenter URL 從 *HTTP* 重新導向至 *HTTPs*時需要。                                                           |
| 443 (TCP)   | 內部部署網路              | 私人雲端管理網路 | 從內部部署網路存取 vCenter 和 NSX-T 管理員所需。                                             |
| 8000 (TCP)  | 內部部署網路              | 私人雲端管理網路 | 從內部部署到私人雲端的虛擬機器 vMotion 需要此項。                                            |
| 8000 (TCP)  | 私人雲端管理網路 | 內部部署網路              | 從私用雲端到內部部署的虛擬機器 vMotion 需要此項。                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>使用內部部署 active directory 作為身分識別來源所需的埠

若要將內部部署 active directory 設定為私人雲端 vCenter 上的身分識別來源，則必須開啟資料表中所定義的埠。  如需設定步驟，請參閱 [使用 Azure AD 作為 CloudSimple 私人雲端上的 vCenter 的身分識別提供者](./azure-ad.md) 。

| Port         | 來源                           | Destination                                         | 目的                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)       | 私人雲端 DNS 伺服器        | 內部部署 DNS 伺服器                             | 將 DNS 查詢從私用雲端 vCenter 中的內部部署 active directory 功能變數名稱轉送至內部部署 DNS 伺服器的必要項。          |
| 389 (TCP/UDP) | 私人雲端管理網路 | 內部部署 active directory 網域控制站     | 從私人雲端 vCenter 伺服器到 active directory 網域控制站的 LDAP 通訊需要進行使用者驗證。                |
| 636 (TCP)      | 私人雲端管理網路 | 內部部署 active directory 網域控制站     | 安全 LDAP (LDAPS) 從私人雲端 vCenter 伺服器到 active directory 網域控制站的通訊需要進行使用者驗證。 |
| 3268 (TCP)     | 私人雲端管理網路 | 內部部署 active directory 通用類別目錄伺服器 | 在多網域控制站部署中進行 LDAP 通訊所需。                                                                        |
| 3269 (TCP)     | 私人雲端管理網路 | 內部部署 active directory 通用類別目錄伺服器 | 多網域控制站部署中的 LDAPS 通訊所需。                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>存取工作負載虛擬機器所需的常見埠

存取私人雲端上執行的工作負載虛擬機器需要在您的內部部署防火牆上開啟埠。  下表顯示一些常見的埠，以及其用途。  如需任何應用程式特定的埠需求，請參閱應用程式檔。

| Port         | 來源                         | Destination                          | 目的                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)       | 內部部署網路            | 私用雲端工作負載網路       | 安全 shell 存取在私用雲端上執行的 Linux 虛擬機器。              |
| 3389 (TCP)     | 內部部署網路            | 私用雲端工作負載網路       | 在私人雲端上執行的 windows 虛擬機器的遠端桌面。                 |
| 80 (TCP)       | 內部部署網路            | 私用雲端工作負載網路       | 存取任何部署在私人雲端上執行之虛擬機器的 web 伺服器。        |
| 443 (TCP)      | 內部部署網路            | 私用雲端工作負載網路       | 存取部署在私人雲端上執行之虛擬機器上的任何安全 web 伺服器。 |
| 389 (TCP/UDP) | 私用雲端工作負載網路 | 內部部署 active directory 網路 | 將 Windows 工作負載虛擬機器加入內部部署 active directory 網域。       |
| 53 (UDP)       | 私用雲端工作負載網路 | 內部部署網路                  | 適用于工作負載虛擬機器至內部部署 DNS 伺服器的 DNS 服務存取權。         |

## <a name="next-steps"></a>接下來的步驟

* [建立及管理 Vlan 和子網](./create-vlan-subnet.md)
* [使用 Azure ExpressRoute 連接到內部部署網路](./on-premises-connection.md)
* [從內部部署設定站對站 VPN](./vpn-gateway.md)
