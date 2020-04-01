---
title: 在 Azure 上安裝微焦點企業伺服器 4.0 和企業開發人員 4.0 |微軟文件
description: 使用 Azure 虛擬機器 (VM) 上的微焦點開發和測試環境重新託管 IBM z/OS 大型機工作負載。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 03/30/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5b3fa5faccf17df17bc4f7cc2d8b023b868fdbe1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411187"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>在 Azure 上安裝微焦點企業伺服器 4.0 和企業開發人員 4.0

本文演示如何在 Azure 上設置[微焦點企業伺服器 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/)和[微焦點企業開發人員 4.0。](https://www.microfocus.com/documentation/enterprise-developer/ed_30/)

Azure 上的常見工作負荷是開發和測試環境。 此方案很常見,因為它非常經濟高效且易於部署和拆除。 借助企業伺服器,Micro Focus 創建了最大的大型主機重新託管平臺之一。 您可以使用 Windows 或 Linux 虛擬機器 (VM) 在 Azure 上較便宜的 x86 平台上執行 z/OS 工作負荷。

> [!NOTE]
> 即將推出:有關在 Azure VM 上設置[微焦點企業伺服器 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110)的說明。

此設置使用 Azure 伺服器 2016 映射從 Azure 應用商店運行的 Azure VM,並且已安裝 Microsoft SQL Server 2017。 此設置也適用於 Azure 堆疊。

企業伺服器的相應開發環境是企業開發人員,它運行在Microsoft Visual Studio 2017或更高版本,Visual Studio社區(免費下載)或Eclipse。 本文演示如何使用 Visual Studio 2017 或更高版本附帶的 Windows Server 2016 虛擬機器進行部署。

## <a name="prerequisites"></a>Prerequisites

在開始之前,請查看以下先決條件:

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 微焦點軟體和有效的許可證(或試用許可證)。 如果您是現有的微焦點客戶,請聯繫您的微焦點代表。 否則,[請要求試用](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

- 獲取[企業伺服器和企業開發人員](https://www.microfocus.com/documentation/enterprise-developer/#")的文檔。

> [!NOTE]
> 最佳做法是設置網站到網站的虛擬專用網路 (VPN) 隧道或跳轉盒,以便您可以控制對 Azure VM 的訪問。

## <a name="install-enterprise-server"></a>安裝 Enterprise Server

1. 為了更好的安全性和可管理性,請考慮只為此專案創建新的資源組,例如 **,RGMicroFocusEntServer。** 使用 Azure 中名稱的第一部分選擇資源類型,以便更輕鬆地在清單中發現。

2. 建立虛擬機器。 在 Azure 應用商店中,選擇所需的虛擬機器和作業系統。 下面是推薦的設定:

    - **企業伺服器**: 選擇 ES2 v3 VM(具有 2 個 vCPU 和 16 GB 記憶體),安裝 Windows 伺服器 2016 和 SQL Server 2017。 此映射可從 Azure 應用商店獲得。 企業伺服器也可以使用 Azure SQL 資料庫。

    - **企業開發人員**:選擇安裝了 Windows 10 和可視化工作室的 B2ms VM(具有 2 個 vCPU 和 8 GB 記憶體)。 此映射可從 Azure 應用商店獲得。

3. 在 **「基礎知識」** 部分中,輸入您的使用者名和密碼。 選擇要用於 VM 的**訂閱**與**位置/區域**。 為資源群組選擇**RGMicro 焦點伺服器**。

4. 將兩個 VM 放入同一虛擬網路中,以便它們能夠相互通信。

5. 接受其餘設置的預設值。 請記住您為這些 VM 的管理員創建的使用者名和密碼。

6. 創建虛擬機後,在企業伺服器電腦上打開用於 HTTP 的入站埠 9003、86 和 80,在開發人員電腦上打開 3389,在開發人員電腦上打開 3389。

7. 要登錄到企業伺服器虛擬機,請在 Azure 門戶中選擇 ES2 v3 VM。 轉到 **「概述」** 部分並選擇「**連接」** 以啟動 RDP 工作階段。 使用為 VM 創建的認證登錄。

8. 從 RDP 會話中載入以下兩個檔。 由於您使用的是 Windows,因此您可以將檔案拖放到 RDP 工作階段中:

    - **es\_40.exe,** 企業伺服器安裝檔。

    - **,** 相應的許可證檔 — 企業伺服器沒有它就不會載入。

9. 按兩下檔以啟動安裝。 在第一個視窗中,選擇安裝位置並接受最終使用者許可協定。

     ![微焦點企業伺服器設定螢幕](media/01-enterprise-server.png)

     安裝程式完成後,將顯示以下訊息:

     ![微焦點企業伺服器設定螢幕](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>檢查更新

安裝後,請務必檢查任何其他更新,因為許多先決條件(如 Microsoft C++可再分發和 .NET 框架)與企業伺服器一起安裝。

### <a name="upload-the-license"></a>上傳授權

1. 啟動微焦點許可證管理。

2. 按下 **「開始**\>**微焦點授權管理員**\>**」,** 然後單擊「**安裝**」選項卡。選擇要上傳的許可證格式類型:許可證檔或 16 個字元的許可證代碼。 例如,對於檔,在**許可證檔中**,瀏覽到以前上載到 VM**的 mflic**檔,然後選擇 **「安裝許可證**」。

     ![微焦點授權管理對話框](media/03-enterprise-server.png)

3. 驗證企業伺服器是否載入。 請嘗試使用此 <http://localhost:86/> URL 從瀏覽器啟動企業伺服器管理網站。 企業伺服器管理頁如圖所示。

     ![企業伺服器管理頁面](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>在開發人員電腦上安裝企業開發人員

1. 選擇較早建立的資源群組(例如 **,RGMicroFocusEntServer),** 然後選擇開發人員映射。

2. 要登入虛擬機,請轉到 **「概述」** 部分並選擇「**連接**」 。 此登錄將啟動 RDP 工作階段。 使用為 VM 創建的認證登錄。

3. 從 RDP 工作階段中,載入以下兩個檔案(如果您願意,請拖放):

    - **edvs2017.exe**,企業伺服器安裝檔。

    - **,** 相應的許可證檔(企業開發人員不會載入沒有它)。

4. 按兩下**edvs2017.exe**檔以啟動安裝。 在第一個視窗中,選擇安裝位置並接受最終使用者許可協定。 如果需要,請選擇 **「安裝 Rumba 9.5」** 來安裝此終端模擬器,您可能需要這樣做。

     ![Visual Studio 2017 安裝對話框的微焦點企業開發人員](media/04-enterprise-server.png)

5. 設定完成後,將顯示以下訊息:

     ![設定成功訊息](media/05-enterprise-server.png)

6. 啟動微焦點許可證管理器,就像為企業伺服器所做的那樣。 選擇 **「開始**\>**微焦點授權管理員**\>**」,** 然後單擊「**安裝**」選項卡。

7. 選擇要上傳的許可證格式類型:許可證檔或 16 個字元的許可證代碼。 例如,對於檔,在**許可證檔中**,瀏覽到以前上載到 VM**的 mflic**檔,然後選擇 **「安裝許可證**」。

     ![微焦點授權管理對話框](media/07-enterprise-server.png)

當企業開發人員載入時,您在 Azure 上部署微焦點開發和測試環境就完成了!

## <a name="next-steps"></a>後續步驟

- [設定銀行示範應用程式](./demo.md)
- [在 Docker 容器執行企業伺服器](./run-enterprise-server-container.md)
- [大型主機應用程式移轉](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
