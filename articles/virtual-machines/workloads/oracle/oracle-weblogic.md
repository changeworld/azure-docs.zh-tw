---
title: Oracle WebLogic Server Azure 應用程式 | Microsoft Docs
description: 了解如何在 Microsoft Azure 上執行 Oracle WebLogic Server。
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664046"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Oracle WebLogic Server Azure 應用程式

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server 是可擴充、符合企業需求的 Java EE 應用程式伺服器。

Oracle WebLogic Server 是全球第一個雲端原生的企業 Java 平台應用程式伺服器，可用於開發及部署多層分散式企業應用程式。 Azure WebLogic Server 供應項目可讓您為 WebLogic 移轉提供較佳的選擇和彈性，包括以最少的投入及最大的影響將 Java EE 應用程式隨即轉移至 Azure 雲端，藉此實現雲端運算。 這些供應項目可讓您自動佈建虛擬網路、儲存體和 Linux 資源，安裝 WebLogic 伺服器，設定網路安全性群組的安全性，使用 Azure App 閘道進行負載平衡，使用 Azure Active Directory 進行驗證，以及簡化資料庫連線，藉此快速啟動您的商務應用程式。

有四個可用的供應項目可以滿足不同的情節：沒有管理員伺服器的單一節點、具有管理伺服器的單一節點、叢集以及動態叢集。  請放心地試用，供應項目為免費提供。

_這些供應項目是自備授權_。 其會假設您已購買了 Oracle 的適當授權，並已適當授權在 Microsoft Azure 中執行供應項目。

_如果您想要與開發這些供應項目的工程小組密切合作以處理您的移轉案例，只要在 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) 中，按下[與我連絡](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)按鈕_。 程式經理、架構設計人員和工程師會盡快回覆您，並起始共同作業！

### <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 單一節點

此供應項目會佈建單一虛擬機器，並在其中安裝 Oracle WebLogic Server。 其不會建立網域或啟動管理伺服器。 這適用於具有高度自訂網域設定的案例。

### <a name="oracle-weblogic-server-with-admin-server"></a>具管理伺服器的 Oracle WebLogic Server

此供應項目會佈建單一虛擬機器，並在其中安裝 Oracle WebLogic Server。 其會建立網域並啟動管理伺服器，讓您能夠管理網域。

### <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 叢集

此供應項目會建立具有高度可用性的 Oracle WebLogic Server 虛擬機器叢集。 依預設會啟動管理伺服器和所有受控伺服器，讓您可以管理網域。

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 動態叢集

此供應項目會建立具有高度可用性且可擴充的 Oracle WebLogic Server 虛擬機器動態叢集。 依預設會啟動管理伺服器和所有受控伺服器，讓您可以管理網域。

## <a name="next-steps"></a>後續步驟

探索 Azure Marketplace 中的供應項目。

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 單一節點](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [具管理伺服器的 Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 叢集](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 動態叢集](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
