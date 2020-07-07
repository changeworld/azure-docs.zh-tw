---
title: 設定 Azure 到 Azure 嚴重損壞修復的行動服務 Proxy 設定 |Microsoft Docs
description: 提供當客戶在其來源環境中使用 proxy 時，如何設定行動服務的詳細資料。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79503123"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>設定 Azure 到 Azure 嚴重損壞修復的行動服務 Proxy 設定

當您使用[Azure Site Recovery](site-recovery-overview.md)將 Azure vm 從一個區域複寫及復原到另一個區域時，本文提供在目標 Azure 虛擬機器（VM）上自訂網路設定的指引。

本檔的目的是要在 Azure 到 Azure 的嚴重損壞修復案例中，提供設定 Azure Site Recovery 行動服務 Proxy 設定的步驟。 

Proxy 是允許/禁止端點之網路連線的網路閘道。 Proxy 通常是用戶端機器外的電腦，它會嘗試存取網路端點。 略過清單可讓用戶端直接連線到端點，而不需要通過 proxy。 網路系統管理員可以選擇性地為 proxy 設定使用者名稱和密碼，如此一來，只有經過驗證的用戶端才能使用 proxy。 

## <a name="before-you-start"></a>在您開始使用 Intune 之前

了解 Site Recovery 如何針對[這種情況](azure-to-azure-architecture.md)提供災害復原。
瞭解當您使用[Azure Site Recovery](site-recovery-overview.md)將 Azure vm 從一個區域複寫及復原到另一個區域時的[網路指引](azure-to-azure-about-networking.md)。
確定您的 proxy 已根據貴組織的需求適當地設定。

## <a name="configure-the-mobility-service"></a>設定行動服務

行動服務僅支援未驗證的 proxy。 它提供兩種方式來輸入 proxy 詳細資料，以便與 Site Recovery 端點進行通訊。 

### <a name="method-1-auto-detection"></a>方法1：自動偵測

行動服務會在啟用複寫期間，自動偵測環境設定或 IE 設定（僅限 Windows）中的 proxy 設定。 

- Windows 作業系統：在啟用複寫期間，行動服務會偵測 Internet Explorer 中針對本機系統使用者所設定的 proxy 設定。 若要設定本機系統帳戶的 proxy，系統管理員可以使用 psexec 來啟動命令提示字元，然後執行 Internet Explorer。 
- Windows OS： Proxy 設定會設定為 HTTP_proxy 和 no_proxy 的環境變數。 
- Linux OS：在/etc/profile 或/etc/environment 中，將 Proxy 設定設定為 HTTP_proxy，no_proxy 的環境變數。 
- 自動偵測到的 proxy 設定會儲存至行動服務 proxy 設定檔 Proxyinfo.conf 
- Proxyinfo.conf 的預設位置 
    - Windows： C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux：/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>方法2：提供自訂的應用程式 proxy 設定

在此情況下，客戶會在行動服務設定檔 Proxyinfo.conf 中提供自訂的應用程式 proxy 設定。 此方法可讓客戶僅針對行動服務提供 proxy，或針對電腦上其餘應用程式的 proxy （或沒有 proxy），為 Azure Site Recovery 行動服務提供不同的 proxy。

## <a name="proxy-template"></a>Proxy 範本
Proxyinfo.conf 包含下列範本 [proxy] Address = http://1.2.3.4 Port = 5678 BypassList = hypervrecoverymanager. windowsazure.storage .com，login.，net.tcp. core.。 BypassList 不支援萬用字元，例如 ' *. windows.net '，但讓 windows.net 的效果足以略過。 

## <a name="next-steps"></a>後續步驟：
- 閱讀複寫 Azure VM 的[網路指引](site-recovery-azure-to-azure-networking-guidance.md)。
- 透過[複寫 Azure VM](site-recovery-azure-to-azure.md) 來部署災害復原。