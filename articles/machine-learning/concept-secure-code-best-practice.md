---
title: 安全程式碼最佳做法
titleSuffix: Azure Machine Learning
description: 瞭解針對 Azure Machine Learning 進行開發時可能存在的潛在安全性威脅。 瞭解 Azure ML 提供的緩和措施，以及確保您的開發環境保持安全的最佳作法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: larryfr
ms.date: 11/12/2019
ms.openlocfilehash: 9e41323d05f1a81fbc73a9158cf30c2b0f7953f3
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147059"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>使用 Azure Machine Learning 保護程式碼最佳做法

在 Azure Machine Learning 中，您可以將來自任何來源的檔案和內容上傳至 Azure。 您載入的 Jupyter 筆記本或腳本內的內容，可能會從您的會話讀取資料、在 Azure 中存取組織內的資料，或代表您執行惡意流程。

> [!IMPORTANT]
> 僅從信任的來源執行筆記本或腳本。 例如，您或您的安全性小組已審查筆記本或腳本。

## <a name="potential-threats"></a>潛在威脅

使用 Azure Machine Learning 的開發通常牽涉到以 web 為基礎的開發環境， (筆記本 & Azure ML studio) 。 使用 web 架構的開發環境時，可能的威脅包括：

* [跨網站腳本 (XSS) ](https://owasp.org/www-community/attacks/xss/)

    * __DOM 插入__：這種類型的攻擊可以修改瀏覽器中顯示的 UI。 例如，藉由變更 [執行] 按鈕在 Jupyter Notebook 中的運作方式。
    * __存取權杖/cookie__： XSS 攻擊也可以存取本機儲存體和瀏覽器 cookie。 您的 Azure Active Directory (AAD) 驗證權杖會儲存在本機儲存體中。 XSS 攻擊可以使用此權杖來代表您進行 API 呼叫，然後將資料傳送至外部系統或 API。

* [跨網站要求偽造 (CSRF) ](https://owasp.org/www-community/attacks/csrf)：這項攻擊可能會將影像或連結的 url 取代為惡意腳本或 API 的 url。 載入影像或按一下連結時，會對 URL 進行呼叫。

## <a name="azure-ml-studio-notebooks"></a>Azure ML studio 筆記本

Azure Machine Learning studio 會在您的瀏覽器中提供託管的筆記本體驗。 筆記本中的資料格可以輸出包含惡意程式碼的 HTML 檔案或片段。  當輸出呈現時，可以執行程式碼。

__可能的威脅__：
* 跨網站腳本 (XSS) 
* 跨網站要求偽造 (CSRF) 

__Azure Machine Learning 所提供的緩和措施__：
* 程式__代碼資料格輸出__會在 iframe 中進行沙箱處理。 Iframe 可防止腳本存取父 DOM、cookie 或會話儲存體。
* __Markdown 資料格__內容會使用 dompurify 程式庫來清除。 這會在轉譯時封鎖使用 markdown 儲存格執行的惡意腳本。
* __影像 URL__和__Markdown 連結__會傳送至 Microsoft 擁有的端點，以檢查是否有惡意的值。 如果偵測到惡意值，則端點會拒絕要求。

__建議的動作__：
* 請先確認您信任檔案的內容，再上傳至 studio。 上傳時，您必須確認您上傳的是信任的檔案。
* 選取連結以開啟外部應用程式時，系統會提示您信任該應用程式。

## <a name="azure-ml-compute-instance"></a>Azure ML 計算實例

Azure Machine Learning 計算實例主機__Jupyter__和__Jupyter 實驗室__。 當您使用時，筆記本中的儲存格或中的程式碼可以輸出包含惡意程式碼的 HTML 檔案或片段。 當輸出呈現時，可以執行程式碼。 使用裝載于計算實例上的__RStudio__時，也適用相同的威脅。

__可能的威脅__：
* 跨網站腳本 (XSS) 
* 跨網站要求偽造 (CSRF) 

__Azure Machine Learning 所提供的緩和措施__：
* 無。 Jupyter 和 Jupyter 實驗室是裝載于 Azure Machine Learning 計算實例上的開放原始碼應用程式。

__建議的動作__：
* 請先確認您信任檔案的內容，再上傳至 studio。 上傳時，您必須確認您上傳的是信任的檔案。

## <a name="report-security-issues-or-concerns"></a>報告安全性問題或考慮 

Azure Machine Learning 符合 Microsoft Azure 豐富計畫的資格。 如需詳細資訊，請造訪  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) 。

## <a name="next-steps"></a>後續步驟

* [Azure Machine Learning 的企業安全性](concept-enterprise-security.md)