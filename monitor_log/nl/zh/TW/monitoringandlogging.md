---

copyright:
  years: 2015, 2016

---


{:shortdesc: .shortdesc} 
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:screen: .screen}

#監視及記載
{: #monitoringandlogging}

前次更新：2016 年 9 月 2 日
{: .last-updated}

監視應用程式以及檢閱日誌，即可遵循應用程式執行及資料流程，以深入瞭解部署。此外，您還可以減少找到任何問題並進行修復所需的時間及工作量。
{:shortdesc}

{{site.data.keyword.Bluemix}} 應用程式可以是廣泛分散在各處的多實例應用程式，而且您的應用程式執行及其資料可以在許多服務之間共用。在此複雜環境中，監視您的應用程式以及檢閱日誌對管理應用程式而言十分重要。

##監視及記載 Cloud Foundry 應用程式
{: #monitoring_logging_bluemix_apps}

{{site.data.keyword.Bluemix_notm}} 具有內建記載機制，可產生執行中應用程式的日誌檔。在日誌中，您可以檢視針對應用程式所產生的錯誤、警告及參考訊息。此外，您也可以配置應用程式將日誌訊息寫入日誌檔中。如需日誌格式及日誌檢視方式的相關資訊，請參閱 [Cloud Foundry 上執行之應用程式的記載](#logging_for_bluemix_apps)。

監視應用程式可讓您查看及控制應用程式部署。運用監視，您可以完成下列作業：

* 收集並監視應用程式實例的效能資訊，以及檢查它們是否正常運作。
* 瞭解應用程式作業（例如，偵測潛在瓶頸或需要升級的時機）。
* 估計資源用量與費用。

為了讓您的部署在 {{site.data.keyword.Bluemix_notm}} 平台上能穩定運作，您會想要迅速地偵測問題，並且有效率地判定原因。若要達成此目標，請在設計應用程式時隨時謹記疑難排解，並在將應用程式部署至 {{site.data.keyword.Bluemix_notm}} 時使用進行監視及記載的服務或工具。

###監視 Cloud Foundry 上執行的應用程式
{: #monitoring_bluemix_apps}

當您使用 Cloud Foundry 基礎架構在 {{site.data.keyword.Bluemix_notm}} 上執行應用程式時，會想要掌握最新的效能資訊（例如性能狀態、資源用量及資料流量度量值）。運用此效能資訊，您便可以相應地進行決策或採取動作。

若要監視 {{site.data.keyword.Bluemix_notm}} 應用程式，請使用下列其中一種方法：

* {{site.data.keyword.Bluemix_notm}} 服務。Monitoring and Analytics 提供一種服務，可用來監視應用程式效能。此外，此服務也提供分析特性（例如日誌分析）。如需相關資訊，請參閱 [Monitoring and Analytics](../services/monana/index.html)。
* 協力廠商選項。例如，[New Relic](http://newrelic.com/){:new_window}。

###Cloud Foundry 上執行之應用程式的記載
{: #logging_for_bluemix_apps}

當您使用 Cloud Foundry 基礎架構在 {{site.data.keyword.Bluemix_notm}} 上執行應用程式時，會自動建立日誌檔。當您在從部署到運行環境的任何階段中發生錯誤時，可以檢查日誌以尋找可能有助於解決問題的線索。

###日誌保留
{: #log_retention}

* 在 Bluemix Cloud Foundry 應用程式中，日誌資料預設會儲存 30 天。

<!-- 2016.1.27: original shortdes: Log files are automatically created when you are using the Cloud Foundry infrastructure to run your apps on {{site.data.keyword.Bluemix_notm}}. You can view logs from the {{site.data.keyword.Bluemix_notm}} Dashboard, the cf command line interface, or external hosts. You can also filter the logs to see the parts that you are interested in. -->



###日誌格式
{: #log_format}

{{site.data.keyword.Bluemix_notm}} 應用程式的日誌會以固定格式顯示，與下列型樣類似：

```
1         2         3         4         5
12345678901234567890123456789012345678901234567890
--------------------------------------------------
yyyy-MM-ddTHH:mm:ss:SS-0500 [App/0]      OUT <message>
```
{:screen}

每個日誌項目都包含四個欄位。如需每一個欄位的簡要說明，請參閱下列清單：

<dl>
<dt><strong>時間戳記</strong></dt>
<dd>
<pre class="pre screen"><code>yyyy-MM-ddTHH:mm:ss:SS-0500</code></pre>
<p>直欄：1 - 27</p>
<p>日誌陳述文字的時間。時間戳記最多定義到毫秒。</p>
</dd>

<dt><strong>元件</strong></dt>
<dd>
<pre class="pre screen"><code>[App/0]</code></pre>
<p>直欄：29 - 40</p>
<p>產生日誌的元件。下列清單提供所有元件的定義：</p>

<dl>
<dt><strong>APP</strong></dt>
<dd>應用程式。元件 APP 的後面接著一個斜線及一個數字，用來表示應用程式實例。其中 0 是第一個實例、1 是第二個，依此類推。</dd>

<dt><strong>API</strong></dt>
<dd>Cloud Foundry API。</dd>

<dt><strong>DEA</strong></dt>
<dd>Droplet Execution Agent。</dd>

<dt><strong>LGR</strong></dt>
<dd>Loggregator。</dd>

<dt><strong>RTR</strong></dt>
<dd>路由器。</dd>

<dt><strong>STG</strong></dt>
<dd>編譯打包。</dd>
</dl>
</dd>

<dt><strong>串流</strong></dt>
<dd>
<pre class="pre screen"><code>OUT</code></pre>
<p>直欄：42 - 44</p>
<p>日誌訊息寫入其中的串流。<samp class="ph codeph">OUT</samp> 指的是 <samp class="ph codeph">stdout</samp> 串流，而 <samp class="ph codeph">ERR</samp> 指的是 <samp class="ph codeph">stderr</samp> 串流。</p>
</dd>

<dt><strong>訊息</strong></dt>
<dd>
<pre class="pre screen"><code>&lt;<var class="keyword varname">Message</var>&gt;</code></pre>
<p>直欄：46 - 行尾</p>
<p>元件所發出的訊息。訊息根據環境定義而不同。</p>
</dd>

</dl>

###檢視日誌
{: #viewing_logs}

您可以在三個位置中檢視 Cloud Foundry 應用程式的日誌：

  * [{{site.data.keyword.Bluemix_notm}} 儀表板](#viewing_logs_UI){:new_window}
  * [指令行介面](#viewing_logs_cli){:new_window}
  * [外部日誌主機](#thirdparty_logging){:new_window}

#### 從 {{site.data.keyword.Bluemix_notm}} 儀表板檢視日誌
{: #viewing_logs_UI}

若要查看部署或運行環境日誌，請完成下列步驟：
1. 登入 {{site.data.keyword.Bluemix_notm}}，然後在「儀表板」按一下您的應用程式磚。即會顯示「應用程式詳細資料」頁面。
2. 在導覽列中，按一下**日誌**。

在**日誌**主控台中，您可以檢視應用程式的最新日誌，或即時讀取日誌尾端的內容。此外，您還可以依日誌類型及通道來過濾日誌。

**附註：**在應用程式當機與部署之間，不會持續保存日誌。



#### 從指令行介面檢視日誌
{: #viewing_logs_cli}

從下列選項中選擇，以從指令行介面檢視日誌：

<ul>
<li>當您部署應用程式時，讀取日誌尾端的內容。
<p>當您將應用程式部署至 {{site.data.keyword.Bluemix_notm}} 時，請使用 **cf logs** 指令來顯示來自應用程式以及來自與應用程式互動之系統元件的日誌。您可以在 cf 指令行介面中鍵入下列指令。如需 cf logs 的相關資訊，請參閱 <a href="http://docs.cloudfoundry.org/devguide/deploy-apps/streaming-logs.html" target="_blank">Cloud Foundry 中的日誌類型及其訊息</a>。</p>
<dl>
<dt><strong>cf logs <var class="keyword varname">appname</var> --recent</strong></dt>
<dd>以最新到過去的順序顯示日誌。</dd>

<dt><strong>cf logs <var class="keyword varname">appname</var></strong></dt>
<dd>顯示從執行此指令的時間點開始所產生的日誌。</dd>
</dl>
<div class="note tip"><span class="tiptitle">提示：</span>當您在某個指令行視窗中執行 <span class="keyword cmdname">cf push</span> 或 <span class="keyword cmdname">cf start</span> 指令時，即可在另一個指令行視窗中輸入 <samp class="ph codeph">cf logs appname --recent</samp>，以即時查看日誌。</div>
</li>

<li>在部署應用程式之後檢視日誌。

<p>啟用應用程式記載時，如果您的應用程式在執行時發生問題，則可以檢視下列應用程式日誌。應用程式日誌有助於判定錯誤原因。</p>

<dl><strong>buildpack.log</strong></dt>
<dd>
<p>此日誌檔會記錄精細的參考資訊事件，以進行除錯。您可以使用此日誌，對建置套件執行問題進行疑難排解。</p>
<p>若要在 <span class="ph filepath">buildpack.log</span> 檔案中產生資料，您必須使用下列指令來啟用建置套件追蹤：
   <pre class="pre">cf set-env <var class="keyword varname">appname</var> JBP_LOG_LEVEL DEBUG</pre>
<p>
<p>若要檢視此日誌，請輸入下列指令：
<pre class="pre">cf files <var class="keyword varname">appname</var> app/.buildpack-diagnostics/buildpack.log</pre>
</p>
</dd>

<dt><strong>staging_task.log</strong></dt>
<dd><p>此日誌檔會在編譯打包作業的主要步驟之後記錄訊息。您可以使用此日誌，對編譯打包問題進行疑難排解。</p>
<p>若要檢視此日誌，請輸入下列指令：
<pre class="pre">cf files <var class="keyword varname">appname</var> logs/staging_task.log</pre>
</p>
</dd>
</dl>
</li></ul>


**附註：**如需如何啟用應用程式記載的相關資訊，請參閱[針對運行環境錯誤進行除錯](../debug/index.html#debugging-runtime-errors)。




###過濾日誌
{: #filtering_logs}

若要檢視感興趣的日誌，或排除不想要檢視的內容，則可以在 cf 指令行介面中搭配使用 **cf logs** 指令與過濾選項（例如 **cut** 及 **grep**）。

* 若要檢視一部分，而非完整詳細日誌，請使用 **cut** 選項。例如，若要顯示元件及訊息資訊，請使用下列指令：
```
cf logs appname --recent | cut -c 29-40,46- 
```

如需 **grep** 選項的相關資訊，請鍵入 cut --help。
* 若要顯示包含特定關鍵字的日誌項目，請使用 **grep** 選項。例如，若要顯示包含關鍵字 `[APP` 的日誌項目，您可以使用下列指令：

```
cf logs appname --recent | grep '\[App'
```
如需 **grep** 選項的相關資訊，請鍵入 `grep --help`。



### 配置外部日誌主機
{: #thirdparty_logging}

{{site.data.keyword.Bluemix_notm}} 在記憶體中保留了有限的日誌資訊量。
在記載資訊時，舊資訊會取代為較新的資訊。
若要保留所有日誌資訊，您可以將日誌儲存到外部日誌主機（例如協力廠商日誌管理服務或其他主機）。

若要將日誌從您的應用程式及系統串流到外部日誌主機，請完成下列步驟：

  1. 決定記載端點。 
     
	 您可以將日誌傳送給協力廠商日誌聚集器（例如 Papertrail、Splunk 或 Sumologic）。您也可以將日誌傳送給 syslog 主機、使用 TLS（傳輸層安全）加密的 syslog 主機，或 HTTPS POST 端點。不同日誌主機取得記載端點的方法會不同。

  2. 建立使用者提供的服務實例。
     
	 使用 `cf create-user-provided-service` 指令（或 `cups`，這是此指令的簡短版本），以建立使用者提供的服務實例： 
	 ```
	 cf create-user-provided-service <service_name> -l <logging_endpoint>
```
	 &lt;service_name&gt;
	 
	 使用者提供的服務實例的名稱。
	 
	 &lt;logging_endpoint&gt;
	 
	 {{site.data.keyword.Bluemix_notm}} 將日誌傳送至其中的記載端點。請參閱下表，以將 *logging_endpoint* 取代為您的值：
	 
	 <table>
     <thead>
     <tr>
     <th>記載端點</th>
     <th>指令</th>
	 <th>注意事項</th>
     </tr>
     </thead>
     <tbody>
     <tr>
     <td>syslog 主機</td>
     <td>`cf cups my-logs -l syslog://HOST:PORT`</td>
	 <td>例如，若要啟用記載至 Papertrail 的功能，請鍵入 `cf cups my-logs -l syslog://<papertrail-url>`。請將 `<papertrail-url>` 取代為來自 Papertrail 的記載端點的 URL。</td>
     </tr>
	 <tr>
     <td>syslog-tls 主機</td>
     <td>`cf cups my-logs -l syslog-tls://HOST:PORT`</td>
	 <td>憑證必須受憑證管理中心信任。請不要使用自簽憑證。</td>
     </tr>
	 <tr>
     <td>HTTPS POST</td>
     <td>`cf cups my-logs -l https://HOST:PORT`</td>
	 <td>此端點必須位在公用網際網路上，並且可供 {{site.data.keyword.Bluemix_notm}} 存取</td>
     </tr>
     </tbody>
     </table>	
  3. 將服務實例連結至您的應用程式。

	 使用下列指令，將服務實例連結至您的應用程式： 
	
	 ```
	 cf bind-service <appname> <service_name>
	 ```
	 &lt;appname&gt;
	 
	 應用程式的名稱。
	 
	 &lt;service_name&gt;
	 
	 使用者提供的服務實例的名稱。
	 
  4. 重新編譯打包應用程式。
     鍵入 `cf restage appname`，讓變更生效。 

#### 從外部主機檢視日誌
{: #viewing_logs_external}

	 
產生日誌時，在短暫延遲之後，您可以在外部日誌主機中檢視訊息，訊息與從 {{site.data.keyword.Bluemix_notm}} 使用者介面或 cf 指令行介面檢視的訊息類似。如果您的應用程式有多個實例，則會聚集日誌，而且您可以查看您應用程式的所有日誌。此外，在應用程式當機與部署之間，會持續保存日誌。

**附註：**您在指令行介面中檢視的日誌並非 syslog 格式，因此可能不完全符合外部日誌主機中顯示的訊息。 

### 範例：將 Cloud Foundry 應用程式日誌串流至 Splunk 
{: #splunk}

在此範例中，一位名叫 Jane 的開發人員使用 IBM Virtual Servers 測試版和 Ubuntu 映像檔來建立虛擬伺服器。Jane 嘗試將 Cloud Foundry 應用程式日誌從 {{site.data.keyword.Bluemix_notm}} 串流至 Splunk。 

  1. 一開始，Jane 先設定 Splunk。

     a. Jane 從[下載 Splunk Light 網站](https://www.splunk.com/en_us/download/splunk-light.html){:new_window}下載 Splunk Light，然後使用下列指令加以安裝。軟體安裝在 */opt/splunk* 中。 
       
	    ```
dpkg -i  ~/splunklight-6.3.0-aa7d4b1ccb80-linux-2.6-amd64.deb
```
	   
     b. Jane 安裝並修補 RFC5424 syslog 技術附加程式，以便與 {{site.data.keyword.Bluemix_notm}} 整合。如需附加程式安裝指示的相關資訊，請參閱 [Cloud Foundry 準則](https://docs.cloudfoundry.org/devguide/services/integrate-splunk.html){:new_window}。  

	    Jane 使用下列指令來安裝附加程式：
        
	    ```
        cd /opt/splunk/etc/apps
        tar xvfz ~/rfc5424-syslog_11.tgz
```
	   
        然後，Jane 修補附加程式，作法是以包含下列文字的新 *transforms.conf* 檔案來取代 */opt/splunk/etc/apps/rfc5424/default/transforms.conf*：
	   
	    ```
        [rfc5424_host]
        DEST_KEY = MetaData:Host
        REGEX = <\d+>\d{1}\s{1}\S+\s{1}(\S+)
        FORMAT = host::$1[rfc5424_header]
        REGEX = <(\d+)>\d{1}\s{1}\S+\s{1}\S+\s{1}(\S+)\s{1}(\S+)\s{1}(\S+)
        FORMAT = prival::$1 appname::$2 procid::$3 msgid::$4
        MV_ADD = true
```
        {:screen}	   

     c. Splunk 設定好之後，Jane 必須開啟 Ubuntu 機器上的某些埠來接受送入的 syslog drain（埠 5140）和 Splunk Web 使用者介面（埠 8000），因為 {{site.data.keyword.Bluemix_notm}} 虛擬伺服器依預設已設定防火牆。
	   
	    **附註：**iptable 配置在這裡完成，以供 Jane 進行評估，這是暫時配置。若要在正式作業環境的 Bluemix 虛擬伺服器中配置防火牆設定，請參閱 [Network Security Groups](https://new-console.ng.bluemix.net/docs/services/networksecuritygroups/index.html){:new_window} 文件，以取得詳細資訊。
	 
	   ```
	      iptables -A INPUT -p tcp --dport 5140 -j ACCEPT
       iptables -A INPUT -p tcp --sport 5140 -j ACCEPT
       iptables -A INPUT -p tcp --dport 8000 -j ACCEPT
       iptables -A INPUT -p tcp --sport 8000 -j ACCEPT
```
	   {:screen}	
	  
	   然後，Jane 使用下列指令來執行 Splunk：

       ```
	   /opt/splunk/bin/splunk start --accept-license
```
		
  2. Jane 配置 Splunk 設定，以從 {{site.data.keyword.Bluemix_notm}} 接受 syslog drain。她必須為 syslog drain 建立資料輸入。

     a. 從 Splunk Web 介面中，Jane 按一下**資料 > 資料輸入**。畫面上會顯示 Splunk 支援的輸入類型清單。 
	 
     b. 她選取 **TCP**，因為 syslog drain 使用 TCP 通訊協定。
	 
     c. 她在 **TCP** 窗格的**埠**欄位中輸入 **5140**，其餘欄位保留空白，然後按**下一步**。
	 
     d. 她從**來源類型**清單中選取**未分類 > rfc5424_syslog**。
	 
     e. 對於**方法**類型，她選取 **IP**。
	 
     f. Jane 在**索引**欄位中按一下**建立新的索引**。她將新索引命名為 "bluemix"，然後按一下**儲存**。
	 
     g. 最後，Jane 在**檢閱**視窗中確認設定都正確，然後按一下**提交**，以啟用此資料輸入。

  3. Jane 在 {{site.data.keyword.Bluemix_notm}} 中建立 syslog drain 服務，並將該服務連結至應用程式。

     a. Jane 從 cf CLI 使用下列指令來建立 syslog drain 服務：
	 
     ```
cf cups splunk -l syslog://dummyhost:5140
```
        
     **附註：***dummyhost* 不是實際名稱。它是用來隱藏實際的主機名稱。 

     b. Jane 將 syslog drain 服務連結至其空間中的應用程式，然後重新編譯打包該應用程式。
	 
	 ```
     cf bind-service myapp splunk
     cf restage myapp
```
		

Jane 試用她的應用程式，然後在 Splunk Web 介面中鍵入下列查詢字串：

```
source="tcp:5140" index="bluemix" sourcetype="rfc5424_syslog"
```

Jane 在她的 Splunk Web 介面中看到一連串的日誌。雖然 Jane 安裝的 Splunk 是 Splunk Light，但她一天還是可以保留 500MB 的日誌。 




