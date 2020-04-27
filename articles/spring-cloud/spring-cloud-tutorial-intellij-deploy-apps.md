---
title: 教學課程 - 使用 IntelliJ 部署 Azure Spring Cloud 應用程式
description: 使用 IntelliJ 將應用程式部署至 Azure Spring Cloud。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 8e473a5692c3fa2be3b0f2d823d2a36ba768d661
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731364"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>使用 IntelliJ 部署 Azure Spring Cloud 應用程式
適用於 Azure Spring Cloud 的 IntelliJ 外掛程式支援從 IntelliJ IDEA 進行應用程式部署。  

## <a name="prerequisites"></a>Prerequisites
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA，Community/Ultimate 版本，2020.1/2019.3 版](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>安裝外掛程式
您可以從 IntelliJ **外掛程式** UI 新增 Azure Toolkit for IntelliJ IDEA 3.35.0。

1. 開始 IntelliJ。  如果您先前已開啟專案，請關閉專案以顯示歡迎對話方塊。 從右下方的連結中選取 [設定]  ，然後按一下 [外掛程式]  以開啟外掛程式設定對話方塊，然後選取 [從磁片安裝外掛程式]  。

    ![選取設定](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. 搜尋 Azure Toolkit for IntelliJ。  按一下 [Install]  。

    ![安裝外掛程式](media/spring-cloud-intellij-howto/install-plugin.png)

1. 按一下 [重新啟動 IDE]  。

## <a name="tutorial-procedures"></a>教學課程程序
下列程序會使用 IntelliJ IDEA 來部署 Hello World 應用程式。

* 開啟 gs-spring-boot 專案
* 部署至 Azure Spring Cloud
* 顯示串流記錄

## <a name="open-gs-spring-boot-project"></a>開啟 gs-spring-boot 專案

1. 下載並解壓縮本教學課程的來源存放庫，或使用 Git 家ˇ以複製：git clone https://github.com/spring-guides/gs-spring-boot.git 
1. cd into gs-spring-boot\complete。
1. 開啟 IntelliJ [歡迎]  對話方塊，選取 [匯入專案]  以開啟匯入精靈。
1. 選取 `gs-spring-boot\complete` 資料夾。

    ![匯入專案](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>部署至 Azure Spring Cloud
若要部署至 Azure，您必須使用您的 Azure 帳戶登入，然後選擇您的訂用帳戶。  如需登入詳細資訊，請參閱[安裝和登入](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)。

1. 在 IntelliJ 專案總管中以滑鼠右鍵按一下專案，然後選取 [Azure]   ->  [部署至 Azure Spring Cloud]  。

    ![部署至 Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. 在 [名稱]  欄位中，接受應用程式的名稱。 [名稱]  是指設定，而不是應用程式名稱。 使用者通常不需要加以變更。
1. 接受專案中 [成品]  的識別碼。
1. 選取 [應用程式:]  ，然後按一下 [建立應用程式...]  。

    ![部署至 Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. 輸入 [應用程式名稱]  ，然後按一下 [確定]  。

    ![部署至 Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. 按一下 [執行]  按鈕開始部署。 

    ![部署至 Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. 外掛程式會在專案上執行命令 `mvn package`，然後建立新的應用程式並部署 `package` 命令所產生的 jar。

1. 如果輸出視窗中未顯示應用程式 URL，請從 Azure 入口網站取得。 從您的資源群組瀏覽至 zure Spring Cloud 的執行個體。  然後按一下 [應用程式]  。  將會列出正在執行的應用程式。

    ![取得測試 URL](media/spring-cloud-intellij-howto/get-test-url.png)

1. 請在瀏覽器中瀏覽至 URL。

    ![在瀏覽器 2 中瀏覽](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>顯示串流記錄
若要取得記錄：
1. 選取 [Azure Explorer]  ，然後選取 [Spring Cloud]  。
1. 以滑鼠右鍵按一下正在執行的應用程式。
1. 從下拉式清單中選取 [串流記錄]  。

    ![選取串流記錄](media/spring-cloud-intellij-howto/streaming-logs.png)

1. 選取執行個體。

    ![選取執行個體](media/spring-cloud-intellij-howto/select-instance.png)

1. 串流記錄會顯示在輸出視窗中。

    ![串流記錄輸出](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>後續步驟
* [準備適用於 Azure Spring Cloud 的 Spring 應用程式](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [深入了解 Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
