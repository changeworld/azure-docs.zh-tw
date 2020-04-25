---
title: 使用 Azure 監視器將記錄與 ArcSight 整合 |Microsoft Docs
description: 瞭解如何使用 Azure 監視器將 Azure Active Directory 記錄與 ArcSight 整合
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f03b146331069371106c1857f2acc68b566d3c5d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129237"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>使用 Azure 監視器將 Azure Active Directory 記錄與 ArcSight 整合

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) 是安全性資訊及事件管理 (SIEM) 解決方案，可協助您偵測及回應您平台中的安全性威脅。 您現在可以使用 Azure 監視器與適用於 Azure AD 的 ArcSight 連接器，將 Azure Active Directory (Azure AD) 記錄路由傳送至 ArcSight。 這項功能可讓您使用 ArcSight 監視租用戶中是否有安全性危害。  

在本文中，您將了解如何使用 Azure 監視器將 Azure AD 記錄路由傳送至 ArcSight。 

## <a name="prerequisites"></a>必要條件

若要使用此功能，您必須要有：
* 包含 Azure AD 活動記錄的 Azure 事件中樞。 了解如何[將活動記錄串流至事件中樞](quickstart-azure-monitor-stream-logs-to-event-hub.md)。 
* 已設定的 ArcSight Syslog NG Daemon SmartConnector (SmartConnector) 或 ArcSight Load Balancer 執行個體。 如果事件傳送到 ArcSight Load Balancer，Load Balancer 便會將其傳送至 SmartConnector。

下載並開啟[適用於 Azure 監視器事件中樞的 ArcSight SmartConnector 設定指南](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)。 本指南包含所需步驟，可用來安裝和設定適用於 Azure 監視器的 ArcSight SmartConnector。 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>將 Azure AD 記錄與 ArcSight 整合

1. 首先，完成設定指南**先決條件**一節中的步驟。 本節包含下列步驟：
    * 在 Azure 中設定使用者權限，以確保有使用者具有**擁有者**角色，而可部署和設定連接器。
    * 在具有 Syslog NG Daemon SmartConnector 的伺服器上開啟連接埠，以便其可供 Azure 存取。 
    * 部署作業會執行 Windows PowerShell 指令碼，因此您必須啟用 PowerShell 以在要用來部署連接器的電腦上執行指令碼。

2. 遵循設定指南**部署連接器**一節中的步驟來部署連接器。 本節會引導您了解如何下載及解壓縮連接器、設定應用程式屬性，以及從解壓縮的資料夾執行部署指令碼。 

3. 使用**確認 Azure 中的部署**中的步驟，來確定連接器的設定和運作皆正常。 驗證下列項目：
    * 已在 Azure 訂用帳戶中建立必要的 Azure 函式。
    * Azure AD 記錄會串流至正確目的地。 
    * 部署中的應用程式設定會保存在 Azure 函式應用程式中的應用程式設定內。 
    * Azure 中已為 ArcSight 建立新的資源群組，並有適用於 ArcSight 連接器的 Azure AD 應用程式，以及包含 CEF 格式對應檔案的儲存體帳戶。

4. 最後，完成設定指南**部署後設定**中的部署後步驟。 本節說明如何執行其他設定，如果您是在 App Service 計畫，以防止函式應用程式在超時時間之後進入閒置狀態，請設定事件中樞的資源記錄串流，並更新 SysLog NG Daemon SmartConnector 金鑰儲存區憑證，使其與新建立的儲存體帳戶產生關聯。

5. 設定指南也會說明如何在 Azure 中自訂連接器屬性，以及如何升級和解除安裝連接器。 另有一節說明如何改進效能，包括升級至 [Azure 取用方案](https://azure.microsoft.com/pricing/details/functions)，以及在事件負載大於單一 Syslog NG Daemon SmartConnector 的處理能力時設定 ArcSight Load Balancer。

## <a name="next-steps"></a>後續步驟

[適用於 Azure 監視器事件中樞的 ArcSight SmartConnector 設定指南](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
