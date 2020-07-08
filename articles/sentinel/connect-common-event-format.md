---
title: 將 CEF 資料連線至 Azure Sentinel 預覽 |Microsoft Docs
description: 連接外部解決方案，以使用 Linux 電腦作為 proxy，將一般事件格式（CEF）訊息傳送至 Azure Sentinel。
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
ms.openlocfilehash: 34091e0c9f18cb87a240054f534f474710eb421d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563935"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>使用一般事件格式來連接您的外部解決方案


當您連接會傳送 CEF 訊息的外部解決方案時，有三個步驟可連接 Azure Sentinel：

步驟1：藉[由部署代理程式來連接 CEF](connect-cef-agent.md)步驟2：[執行解決方案特定步驟](connect-cef-solution-config.md)步驟3：[驗證連線能力](connect-cef-verify.md)

本文說明連線的運作方式、提供必要條件，並提供在安全性解決方案上部署代理程式的步驟，以在 Syslog 上傳送通用事件格式（CEF）訊息。 

> [!NOTE] 
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

若要進行此連線，您必須在專用的 Linux 機器（VM 或內部部署）上部署代理程式，以支援設備與 Azure Sentinel 之間的通訊。 下圖說明 Azure 中 Linux VM 的事件設定。

 ![Azure 中的 CEF](./media/connect-cef/cef-syslog-azure.png)

或者，如果您在另一個雲端或內部部署機器中使用 VM，則會有此設定。 

 ![內部部署的 CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>安全性考量

請務必根據貴組織的安全性原則來設定電腦的安全性。 例如，您可以設定您的網路以配合公司網路安全性原則，並變更背景程式中的埠和通訊協定，以符合您的需求。 您可以使用下列指示來改善您的電腦安全性性設定：  [Azure 中的安全 VM](../virtual-machines/linux/security-policy.md)、[網路安全性的最佳作法](../security/fundamentals/network-best-practices.md)。

若要在安全性解決方案和 Syslog 電腦之間使用 TLS 通訊，您必須將 Syslog daemon （rsyslog 或 Syslog）設定為在 TLS 中進行通訊：[使用 tls Rsyslog 加密 Syslog 流量](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)，[並使用 tls-Syslog-ng 加密記錄檔訊息](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)。

 
## <a name="prerequisites"></a>必要條件
請確定您用來做為 proxy 的 Linux 機器正在執行下列其中一個作業系統：

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
 
 - Daemon 版本
   - Syslog-ng： 2.1-3.22。1
   - Rsyslog： v8
  
 - 支援的 Syslog Rfc
   - Syslog RFC 3164
   - Syslog RFC 5424
 
請確定您的電腦也符合下列需求： 
- 權限
    - 您的電腦上必須具有更高的許可權（sudo）。 
- 軟體需求
    - 確定您的電腦上正在執行 Python



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 CEF 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

