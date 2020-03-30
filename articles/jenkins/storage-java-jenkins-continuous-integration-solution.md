---
title: 將 Azure 儲存體用於 Jenkins 連續整合解決方案
description: 本教程演示如何將 Azure Blob 服務用作由 Jenkins 連續集成解決方案創建的生成工件的存儲庫。
keywords: 詹金斯， 蔚藍， devops， 存儲， cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624679"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>將 Azure 儲存體用於 Jenkins 連續整合解決方案

本文說明如何使用 Blob 儲存體作為 Jenkins 連續整合 (CI) 解決方案所建立的組建構件存放庫，或作為在組建程序中要使用的可下載檔案的來源。 您會發現此解決方案有其效用的情況之一，是當您在敏捷式開發環境中編寫程式碼 (使用 Java 或其他語言) 時，組建是根據連續整合來執行，而您需要一個存放庫來存放組建構件，以便能夠將這些構件分享給其他組織成員或客戶，或是維護封存等等。 另一種情況是當組建工作本身需要其他檔案時，例如，要隨組建輸入一起下載的相依性項目。

在本教學課程中，您將使用由 Microsoft 所提供適用於 Jenkins CI 的 Azure 儲存體外掛程式。

## <a name="jenkins-overview"></a>Jenkins 概觀

Jenkins 透過讓開發人員輕鬆整合自己的程式碼變更，並會以自動且頻繁的方式產生組建，來實現軟體專案的連續整合，從而提升開發人員產能。 組建會分版本存在，而組建成品可以上傳至各種存放庫。 本文說明如何使用 Azure Blob 儲存體作為組建構件的存放庫。 其中也示範如何從 Azure Blob 儲存體下載相依性項目。

如需 Jenkins 的詳細資訊，請參閱 [認識 Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)(英文)。

## <a name="benefits-of-using-the-blob-service"></a>使用 Blob 服務的優點

使用 Blob 服務來裝載您敏捷式開發組建成品的優點包括：

* 組建成品和/或可下載相依性項目的高可用性。
* 在您的 Jenkins CI 解決方案上傳組建成品時提供良好的效能。
* 在您的客戶和合作夥伴下載組建成品時提供良好的效能。
* 提供使用者存取原則控制，可以選擇匿名存取、期限型共用存取簽章存取、私用存取等。

## <a name="prerequisites"></a>Prerequisites

* 一個 Jenkins 連續整合解決方案。
  
    如果您目前沒有 Jenkins CI 解決方案，可以透過下列步驟執行 Jenkins CI 解決方案：
  
  1. 在已啟用 Java 的機器上，從 <https://jenkins-ci.org> 下載 jenkins.war。
  2. 在命令提示字元中切換至包含 jenkins.war 的資料夾，然後執行：
     
      `java -jar jenkins.war`

  3. 在瀏覽器中開啟 `http://localhost:8080/` 以開啟 Jenkins 儀表板，您將使用此儀表板來安裝及設定 Azure 儲存體外掛程式。
     
      雖然一般 Jenkins CI 解決方案會設定成以服務的形式執行，但對本教學課程來說，在命令列執行 Jenkins war 已經足夠。
* 一個 Azure 帳戶。 您可以在 <https://www.azure.com> 註冊 Azure 帳戶。
* 一個 Azure 儲存體帳戶。 如果您還沒有儲存體帳戶，可以使用 [建立儲存體帳戶](../storage/common/storage-account-create.md)中的步驟建立一個帳戶。
* 建議您熟悉 Jenkins CI 解決方案，但這並非必要，因為下列內容將使用一個基本範例來示範使用 Blob 服務作為 Jenkins CI 組建成品的存放庫時所需的步驟。

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>如何使用 Blob 服務搭配 Jenkins CI
若要使用 Blob 服務搭配 Jenkins，您將要安裝 Azure 儲存體外掛程式、設定外掛程式來使用您的儲存體帳戶，然後建立一個會將您的組建成品上傳至您儲存體帳戶的建置後動作。 這些步驟將於下列各節中說明。

## <a name="how-to-install-the-azure-storage-plugin"></a>如何安裝 Azure 儲存體外掛程式
1. 在 Jenkins 儀表板中，選取 [管理 Jenkins]****。
2. 在 [管理 Jenkins]**** 頁面中，選取 [管理外掛程式]****。
3. 選取 [可用]**** 索引標籤。
4. 在 [構件上傳程式]**** 區段中，取核 [Microsoft Azure 儲存體外掛程式]****。
5. 選取 [直接安裝而不重新啟動]**** 或 [立即下載並於重新啟動後安裝]****。
6. 重新啟動 Jenkins。

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>如何設定 Azure 儲存體外掛程式來使用您的儲存體帳戶
1. 在 Jenkins 儀表板中，選取 [管理 Jenkins]****。
2. 在 [管理 Jenkins]**** 頁面中，選取 [設定系統]****。
3. 在 [Microsoft Azure Storage Account Configuration] **** 區段中：
   1. 輸入存儲帳戶名稱，可以從[Azure 門戶](https://portal.azure.com)獲取該名稱。
   2. 輸入存儲帳戶金鑰，也可以從[Azure 門戶](https://portal.azure.com)獲取。
   3. 如果您使用全域 Azure 雲端，請讓 [Blob 服務端點 URL]**** 使用預設值。 如果使用其他 Azure 雲，請使用存儲帳戶的[Azure 門戶](https://portal.azure.com)中指定的終結點。 
   4. 按一下 [驗證儲存體認證]**** 以驗證您的儲存體帳戶。 
   5. [選擇性] 如果您有其他儲存體帳戶要提供給 Jenkins CI 使用，請選取 [新增更多儲存體帳戶]****。
   6. 選擇 **"保存"** 以保存設置。

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>如何建立會將您的組建成品上傳至您儲存體帳戶的建置後動作
為了方便說明，您會先建立一個會建立數個檔案的作業，然後新增會將這些檔案上傳至儲存體帳戶的建置後動作。

1. 在 Jenkins 儀表板中，選取 [新增項目]****。
2. 將作業命名為 **MyJob**，選取 [建立任意樣式的軟體專案]****，然後選取 [確定]****。
3. 在作業組態的 [組建]**** 區段中，選取 [新增組建步驟]****，然後選取 [執行 Windows 批次命令]****。
4. 在 [Command] **** 中，使用下列命令：

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. 在作業組態的 [建置後動作]**** 區段中，選取 [新增建置後動作]****，然後選取 [將構件上傳至 Azure Blob 儲存體]****。
6. 在 [儲存體帳戶名稱] **** 中，選取要使用的儲存體帳戶。
7. 在 [容器名稱]**** 中，指定容器名稱。 （如果上載生成專案時容器不存在，將創建該容器。您可以使用環境變數，因此對於此示例，請輸入`${JOB_NAME}`為容器名稱。
   
    **提示**
   
    在您為 [執行 Windows 批次命令]**** 輸入指令碼的 [命令]**** 區段底下有一個 Jenkins 所辨識環境變數的連結。 選取該連結可了解各環境變數名稱和描述。 含有特殊字元的環境變數 (例如 **BUILD_URL** 環境變數) 不能當做容器名稱或共同虛擬路徑。
8. 在此範例中，請選取 [依預設將新容器設為公用]****。 (如果您想要使用私用容器，則需要建立共用存取簽章來允許存取，但這不在本文的討論之列。 若要深入了解共用存取簽章，請參閱[使用共用存取簽章 (SAS)](../storage/common/storage-sas-overview.md)。)
9. [選擇性] 如果您要在上傳組建構件之前清除容器的內容，請選取 [在上傳之前清除容器]**** (若不想清除容器的內容，請維持不核取)。
10. 針對 [要上傳的構件清單]****，輸入 `text/*.txt`。
11. 在 [上傳構件的常用虛擬路徑]**** 中，基於本教學課程的目的，輸入 `${BUILD\_ID}/${BUILD\_NUMBER}`。
12. 選擇 **"保存"** 以保存設置。
13. 在 Jenkins 儀表板中，選取 [立即建置]**** 以執行 **MyJob**。 檢查主控台輸出中的狀態。 當建置後動作開始上傳組建成品時，主控台輸出中將會包含 Azure 儲存體的狀態訊息。
14. 順利完成作業時，您就可以開啟公用 Blob 來檢查組建成品。
    1. 登錄到 Azure[門戶](https://portal.azure.com)。
    2. 選取 [**儲存體**]。
    3. 選取您用於 Jenkins 的儲存體帳戶名稱。
    4. 選取 [容器] ****。
    5. 選取名為 **myjob**的容器，這是您建立 Jenkins 工作時所指派的工作名稱小寫版本。 在 Azure 儲存體中，容器名稱和 Blob 名稱皆為小寫 (並且區分大小寫)。 在名為**myjob**的容器的 blob 清單中，您應該會看到**hello.txt**和**date.txt**。 請複製這些項目中任何一項的 URL，然後在瀏覽器中開啟它。 您會看到文字檔已上傳作為組建成品。

每一工作只能建立一個將成品上傳至 Azure Blob 儲存體的建置後動作。 將構件上傳至 Azure Blob 儲存體的單一建置後動作，可在 [要上船的構件清單]**** 內使用分號作為分隔符號來指定不同的檔案 (包含萬用字元) 和檔案路徑。 例如，若 Jenkins 組建在您工作區的 **build** 資料夾中產生 JAR 檔和 TXT 檔，且您想將兩者都上傳至 Azure Blob 儲存體，請在 [要上傳的構件清單]**** 選項中使用下列值：`build/\*.jar;build/\*.txt`。 您也可以使用雙冒號語法來指定要在 Blob 名稱內使用的路徑。 例如，若您想要在 Blob 路徑中使用 **binaries** 上傳 JAR，並在 Blob 路徑中使用 **notices** 上傳 TXT，請在 [要上傳的構件清單]**** 中使用下列值：`build/\*.jar::binaries;build/\*.txt::notices`。

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>如何建立從 Azure Blob 儲存體下載的組建步驟
下列步驟說明如何設定從 Azure Blob 儲存體下載項目的組建步驟，如果您想要在組件中包含項目，則可以使用。 使用此模式的範例，是您可能想要在 Azure Blob 儲存體中保存的 JAR。

1. 在工作組態的 [組建]**** 區段中，選取 [新增組建步驟]****，然後選取 [從 Azure Blob 儲存體下載]****。
2. 在 [儲存體帳戶名稱] **** 中，選取要使用的儲存體帳戶。
3. 在 [容器名稱] **** 中，指定您要下載的 Blob 所在之容器的名稱。 您可以使用環境變數。
4. 在 [Blob 名稱]**** 中，指定 Blob 名稱。 您可以使用環境變數。 另外，您也可以在指定 Blob 名稱的開頭字母之後，使用星號作為萬用字元。 例如 **，project\\*** 將指定名稱以**專案**開頭的所有 Blob。
5. [選擇性] 在 [下載路徑]**** 中，指定在 Jenkins 機器上您要從 Azure Blob 儲存體下載檔案的路徑。 也可以使用環境變數 (如果您未提供 [下載路徑]**** 的值，則 Azure Blob 儲存體中的檔案會下載至工作的工作區。)

如果您還有其他項目要從 Azure Blob 儲存體下載，您可以建立其他組建步驟。

執行組建之後，您可以檢查組建記錄主控台輸出，或查看下載位置，了解是否已成功下載您想要的 Blob。  

## <a name="components-used-by-the-blob-service"></a>Blob 服務所使用的元件
本節提供 Blob 服務元件的概觀。

* **存儲帳戶**：對 Azure 存儲的所有訪問都通過存儲帳戶完成。 儲存體帳戶是存取 Blob 的最上層命名空間。 帳戶可以包含不限數目的容器，只要它們的大小總計低於 100 TB 即可。
* **容器**：容器提供一組 Blob 的群組。 所有 Blob 都必須位於容器中。 帳戶可以包含不限制數目的容器。 容器可以儲存不限數目的 Blob。
* **Blob**：任何類型和大小的檔案。 Azure 儲存體可以儲存的 Blob 類型有兩種：區塊和分頁 Blob。 大部分檔案都是區塊 Blob。 單一區塊 Blob 的大小上限為 200 GB。 本教學課程使用區塊 Blob。 分頁 Blob (另一種 Blob 類型) 的大小上限為 1 TB，當檔案中的位元組範圍經常修改時，分頁 Blob 的效率較高。 如需關於 Blob 的詳細資訊，請參閱 [了解區塊 Blob、附加 Blob 及分頁 Blob](https://msdn.microsoft.com/library/azure/ee691964.aspx)。
* **URL 格式**：可利用下列 URL 格式來定址 Blob：
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (上述格式適用於全域 Azure 雲端。 如果使用其他 Azure 雲，請使用[Azure 門戶](https://portal.azure.com)中的終結點來確定 URL 終結點。
  
    在上述格式中，`storageaccount` 代表您的儲存體帳戶名稱，`container_name` 代表您的容器名稱，而 `blob_name` 代表您的 Blob 名稱。 在容器名稱中，可以有多個路徑，由正向斜杠分隔。 **/** 本教學課程中使用的範例容器名稱為 **MyJob**，使用的共同虛擬路徑則是 **${BUILD\_ID}/${BUILD\_NUMBER}**，產生的 Blob URL 格式如下：
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>對 Jenkins 外掛程式進行疑難排解

如果您遇到任何有關 Jenkins 外掛程式的錯誤，請在 [Jenkins JIRA](https://issues.jenkins-ci.org/) 的特定元件中提交問題。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure 上的 Jenkins](/azure/Jenkins/)