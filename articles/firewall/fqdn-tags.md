---
title: Azure 防火牆的 FQDN 標籤概觀
description: FQDN 標籤代表一群與知名 Microsoft 服務相關聯的完整網域名稱 (FQDN)。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c0bd2f6a021baae80dafcc3d544e1062d8e022e8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653329"
---
# <a name="fqdn-tags-overview"></a>FQDN 標籤概觀

FQDN 標籤代表一群與知名 Microsoft 服務相關聯的完整網域名稱 (FQDN)。 您可以在應用程式規則中使用 FQDN 標籤，以允許必要的輸出網路流量通過防火牆。

例如，若要手動允許 Windows Update 網路流量通過防火牆，您需要根據 Microsoft 文件建立多個應用程式規則。 使用 FQDN 標籤，您可以建立應用程式規則、納入 **Windows Update** 標籤，而且現在流向 Microsoft Windows Update 端點的網路流量可以通過防火牆。

您無法建立您自己的 FQDN 標籤，也不能指定標籤內所要包含的 FQDN。 Microsoft 會管理 FQDN 標籤所包含的 FQDN，並將標籤更新為 FQDN 變更。 

<!--- screenshot of application rule with a FQDN tag.-->

下表顯示您目前可以使用的 FQDN 標籤。 Microsoft 會維護這些標籤，而且預計會定期新增其他標籤。

## <a name="current-fqdn-tags"></a>目前的 FQDN 標記

|FQDN 標籤  |說明  |
|---------|---------|
|Windows Update     |允許對 Microsoft Update 進行輸出存取，如[如何為軟體更新設定防火牆](/mem/configmgr/sum/get-started/install-a-software-update-point)中所述。|
|Windows 診斷|允許對所有 [Windows 診斷端點](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)進行輸出存取。|
|Microsoft Active Protection Service (MAPS)|允許對 [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/) 進行輸出存取。|
|App Service Environment (ASE)|允許對 ASE 平台流量進行輸出存取。 此標籤未涵蓋客戶專屬的儲存體和 ASE 所建立的 SQL 端點。 這些應該透過 [服務端點](../virtual-network/tutorial-restrict-network-access-to-resources.md) 來啟用，或手動加入。<br><br>如需整合 Azure 防火牆與 ASE 的詳細資訊，請參閱 [鎖定 App Service 環境](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)。|
|Azure 備份|允許對 Azure 備份服務進行輸出存取。|
|Azure HDInsight|允許 HDInsight 平臺流量的輸出存取。 此標記未涵蓋客戶特定的儲存體或來自 HDInsight 的 SQL 流量。 使用 [服務端點](../virtual-network/tutorial-restrict-network-access-to-resources.md) 或手動新增它們來啟用這些端點。|
|WindowsVirtualDesktop (WVD) |允許輸出 Windows 虛擬桌面平臺流量。 此標記未涵蓋 WVD 所建立的部署特定儲存體和服務匯流排端點。 此外，也需要 DNS 和 KMS 網路規則。 如需將 Azure 防火牆與 WVD 整合的詳細資訊，請參閱 [使用 Azure 防火牆來保護 Windows 虛擬桌面部署](protect-windows-virtual-desktop.md)。|
|Azure Kubernetes Service (AKS)|允許對 AKS 的輸出存取。 如需詳細資訊，請參閱 [使用 Azure 防火牆來保護 Azure Kubernetes Service (AKS) 部署](protect-azure-kubernetes-service.md)。|

> [!NOTE]
> 選取應用程式規則中的 FQDN 標籤時，[通訊協定:連接埠] 欄位必須設定為 [https]。

## <a name="next-steps"></a>後續步驟

若要了解如何部署 Azure 防火牆，請參閱[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。