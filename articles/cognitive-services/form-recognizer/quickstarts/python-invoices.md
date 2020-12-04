---
title: 快速入門：使用 Python 擷取發票資料 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用表單辨識器 REST API 和 Python 來擷取發票上的資料
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/12/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 7c6b5406728817c3dd700ec285d9af77c9d3cf60
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96009308"
---
# <a name="quickstart-extract-invoice-data-using-the-form-recognizer-rest-api-with-python"></a>快速入門：搭配使用表單辨識器 REST API 和 Python 來擷取發票資料

在本快速入門中，您將搭配使用 Azure 表單辨識器 REST API 與 Python 來擷取及識別發票上的相關資訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>必要條件

若要完成此快速入門，您必須：
- 已安裝 [Python](https://www.python.org/downloads/) (如果您想要在本機執行此範例)。
- 發票文件。 您可以使用本快速入門的[範例發票](../media/sample-invoice.jpg)。

> [!NOTE]
> 本快速入門會使用本機檔案。 若要改為使用透過 URL 存取的發票文件，請參閱[參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeInvoiceAsync)。

## <a name="create-a-form-recognizer-resource"></a>建立表單辨識器資源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-an-invoice"></a>分析發票

若要開始分析發票，請使用下列 Python 指令碼呼叫 **[分析發票](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** API。 執行指令碼之前，請進行下列變更：

1. 將 `<Endpoint>` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `<subscription key>` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 將 `<path to your invoice>` 取代為您已儲存範例發票的本機路徑。

    ```python
        ########### Python Form Recognizer Async Invoice #############
    
        import json
        import time
        from requests import get, post
        
        # Endpoint URL
        endpoint = r"<Endpoint>"
        apim_key = "<subscription key>"
        post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
        source = r"<path to your invoice>"
        
        headers = {
            # Request headers
            'Content-Type': '<file type>',
            'Ocp-Apim-Subscription-Key': apim_key,
        }
        
        params = {
            "includeTextDetails": True
            "locale": "en-US"
        }
        
        with open(source, "rb") as f:
            data_bytes = f.read()
        
        try:
            resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
            if resp.status_code != 202:
                print("POST analyze failed:\n%s" % resp.text)
                quit()
            print("POST analyze succeeded:\n%s" % resp.headers)
            get_url = resp.headers["operation-location"]
        except Exception as e:
            print("POST analyze failed:\n%s" % str(e))
            quit()
    ```

1. 將程式碼儲存在副檔名為 .py 的檔案中。 例如，「form-recognizer-invoice.py」。
1. 開啟 [命令提示字元] 視窗。
1. 出現提示時，使用 `python` 命令執行範例。 例如： `python form-recognizer-invoice.py` 。

您會收到包含 **Operation-Location** 標頭的 `202 (Success)` 回應，而指令碼會將其輸出至主控台。 此標頭包含可用來查詢非同步作業狀態並取得結果的作業識別碼。 在下列範例值中，`operations/` 之後的字串就是作業識別碼。

## <a name="get-the-invoice-results"></a>取得發票結果

呼叫 **分析發票** API 之後，您可以呼叫 **[取得分析發票結果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** API 來取得作業狀態並擷取資料。 將下列程式碼新增到 Python 指令碼底部。 這會在新的 API 呼叫中使用作業識別碼值。 此指令碼會定期呼叫 API，直到有結果為止。 我們建議的間隔為一秒以上。

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Invoice results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Invoice Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Invoice Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. 儲存指令碼。
1. 再次使用 `python` 命令執行範例。 例如： `python form-recognizer-invoice.py` 。

### <a name="examine-the-response"></a>檢查回應

指令碼會將回應輸出到主控台，直到 **分析發票** 作業完成。 接著，指令碼會將已擷取的文字資料以 JSON 格式輸出。 `"readResults"` 欄位會包含擷取自發票的每一行文字，`"pageResults"` 欄位會包含擷取自發票的資料表和選取標記，而 `"documentResults"` 會包含發票中最相關部分的索引鍵/值資訊。

請參閱下列發票文件和其對應的 JSON 輸出：

* [發票範例](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)
* [範例發票 JSON 輸出](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)


### <a name="sample-python-script-to-extract-invoice-or-a-batch-of-invoices-into-a-csv-file"></a>用來將發票或一批發票擷取至 CSV 檔案的範例 Python 指令碼

此範例 python 指令碼說明如何開始使用發票 API。 可以將單一發票當作參數或資料夾來搭配執行，並且會輸出 JSON 檔案 ".invoice.json" 和 CSV 檔案 _invoiceResutls.csv_，具有已擷取的值結果。 在資料夾上執行時，會在所有 "pdf"、"jpg"、"jpeg"、"png"、"bmp"、"tif"、"tiff" 檔案進行掃描，並且使用 API 來執行。 
 
```python
########### Python Form Recognizer Async Invoice #############

import json
import time
import os
import ntpath
import sys
from requests import get, post
import csv


def analyzeInvoice(filename):
    invoiceResultsFilename = filename + ".invoice.json"

    # do not run analyze if .invoice.json file is present on disk
    if os.path.isfile(invoiceResultsFilename):
        with open(invoiceResultsFilename) as json_file:
            return json.load(json_file)

    # Endpoint URL
    #endpoint = r"<Endpoint>"
    #apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
    headers = {
        # Request headers
        'Content-Type': 'application/octet-stream',
        'Ocp-Apim-Subscription-Key': apim_key,
    }

    params = {
        "includeTextDetails": True
    }

    with open(filename, "rb") as f:
        data_bytes = f.read()

    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            return None
        print("POST analyze succeeded: %s" % resp.headers["operation-location"])
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        return None

    n_tries = 50
    n_try = 0
    wait_sec = 6

    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
            resp_json = json.loads(resp.text)
            if resp.status_code != 200:
                print("GET Invoice results failed:\n%s" % resp_json)
                return None
            status = resp_json["status"]
            if status == "succeeded":
                print("Invoice analysis succeeded.")
                with open(invoiceResultsFilename, 'w') as outfile:
                    json.dump(resp_json, outfile, indent=4)
                return resp_json
            if status == "failed":
                print("Analysis failed:\n%s" % resp_json)
                return None
            # Analysis still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1     
        except Exception as e:
            msg = "GET analyze results failed:\n%s" % str(e)
            print(msg)
            return None

    return resp_json

def parseInvoiceResults(resp_json):
    docResults = resp_json["analyzeResult"]["documentResults"]
    invoiceResult = {}
    for docResult in docResults:
        for fieldName, fieldValue in sorted(docResult["fields"].items()):
            valueFields = list(filter(lambda item: ("value" in item[0]) and ("valueString" not in item[0]), fieldValue.items()))
            invoiceResult[fieldName] = fieldValue["text"]            
            if len(valueFields) == 1:
                print("{0:26} : {1:50}      NORMALIZED VALUE: {2}".format(fieldName , fieldValue["text"], valueFields[0][1]))
                invoiceResult[fieldName + "_normalized"] = valueFields[0][1]
            else:
                print("{0:26} : {1}".format(fieldName , fieldValue["text"]))
    print("")
    return invoiceResult

def main(argv):
    if (len(argv)  != 2):
        print("ERROR: Please provide invoice filename or root directory with invoice PDFs/images as an argument to the python script")
        return

    # list of invoice to analyze
    invoiceFiles = []
    csvPostfix = '-invoiceResults.csv'
    if os.path.isfile(argv[1]):
        # Single invoice
        invoiceFiles.append(argv[1])
        csvFileName = argv[1] + csvPostfix
    else:
        # Folder of invoices
        supportedExt = ['.pdf', '.jpg','.jpeg','.tif','.tiff','.png','.bmp']
        invoiceDirectory = argv[1]
        csvFileName = os.path.join(invoiceDirectory, os.path.basename(os.path.abspath(invoiceDirectory)) + csvPostfix)
        for root, directories, filenames in os.walk(invoiceDirectory):
            for invoiceFilename in filenames:
                ext = os.path.splitext(invoiceFilename)[-1].lower()
                if ext in supportedExt:
                    fullname = os.path.join(root, invoiceFilename)
                    invoiceFiles.append(fullname)
                    
    with open(csvFileName, mode='w', newline='\n', encoding='utf-8') as csv_file:
        fieldnames = ['Filename',
                      'FullFilename','InvoiceTotal','InvoiceTotal_normalized','AmountDue','AmountDue_normalized','SubTotal','SubTotal_normalized','TotalTax','TotalTax_normalized','CustomerName','VendorName',
                      'InvoiceId','CustomerId','PurchaseOrder','InvoiceDate','InvoiceDate_normalized','DueDate','DueDate_normalized',
                      'VendorAddress','VendorAddressRecipient','BillingAddress','BillingAddressRecipient','ShippingAddress','ShippingAddressRecipient','CustomerAddress','CustomerAddressRecipient','ServiceAddress','ServiceAddressRecipient','RemittanceAddress','RemittanceAddressRecipient', 'ServiceStartDate','ServiceStartDate_normalized','ServiceEndDate','ServiceEndDate_normalized','PreviousUnpaidBalance','PreviousUnpaidBalance_normalized']
        writer = csv.DictWriter(csv_file, fieldnames=fieldnames)
        writer.writeheader()
        counter = 0
        for invoiceFullFilename in invoiceFiles:
            counter = counter + 1
            invoiceFilename = ntpath.basename(invoiceFullFilename)
            print("----- Processing {0}/{1} : {2} -----".format(counter, len(invoiceFiles),invoiceFullFilename))
            
            resp_json = analyzeInvoice(invoiceFullFilename)

            if (resp_json is not None):
                invoiceResults = parseInvoiceResults(resp_json)
                invoiceResults["FullFilename"] = invoiceFullFilename
                invoiceResults["Filename"] = invoiceFilename
                writer.writerow(invoiceResults)
                    
if __name__ == '__main__':
    main(sys.argv)
```

1. 將程式碼儲存在副檔名為 .py 的檔案中。 例如，「form-recognizer-invoice-to-csv.py」。
1. 將 `<Endpoint>` 取代為您使用表單辨識器訂用帳戶取得的端點。
1. 將 `<subscription key>` 取代為您在先前的步驟中複製的訂用帳戶金鑰。
1. 開啟 [命令提示字元] 視窗。
1. 出現提示時，使用 `python` 命令執行範例。 例如，`python form-recognizer-invoice.py {file name or folder name}` Python 指令碼可以將單一發票或資料夾當作參數來搭配執行，並且會將 JSON 檔案 ".invoice.json" 和擷取自發票的值輸出至 CSV 檔案 "-invoiceResults.csv"，並產生結果。 在資料夾上執行時，會在所有 "pdf"、"jpg"、"jpeg"、"png"、"bmp"、"tif"、"tiff" 檔案進行掃描，並且使用 API 來執行。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用表單辨識器 REST API 搭配 Python 來擷取發票上的內容。 接下來，請參閱參考文件來深入探索表單辨識器 API。

> [!div class="nextstepaction"]
> [REST API 參考文件](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeInvoiceAsync)

   
