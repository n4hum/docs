---

 

copyright:

  years: 2016

 

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 建立及呼叫 {{site.data.keyword.openwhisk_short}} 動作
{: #openwhisk_actions}

前次更新：2016 年 9 月 9 日
{: .last-updated}

動作是在 {{site.data.keyword.openwhisk}} 平台上執行的無狀態程式碼 Snippet。動作可以是 JavaScript 函數、Swift 函數，或包裝在 Docker 容器中的自訂可執行程式。例如，動作可以用來偵測映像檔中的樣式、聚集一組 API 呼叫，或張貼推文。
{:shortdesc}

可以明確地呼叫動作，或為回應某事件而執行動作。在任一情況下，執行動作都會根據唯一啟動 ID 來識別啟動記錄。動作的輸入及動作的結果是鍵值組的字典，其中索引鍵是字串，而值是有效的 JSON 值。

動作是由其他動作的呼叫或定義的一連串動作所組成。

## 建立及呼叫 JavaScript 動作
{: #openwhisk_create_action_js}

下列各節會引導您透過 JavaScript 逐步執行動作。您可以開始建立及呼叫簡單動作。然後，您可以將參數新增至動作，並使用參數來呼叫該動作。接下來是設定並呼叫預設參數。然後，您可以建立非同步動作，最後使用動作序列。


### 建立及呼叫簡單 JavaScript 動作
{: #openwhisk_single_action_js}

請檢閱下列步驟及範例，以建立您的第一個 JavaScript 動作。

1. 建立含有下列內容的 JavaScript 檔。在此範例中，檔名是 'hello.js'。
  
  ```
function main() {return {payload: 'Hello world'};
  }
  ```
  {: codeblock}

  JavaScript 檔可能包含其他函數。不過，依慣例，必須要有稱為 `main` 的函數，以提供動作的進入點。

2. 從下列 JavaScript 函數建立動作。在此範例中，動作稱為 'hello'。

  ```
wsk action create hello hello.js
  ```
  {: pre}
  ```
ok: created action hello
  ```
  {: screen}

3. 列出您已建立的動作：
  
  ```
wsk action list
  ```
  {: pre}
  ```
actions
  hello       private
  ```
  {: screen}

  您可以看到剛剛建立的 `hello` 動作。

4. 建立動作之後，即可使用 'invoke' 指令透過 OpenWhisk 在雲端執行它。在指令中指定旗標，即可透過*封鎖* 呼叫（即要求/回應樣式）或*非封鎖* 呼叫來呼叫動作。封鎖呼叫要求將*等待* 啟動結果可供使用。等待期間小於 60 秒或動作的已配置[時間限制](./reference.md#per-action-timeout-ms-default-60s)。如果在等待期間內有啟動結果，則會予以傳回。否則，會在系統中繼續處理啟動，並傳回啟動 ID，讓您可以稍後檢查結果，這與非封鎖要求相同（如需監視啟動的提示，請參閱[這裡](#watching-action-output)）。

  這個範例使用封鎖參數 `--blocking`：

  ```
wsk action invoke --blocking hello
  ```
  {: pre}
  ```
ok: invoked hello with id 44794bd6aab74415b4e42a308d880e5b
  {"result": {
          "payload": "Hello world"
      },
      "status": "success",
      "success": true
  }
  ```
  {: screen}

  此指令輸出兩個重要的資訊部分：
  * 啟動 ID (`44794bd6aab74415b4e42a308d880e5b`)
  * 如果在預期的等待期間內有呼叫結果，則為呼叫結果

  在此情況下，結果是 JavaScript 函數所傳回的 `Hello world` 字串。啟動 ID 之後可以用來擷取日誌或呼叫結果。  

5. 如果您不是立即需要動作結果，則可以省略 `--blocking` 旗標，以進行非封鎖呼叫。您稍後可以透過使用啟動 ID 來取得結果。請參閱下列範例：

  ```
wsk action invoke hello
  ```
  {: pre}
  ```
ok: invoked hello with id 6bf1f670ee614a7eb5af3c9fde813043
  ```
  {: screen}

  ```
wsk activation result 6bf1f670ee614a7eb5af3c9fde813043
  ```
  {: pre}
  ```
  {
      "payload": "Hello world"
  }
  ```
  {: screen}

6. 如果您忘記記錄啟動 ID，則可以取得從最近到最舊排列的啟動清單。執行下列指令，以取得啟動的清單：

  ```
wsk activation list
  ```
  {: pre}
  ```
activations
  44794bd6aab74415b4e42a308d880e5b         hello
  6bf1f670ee614a7eb5af3c9fde813043         hello
  ```
  {: screen}

### 將參數傳遞給動作
{: #openwhisk_adding_parameters_js}

呼叫動作時，可以將參數傳遞給動作。

1. 在動作中使用參數。例如，使用下列內容來更新 'hello.js' 檔：
  
  ```
function main(params) {
     return {payload:  'Hello, ' + params.name + ' from ' + params.place};
  }
  ```
  {: codeblock}

  輸入參數被當作 `main` 函數的 JSON 物件參數來傳遞。請注意，在此範例中，`name` 及 `place` 參數擷取自 `params` 物件的方式。

2. 更新 `hello` 動作並呼叫動作，同時將 `name` 及 `place` 參數值傳遞給它。請參閱下列範例：
  
  ```
wsk action update hello hello.js
  ```
  {: pre}
  ```
wsk action invoke --blocking --result hello --param name 'Bernie' --param place 'Vermont'
  ```
  {: pre}
  ```
  {
      "payload": "Hello, Bernie from Vermont"
  }
  ```
  {: screen}

  請注意，`--param` 選項用來指定參數名稱及值，而 `--result` 選項用來只顯示呼叫結果。

### 設定預設參數
{: #openwhisk_binding_actions}

您可以使用多個具名參數來呼叫動作。請記住，前一個範例中的 `hello` 動作預期會有兩個參數：人員的名稱 (*name*) 及其所在位置 (*place*)。

您可以連結特定參數，而非每次都將所有參數傳遞給動作。下列範例會連結 *place* 參數，以將動作預設為 "Vermont" 這個位置：
 
1. 使用 `--param` 選項來連結參數值，以更新動作。

  ```
wsk action update hello --param place 'Vermont'
  ```
  {: pre}

2. 呼叫動作，但這次只傳遞 `name` 參數。

  ```
wsk action invoke --blocking --result hello --param name 'Bernie'
  ```
  {: pre}
  ```
  {
      "payload": "Hello, Bernie from Vermont"
  }
  ```
  {: screen}

  請注意，呼叫動作時，您不需要指定 place 參數。在呼叫時指定參數值，仍然可以改寫連結的參數。

3. 呼叫動作，並傳遞 `name` 及 `place` 值。後者會改寫連結至動作的值。

  ```
wsk action invoke --blocking --result hello --param name 'Bernie' --param place 'Washington, DC'
  ```
  {: pre}
  ```
  {  
      "payload": "Hello, Bernie from Washington, DC"
  }
  ```
  {: screen}

### 建立非同步動作
{: #openwhisk_asynchrony_js}

`main` 函數返回之後，非同步執行的 JavaScript 函數可能需要傳回啟動結果。您可以在動作中傳回 Promise 以達成此作業。

1. 將下列內容儲存至稱為 `asyncAction.js` 的檔案中。

  ```
  function main(args) {
       return new Promise(function(resolve, reject) {
         setTimeout(function() {
          resolve({ done: true });
         }, 2000);
      })
   }
  ```
  {: codeblock}

  請注意，`main` 函數會傳回 Promise，這表示啟動尚未完成，但預期未來可完成。

  在此情況下，`setTimeout()` JavaScript 函數會先等待 20 秒，再呼叫回呼函數。這會呈現非同步程式碼，並進入 Promise 的回呼函數內。

  Promise 的回呼接受兩個引數（resolve 及 reject），這兩個都是函數。`resolve()` 呼叫可滿足 Promise，並指出啟動正常完成。

  `reject()` 呼叫可以用來拒絕 Promise，並發出信號指出啟動異常完成。

2. 執行下列指令，以建立並呼叫動作：

  ```
wsk action create asyncAction asyncAction.js
  ```
  {: pre}
  ```
wsk action invoke --blocking --result asyncAction
  ```
  {: pre}
  ```
  {
      "done": true
  }
  ```
  {: screen}

  請注意，您已執行非同步動作的封鎖呼叫。

3. 提取啟動日誌，來查看啟動需要多久時間才能完成：

  ```
wsk activation list --limit 1 asyncAction
  ```
  {: pre}
  ```
activations
  b066ca51e68c4d3382df2d8033265db0             asyncAction
  ```
  {: screen}


  ```
wsk activation get b066ca51e68c4d3382df2d8033265db0
  ```
  {: pre}
 ```
  {
      "start": 1455881628103,
      "end":   1455881648126,
      ...
  }
  ```
  {: screen}

  比較啟動記錄中的 `start` 與 `end` 時間戳記，您可以發現完成此啟動所需的時間略高於兩秒。


### 使用動作來呼叫外部 API
{: #openwhisk_apicall_action}

到目前為止，這些範例已自行包含 JavaScript 函數。您也可以建立呼叫外部 API 的動作。

此範例會呼叫 Yahoo Weather 服務，以取得特定位置的現行狀況。 

1. 將下列內容儲存至稱為 `weather.js` 的檔案中。
  
  
  ```
var request = require('request');function main(params) {
     var location = params.location || 'Vermont';
        var url = 'https://query.yahooapis.com/v1/public/yql?q=select item.condition from weather.forecast where woeid in (select woeid from geo.places(1) where text="' + location + '")&format=json';
    
        return new Promise(function(resolve, reject) {
            request.get(url, function(error, response, body) {
            if (error) {
                    reject(error);    
                }
                else {
                    var condition = JSON.parse(body).query.results.channel.item.condition;
                    var text = condition.text;
                    var temperature = condition.temp;
                    var output = 'It is ' + temperature + ' degrees in ' + location + ' and ' + text;
                    resolve({msg: output});
                }
            });
      });
  }
  ```
  {: codeblock}
  
  請注意，此範例中的動作使用 JavaScript `request` 程式庫，對 Yahoo Weather API 發出 HTTP 要求，以及從 JSON 結果中擷取欄位。[參照](./reference.md#javascript-runtime-environments)詳述可在動作中使用的 Node.js 套件。
  
  此範例也會顯示需要非同步動作。此動作會傳回 Promise，指出在函數返回時還無法取得這個動作的結果。而是，在 HTTP 呼叫完成之後，會在 `request` 回呼中取得結果，並且它會作為 `resolve()` 函數的引數傳遞。
  
2. 執行下列指令，以建立並呼叫動作：
  
  ```
wsk action create weather weather.js
  ```
  {: pre}
  ```
wsk action invoke --blocking --result weather --param location 'Brooklyn, NY'
  ```
  {: pre}
  ```
  {
      "msg": "It is 28 degrees in Brooklyn, NY and Cloudy"
  }
  ```
  {: screen}
  
### 建立動作序列
{: #openwhisk_create_action_sequence}

您可以建立一個動作，以將一連串的動作鏈結在一起。

在稱為 `/whisk.system/utils` 的套件中提供數個公用程式動作，可用來建立第一個序列。您可以在[套件](./packages.md)小節中進一步瞭解套件。

1. 顯示 `/whisk.system/utils` 套件中的動作。
  
  ```
  wsk package get --summary /whisk.system/utils
  ```
  {: pre}
  ```
  package /whisk.system/utils: Building blocks that format and assemble data
   action /whisk.system/utils/head: Extract prefix of an array
   action /whisk.system/utils/split: Split a string into an array
   action /whisk.system/utils/sort: Sorts an array
   action /whisk.system/utils/echo: Returns the input
   action /whisk.system/utils/date: Current date and time
   action /whisk.system/utils/cat: Concatenates input into a string
  ```
  {: screen}

  在此範例中，您將使用 `split` 及 `sort` 動作。

2. 建立動作序列，以將某個動作的結果當作下一個動作的引數來傳遞。
  
  ```
  wsk action create sequenceAction --sequence /whisk.system/utils/split,/whisk.system/utils/sort
  ```
  {: pre}
  
  此動作序列會將數行文字轉換成一個陣列，並排序這些行。
  
3. 呼叫動作：
  
  ```
  wsk action invoke --blocking --result sequenceAction --param payload "Over-ripe sushi,\nThe Master\nIs full of regret."
  ```
  {: pre}
  ```
  {
      "length": 3,
      "lines": [
          "Is full of regret.",
          "Over-ripe sushi,",
          "The Master"
      ]
  }
  ```
  {: screen}
  
  在結果中，會排序這些行。

**附註**：序列中動作之間所傳遞的參數十分明確，但預設參數除外。
因此，傳遞給動作序列的參數僅適用於序列中的第一個動作。
序列中第一個動作的結果會變成序列中第二個動作的輸入 JSON 物件（以此類推）。
此物件不會包括一開始傳遞給序列的任何參數，除非第一個動作將它們明確地包括在結果中。
動作的輸入參數會與動作的預設參數合併，而前者的優先順序較高，並且會置換任何相符的預設參數。
如需使用多個具名參數來呼叫動作序列的相關資訊，請參閱[設定預設參數](./actions.md#setting-default-parameters)。

## 建立 Python 動作
{: #openwhisk_actions_python}

建立 Python 動作的程序，與建立 JavaScript 動作的程序類似。下列各節會引導您建立及呼叫單一 Python 動作，以及將參數新增至該動作。

### 建立及呼叫動作
{: #openwhisk_actions_python_invoke}

動作只是最上層的 Python 函數，這表示必須要有名為 `main` 的方法。例如，建立稱為 `hello.py` 且含有下列內容的檔案：

```
    def main(dict):
        name = dict.get("name", "stranger")
        greeting = "Hello " + name + "!"
        print(greeting)
        return {"greeting": greeting}
```
{: codeblock}

Python 動作一律會使用某個字典，並產生一個字典。

您可以從此函數建立稱為 `helloPython` 的 OpenWhisk 動作，如下所示：

```
wsk action create helloPython hello.py
```
{: pre}

使用指令行及 `.py` 原始檔時，不需要指定是在建立 Python 動作（與 JavaScript 動作相反）；工具是透過副檔名來判斷。

Python 動作與 JavaScript 動作的動作呼叫相同：

```
wsk action invoke --blocking --result helloPython --param name World
```
{: pre}

```
  {
      "greeting": "Hello World!"
  }
```
{: screen}


## 建立 Swift 動作
{: #openwhisk_actions_swift}

建立 Swift 動作的程序，與建立 JavaScript 動作的程序類似。下列各節會引導您建立及呼叫單一 Swift 動作，以及將參數新增至該動作。

您也可以使用線上 [Swift 沙盤推演](https://swiftlang.ng.bluemix.net)來測試 Swift 程式碼，而不需要在機器上安裝 Xcode。

### 建立及呼叫動作
{: #openwhisk_actions_invoke_swift}

動作只是最上層 Swift 函數。例如，建立稱為 `hello.swift` 且含有下列內容的檔案：

```
func main(args: [String:Any]) -> [String:Any] {if let name = args["name"] as? String {
          return [ "greeting" : "Hello \(name)!" ]
    } else {
return [ "greeting" : "Hello stranger!" ]
    }
}
```
{: codeblock}

Swift 動作一律會使用某個字典，並產生一個字典。

您可以從此函數建立稱為 `helloSwift` 的 {{site.data.keyword.openwhisk_short}} 動作，如下所示：

```
wsk action create helloSwift hello.swift
```
{: pre}

使用指令行及 `.swift` 原始檔時，不需要指定是在建立 Swift 動作（與 JavaScript 動作相反）；工具是透過副檔名來判斷。

Swift 動作與 JavaScript 動作的動作呼叫相同：

```
wsk action invoke --blocking --result helloSwift --param name World
```
{: pre}

```
  {
      "greeting": "Hello World!"
  }
```
{: screen}

**注意：**Swift 動作是在 Linux 環境中執行。Swift on Linux 仍在開發中，而且 {{site.data.keyword.openwhisk_short}} 通常會使用最新的可用版本，但此版本不一定是穩定的。此外，與 {{site.data.keyword.openwhisk_short}} 搭配使用的 Swift 版本，可能與 MacOS 上穩定 XCode 版本的 Swift 版本不一致。


## 建立 Docker 動作
{: #openwhisk_actions_docker}

使用 {{site.data.keyword.openwhisk_short}} Docker 動作，您可以使用任何語言來撰寫動作。

您的程式碼會編譯成可執行的二進位檔，並內嵌在 Docker 映像檔中。二進位程式與系統互動的方式是從 `stdin` 取得輸入，並透過 `stdout` 回覆。

先決條件是您必須具備 Docker Hub 帳戶。若要設定免費 Docker ID 及帳戶，請移至 [Docker Hub](https://hub.docker.com){: new_window}。

對於下面的指示，假設 Docker 使用者 ID 是 `janesmith`，而密碼是 `janes_password`。假設已設定 CLI，則需要三個步驟才能設定供 {{site.data.keyword.openwhisk_short}} 使用的自訂二進位檔。之後，上傳的 Docker 映像檔可以當作動作使用。

1. 下載 Docker 架構。您可以使用 CLI 進行下載，如下所示：

  ```
wsk sdk install docker
  ```
  {: pre}
  ```
Docker 架構現在安裝在現行目錄中。
  ```
  {: screen}

  ```
ls dockerSkeleton/
  ```
  {: pre}
  ```
  Dockerfile      README.md       buildAndPush.sh example.c
  ```
  {: screen}

  架構是一個 Docker 容器範本，您可以在其中以自訂二進位檔形式來注入程式碼。

2. 在 blackbox 架構中，設定您的自訂二進位檔。此架構已包含您可以使用的 C 程式。

  ```
  cat dockerSkeleton/example.c
  ```
  {: pre}
  ```
  #include <stdio.h>
  
  int main(int argc, char *argv[]) {
      printf("This is an example log message from an arbitrary C program!\n");
      printf("{ \"msg\": \"Hello from arbitrary C program!\", \"args\": %s }",
             (argc == 1) ? "undefined" : argv[1]);
  }
  ```
  {: codeblock}

  您可以視需要修改此檔案，或者將其他程式碼及相依關係新增至 Docker 映像檔。
如果是後者，您可能需要在必要時調整 `Dockerfile` 以建置執行檔。
二進位檔必須位在 `/action/exec` 的容器內。

  執行檔會從指令行接收到單一引數。它是代表動作引數之 JSON 物件的字串序列化。程式可能會記載至 `stdout` 或 `stderr`。
依照慣例，輸出的最後一行*必須* 是代表動作結果的字串化 JSON 物件。

3. 建置 Docker 映像檔，並使用提供的 Script 予以上傳。您必須先執行 `docker login` 進行鑑別，然後執行具有所選擇映像檔名稱的 Script。
  
  ```
docker login -u janesmith -p janes_password
  ```
  {: pre}
  ```
cd dockerSkeleton
  ```
  {: pre}
  ```
  chmod +x buildAndPush.sh
  ```
  {: pre}
  ```
./buildAndPush.sh janesmith/blackboxdemo
  ```
  {: pre}
  
  請注意，example.c 檔案的一部分會在 Docker 映像檔建置程序之中編譯，因此不需要在機器上編譯 C。實際上，除非您是在相容主機上編譯二進位檔，否則可能無法在容器內執行，因為格式不相符。
  
  Docker 容器現在可能已作為 {{site.data.keyword.openwhisk_short}} 動作使用。
  
  
  ```
wsk action create --docker example janesmith/blackboxdemo
  ```
  {: pre}
  
  請注意，建立動作時，應如何使用 `--docker`。目前所有 Docker 映像檔都假設是在 Docker Hub 上進行管理。
動作可能會呼叫為任何其他 {{site.data.keyword.openwhisk_short}} 動作。
  
  ```
wsk action invoke --blocking --result example --param payload Rey
  ```
  {: pre}
  ```
  {
      "args": {
          "payload": "Rey"
      },
      "msg": "Hello from arbitrary C program!"
  }
  ```
  {: screen}
  
  若要更新 Docker 動作，請執行 buildAndPush.sh 來重新整理 Docker Hub 上的映像檔，這可容許下次系統取回您的 Docker 映像檔來執行您動作的新程式碼。
如果沒有暖容器，任何新呼叫將會使用新的 Docker 映像檔。
請考慮，如果有暖容器使用舊版 Docker 映像檔，則除非您執行 wsk 動作更新，否則任何新呼叫都會繼續使用此映像檔，這指出針對任何新呼叫，系統都會強制 Docekr 在取回新的 Docker 映像檔時取回結果。
  
  ```
./buildAndPush.sh janesmith/blackboxdemo
  ```
  {: pre}
  ```
  wsk action update --docker example janesmith/blackboxdemo
  ```
  {: pre}
  
  您可以在[參照](./openwhisk_reference.html#openwhisk_ref_docker)小節找到建立 Docker 動作的相關資訊。

## 監看動作輸出
{: #openwhisk_actions_polling}

其他使用者可能會呼叫 {{site.data.keyword.openwhisk_short}} 動作來回應各種事件，或是作為動作序列的一部分。在這類情況下，監視呼叫可能十分有用。

您可以使用 {{site.data.keyword.openwhisk_short}} CLI 來監看所呼叫動作的輸出。

1. 從 Shell，發出下列指令：
  
  ```
wsk activation poll
  ```
  {: pre}

  此指令會啟動輪詢迴圈，以從啟動開始持續檢查日誌。

2. 切換至另一個視窗，然後呼叫動作：

  ```
wsk action invoke /whisk.system/samples/helloWorld --param payload Bob
  ```
  {: pre}
  ```
ok: invoked /whisk.system/samples/helloWorld with id 7331f9b9e2044d85afd219b12c0f1491
  ```
  {: screen}

3. 在輪詢視窗中，觀察啟動日誌：

  ```
  Activation: helloWorld (7331f9b9e2044d85afd219b12c0f1491)
    2016-02-11T16:46:56.842065025Z stdout: hello bob!
  ```
  {: screen}

  同樣地，只要執行輪詢公用程式，就可即時看到日誌中是否有任何代表您在 {{site.data.keyword.openwhisk_short}} 中執行的動作。

## 刪除動作
{: #openwhisk_delete_action}

刪除您不要使用的動作來進行清除。

1. 執行下列指令，以刪除動作：
  
  ```
wsk action delete hello
  ```
  {: pre}
  ```
ok: deleted hello
  ```
  {: screen}

2. 驗證動作不再出現於動作清單中。
  
  ```
wsk action list
  ```
  {: pre}
  ```
actions
  ```
  {: screen}
