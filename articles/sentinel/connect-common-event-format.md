---
title: 將 CEF 資料連線到 Azure Sentinel 預覽 |Microsoft Docs
description: 將傳送常見事件格式的外部解決方案 (CEF) 訊息傳送至 Azure Sentinel，並使用 Linux 機器作為記錄轉寄站。
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: fd08e6cc953f9d8526174fc96dd4e4d1dc9063f5
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517966"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>使用常見事件格式連接您的外部解決方案

當您連線到傳送 CEF 訊息的外部解決方案時，有三個步驟可以連接 Azure Sentinel：

步驟1： [部署 Syslog/CEF](connect-cef-agent.md) 轉寄站來連線 CEF 步驟2： [執行解決方案特定步驟](connect-cef-solution-config.md) 步驟3： [驗證連線能力](connect-cef-verify.md)

本文說明連線的運作方式、列出必要條件，並顯示部署安全性解決方案機制以傳送常見事件格式 (CEF) 訊息在 Syslog 上的步驟。 

> [!NOTE] 
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

若要進行此連線，您必須部署 Syslog 轉寄站伺服器，以支援設備與 Azure Sentinel 之間的通訊。  伺服器是由已安裝 Log Analytics Linux 代理程式的專用 Linux 機器 (VM 或內部部署) 所組成。 

下圖說明在 Azure 中 Linux VM 的情況下的設定：

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果您使用另一個雲端或內部部署機器中的 VM，則會有此設定： 

 ![內部部署的 CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>安全性考量

請務必根據您組織的安全性原則來設定電腦的安全性。 例如，您可以設定您的網路，使其符合您的公司網路安全性原則，並變更 daemon 中的埠和通訊協定，以符合您的需求。 您可以使用下列指示來改進您的電腦安全性性設定：  [Azure 中的安全 VM](../virtual-machines/security-policy.md)、 [網路安全性的最佳做法](../security/fundamentals/network-best-practices.md)。

若要使用 Syslog 來源與 Syslog 轉寄站之間的 TLS 通訊，您必須設定 Syslog daemon (rsyslog 或 syslog-ng) 在 TLS 中進行通訊： [使用 tls Rsyslog 加密 Syslog 流量](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)、 [使用 tls 加密記錄訊息– Syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)。
 
## <a name="prerequisites"></a>必要條件

請確定您用來作為記錄轉寄站的 Linux 機器正在執行下列其中一個作業系統：

- 64 位元
  - CentOS 7 和8（包括次要版本） (不是 6) 
  - Amazon Linux 2017.09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 和8，包括 (不是6的次要版本) 
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
  - SUSE Linux Enterprise Server 12，15

- 32 位元
  - CentOS 7 和8（包括次要版本） (不是 6) 
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 和8，包括 (不是6的次要版本) 
  - Debian GNU/Linux 8 和 9
  - Ubuntu Linux 14.04 LTS 和 16.04 LTS
 
- Daemon 版本
  - Syslog-ng： 2.1-3.22。1
  - Rsyslog： v8
  
- 支援 Syslog Rfc
  - Syslog RFC 3164
  - Syslog RFC 5424
 
請確定您的電腦也符合下列需求： 

- 權限
  - 您必須在電腦上擁有較高的許可權 (sudo) 。 

- 軟體需求
  - 確定您的電腦上正在執行 python 2.7 或3。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解 Azure Sentinel 如何從安全性解決方案和設備收集 CEF 記錄。 若要瞭解如何將您的解決方案連線至 Azure Sentinel，請參閱下列文章：

- 步驟1： [部署 Syslog/CEF 轉寄站以連接 CEF](connect-cef-agent.md)
- 步驟2： [執行特定解決方案的步驟](connect-cef-solution-config.md)
- 步驟3： [驗證連線能力](connect-cef-verify.md)

若要深入瞭解您在 Azure Sentinel 中收集到的資料，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

