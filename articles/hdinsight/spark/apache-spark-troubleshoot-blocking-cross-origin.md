---
title: Jupyter 404 錯誤 - "阻止交叉原點 API" - Azure HDInsight
description: 由於 Azure HDInsight 中的"阻止跨源 API"，聚居伺服器 404"未找到"錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894418"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>方案：由於 Azure HDInsight 中的"阻止跨源 API"，Jupyter 伺服器 404"未找到"錯誤

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

當您在 HDInsight 上訪問 Jupyter 服務時，您會看到一個錯誤框，上面寫著"未找到"。 如果檢查 Jupyter 日誌，您將看到類似內容：

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

您還可以在 Jupyter 日誌中的"原點"欄位中看到 IP 位址。

## <a name="cause"></a>原因

此錯誤可能由以下幾項原因引起：

- 如果已配置網路安全性群組 （NSG） 規則以限制對群集的訪問。 使用 NSG 規則限制訪問仍將允許您使用 IP 位址（而不是群集名稱）直接存取 Apache Ambari 和其他服務。 但是，在訪問 Jupyter 時，您可能會看到 404"未找到"錯誤。

- 如果您已為 HDInsight 閘道指定了標準`xxx.azurehdinsight.net`以外的自訂 DNS 名稱。

## <a name="resolution"></a>解決方案

1. 修改以下兩個位置jupyter.py檔：

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 找到行，說：`NotebookApp.allow_origin='\"https://{2}.{3}\"'`並改變它： 。 `NotebookApp.allow_origin='\"*\"'`

1. 從安巴里重新開機朱皮特服務。

1. 在命令`ps aux | grep jupyter`提示符處鍵入應表明它允許任何 URL 連接到它。

這比我們已有的設置要不安全。 但假定對群集的訪問受到限制，並且允許外部訪問群集，因為我們有 NSG。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
