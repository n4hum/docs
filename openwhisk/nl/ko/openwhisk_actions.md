---

 

copyright:

  years: 2016

 

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.openwhisk_short}} 조치 작성 및 호출
{: #openwhisk_actions}

마지막 업데이트 날짜: 2016년 9월 9일
{: .last-updated}

조치는 {{site.data.keyword.openwhisk}} 플랫폼에서 실행되는 Stateless 코드 스니펫입니다. 조치는 JavaScript 함수, Swift 함수 또는 Docker 컨테이너에 패키지화된 사용자 정의 실행 가능 프로그램일 수 있습니다. 예를 들어, 이미지에서 얼굴을 발견하거나 일련의 API 호출을 집계하거나 트윗을 게시하기 위해 조치를 사용할 수 있습니다.
{:shortdesc}

조치는 명시적으로 호출되거나 이벤트에 대한 응답으로 실행될 수 있습니다. 어느 경우에나 조치 실행의 결과는 고유 활성화 ID에 의해 식별된 활성화 레코드입니다. 조치에 대한 입력 및 조치의 결과는 키-값 쌍의 사전입니다. 이 때, 키는 문자열이며 값은 유효한 JSON 값입니다.

조치는 다른 조치에 대한 호출 또는 정의된 조치 순서로 구성될 수 있습니다.

## JavaScript 조치 작성
{: #openwhisk_create_action_js}

다음 절에서는 JavaScript에서 조치에 대해 작업하는 방법을 안내합니다. 단순 조치의 작성과 호출로 시작합니다. 그런 다음 조치에 매개변수를 추가하고 매개변수가 있는 해당 조치를 호출합니다. 다음으로 기본 매개변수를 설정하고 이를 호출한 후 비동기 조치를 작성하고 마지막으로 조치 시퀀스에 대한 작업을 수행합니다. 


### 단순 JavaScript 조치 작성 및 호출
{: #openwhisk_single_action_js}

다음 단계 및 예를 검토하여 첫 번째 JavaScript 조치를 작성하십시오.

1. 다음 컨텐츠를 사용하여 JavaScript 파일을 작성하십시오. 이 예에서 파일 이름은 'hello.js'입니다.
  
  ```
function main() {      return {payload: 'Hello world'};
  }
  ```
  {: codeblock}

  JavaScript 파일은 추가 기능을 포함할 수 있습니다. 그러나 편의상 `main`을 호출하는 함수는 조치에 대한 시작점을 제공하기 위해 존재해야 합니다.

2. 다음 JavaScript 함수에서 조치를 작성하십시오. 이 예에서 조치를 'hello'라고 합니다.

  ```
wsk action create hello hello.js
  ```
  {: pre}
  ```
ok: created action hello
  ```
  {: screen}

3. 작성한 조치 나열:
  
  ```
wsk action list
  ```
  {: pre}
  ```
actions
  hello       private
  ```
  {: screen}

  방금 작성한 `hello` 조치를 볼 수 있습니다.

4. 조치를 작성한 후에 'invoke' 명령을 사용하여 OpenWhisk의 클라우드 내에서 이를 실행할 수 있습니다. 명령에 플래그를 지정하여 *블로킹* 호출(즉, 요청/응답 스타일) 또는 *비블로킹* 호출로 조치를 호출할 수 있습니다. 블로킹 호출 요청은 활성화 결과를 사용할 수 있을 때까지 *대기*합니다. 대기 기간은 조치의 구성된 [시간 한계](./reference.md#per-action-timeout-ms-default-60s) 또는 60초 미만입니다. 대기 기간 내에 활성화 결과를 사용할 수 있는 경우 활성화 결과가 리턴됩니다. 그렇지 않은 경우, 비블로킹 요청과 마찬가지로 시스템에서 활성화 처리가 계속되고 나중에 결과를 확인할 수 있도록 활성화 ID가 리턴됩니다(활성화 모니터링에 대한 팁은 [여기](#watching-action-output) 참조). 

  이 예에서는 블로킹 매개변수인 `--blocking`을 사용합니다. 

  ```
wsk action invoke --blocking hello
  ```
  {: pre}
  ```
ok: invoked hello with id 44794bd6aab74415b4e42a308d880e5b
  {      "result": {
          "payload": "Hello world"
      },
      "status": "success",
      "success": true
  }
  ```
  {: screen}

  명령은 두 가지 중요한 정보 조각을 출력합니다.
  * 활성화 ID(`44794bd6aab74415b4e42a308d880e5b`)
  * 예상 대기 기간 내에 사용 가능한 경우 호출 결과

  이 경우의 결과는 JavaScript 함수에 의해 리턴되는 `Hello world` 문자열입니다. 활성화 ID는 나중에 로그 또는 활성화의 결과를 검색하는 데 사용될 수 있습니다.  

5. 지금 당장 조치 결과가 필요하지 않은 경우, `--blocking` 플래그를 생략하여 비블로킹 호출을 작성할 수 있습니다. 나중에 활성화 ID를 사용하여 결과를 얻을 수 있습니다. 다음 예를 참조하십시오.

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

6. 활성화 ID를 기록하는 것을 잊은 경우, 가장 최신에서 가장 오래된 순서로 정렬된 활성화 목록을 얻을 수 있습니다. 다음 명령을 실행하여 활성화 목록을 얻으십시오.

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

### 조치에 매개변수 전달
{: #openwhisk_adding_parameters_js}

매개변수는 호출될 때 조치에 전달될 수 있습니다.

1. 조치에 매개변수를 사용하십시오. 예를 들어, 다음 컨텐츠로 'hello.js' 파일을 업데이트하십시오.
  
  ```
function main(params) {
     return {payload:  'Hello, ' + params.name + ' from ' + params.place};
  }
  ```
  {: codeblock}

  입력 매개변수가 JSON 오브젝트 매개변수로 `main` 함수에 전달됩니다. 이 예에서 `name` 및 `place` 매개변수가 `params` 오브젝트에서 검색됩니다.

2. `name` 및 `place` 매개변수값을 전달하면서 `hello` 조치를 업데이트하고 조치를 호출하십시오. 다음 예를 참조하십시오.
  
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

  `--param` 옵션을 사용하여 매개변수 이름 및 값을 지정하고 `--result` 옵션을 사용하여 호출 결과만 표시하는 방법에 주의하십시오.

### 기본 매개변수 설정
{: #openwhisk_binding_actions}

다중으로 이름 지정된 매개변수를 사용하여 조치를 호출할 수 있습니다. 이전 예에서 `hello` 조치를 호출하려면 두 개의 매개변수, 즉, 개인의 *name* 및 출신 *place*가 필요합니다.

모든 매개변수를 매번 조치에 전달하기보다 특정 매개변수를 바인딩할 수 있습니다. 다음은 조치가 "Vermont"라는 위치를 기본값으로 사용하도록 *place* 매개변수를 바인딩하는 예입니다.
 
1. `--param` 옵션을 사용하여 매개변수값을 바인딩하도록 조치를 업데이트하십시오. 

  ```
wsk action update hello --param place 'Vermont'
  ```
  {: pre}

2. 이번에는 `name` 매개변수만 전달하여 조치를 호출하십시오. 

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

  조치를 호출할 때 장소 매개변수를 지정할 필요가 없습니다. 호출 시에 매개변수값을 지정하여 바인딩된 매개변수를 겹쳐쓸 수 있습니다.

3. `name` 및 `place` 값을 둘 다 전달하여 조치를 호출하십시오. 나중에 조치에 바인딩된 값을 겹쳐씁니다. 

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

### 비동기 조치 작성
{: #openwhisk_asynchrony_js}

비동기로 실행되는 JavaScript 함수는 `main` 함수가 리턴한 후 활성화 결과를 리턴해야 하는 경우가 있습니다. 조치에서 Promise를 리턴하여 이를 수행할 수 있습니다. 

1. `asyncAction.js`라는 파일에 다음 컨텐츠를 저장하십시오.

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

  `main` 함수가 Promise를 리턴하며 이는 활성화가 아직 완료되지 않았지만 나중에 완료될 것으로 예상됨을 표시합니다. 

  이 경우에 `setTimeout()` JavaScript 함수는 콜백 함수를 호출하기 전에 20초 동안 대기합니다. 이는 비동기 코드를 나타내며 Promise의 콜백 함수 내부에 들어갑니다. 

  Promise의 콜백에는 resolve와 reject라는 두 개의 인수가 사용되며 이는 둘 다 함수입니다. `resolve()`를 호출하면 Promise를 이행하고 활성화가 정상적으로 완료되었음을 표시합니다. 

  `reject()`를 호출하여 Promise를 거부하고 활성화가 비정상적으로 완료되었음을 표시할 수 있습니다. 

2. 다음 명령을 실행하여 조치를 작성하고 호출하십시오.

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

  비동기 조치의 블로킹 호출이 수행되었습니다.

3. 활성화 완료에 걸린 시간을 보려면 활성화 로그를 페치하십시오.

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

  활성화 레코드에서 `start` 시간소인과 `end` 시간소인을 비교하면 이 활성화를 완료하는 데 20초가 약간 넘는 시간이 걸렸음을 알 수 있습니다. 


### 외부 API를 호출하기 위해 조치 사용
{: #openwhisk_apicall_action}

지금까지는 예에 자체 포함된 JavaScript 함수가 있었습니다. 외부 API를 호출하는 조치도 작성할 수 있습니다.

이 예에서는 Yahoo 날씨 서비스를 호출하여 특정 위치에서의 현재 상태를 얻습니다. 

1. `weather.js`라는 파일에 다음 컨텐츠를 저장하십시오.
  
  
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
  
  예에서 조치가 JavaScript `request` 라이브러리를 사용하여 Yahoo Weather API에 대한 HTTP 요청을 작성하고 JSON 결과에서 필드를 추출합니다. [참조](./reference.md#javascript-runtime-environments)에서는 사용자의 조치에서 사용할 수 있는 Node.js 패키지에 대해 자세히 설명합니다.
  
  또한 이 예에서는 비동기 조치에 대한 필요성을 표시합니다. 이 조치는 Promise를 리턴하여 함수가 리턴할 때 이 조치의 결과를 아직 사용할 수 없음을 표시합니다. 대신 HTTP 호출이 완료된 후 `request` 콜백에서 결과를 사용할 수 있으며 결과는 `resolve()` 함수에 인수로 전달됩니다. 
  
2. 다음 명령을 실행하여 조치를 작성하고 호출하십시오.
  
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
  
### 조치 시퀀스 작성
{: #openwhisk_create_action_sequence}

일련의 조치를 연결하는 조치를 작성할 수 있습니다.

몇 개의 유틸리티 조치가 `/whisk.system/utils`라는 패키지에서 제공되며 첫 번째 시퀀스를 작성하는 데 이를 사용할 수 있습니다. [패키지](./packages.md) 절에서 패키지에 대한 자세한 정보를 볼 수 있습니다.

1. `/whisk.system/utils` 패키지의 조치를 표시하십시오.
  
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

  이 예에서는 `split` 및 `sort` 조치를 사용합니다. 

2. 한 조치의 결과가 다음 조치에 인수로 전달되도록 조치 시퀀스를 작성하십시오.
  
  ```
  wsk action create sequenceAction --sequence /whisk.system/utils/split,/whisk.system/utils/sort
  ```
  {: pre}
  
  이 조치 시퀀스는 텍스트의 일부 행을 배열로 변환하여 해당 행을 정렬합니다.
  
3. 조치 호출:
  
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
  
  결과에서 행이 정렬된 것을 볼 수 있습니다.

**참고**: 시퀀스의 조치 간에 전달되는 매개변수는 기본 매개변수를 제외하곤 명시적입니다.
따라서 조치 시퀀스에 전달되는 매개변수는 시퀀스의 첫 번째 조치에서만 사용 가능합니다.
시퀀스에 있는 첫 번째 조치의 결과는 시퀀스의 두 번째 조치에 대한 입력 JSON 오브젝트가 됩니다(계속해서 동일하게 반복).
시퀀스에 처음 전달된 매개변수를 첫 번째 조치가 명시적으로 해당 결과에 포함시키지 않는 경우 이 오브젝트는 그러한 매개변수를 포함하지 않습니다.
조치에 대한 입력 매개변수는 조치의 기본 매개변수와 병합되며, 앞에 있는 것이 우선되고 일치하는 기본 매개변수를 대체합니다.
이름 지정된 여러 매개변수를 사용하여 조치 시퀀스를 호출하는 방법에 대한 자세한 정보는 [기본 매개변수 설정](./actions.md#setting-default-parameters)을 참조하십시오.

## Python 조치 작성
{: #openwhisk_actions_python}

Python 조치 작성 프로세스는 JavaScript 조치 작성 프로세스와 유사합니다. 다음 절에서는 단일 Python 조치를 작성하고 호출하며 해당 조치에 매개변수를 추가하는 방법에 대해 안내합니다. 

### 조치 작성 및 호출
{: #openwhisk_actions_python_invoke}

조치는 단순히 최상위 레벨 Python 함수이며 이는 `main`이라는 메소드가 있어야 함을 의미합니다. 예를 들어, 다음과 같은 컨텐츠가 있는
`hello.py` 파일을 작성합니다. 

```
    def main(dict):
        name = dict.get("name", "stranger")
        greeting = "Hello " + name + "!"
        print(greeting)
        return {"greeting": greeting}
```
{: codeblock}

Python 조치에서는 항상 사전을 이용하고 사전을 생성합니다. 

다음과 같이 이 함수에서 `helloPython`이라는 OpenWhisk 조치를 작성할 수
있습니다. 

```
wsk action create helloPython hello.py
```
{: pre}

명령행과 `.py` 소스 파일을 사용하는 경우 (JavaScript
조치와 반대로) Python 조치를 작성 중임을 지정할 필요가 없습니다.
도구가 파일 확장자에서 이를 판별합니다. 

Python 조치에 대한 조치 호출은 JavaScript 조치에 대한 조치 호출과 동일합니다. 

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


## Swift 조치 작성
{: #openwhisk_actions_swift}

Swift 조치 작성 프로세스는 JavaScript 조치 작성 프로세스와 유사합니다. 다음 절에서는 단일 Swift 조치를 작성 및 호출하여 해당 조치에 매개변수를 추가하는 방법에 대해 안내합니다.

또한 온라인 [Swift 샌드박스](https://swiftlang.ng.bluemix.net)를 사용하여 시스템에 Xcode를 설치할 필요 없이 Swift 코드를 테스트할 수 있습니다.

### 조치 작성 및 호출
{: #openwhisk_actions_invoke_swift}

조치는 단순히 최상위 레벨의 Swift 함수입니다. 예를 들어, 다음 컨텐츠를 사용하여 `hello.swift`라는 파일을 작성하십시오.

```
func main(args: [String:Any]) -> [String:Any] {if let name = args["name"] as? String {
          return [ "greeting" : "Hello \(name)!" ]
    } else {
return [ "greeting" : "Hello stranger!" ]
    }
}
```
{: codeblock}

Swift 조치는 항상 사전을 이용하며 사전을 생성합니다. 

다음과 같이 이 함수에서 `helloSwift`라는 {{site.data.keyword.openwhisk_short}} 조치를 작성할 수 있습니다.

```
wsk action create helloSwift hello.swift
```
{: pre}

명령행과 `.swift` 소스 파일을 사용하는 경우 (JavaScript
조치와 반대로) Swift 조치를 작성 중임을 지정할 필요가 없습니다.
도구가 파일 확장자에서 이를 판별합니다. 

조치 호출은 JavaScript 호출과 마찬가지로 Swift 호출에 대해서도 동일합니다.

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

**주의:** Swift 조치는 Linux 환경에서 실행됩니다. Swift on Linux는 아직
개발 중이며 {{site.data.keyword.openwhisk_short}}에서는 일반적으로 사용 가능한 가장 최신 릴리스를
사용합니다. 이러한 최신 릴리스가 항상 안정적인 것은 아닙니다. 또한 {{site.data.keyword.openwhisk_short}}와
함께 사용되는 Swift의 버전이 XCode on MacOS의 안정적인 릴리스의 Swift 버전과 일치하지 않을 수 있습니다.


## Docker 조치 작성
{: #openwhisk_actions_docker}

{{site.data.keyword.openwhisk_short}} Docker 조치를 사용하면 사용자의 조치를 어떤 언어로든 작성할 수 있습니다.

사용자의 코드가 실행 가능 2진으로 컴파일되어 Docker 이미지에 임베드됩니다. 2진 프로그램은 `stdin`에서 입력을 가져와서 `stdout`을 통해 응답함으로써 시스템과 상호작용합니다.

전제조건으로, Docker 허브 계정이 있어야 합니다. 무료 Docker ID 및 계정을 설정하려면 [Docker 허브](https://hub.docker.com){: new_window}로 이동하십시오.

다음 지시사항에서는 Docker 사용자 ID가 `janesmith`이고 비밀번호가 `janes_password`라고 가정합니다. CLI가 이미 설정되었다고 가정하면, {{site.data.keyword.openwhisk_short}}에서 사용할 사용자 정의 2진을 설정하는 데 세 단계가 필요합니다. 그런 다음 업로드된 Docker 이미지가 조치로 사용될 수 있습니다.

1. Docker 스켈레톤을 다운로드하십시오. 다음과 같이 CLI를 사용하여 다운로드할 수 있습니다.

  ```
wsk sdk install docker
  ```
  {: pre}
  ```
The Docker skeleton is now installed at the current directory.
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

  스켈레톤은 사용자 정의 2진 형식으로 사용자의 코드를 삽입할 수 있는 Docker 컨테이너 템플리트입니다.

2. Docker 스켈레톤에 사용자 정의 2진을 설정하십시오. 스켈레톤은 사용할 수 있는 C 프로그램을 이미 포함하고 있습니다.

  ```
  cat dockerSkeleton/example.c
  ```
  {: pre}
  ```
  #include <stdio.h>
  
  int main(int argc, char *argv[]) {printf("This is an example log message from an arbitrary C program!\n");
      printf("{ \"msg\": \"Hello from arbitrary C program!\", \"args\": %s }",
             (argc == 1) ? "undefined" : argv[1]);
  }
  ```
  {: codeblock}

  필요에 따라 이 파일을 수정하거나 Docker 이미지에 추가 코드 및 종속 항목을 추가할 수 있습니다.
  후자의 경우, 실행 파일을 빌드하기 위해 필요에 따라 `Dockerfile`을 수정해야 할 수도 있습니다.
  2진은 컨테이너 내부(`/action/exec`)에 있어야 합니다. 

  실행 파일은 명령행에서 단일 변수를 수신합니다. 이는 조치에 대한 인수를 나타내는 JSON
  오브젝트의 문자열 직렬화입니다. 프로그램은 `stdout` 또는 `stderr`에 로그할 수 있습니다.
  관례상 출력의 마지막 행은 *반드시* 조치의 결과를 나타내는 문자열로 변환된 JSON 오브젝트여야 합니다. 

3. Docker 이미지를 빌드하고 제공된 스크립트를 사용하여 이를 업로드하십시오. 먼저 `docker login`을 실행하여 인증한 다음 선택된 이미지 이름을 사용하여 스크립트를 실행하십시오.
  
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
  
  example.c 파일의 일부는 Docker 이미지 빌드 프로세스의 일부로 컴파일되므로 사용자 시스템에서 C 컴파일이 필요하지 않습니다. 실제로 호환 가능한 호스트 시스템에서 2진을 컴파일하지 않는 경우 형식이 일치하지 않기 때문에 컨테이너 내에서 실행되지 않을 수 있습니다. 
  
  Docker 컨테이너는 이제 {{site.data.keyword.openwhisk_short}} 조치로 사용될 수 있습니다. 
  
  
  ```
wsk action create --docker example janesmith/blackboxdemo
  ```
  {: pre}
  
  조치를 작성할 때 `--docker`의 사용에 주의하십시오. 현재 모든 Docker 이미지는 Docker 허브에 호스팅되는 것으로 가정합니다.
  이 조치는 다른 {{site.data.keyword.openwhisk_short}} 조치로서 호출할 수 있습니다. 
  
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
  
  Docker 조치를 업데이트하려면 buildAndPush.sh를 실행하여 Docker 허브의 이미지를 새로 고치십시오. 그러면 다음 번에 시스템이 Docker 이미지를 가져와 조치의 새 코드를 실행할 수 있습니다.
  웜(warm) 컨테이너가 없는 경우 새 호출은 새 Docker 이미지를 사용합니다.
  이전 버전의 Docker 이미지를 사용하는 웜(warm) 컨테이너가 있는 경우, wsk 조치 업데이트를 실행하지 않는 한 새 호출은 계속해서 이 이미지를 사용합니다. wsk 조치 업데이트는 새 호출에 대해 새 Docker 이미지를 가져오는 docker pull을 강제 실행하도록 시스템에 지시합니다. 
  
  ```
./buildAndPush.sh janesmith/blackboxdemo
  ```
  {: pre}
  ```
  wsk action update --docker example janesmith/blackboxdemo
  ```
  {: pre}
  
  [참조](./openwhisk_reference.html#openwhisk_ref_docker) 절에서 Docker 조치 작성에 대한 자세한 정보를 찾을 수 있습니다.

## 조치 출력 감시
{: #openwhisk_actions_polling}

{{site.data.keyword.openwhisk_short}} 조치는 다양한 이벤트에 대한 응답으로 또는 조치 시퀀스의 일부로 기타 사용자에 의해 호출될 수 있습니다. 그런 경우, 호출을 모니터하는 것이 유용합니다.

{{site.data.keyword.openwhisk_short}} CLI를 사용하여 조치가 호출될 때 조치의 출력을 감시할 수 있습니다.

1. 쉘에서 다음 명령 실행:
  
  ```
wsk activation poll
  ```
  {: pre}

  이 명령은 활성화부터 계속해서 로그를 검사하는 폴링 루프를 시작합니다.

2. 다른 창으로 전환하여 조치 호출:

  ```
wsk action invoke /whisk.system/samples/helloWorld --param payload Bob
  ```
  {: pre}
  ```
ok: invoked /whisk.system/samples/helloWorld with id 7331f9b9e2044d85afd219b12c0f1491
  ```
  {: screen}

3. 폴링 창에서 활성화 로그 관찰:

  ```
  Activation: helloWorld (7331f9b9e2044d85afd219b12c0f1491)
    2016-02-11T16:46:56.842065025Z stdout: hello bob!
  ```
  {: screen}

  이와 유사하게 폴링 유틸리티를 실행할 때 {{site.data.keyword.openwhisk_short}}에서 사용자를 위해 실행 중인 모든 조치에 대한 실시간 로그를 볼 수 있습니다.

## 조치 삭제
{: #openwhisk_delete_action}

사용하지 않는 조치를 삭제하여 정리할 수 있습니다.

1. 다음 명령을 실행하여 조치를 삭제하십시오.
  ```
  wsk action delete hello
  ```
  {: pre}
  ```
  ok: deleted hello
  ```
  {: screen}

2. 조치가 더 이상 조치 목록에 표시되지 않는지 확인하십시오.
  ```
  wsk action list
  ```
  {: pre}
  ```
  actions
  ```
  {: screen}
