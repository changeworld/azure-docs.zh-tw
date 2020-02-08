---
title: 從內部部署環境存取 Azure VMware 解決方案（AVS）
description: 透過防火牆從內部部署網路存取您的 Azure VMware 解決方案（AVS）
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a4a9760b5c7a70c58a1afe1b14b781a35f2b9b18
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082963"
---
# <a name="accessing-your-avs-private-cloud-environment-and-applications-from-on-premises"></a>從內部部署存取您的 AVS 私用雲端環境和應用程式

使用 Azure ExpressRoute 或站對站 VPN，可將連線設定為從內部部署網路到 AVS。 使用連線來存取您的 AVS 私用雲端 vCenter，以及您在 AVS 私人雲端上執行的任何工作負載。 您可以使用內部部署網路中的防火牆，控制要在連接上開啟哪些埠。 本文討論一些典型的應用程式埠需求。 針對任何其他應用程式，請參閱應用程式檔中的埠需求。

## <a name="ports-required-for-accessing-vcenter"></a>存取 vCenter 所需的埠

若要存取您的 AVS 私用雲端 vCenter 和 NSX-T 管理員，必須在內部部署防火牆上開啟下表中定義的埠。 

| 連接埠       | 來源                           | Destination                      | 目的                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53（UDP）   | 內部部署 DNS 伺服器          | AVS 私人雲端 DNS 伺服器        | 將*az.cloudsimple.io*的 DNS 查閱從內部部署網路轉送到 AVS 私人雲端 DNS 伺服器時的必要項。     |
| 53（UDP）   | AVS 私人雲端 DNS 伺服器        | 內部部署 DNS 伺服器          | 將 DNS 查詢從 AVS 私用雲端 vCenter 到內部部署 DNS 伺服器時，所需的資訊。 |
| 80（TCP）   | 內部部署網路              | AVS 私用雲端管理網路 | 將 vCenter URL 從*HTTP*重新導向至*HTTPs*時的必要。                                                         |
| 443（TCP）  | 內部部署網路              | AVS 私用雲端管理網路 | 從內部部署網路存取 vCenter 和 NSX-T 管理員所需。                                           |
| 8000（TCP） | 內部部署網路              | AVS 私用雲端管理網路 | 從內部部署虛擬機器到 AVS 私人雲端的 vMotion 所需。                                          |
| 8000（TCP） | AVS 私用雲端管理網路 | 內部部署網路              | 從 AVS 私人雲端到內部部署虛擬機器的 vMotion 所需。                                          |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>使用內部部署 active directory 做為身分識別來源所需的埠

若要將內部部署 active directory 設定為 AVS 私用雲端 vCenter 上的身分識別來源，必須開啟資料表中定義的埠。 如需設定步驟，請參閱[在 AVS Avs 私人雲端上使用 Azure AD 做為 vCenter 的識別提供者](https://docs.azure.cloudsimple.com/azure-ad/)。

| 連接埠         | 來源                           | Destination                                         | 目的                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53（UDP）      | AVS 私人雲端 DNS 伺服器        | 內部部署 DNS 伺服器                             | 將內部部署 active directory 功能變數名稱從 AVS 私用雲端 vCenter 轉送至內部部署 DNS 伺服器時的必要項。        |
| 389 (TCP/UDP) | AVS 私用雲端管理網路 | 內部部署 active directory 網域控制站     | 從 AVS 私用雲端 vCenter server 到 active directory 網域控制站的 LDAP 通訊需要進行使用者驗證。              |
| 636（TCP）     | AVS 私用雲端管理網路 | 內部部署 active directory 網域控制站     | 從 AVS 私用雲端 vCenter server 到 active directory 網域控制站的安全 LDAP （LDAPS）通訊所需，以進行使用者驗證。 |
| 3268（TCP）    | AVS 私用雲端管理網路 | 內部部署 active directory 通用類別目錄伺服器 | 在多網域控制站部署中 LDAP 通訊的必要項。                                                                      |
| 3269（TCP）    | AVS 私用雲端管理網路 | 內部部署 active directory 通用類別目錄伺服器 | 多網域控制站部署中的 LDAPS 通訊所需。                                                                     |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>存取工作負載虛擬機器所需的一般埠

在 AVS 私人雲端上執行的存取工作負載虛擬機器，需要在您的內部部署防火牆上開啟埠。 下表顯示所需的一些常用埠和其用途。 如需任何應用程式特定的埠需求，請參閱應用程式檔。

| 連接埠         | 來源                         | Destination                          | 目的                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22（TCP）      | 內部部署網路            | AVS 私人雲端工作負載網路       | 對在 AVS 私用雲端上執行的 Linux 虛擬機器進行安全 shell 存取。            |
| 3389（TCP）    | 內部部署網路            | AVS 私人雲端工作負載網路       | 在 AVS 私用雲端上執行的 windows 虛擬機器的遠端桌面。               |
| 80（TCP）      | 內部部署網路            | AVS 私人雲端工作負載網路       | 存取部署在在 AVS 私用雲端上執行之虛擬機器上的任何 web 伺服器。      |
| 443（TCP）     | 內部部署網路            | AVS 私人雲端工作負載網路       | 存取部署在在 AVS 私用雲端上執行之虛擬機器上的任何安全 web 伺服器。 |
| 389 (TCP/UDP) | AVS 私人雲端工作負載網路 | 內部部署 active directory 網路 | 將 Windows 工作負載虛擬機器加入內部部署 active directory 網域。     |
| 53（UDP）      | AVS 私人雲端工作負載網路 | 內部部署網路                  | 對內部部署 DNS 伺服器進行工作負載虛擬機器的 DNS 服務存取。       |

## <a name="next-steps"></a>後續步驟

* [建立和管理 Vlan 和子網](https://docs.azure.cloudsimple.com/create-vlan-subnet/)
* [使用 Azure ExpressRoute 連接到內部部署網路](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [從內部部署環境設定站對站 VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)
