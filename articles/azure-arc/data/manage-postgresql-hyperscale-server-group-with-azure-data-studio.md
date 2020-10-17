---
title: 使用 Azure Data Studio 來管理您的于 postgresql 實例
description: 使用 Azure Data Studio 來管理您的于 postgresql 實例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a073df78adbf0104d81a96eb92ea427f60e53b01
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151842"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>使用 Azure Data Studio 來管理已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組


這篇文章說明如何：
- 使用儀表板視圖（如總覽、連接字串、屬性資源健康狀態 ...）來管理您的于 postgresql 實例 .。。
- 使用您的資料和架構

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>必要條件

- [安裝 azdata、Azure Data Studio 和 Azure CLI](install-client-tools.md)
- Azure Data Studio **Azure DATA CLI** 和 **Azure Arc** 與 **于 postgresql** 延伸模組中安裝
- 建立 [Azure Arc 資料控制器](create-data-controller-using-azdata.md)
- 啟動 Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>連接到 Azure Arc 資料控制器

在 Azure Data Studio 中，展開節點 **Azure Arc 控制器** ，然後選取 [ **連接控制器]** 按鈕：

輸入 Azure 資料控制器的連接資訊：

- **控制器 URL：**

    要在 Kubernetes 中連接至控制器的 URL。 以的形式輸入 `https://<IP_address_of_the_controller>:<Kubernetes_port.` ，例如：

    ```console
    https://12.345.67.890:30080
    ```
- **使用者名稱:**

    您用來連接到控制器的使用者帳戶名稱。 使用您在執行時通常使用的名稱 `azdata login` 。 它不是您用來連接到于 postgresql 資料庫引擎的于 postgresql 使用者名稱，通常是從 psql。
- **密碼：** 您用來連接到控制器的使用者帳戶密碼


Azure data studio 會顯示您的 Arc 資料控制器。 將它展開，並顯示其所管理的于 postgresql 實例清單。

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>管理已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組

以滑鼠右鍵按一下您想要管理的于 postgresql 實例，然後選取 [管理]

于 postgresql 儀表板視圖：

這項功能會在該窗格的左邊列出數個儀表板：

- **總覽：** 顯示有關您的實例的摘要資訊，例如名稱、于 postgresql 管理員使用者名稱、Azure 訂用帳戶識別碼、設定、資料庫引擎的版本、Grafana 和 Kibana 的端點 .。。
- **連接字串：** 顯示您可能需要連接至於 postgresql 實例的各種連接字串，例如 psql、Node.js、PHP、Ruby .。。
- **診斷並解決問題：** 是登陸頁面，您可以在其中找到各種資源，以協助您在擴充疑難排解筆記本時對實例進行疑難排解
- **新的支援要求：** 是登陸頁面，您可以從此處取得支援服務的協助，從公開預覽公告開始。

## <a name="work-with-your-data-and-schema"></a>使用您的資料和架構

在 [Azure Data Studio] 視窗的左側，展開節點 **伺服器**：

然後選取 [新增連接]，並在於 postgresql 實例中填入連線詳細資料：
- **連線類型：** 于 postgresql
- **伺服器名稱：** 輸入于 postgresql 實例的名稱。 例如： postgres01
- **驗證類型：** 密碼
- **使用者名稱：** 例如，您可以使用標準/預設于 postgresql 系統管理員使用者名稱。 請注意，此欄位會區分大小寫。
- **密碼：** 在命令的輸出中，您會在 psql 連接字串中找到于 postgresql 使用者名稱的密碼 `azdata postgres server endpoint -n postgres01`
- **資料庫名稱：** 設定您想要連接的資料庫名稱。 您可以讓它設定為 __預設值__
- **伺服器群組：** 您可以讓它設定為 __預設值__
- **名稱 (選擇性) ：** 您可以讓這個空白
- **先進：**
    - **主機 IP 位址：** 是 Kubernetes 叢集的公用 ip 位址
    - **Port：** 是于 postgresql 實例正在接聽的埠。 您可以在命令的輸出中，于 psql 連接字串的結尾找到此埠 `azdata postgres server endpoint -n postgres01` 。 Kubernetes 正在接聽的埠30080，以及您在 Azure Data Studio 中連線到 Azure 資料控制器時所輸入的埠。
    - **其他參數：** 它們應該是自我明確的，您可以使用它們所顯示的預設/空白值。

選取 **[確定] 和 [連接]** 以連接到您的伺服器。

連接之後，有數個可用的體驗：
- **新查詢**
- **新增筆記本**
- **展開伺服器的顯示，然後流覽/處理資料庫內的物件**
- **...**

## <a name="next-step"></a>後續步驟
[監視您的伺服器群組](monitor-grafana-kibana.md)
