---
title: 為 Azure 配置 Azure 到 Azure 災害復原的移動服務代理設置 |微軟文檔
description: 提供有關客戶在其源環境中使用代理時如何配置移動服務的詳細資訊。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503123"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>為 Azure 配置移動服務代理設置以 Azure 災害復原

本文提供有關使用[Azure 網站恢復](site-recovery-overview.md)將 Azure VM 從一個區域複製和恢復 Azure VM 時在目標 Azure 虛擬機器 （VM） 上自訂網路設定的指導。

本文檔的目的是提供步驟，在 Azure 到 Azure 災害復原方案中為 Azure 網站恢復移動服務配置代理設置。 

代理是允許/不允許網路連接到終結點的網路閘道。 通常，代理是用戶端電腦外嘗試訪問網路終結點的電腦。 旁路清單允許用戶端直接連接到終結點，而無需通過代理。 網路系統管理員可以選擇為代理設置使用者名和密碼，以便只有經過身份驗證的用戶端才能使用代理。 

## <a name="before-you-start"></a>開始之前

了解 Site Recovery 如何針對[這種情況](azure-to-azure-architecture.md)提供災害復原。
使用[Azure 網站恢復](site-recovery-overview.md)將 Azure VM 從一個區域複製和恢復到另一個區域時，瞭解[網路指南](azure-to-azure-about-networking.md)。
確保根據組織的需求適當地設置代理。

## <a name="configure-the-mobility-service"></a>配置移動服務

移動服務僅支援未經身份驗證的代理。 它提供了兩種輸入代理詳細資訊的方法，以便與網站恢復終結點通信。 

### <a name="method-1-auto-detection"></a>方法 1：自動檢測

移動服務在啟用複製期間自動檢測環境設置或 IE 設置（僅限 Windows）的代理設置。 

- Windows OS：在啟用複製期間，移動服務檢測本地系統使用者在 Internet 資源管理器中配置的代理設置。 要為本機系統帳戶設置代理，管理員可以使用 psexec 啟動命令提示符，然後使用 Internet 資源管理器。 
- Windows OS：代理設置配置為環境變數HTTP_proxy和no_proxy。 
- Linux OS：代理設置在 /etc/設定檔或 /etc//環境中配置為環境變數HTTP_proxy，no_proxy。 
- 自動檢測到的代理設置將保存到移動服務代理設定檔代理資訊.conf 
- 代理資訊.conf 的預設位置 
    - 視窗：C：\程式資料\微軟 Azure 網站恢復\Config_代理資訊.conf 
    - Linux： /usr/本地/Inmage/配置/代理資訊.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>方法 2：提供自訂應用程式代理設置

在這種情況下，客戶在移動服務設定檔 ProxyInfo.conf 中提供自訂應用程式代理設置。 此方法允許客戶僅為移動服務提供代理，或者為 Azure 網站恢復移動服務提供與電腦上的其餘應用程式的代理（或無代理）不同的代理。

## <a name="proxy-template"></a>代理範本
ProxyInfo.conf 包含以下範本 [代理] 位址http://1.2.3.4= 埠=5678 繞過清單_超v恢復管理器.windowsazure.com，login.microsoftonline.com，blob.core.windows.net。 旁路清單不支援"*.windows.net"之類的萬用字元，但給出windows.net足以繞過。 

## <a name="next-steps"></a>後續步驟：
- 閱讀複製 Azure VM[的網路指南](site-recovery-azure-to-azure-networking-guidance.md)。
- 透過[複寫 Azure VM](site-recovery-azure-to-azure.md) 來部署災害復原。