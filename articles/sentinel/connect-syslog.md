---
title: 將 Syslog 資料連線到 Azure Sentinel |Microsoft Docs
description: 使用設備和 Sentinel 之間 Linux 電腦上的代理程式，將支援 Syslog 的任何內部部署應用裝置連接到 Azure Sentinel。 
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
ms.openlocfilehash: 38e47469723d767561dd778b8f175780ab181fd4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076258"
---
# <a name="connect-your-external-solution-using-syslog"></a>使用 Syslog 連接您的外部解決方案

您可以將任何支援 Syslog 的內部部署設備連線到 Azure Sentinel。 這項作業是使用以設備和 Azure Sentinel 之間的 Linux 機器為基礎的代理程式來完成。 如果您的 Linux 機器位於 Azure 中，您可以將應用程式中的記錄串流至您在 Azure 中建立的專用工作區，並加以連接。 如果您的 Linux 電腦不在 Azure 中，您可以將應用程式中的記錄串流至專用的內部部署 VM 或電腦上，以安裝 Linux 的代理程式。 

> [!NOTE]
> 如果您的設備支援 Syslog CEF，連線就會更完整，而且您應該選擇此選項，並遵循[從 CEF 連接資料](connect-common-event-format.md)中的指示。

## <a name="how-it-works"></a>運作方式

Syslog 是通用於 Linux 的事件記錄通訊協定。 應用程式將傳送的訊息可能會儲存在本機電腦上，或傳遞到 Syslog 收集器。 安裝 Log Analytics Linux 代理程式時，它會設定本機 Syslog 精靈來將訊息轉送到代理程式。 然後，代理程式會將訊息傳送至 Azure 監視器 (建立相對應記錄的位置)。

如需詳細資訊，請參閱[Azure 監視器中的 Syslog 資料來源](../azure-monitor/platform/data-sources-syslog.md)。

> [!NOTE]
> - 代理程式可以從多個來源收集記錄，但必須安裝在專用的 proxy 電腦上。
> - 如果您想要在相同的 VM 上同時支援 CEF 和 Syslog 的連接器，請執行下列步驟以避免重復資料：
>    1. 遵循指示來[連接您的 CEF](connect-common-event-format.md)。
>    2. 若要連接 Syslog 資料，請移至 [**設定**] [工作區] [設定]  >  **Workspace settings**  >  [**Advanced settings**]  >  [**data**  >  **Syslog** ]，並設定設備和其優先順序，讓它們不是您在 CEF 設定中使用的相同設備和屬性。 <br></br>如果您選取 [**將下列設定套用到我的電腦**]，它會將這些設定套用到所有連線到此工作區的 vm。


## <a name="connect-your-syslog-appliance"></a>連接您的 Syslog 設備

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後選取 [ **Syslog**連接器]。

2. 在 [ **Syslog** ] 分頁上，選取 [**開啟連接器] 頁面**。

3. 安裝 Linux 代理程式：
    
    - 如果您的 Linux 虛擬機器位於 Azure 中，請選取 [在**Azure Linux 虛擬機器上下載並安裝代理程式**]。 在 [**虛擬機器**] 分頁中，選取要安裝代理程式的虛擬機器，然後按一下 **[連線]**。
    - 如果您的 Linux 電腦不在 Azure 中，請選取 [**在 linux 非 azure 機器上下載並安裝代理程式**]。 在 [**直接代理程式**] 分頁中，複製 [**下載並上架代理程式**] 命令，並在您的電腦上加以執行。 
    
   > [!NOTE]
   > 請務必根據貴組織的安全性原則來設定這些電腦的安全性設定。 例如，您可以設定網路設定，使其符合組織的網路安全性原則，並變更背景程式中的埠和通訊協定，使其符合安全性需求。

4. 選取 [**開啟您的工作區] [advanced settings configuration**]。

5. 在 [**高級設定**] 分頁上，選取 [**資料**  >  **Syslog**]。 然後新增要收集之連接器的設備。
    
    新增您的 syslog 應用裝置在其記錄標頭中所包含的功能。 您可以在您的 Syslog**設備中**，于資料夾中 `/etc/rsyslog.d/security-config-omsagent.conf` 以及從的**r-syslog**中看到這項設定 `/etc/syslog-ng/security-config-omsagent.conf` 。
    
    如果您想要搭配所收集的資料使用異常的 SSH 登入偵測，請新增**auth**和**authpriv**。 如需其他詳細資料，請參閱[下一節](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)。

6. 當您已新增要監視的所有設備，並調整每個設施的任何嚴重性選項時，請選取 [**將下列設定套用到我的電腦**] 核取方塊。

7. 選取 [儲存]。 

8. 在您的 syslog 設備上，請確定您正在傳送您所指定的設備。

9. 若要在 Azure 監視器中使用適用于 syslog 記錄的相關架構，請搜尋**syslog**。

10. 您可以使用在[Azure 監視器記錄查詢中使用](../azure-monitor/log-query/functions.md)函式中所述的 Kusto 函數來剖析 Syslog 訊息。 然後，您可以將它們儲存為新的 Log Analytics 函式，以當做新的資料類型使用。

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>設定 Syslog 連接器以進行異常的 SSH 登入偵測

> [!IMPORTANT]
> 異常的 SSH 登入偵測目前為公開預覽版。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以將機器學習（ML）套用至 syslog 資料，以識別異常的安全殼層（SSH）登入活動。 案例包括：

- 不可能的移動–當兩個成功的登入事件從兩個位置（不可能到達兩個登入事件的時間範圍內）發生時。
- 非預期的位置–發生成功登入事件的位置可疑。 例如，最近看不到位置。
 
此偵測需要特定的 Syslog 資料連線器設定： 

1. 在上一個程式的步驟5中，請確定已選取 [**驗證**] 和 [ **authpriv** ] 做為要監視的設備。 保留 [嚴重性] 選項的預設設定，讓它們全部選取。 例如:
    
    > [!div class="mx-imgBorder"]
    > ![異常 SSH 登入偵測所需的設備](./media/connect-syslog/facilities-ssh-detection.png)

2. 允許收集 syslog 資訊足夠的時間。 然後，流覽至**Azure Sentinel 記錄**檔，並複製並貼上下列查詢：
    
    ```console
    Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    ```
    
    視需要變更**時間範圍**，然後選取 [**執行**]。
    
    如果產生的計數為零，請確認連接器的設定，以及受監視的電腦在您為查詢指定的時間週期內，是否有成功的登入活動。
    
    如果產生的計數大於零，則您的 syslog 資料適用于異常的 SSH 登入偵測。 您可以從**分析**  >   **規則範本**  >  **（預覽）異常的 SSH 登入偵測**來啟用此偵測。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Syslog 內部部署應用裝置連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

