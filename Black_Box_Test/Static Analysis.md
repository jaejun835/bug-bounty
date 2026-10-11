# APK 정적 분석 
APK 정적 분석은 안드로이드 앱 설치 파일을 실행하지 않고 뜯어서 안의 코드와 설정을 읽는 작업이다.  
앱 자체를 테스트 하진 않지만 API의 경로를 찾는 수단으로써 이용이 가능하다.  
(찾은 API 경로는 후보일 뿐이다. 실제로 테스트하고 증명하는 건 Burp 에서 API 서버에 직접 요청을 보내는 단계에서 한다.)  

리눅스 명령어:
```
# 현재 PC의 종류 출력(x86_64면 아래와 같은 명령어 사용 가능)
uname -m

# 작업 폴더 생성
mkdir -p ~/<경로 이름>
cd ~/<경로 이름>

# apkeep 실행 파일 다운로드
curl -sSL -o apkeep https://github.com/EFForg/apkeep/releases/latest/download/apkeep-x86_64-unknown-linux-gnu

# 권한 부여
chmod +x apkeep

# apkeep 실행
./apkeep -d apk-pure -o 'acknowledge_dangers=true' -a <package_name> .
```
