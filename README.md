# NAVER CSS(Clova Speech Synthesis) Example

NAVER Clova Speech Synthesis의 Swift 버전 예제가 없어 직접 만들어보았습니다.

더욱 자세한 내용은 [NAVER CLOUD PLATFORM 사용자 가이드](http://docs.ncloud.com/ko/)에서 확인하시면 됩니다.

AI Application에 관한 가이드는 [http://docs.ncloud.com/ko/naveropenapi_v2/naveropenapi-2-2.html](http://docs.ncloud.com/ko/naveropenapi_v2/naveropenapi-2-2.html) 에 나와있습니다. [Clova 사용자 가이드](http://docs.ncloud.com/ko/naveropenapi_v2/naveropenapi-4-2.html)

### 1. Application 등록

##### 1-1. Application 등록하기

iOS Bundle ID를 Xcode프로젝트의 **Bundle ID**와 꼭 같게 해야합니다.

![스크린샷 2018-08-01 오후 7.37.57](/Users/igwang-yong/Downloads/스크린샷 2018-08-01 오후 7.37.57.png)

![스크린샷 2018-08-01 오후 8.59.11](/Users/igwang-yong/Downloads/스크린샷 2018-08-01 오후 8.59.11.png)

##### 1-2. Application 정보 확인

[ AI NAVER API ] > [ Application ] > [App 선택 ] > [ Application Key 관리 ] 를 누르면, 네트워크 요청시 필요한 ID와 키값이 있습니다.

![스크린샷 2018-08-01 오후 8.38.57](/Users/igwang-yong/Downloads/스크린샷 2018-08-01 오후 8.38.57.png)

```swift
let header = [ "X-NCP-APIGW-API-KEY-ID": "[Client ID]",
				"X-NCP-APIGW-API-KEY": "[Client Secret]"]
```

### 2. 프로젝트

##### 2-1. Alamofire 설치

request를 보내기 위해 Alamofire을 사용합니다.

```swift
pod 'Alamofire'
```

##### 2-2. Import

네트워킹의 결과는 mp3 바이너리 데이터로 오기 때문에, 재생을 위하여 `AVFoundation` 을 import 시킵니다.

![image-20180801211128968](/var/folders/q5/jd37stc93wnbg03qc25l_yc00000gp/T/abnerworks.Typora/image-20180801211128968.png)

```swift
import Alamofire
import AVFoundation
```

##### 2-3. Networking,

Header에는 1-2.에서 확인한 `Client ID` 와 `Client Secret` 을 넣어줍니다.

![스크린샷 2018-08-01 오후 9.20.51](/Users/igwang-yong/Downloads/스크린샷 2018-08-01 오후 9.20.51.png)

```swift
	func requestTTS(text: String) {
        let header = [ "X-NCP-APIGW-API-KEY-ID": "[Client ID]",
                       "X-NCP-APIGW-API-KEY": "[Client Secret]"]
        
        let parameter: [String: Any] = ["speaker": "mijin",
                                        "speed": 0,
                                        "text": text]
        
        Alamofire.request("https://naveropenapi.apigw.ntruss.com/voice/v1/tts",
                          method: HTTPMethod.post,
                          parameters: parameter,
            headers: header).responseData { (response) in
                switch response.result {
                case .success :
                    guard let statusCode = response.response?.statusCode as Int? else {return}
                    switch statusCode {
                    case 200..<400 :
                        guard let data = response.data else {return}
                        self.play(data: data)
                    default :
                        print(statusCode)
                    }
                case .failure(let error) :
                    print(error.localizedDescription)
                }
        }
    }

	func play(data: Data) {
        do {
            audio = try AVAudioPlayer(data: data)
            audio?.prepareToPlay()
            audio?.play()
        } catch {
            print(error.localizedDescription)
        }
    }
```

`func requestTTS(text: String)` method에서 `Alamofire` 를 이용하여 request를 하고, `responseData` 를 이용하여 mp3 바이너리 데이터를 받아옵니다.

받아온 데이터는 `func play(data: Data)` method에서 `AVAudioPlayer` 를 이용하여 스피커로 출력시킵니다.

참고로, speaker가 지원하지 않는 언어가 입력되거나, speed에 지원되지 않는 값이 입력되어도 `statusCode: 400` 을 보냅니다.

CSS를 좀더 사용해보기 위해 **속도 조절**, **언어 선택**, **성별 선택**을 추가해보았습니다.

[![Thumbnail](https://img.youtube.com/vi/D-VcKwPoCSw/0.jpg)](https://www.youtube.com/watch?v=D-VcKwPoCSw)