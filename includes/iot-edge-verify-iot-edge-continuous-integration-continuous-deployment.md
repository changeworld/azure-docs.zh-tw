---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302126"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>使用組建和發行管線驗證 IoT Edge CI/CD

若要觸發組建作業，您可以推送認可至原始程式碼存放庫，或是手動觸發。 在本節中，您會手動觸發 CI/CD 管線以測試它是否有效。 接著驗證部署是否成功。

1. 從左窗格功能表中選取 [ **管線** ]，然後開啟您在本文開頭建立的組建管線。

2. 您可以選取右上方的 [ **執行管線** ] 按鈕，在組建管線中觸發組建作業。

    ![使用 [執行管線] 按鈕手動觸發組建管線](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. 檢查 **執行管線** 設定。 然後選取 [ **執行**]。

    ![指定執行管線選項並選取執行](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. 選取 [ **代理程式作業 1** ] 以監看執行的進度。 您可以藉由選取作業來檢查作業的輸出記錄。 

    ![檢查作業的記錄輸出](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. 如果組建管線已順利完成，就會觸發發行至 **開發** 階段。 成功的 **開發** 版會建立 IoT Edge 部署至目標 IoT Edge 裝置。

    ![發行至開發人員](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. 按一下 [ **開發** 階段] 以查看發行記錄。

    ![發行記錄](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)