---
title: 設定 Azure 至 Azure 損毀修復的行動服務 Proxy 設定 |Microsoft Docs
description: 提供有關如何在客戶在其來源環境中使用 proxy 時設定行動服務的詳細資料。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86133228"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>設定 Azure 至 Azure 嚴重損壞修復的行動服務 Proxy 設定

本文提供在使用 [Azure Site Recovery](site-recovery-overview.md)將 azure vm 從一個區域複寫及復原到另一個區域時，自訂目標 Azure 虛擬機器上的網路設定 (VM) 的指引。

本檔的目的是要提供在 Azure 至 Azure 嚴重損壞修復案例中，為 Azure Site Recovery 行動服務設定 Proxy 設定的步驟。 

Proxy 是允許/不允許端點的網路連接的網路閘道。 Proxy 通常是用戶端電腦外部嘗試存取網路端點的電腦。 略過清單可讓用戶端直接連接到端點，而不需要通過 proxy。 網路系統管理員可以選擇性地為 proxy 設定使用者名稱和密碼，如此一來，只有經過驗證的用戶端可以使用 proxy。 

## <a name="before-you-start"></a>開始之前

了解 Site Recovery 如何針對[這種情況](azure-to-azure-architecture.md)提供災害復原。
瞭解當您使用[Azure Site Recovery](site-recovery-overview.md)將 Azure vm 從一個區域複寫及復原到另一個區域時的[網路指導](azure-to-azure-about-networking.md)方針。
確定您的 proxy 已根據組織的需求適當地設定。

## <a name="configure-the-mobility-service"></a>設定行動服務

行動服務僅支援未經驗證的 proxy。 它提供兩種方式來輸入 proxy 詳細資料，以便與 Site Recovery 端點進行通訊。 

### <a name="method-1-auto-detection"></a>方法1：自動偵測

行動服務會在啟用複寫期間，自動從環境設定或 IE 設定 (Windows) 中偵測 proxy 設定。 

- Windows OS：在啟用複寫期間，行動服務會偵測本機系統使用者 Internet Explorer 中所設定的 proxy 設定。 若要設定本機系統帳戶的 proxy，系統管理員可以使用 psexec 來啟動命令提示字元，然後 Internet Explorer。 
- Windows OS： Proxy 設定會設定為環境變數 HTTP_proxy 和 no_proxy。 
- Linux OS： Proxy 設定在/etc/profile 或/etc/environment 中設定為環境變數 HTTP_proxy，no_proxy。 
- 自動偵測到的 proxy 設定會儲存在行動服務 proxy 設定檔 Proxyinfo.conf 中。 
- Proxyinfo.conf 的預設位置 
    - Windows： C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux：/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>方法2：提供自訂應用程式 proxy 設定

在此情況下，客戶會在行動服務設定檔 Proxyinfo.conf 中提供自訂應用程式 proxy 設定。 這種方法可讓客戶只針對行動服務提供 proxy，或針對 Azure Site Recovery 行動服務提供不同于 proxy (的 proxy，或針對電腦上的其餘應用程式提供 proxy) 。

## <a name="proxy-template"></a>Proxy 範本
Proxyinfo.conf 包含下列範本 [proxy] Address = http://1.2.3.4 Port = 5678 BypassList = hypervrecoverymanager. windowsazure .com，login. microsoftonline .com，net.tcp。 BypassList 不支援萬用字元（例如 ' *. windows.net '），但提供 windows.net 夠好用來略過。 

## <a name="next-steps"></a>後續步驟：
- 閱讀複寫 Azure VM 的[網路指引](./azure-to-azure-about-networking.md)。
- 透過[複寫 Azure VM](./azure-to-azure-quickstart.md) 來部署災害復原。
