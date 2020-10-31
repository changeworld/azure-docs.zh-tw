---
title: 在 Azure 上安裝微焦點 Enterprise Server 5.0 與 Enterprise Developer 5.0 |Microsoft Docs
description: 在本文中，您將瞭解如何在 Microsoft Azure 上安裝微焦點 Enterprise Server 5.0 與 Enterprise Developer 5.0。
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: bd8da9ae7e31fb60ba0ca553f5aa304ccd3621d2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127173"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>在 Azure 上安裝微焦點企業伺服器5.0 和企業開發人員5。0

本文說明如何在 Microsoft Azure 上設定 [微型企業伺服器 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) 和 [微型焦點 enterprise Developer 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) 。

Azure 上的常見工作負載是開發和測試環境。 這種情況很常見，因為它符合成本效益且易於部署和終止。 使用企業伺服器，微焦點已建立最大的大型大型主機重新裝載平臺之一。 您可以使用 Windows 或 Linux 虛擬機器 (Vm) ，在 Azure 上成本較低的 x86 平臺上執行 z/OS 工作負載。

此安裝程式會使用已安裝 Microsoft SQL Server 2017 的 Azure Marketplace 中執行 Windows Server 2016 映射的 Azure Vm。 此設定也適用于 Azure Stack。

企業伺服器的對應開發環境是企業開發人員，可在 Microsoft Visual Studio 2017 或更新版本上執行，Visual Studio Community (免費下載) 或 Eclipse。 本文說明如何使用安裝 Visual Studio 2017 或更新版本的 Windows Server 2016 虛擬機器來部署它。

## <a name="prerequisites"></a>Prerequisites

開始使用之前，請先參閱下列必要條件：

-   Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

-   微焦點軟體和有效的授權 (或試用版授權) 。 如果您是現有的微型焦點客戶，請洽詢您的微型焦點代表。 否則，請 [要求試用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

-   取得 [企業伺服器和企業開發人員](https://www.microfocus.com/documentation/enterprise-developer/ed50/)的檔。

    > [!Note]
    > 有幾個選項可控制 Vm 的存取：
    > -   最佳做法是設定 [Azure](https://azure.microsoft.com/services/azure-bastion/)防禦。
    > -   [站對站虛擬私人網路 (VPN) ](../../../../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md)通道。
    > -   Jumpbox VM。

## <a name="install-enterprise-server"></a>安裝 Enterprise Server

1.  為了獲得更好的安全性和管理能力，請考慮只為此專案建立新的資源群組，例如 **RGMicroFocusEntServer** 。 在 Azure 中使用名稱的第一個部分，選擇資源類型，讓它更容易在清單中找到。

2.  建立虛擬機器。 在 [Azure Marketplace 中，選取您要的虛擬機器和作業系統。 以下是建議的設定：

    -   **企業伺服器：** 選取 **ES2 V3 VM** (，其中有2個個 vcpu 和 16 GB 記憶體) 已安裝 Windows Server 2016 和 SQL Server 2017。 您可以從 Azure Marketplace 取得此映射。 企業伺服器也可以使用 Azure SQL Database。

    -   **企業開發人員：** 選取 **B2MS VM** (，其中有2個個 vcpu 和 8 GB 記憶體) Windows 10 和 Visual Studio 安裝。 您可以從 Azure Marketplace 取得此映射。

3.  在 [ **基本** ] 區段中，輸入您的使用者名稱和密碼。 選取您要用於 Vm 的 **訂** 用帳戶和 **位置/區域** 。 選取資源群組的 [ **RGMicroFocusEntServer** ]。

4.  將兩個 Vm 放在相同的虛擬網路中，讓它們可以彼此通訊。

5.  接受其餘設定的預設值。 請記住您為這些 Vm 的系統管理員所建立的使用者名稱和密碼。

6.  建立虛擬機器之後，請開啟適用于 HTTP 和 **3389** 的輸入埠 **9003、86** 和 **80** ，以供企業伺服器電腦上的遠端桌面通訊協定 (RDP) 和開發人員電腦上的 **3389** 。

7.  若要登入企業伺服器虛擬機器，請在 Azure 入口網站中，選取 ES2 v3 VM。 移至 [ **總覽** ] 區段，然後選取 **[連線]** 以啟動 RDP 會話。 使用您為 VM 建立的認證登入。

8.  從 RDP 會話載入下列兩個檔案。 由於您使用的是 Windows，因此您可以將檔案拖放到 RDP 會話中：

    -   `es\_50.exe`，企業伺服器安裝檔案。

    -   `mflic`，對應的授權檔案—企業伺服器不會載入。

9.  按兩下檔案以開始安裝。 在第一個視窗中，選取安裝位置並接受使用者授權合約。

    ![螢幕擷取畫面顯示 [微型焦點企業伺服器] 對話方塊，您可以在其中開始安裝。](media/install-image-1.png)

    當安裝程式完成時，會出現下列訊息：

    ![螢幕擷取畫面顯示 [微型焦點企業伺服器] 對話方塊中的成功訊息。](media/install-image-2.png)

 ### <a name="check-for-updates"></a>檢查更新

安裝之後，請務必檢查是否有一些必要條件（例如 Microsoft c + + 可轉散發套件和 .NET Framework）與 Enterprise Server 一起安裝，以查看是否有任何其他更新。

### <a name="upload-the-license"></a>上傳授權

1.  啟動微焦點授權管理。

2.  選取 [ **開始** \> **微焦點授權管理員** \> **許可證** 管理]，然後按一下 [ **安裝** ] 索引標籤。選擇要上傳的授權格式類型：授權檔案或16字元的授權碼。 例如，針對檔案，在 [ **授權檔案** ] 中，流覽至 `mflic` 先前上傳至 VM 的 * 檔案，然後選取 [ **安裝授權** ]。

    ![螢幕擷取畫面顯示 [微焦點授權管理] 對話方塊，您可以在其中選取 [安裝授權]。](media/install-image-3.png)

3.  確認企業伺服器是否已載入。 請嘗試使用下列 URL 從瀏覽器啟動企業伺服器管理網站： `http://localhost:86/` 。 [企業伺服器管理] 頁面隨即顯示，如下所示。

    ![企業伺服器管理頁面](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>在開發人員電腦上安裝 Enterprise Developer

1.  選取稍早建立的資源群組 (例如， **RGMicroFocusEntServer** ) ，然後選取開發人員映射。

2.  若要登入虛擬機器，請移至 [ **總覽** ] 區段，然後選取 **[連線]** 。 此登入會啟動 RDP 會話。 使用您為 VM 建立的認證登入。

3.  如果您想要) ，請從 RDP 會話載入下列兩個檔案 (拖放：

    -   `edvs2017.exe`，企業伺服器安裝檔案。

    -   `mflic`， (Enterprise Developer 的對應授權檔案將不會載入，) 。

4.  按兩下 **edvs2017.exe** 檔案以開始安裝。 在第一個視窗中，選取安裝位置並接受使用者授權合約。 如果您想要的話，請選擇 [ **安裝 Rumba 9.5** ] 來安裝此終端機模擬器（您可能需要這樣做）。

    ![Visual Studio 2017 安裝程式對話方塊的微型焦點企業開發人員](media/install-image-5.png)

5.  安裝程式完成之後，會出現下列訊息：

    ![設定成功訊息](media/install-image-6.png)

6.  啟動微焦點授權管理員，就像您針對 Enterprise Server 所做的一樣。 選擇 [ **開始** \> **微焦點授權管理員** \> **許可證** 管理]，然後按一下 [ **安裝** ] 索引標籤。

7.  選擇要上傳的授權格式類型：授權檔案或16個字元的授權碼。 例如，針對檔案，在 [ **授權檔案** ] 中，流覽至 `mflic` 先前上傳至 VM 的檔案，然後選取 [  **安裝授權** ]。

    ![微焦點授權管理對話方塊](media/install-image-7.png)

當企業開發人員載入時，您在 Azure 上部署的微焦點開發和測試環境已完成！

**後續步驟**

-   [設定 BankDemo 應用程式](./demo.md)

-   [在 Docker 容器中執行企業伺服器](./run-enterprise-server-container.md)

-   [大型主機應用程式移轉](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
