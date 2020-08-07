---
title: 什麼是 Azure 上的 Oracle WebLogic Server？
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
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851848"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>什麼是 Azure 上的 Oracle WebLogic Server？

本頁面說明在 Azure 虛擬機器上執行 WebLogic Server (WLS) 的解決方案。  這些解決方案是由 Oracle 和 Microsoft 共同開發。

Oracle WebLogic Server 是全球第一個雲端原生的企業 Java 平台應用程式伺服器，可用於開發及部署多層分散式企業應用程式。 Azure WebLogic Server 優惠可讓您採用雲端運算。  您可為 WebLogic 遷移提供更大的選擇和彈性，包括將 JAVA EE 應用程式隨即轉移至 Azure 雲端。   Azure 上的 WLS 會大幅影響您的工作。 這項供應專案可讓您快速啟動企業營運 (LOB) 應用程式。  每個供應專案都會自動布建虛擬網路、儲存體和 Linux 資源。  不耗費任何人力，就會安裝 WebLogic 伺服器。  Azure 上的 WLS 使用網路安全性群組來設定安全性、使用 Azure App 閘道進行負載平衡、使用 Azure Active Directory 進行驗證，並自動連接到您現有的資料庫。

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="您可以使用 Azure 入口網站在 Azure 上部署 WebLogic 伺服器":::

有四個可用的供應專案可以滿足不同的案例：沒有管理員伺服器的單一節點、具有管理伺服器的單一節點、叢集和動態叢集。  試用供應專案，免費提供。

_這些供應項目是自備授權_。 他們假設您已經有 Oracle 的適當授權，並且已獲得正確的授權，可在 Microsoft Azure 中執行供應專案。

_如果您想要與開發這些供應專案的工程小組密切合作進行遷移案例，請選取 Azure Marketplace 中的 [[連絡人](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)] 按鈕_。 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) 程式經理、架構設計人員和工程師會在不久後與您合作，開始共同作業！

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 單一節點

這種供應專案會布建一部虛擬機器，並在其中安裝 WLS。 它不會建立網域或啟動管理伺服器。 單一節點適用于具有高度自訂網域設定的案例。

## <a name="oracle-weblogic-server-with-admin-server"></a>具管理伺服器的 Oracle WebLogic Server

這種供應專案會布建一部虛擬機器，並在其中安裝 WLS。 其會建立網域並啟動管理伺服器，讓您能夠管理網域。

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 叢集

這項供應專案可建立 WLS 虛擬機器的高可用性叢集。 依預設會啟動管理伺服器和所有受控伺服器，讓您可以管理網域。

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 動態叢集

這項供應專案會建立 WLS 虛擬機器的高度可用且可擴充的動態叢集。 依預設會啟動管理伺服器和所有受控伺服器，讓您可以管理網域。

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
