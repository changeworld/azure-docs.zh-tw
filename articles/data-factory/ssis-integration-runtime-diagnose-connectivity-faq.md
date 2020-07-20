---
title: 針對在 SSIS 整合執行時間中診斷連線問題進行疑難排解
description: 本文提供在 SSIS 整合執行時間中診斷連線能力的疑難排解指引
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172538"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>針對在 SSIS 整合執行時間中診斷連線問題進行疑難排解

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果您在 SSIS 整合執行時間中執行 SSIS 套件時遇到連線問題，特別是當您的 SSIS 整合執行時間已加入 Azure 虛擬網路時。 您可以使用 Azure Data Factory 入口網站的 [監視 SSIS 整合執行時間] 頁面底下的 [診斷連線能力] 功能，嘗試自行診斷問題。 

 ![監視頁面-診斷連線能力 ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ 監視頁面-測試連接](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
本文包含當您在 SSIS 整合執行時間中測試連線時，可能會發現的最常見錯誤。 其中說明可能的原因和解決錯誤的動作。 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>常見錯誤、可能原因和建議解決方案

### <a name="error-code-invalidinput"></a>錯誤碼： InvalidInput。
* **錯誤訊息**：請確認您的輸入是否正確。
* **可能的原因：** 您的輸入不正確。
* **建議：** 請檢查您的輸入。

### <a name="error-code-firewallornetworkissue"></a>錯誤碼： FirewallOrNetworkIssue。
* **錯誤訊息**：請確認您的防火牆/伺服器/NSG 上已開啟此埠，且網路穩定。
* **可能的原因：** 
  * 您的伺服器不會開啟此埠。
  * 您的網路安全性群組已拒絕此埠上的輸出流量
  * 您的 NVA/Azure 防火牆/內部內部部署防火牆不會開啟此埠。
* **建議：** 
  * 在伺服器上開啟此埠。
  * 更新網路安全性群組，以允許此埠上的輸出流量。
  * 在 [NVA]/[Azure 防火牆]/[內部內部部署] 防火牆上開啟此埠。

### <a name="error-code-misconfigureddnssettings"></a>錯誤碼： MisconfiguredDnsSettings。
* **錯誤訊息**：如果您在 AZURE SSIS IR 所聯結的 VNet 中使用自己的 DNS 伺服器，請確認它可以解析您的主機名稱。
* **可能的原因：** 
  *  自訂 DNS 的問題
  *  您的私用主機名稱未使用完整功能變數名稱 (FQDN) 
* **建議：** 
  *  請修正您的自訂 DNS 問題，確定它可以解析主機名稱。
  *  請使用 (FQDN) 的完整功能變數名稱，例如使用 <your_private_server>. contoso.com，而不是 <your_private_server>，因為 Azure SSIS IR 不會自動附加您自己的 DNS 尾碼。

### <a name="error-code-servernotallowremoteconenction"></a>錯誤碼： ServerNotAllowRemoteConenction。
* **錯誤訊息**：請確認您的伺服器允許透過此埠進行遠端 TCP 連線。
* **可能的原因：** 
  *  您的伺服器防火牆不允許遠端 TCP 連線。
  *  您的伺服器不在線上。
* **建議：** 
  *  允許伺服器防火牆上的遠端 TCP 連接。
  *  啟動伺服器。
   
### <a name="error-code-misconfigurednsgsettings"></a>錯誤碼： MisconfiguredNsgSettings。
* **錯誤訊息**：請確認您的 VNet NSG 允許透過此埠的輸出流量。 如果您使用 Azure ExpressRoute 和或 UDR，請確認您的防火牆/伺服器上已開啟此埠。
* **可能的原因：** 
  *  您的網路安全性群組已拒絕此埠上的輸出流量。
  *  您的 NVA/Azure 防火牆/內部內部部署防火牆不會開啟此埠。
* **建議：** 
  *  更新網路安全性群組，以允許此埠上的輸出流量。
  *  在 [NVA]/[Azure 防火牆]/[內部內部部署] 防火牆上開啟此埠。

### <a name="error-code-genericissues"></a>錯誤碼： GenericIssues。
* **錯誤訊息**：由於一般問題，導致測試連接失敗。
* **可能的原因：** 測試連接發生一般暫時性的問題。
* **建議：** 請稍後再重試測試連接。 如果重試不提供協助，請洽詢 Azure Data Factory 支援小組。


### <a name="error-code-pspingexecutiontimeout"></a>錯誤碼： PSPingExecutionTimeout。
* **錯誤訊息**：測試連接逾時，請稍後再試一次。
* **可能的原因：** 測試連接逾時。
* **建議：** 請稍後再重試測試連接。 如果重試不提供協助，請洽詢 Azure Data Factory 支援小組。

### <a name="error-code-networkinstable"></a>錯誤碼： NetworkInstable。
* **錯誤訊息**：由於網路不穩定，導致測試連線不規則地成功。
* **可能的原因：** 暫時性的網路問題。
* **建議：** 請檢查伺服器或防火牆網路是否穩定。

## <a name="next-steps"></a>後續步驟

- 部署您的套件。 如需詳細資訊，請參閱[使用 SSMS 將 SSIS 專案部署到 Azure](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)。
- 執行您的套件。 如需詳細資訊，請參閱[在 Azure 中使用 SSMS 執行 SSIS 套件](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)。
- 排程您的套件。 如需詳細資訊，請參閱[在 Azure 中排程 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)。

