---
title: 教學課程：在 Azure HDInsight 中設定 Apache Ambari 電子郵件通知
description: 本文說明如何將 SendGrid 搭配 Apache Ambari 用於電子郵件通知。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: bdce735bdacbe7ff0752650c6949fdb361342c73
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542551"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>教學課程：在 Azure HDInsight 中設定 Apache Ambari 電子郵件通知

在本教學課程中，您將使用 SendGrid 來設定 Apache Ambari 電子郵件通知。 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 提供容易使用的 Web UI 和 REST API，可簡化 HDInsight 叢集的管理和監視。 Ambari 包含於 HDInsight 叢集中，可用來監視叢集及進行設定變更。 [SendGrid](https://sendgrid.com/solutions/) 是一項雲端架構電子郵件服務，能提供可靠的交易式電子郵件傳遞、可擴縮性和即時分析，以及有彈性而可簡化自訂整合的 API。 Azure 客戶每月可以解除鎖定 25,000 封免費電子郵件。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 取得 Sendgrid 使用者名稱
> * 設定 Apache Ambari 電子郵件通知

## <a name="prerequisites"></a>Prerequisites

* SendGrid 電子郵件帳戶。 如需相關指示，請參閱[如何在 Azure 上使用 SendGrid 傳送電子郵件](../sendgrid-dotnet-how-to-send-email.md)。

* HDInsight 叢集。 請參閱[使用 Azure 入口網站建立 Apache Hadoop 叢集](./hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="obtain-sendgrid-username"></a>取得 SendGrid 使用者名稱

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 SendGrid 資源。

1. 從 [概觀] 頁面中選取 [管理]  ，以移至您帳戶的 SendGrid 網頁。

    ![Azure 入口網站中的 SendGrid 概觀](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. 從左側功能表瀏覽至您的帳戶名稱，然後瀏覽至 [帳戶詳細資料]  。

    ![SendGrid 儀表板導覽](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. 在 [帳戶詳細資料]  頁面上，記錄 [使用者名稱]  。

    ![SendGrid 帳戶詳細資料](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>設定 Ambari 電子郵件通知

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 從 [動作]  下拉式清單中，選取 [管理通知]  。

1. 在 [管理警示通知]  視窗中，選取 **+** 圖示。

    ![此螢幕擷取畫面顯示 [管理警示通知] 對話方塊。](./media/apache-ambari-email/azure-portal-create-notification.png)

1. 在 [建立警示通知]  對話方塊中，提供下列資訊：

    |屬性 |描述 |
    |---|---|
    |名稱|提供通知的名稱。|
    |群組|請視需要進行設定。|
    |Severity|請視需要進行設定。|
    |描述|選擇性。|
    |方法|保持為 [電子郵件]  。|
    |電子郵件收件者|提供要接收通知的電子郵件地址 (以逗號分隔)。|
    |SMTP 伺服器|`smtp.sendgrid.net`|
    |SMTP 連接埠|25 或 587 (用於未加密/TLS 連線)。|
    |電子郵件寄件者|提供電子郵件地址。 此地址不需是真實的。|
    |使用驗證|選取此核取方塊。|
    |使用者名稱|提供 SendGrid 使用者名稱。|
    |密碼|提供您在 Azure 中建立 SendGrid 資源時所使用的密碼。|
    |密碼確認|再次輸入密碼。|
    |啟動 TLS|選取此核取方塊|

    ![此螢幕擷取畫面顯示 [建立警示通知] 對話方塊。](./media/apache-ambari-email/ambari-create-alert-notification.png)

    選取 [儲存]  。 您會回到 [管理警示通知]  視窗。

1. 從 [管理警示通知]  視窗中，選取 [關閉]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將使用 SendGrid 來設定 Apache Ambari 電子郵件通知。 若要深入了解 Apache Ambari，請參考下列內容：

* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](./hdinsight-hadoop-manage-ambari.md)

* [建立警示通知](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)