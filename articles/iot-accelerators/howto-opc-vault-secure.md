---
title: 使用 OPC 保存庫保護 OPC UA 設備的通信 - Azure |微軟文檔
description: 如何註冊 OPC UA 應用程式，以及如何向 OPC 保存庫頒發 OPC UA 設備的已簽名應用程式證書。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: af5e511cbf273bc4e4fa0a08d089a955426fe75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75454200"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>使用 OPC 保存庫證書管理服務

本文介紹如何註冊應用程式，以及如何為 OPC UA 設備頒發已簽名的應用程式證書。

## <a name="prerequisites"></a>Prerequisites

### <a name="deploy-the-certificate-management-service"></a>部署證書管理服務

首先，將服務部署到 Azure 雲。 有關詳細資訊，請參閱[部署 OPC 保存庫證書管理服務](howto-opc-vault-deploy.md)。

### <a name="create-the-issuer-ca-certificate"></a>創建頒發者 CA 憑證

如果尚未這樣做，請創建頒發者 CA 憑證。 有關詳細資訊，請參閱[為 OPC 保存庫創建和管理頒發者證書](howto-opc-vault-manage.md)。

## <a name="secure-opc-ua-applications"></a>安全 OPC UA 應用程式

### <a name="step-1-register-your-opc-ua-application"></a>第 1 步：註冊 OPC UA 應用程式 

> [!IMPORTANT]
> 註冊應用程式需要寫入器角色。

1. 在 打開憑證服務`https://myResourceGroup-app.azurewebsites.net`，然後登錄。
2. 轉到**註冊新**。 對於應用程式註冊，使用者至少需要分配 Writer 角色。
2. 條目表單遵循 OPC UA 中的命名約定。 例如，在以下螢幕截圖中，將顯示 OPC UA .NET 標準堆疊中[OPC UA 參考伺服器](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference)示例的設置：

   ![UA 參考伺服器註冊螢幕截圖](media/howto-opc-vault-secure/reference-server-registration.png "UA 參考伺服器註冊")

5. 選擇 **"註冊"** 以在憑證服務應用程式資料庫中註冊應用程式。 工作流直接引導使用者執行下一步，以請求應用程式的已簽章憑證。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>第 2 步：使用 CA 簽名的應用程式證書保護應用程式

通過基於證書簽名請求 （CSR） 頒發簽章憑證來保護您的 OPC UA 應用程式。 或者，您可以請求一個新的金鑰組，其中包括一個新的私密金鑰在 PFX 或 PEM 格式。 有關應用程式支援哪種方法的資訊，請參閱 OPC UA 設備的文檔。 通常，建議使用 CSR 方法，因為它不需要通過導線傳輸私密金鑰。

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>使用新金鑰組請求新證書

1. 轉到**應用程式**。
3. 為列出的應用程式選擇 **"新請求**"。

   ![請求新證書的螢幕截圖](media/howto-opc-vault-secure/request-new-certificate.png "請求新證書")

3. 選擇 **"請求新的金鑰組合和證書**"以請求私密金鑰和帶有應用程式公開金鑰的新簽章憑證。

   ![生成新金鑰組和證書的螢幕截圖](media/howto-opc-vault-secure/generate-new-key-pair.png "生成新金鑰組")

4. 填寫表格中，填寫主題和功能變數名稱。 對於私密金鑰，選擇帶有密碼的 PEM 或 PFX。 選擇 **"生成新金鑰組合**"以創建證書請求。

   ![查看證書請求詳細資訊的螢幕截圖](media/howto-opc-vault-secure/approve-reject.png "批准證書")

5. 審批需要具有核准者角色的使用者，並在 Azure 金鑰保存庫中具有簽名許可權。 在典型的工作流中，應將核准者和要求者角色指派給不同的使用者。 選擇 **"批准**"或 **"拒絕**"以啟動或取消金鑰組的實際創建和簽名操作。 新的金鑰組將安全地創建並存儲在 Azure 金鑰保存庫中，直到證書要求者下載。 使用公開金鑰生成的證書由 CA 簽名。 這些操作可能需要幾秒鐘才能完成。

   ![查看證書請求詳細資訊的螢幕截圖，底部有批准消息](media/howto-opc-vault-secure/view-key-pair.png "查看金鑰組")

7. 生成的私密金鑰 （PFX 或 PEM） 和證書 （DER） 可以在此處以選擇為二進位檔案下載的格式下載。 base64 編碼版本也可用於複製證書並將其粘貼到命令列或文本條目。 
8. 在安全下載並存儲私密金鑰後，您可以選擇 **"刪除私密金鑰**"。 帶有公開金鑰的證書仍可供將來使用。
9. 由於使用 CA 簽章憑證，CA 憑證和憑證撤銷清單 （CRL） 也應在此處下載。

現在，它取決於 OPC UA 設備如何應用新的金鑰組。 通常，CA 憑證和 CRL 複製到`trusted`資料夾，而應用程式證書的公共金鑰和私密金鑰將應用於憑證存放區中的`own`資料夾。 某些設備可能已支援伺服器推送證書更新。 請參閱 OPC UA 設備的文檔。

#### <a name="request-a-new-certificate-with-a-csr"></a>使用 CSR 請求新證書 

1. 轉到**應用程式**。
3. 為列出的應用程式選擇 **"新請求**"。

   ![請求新證書的螢幕截圖](media/howto-opc-vault-secure/request-new-certificate.png "請求新證書")

3. 選擇 **"使用簽名請求請求新證書**"，以請求應用程式的新簽章憑證。

   ![生成新證書的螢幕截圖](media/howto-opc-vault-secure/generate-new-certificate.png "生成新證書")

4. 通過選擇本地檔或在表單中粘貼 base64 編碼 CSR 來上載 CSR。 選擇 **"生成新證書**"。

   ![查看證書請求詳細資訊的螢幕截圖](media/howto-opc-vault-secure/approve-reject-csr.png "批准 CSR")

5. 審批需要具有核准者角色的使用者，並在 Azure 金鑰保存庫中具有簽名許可權。 選擇 **"批准**"或 **"拒絕**"以啟動或取消實際的簽名操作。 使用公開金鑰生成的證書由 CA 簽名。 此操作可能需要幾秒鐘才能完成。

   ![查看證書請求詳細資訊的螢幕截圖，底部有批准消息](media/howto-opc-vault-secure/view-cert-csr.png "檢視憑證")

6. 生成的證書 （DER） 可以在此處作為二進位檔案下載。 base64 編碼版本也可用於複製證書並將其粘貼到命令列或文本條目。 
10. 安全下載並存儲證書後，您可以選擇 **"刪除證書**"。
11. 由於使用 CA 簽章憑證，CA 憑證和 CRL 也應在此處下載。

現在，它取決於 OPC UA 設備如何應用新證書。 通常，CA 憑證和 CRL 複製到`trusted`資料夾，而應用程式證書將應用於憑證存放區中的`own`資料夾。 某些設備可能已支援伺服器推送證書更新。 請參閱 OPC UA 設備的文檔。

### <a name="step-3-device-secured"></a>第 3 步：設備安全

OPC UA 設備現在已準備好與受 CA 簽章憑證保護的其他 OPC UA 設備進行通信，無需進一步配置。

## <a name="next-steps"></a>後續步驟

現在，您已經學會了如何保護 OPC UA 設備，您可以：

> [!div class="nextstepaction"]
> [運行安全證書管理服務](howto-opc-vault-secure-ca.md)
