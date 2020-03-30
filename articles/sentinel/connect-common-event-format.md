---
title: 將 CEF 資料連線到 Azure 哨兵預覽*微軟文檔
description: 瞭解如何將 CEF 資料連線到 Azure 哨兵。
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588343"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>使用通用事件格式連接外部解決方案


連接發送 CEF 消息的外部解決方案時，有三個步驟用於連接 Azure Sentinel：

第 1 步：通過部署代理步驟 2[連接 CEF：](connect-cef-agent.md)[執行特定于解決方案的步驟](connect-cef-solution-config.md)步驟 3：[驗證連線性](connect-cef-verify.md)

本文介紹了連接的工作原理，提供了先決條件，並為您提供了在 Syslog 上發送公共事件格式 （CEF） 消息的安全解決方案上部署代理的步驟。 

> [!NOTE] 
> 資料存儲在運行 Azure Sentinel 的工作區的地理位置中。

為了建立此連接，您需要在專用 Linux 電腦（VM 或本地）上部署代理，以支援設備與 Azure Sentinel 之間的通信。 下圖描述了在 Azure 中發生 Linux VM 時設置。

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果您在另一個雲或本地電腦中使用 VM，則此設置將存在。 

 ![內部部署的 CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>安全性考量

確保根據組織的安全性原則配置電腦的安全性。 例如，您可以將網路設定為與公司網路安全性原則保持一致，並更改守護進程中的埠和協定以符合您的要求。 您可以使用以下說明來改進電腦安全性配置  [：Azure 中的安全 VM，](../virtual-machines/linux/security-policy.md)[網路安全的最佳做法](../security/fundamentals/network-best-practices.md)。

要使用安全解決方案和 Syslog 電腦之間的 TLS 通信，您需要配置 Syslog 守護進程（rsyslog 或 syslog-ng）以 TLS 進行通信：[使用 TLS-rsyslog 加密 Syslog 流量](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)，[使用 TLS _syslog-ng 加密日誌消息](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)。

 
## <a name="prerequisites"></a>Prerequisites
確保用作代理的 Linux 電腦運行以下作業系統之一：

- 64 位元
  - CentOS 6 和 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 和 7
  - Red Hat Enterprise Linux Server 6 和 7
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 位元
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 和 9
   - Ubuntu Linux 14.04 LTS 和 16.04 LTS
 
 - 守護進程版本
   - Syslog-ng： 2.1 - 3.22.1
   - Rsyslog： v8
  
 - 支援系統 RFC
   - 系統 RFC 3164
   - 系統 RFC 5424
 
確保機器也滿足以下要求： 
- 權限
    - 您的電腦上必須具有較高的權限 （sudo）。 
- 軟體需求
    - 確保 Python 在電腦上運行



## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 CEF 設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

