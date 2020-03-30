---
title: 將 Syslog 資料連線到 Azure 哨兵 |微軟文檔
description: 瞭解如何將 Syslog 資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588071"
---
# <a name="connect-your-external-solution-using-syslog"></a>使用 Syslog 連接外部解決方案

您可以將任何支援 Syslog 的本地設備連接到 Azure Sentinel。 這是通過使用基於設備和 Azure Sentinel 之間的 Linux 電腦的代理來完成的。 如果 Linux 電腦位於 Azure 中，則可以將日誌從設備或應用程式資料流到在 Azure 中創建的專用工作區並連接它。 如果 Linux 電腦不在 Azure 中，則可以將日誌從設備資料流到安裝 Linux 代理的專用本地 VM 或電腦。 

> [!NOTE]
> 如果您的設備支援 Syslog CEF，則連接更加完整，您應該選擇此選項，並按照 CEF 中的["連接資料"](connect-common-event-format.md)中的說明進行操作。

## <a name="how-it-works"></a>運作方式

Syslog 是通用於 Linux 的事件記錄通訊協定。 應用程式將傳送的訊息可能會儲存在本機電腦上，或傳遞到 Syslog 收集器。 安裝 Log Analytics Linux 代理程式時，它會設定本機 Syslog 精靈來將訊息轉送到代理程式。 然後，代理程式會將訊息傳送至 Azure 監視器 (建立相對應記錄的位置)。

有關詳細資訊，請參閱[Azure 監視器 中的 Syslog 資料來源](../azure-monitor/platform/data-sources-syslog.md)。

> [!NOTE]
> - 代理可以從多個源收集日誌，但必須安裝在專用代理電腦上。
> - 如果要在同一 VM 上支援 CEF 和 Syslog 的連接器，請執行以下步驟以避免重復資料：
>    1. 按照說明[連接您的CEF。](connect-common-event-format.md)
>    2. 要連接 Syslog 資料，請**轉到"設置** > **工作區設置** > **高級設置** > **""資料** > **系統日誌**並設置設施及其優先順序，使其與您在 CEF 配置中使用的設施和屬性不同。 <br></br>如果選擇 **"將下面的配置應用於我的電腦**"，它將這些設置應用於連接到此工作區的所有 VM。


## <a name="connect-your-syslog-appliance"></a>連接 Syslog 設備

1. 在 Azure 哨兵中，選擇**資料連線器**，然後選擇**Syslog**連接器。

2. 在**Syslog**邊欄選項卡上，選擇 **"打開連接器"頁**。

3. 安裝 Linux 代理：
    
    - 如果 Linux 虛擬機器位於 Azure 中，請選擇**Azure Linux 虛擬機器上的"下載"和"安裝代理**"。 在**虛擬機器**邊欄選項卡中，選擇要安裝代理的虛擬機器，然後按一下"**連接**"。
    - 如果 Linux 電腦不在 Azure 中，請選擇**Linux 非 Azure 電腦上的"下載"和"安裝代理**"。 在**直接代理**邊欄選項卡中，複製**LINUX 的下載和 ONBOARD 代理**命令，並在您的電腦上運行它。 
    
   > [!NOTE]
   > 請確保根據組織的安全性原則為這些電腦配置安全設置。 例如，您可以將網路設置配置為與組織的網路安全性原則保持一致，並更改守護進程中的埠和協定以符合安全要求。

4. 選擇 **"打開工作區高級設置配置**"。

5. 在 **"高級設置"** 邊欄選項卡上，選擇 **"資料** > **系統"。** 然後添加要收集的連接器的設施。
    
    添加 syslog 設備在其日誌標頭中包括的設施。 您可以在`/etc/rsyslog.d/security-config-omsagent.conf`資料夾中**的 Syslog-d**中的 Syslog 設備中以及 中的`/etc/syslog-ng/security-config-omsagent.conf` **r-Syslog**中看到此配置。
    
    如果要使用異常的 SSH 登錄檢測與您收集的資料，添加**auth**和**authpriv**。 有關詳細資訊，請參閱[以下部分](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)。

6. 添加要監視的所有設施，並為每個功能調整任何嚴重性選項後，選擇"**將下面的配置應用於我的電腦**"核取方塊。

7. 選取 [儲存]****。 

8. 在 syslog 設備上，請確保發送指定的設施。

9. 要在 Azure 監視器中使用相關架構進行系統日誌，請搜索**Syslog**。

10. 可以使用[Azure 監視器日誌查詢中的"使用函數"中描述的](../azure-monitor/log-query/functions.md)Kusto 函數來分析 Syslog 消息。 然後，您可以將它們另存為新的日誌分析函數，以用作新的資料類型。

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>配置 Syslog 連接器以進行異常 SSH 登錄檢測

> [!IMPORTANT]
> 異常 SSH 登錄檢測當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以將機器學習 （ML） 應用於系統日誌資料，以識別異常的安全外殼 （SSH） 登錄活動。 方案包括：

- 不可能的旅行 - 當兩個成功的登錄事件從兩個位置發生，不可能在兩個登錄事件的時間範圍內到達。
- 意外位置 – 成功發生登錄事件的位置可疑。 例如，最近未看到該位置。
 
此檢測需要 Syslog 資料連線器的特定配置： 

1. 對於前一過程的步驟 5，請確保選擇**auth**和**authpriv**作為要監視的設施。 保留嚴重性選項的預設設置，以便全部選中。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![異常 SSH 登錄檢測所需的設施](./media/connect-syslog/facilities-ssh-detection.png)

2. 留出足夠的時間收集系統日誌資訊。 然後，導航到**Azure Sentinel - 日誌**，然後複製並粘貼以下查詢：
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    如果需要，更改**時間範圍**，然後選擇 **"運行**"。
    
    如果生成的計數為零，請確認連接器的配置，並且受監視的電腦確實具有為查詢指定的時間段內成功的登錄活動。
    
    如果生成的計數大於零，則 syslog 資料適用于異常的 SSH 登錄檢測。 您啟用此檢測從**分析** >  **規則範本** > **（預覽）異常 SSH 登錄檢測**。

## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Syslog 本地設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

