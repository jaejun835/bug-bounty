# APK 정적 분석 
APK 정적 분석은 안드로이드 앱 설치 파일을 실행하지 않고 뜯어서 안의 코드와 설정을 읽는 작업이다.  
앱 자체를 테스트 하진 않지만 API의 경로를 찾는 수단으로써 이용이 가능하다.  
(찾은 API 경로는 후보일 뿐이다. 실제로 테스트하고 증명하는 건 Burp 에서 API 서버에 직접 요청을 보내는 단계에서 한다.)  

## 실행
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

# 버전 확인
./apkeep -d apk-pure -l -a com.infomil.leclercdrive

# apkeep 실행
./apkeep -d apk-pure -o 'acknowledge_dangers=true' -a <패키지 이름>@<버젼 번호> .
```

## 실행 후 압축 해제
```
# 크기 비교와 묶음 안 목록 확인
ls -la

# zip 풀지 않고 파일 목록과 크기만 확인
unzip -l '<apkeep 파일>'

# 묶음 안 목록 확인
unzip -l '<apkeep 파일>'

# 파일 풀기 전 경로 생성
mkdir -p <생성 경로>

# 파일 압춥 해제
unzip -o '<apkeep 파일>' -d <생성 경로>

# APK 서명이 유효한지 검사(악성 파일 가능성 존재하기 때문)
apksigner verify --print-certs <apk_file> # 오류 없이 인증서 정보만 나왔다는 건 앱 내용이 서명된 뒤로 바뀌지 않았다는 뜻
for f in xapk_extracted/*.apk; do echo "== $f"; apksigner verify --print-certs "$f" | grep -E "Signer #1 certificate SHA-256|Source Stamp Signer certificate SHA-256"; done # 네 파일 모두 Signer 지문이 같으면 묶음 전체가 같은 출처다
```
### 압축 파일 종류

#### <패키지 경로>.apk
기본 APK로 앱 코드인 dex 파일, 설정 파일 AndroidManifest.xml, 공통 리소스가 들어 있다.  
API 주소와 인증 흐름을 찾는 주 분석 대상이다.  

#### config.arm64_v8a.apk
arm64 기기용 네이티브 라이브러리 분할 APK로 C, C++ 등으로 컴파일된 .so 파일이 들어 있다.  
일반 앱치고 매우 커서, 프레임워크 앱이라면 앱 로직이 여기 있을 수 있다.   

#### config.hdpi.apk
고해상도 화면용 이미지 리소스 분할 APK로 API 분석과는 관련이 거의 없다  

#### config.en.apk
영어 문자열 리소스 분할 APK로 화면 문구 외에 설정용 문자열이 섞여 있을 수 있어 가볍게 확인할 가치는 있다  

#### manifest.json
APKPure 가 만든 XAPK 묶음 정보로 앱과는 무관하다.  
패키지 이름, 버전 30.0.1, 버전 코드 2030000100, 최소 SDK 26, 타깃 SDK 36, 권한 목록, 분할 목록이 들어 있다  
APKPure 가 쓴 파일이라 서명 대상이 아니며 확정값은 기본 APK 안의 AndroidManifest.xml 로 다시 확인해야 한다  

##APK 파일 분석 방법
```
# 네이티브 분할 APK 안에서 큰 파일 확인
unzip -l xapk_extracted/config.arm64_v8a.apk | sort -k1 -n -r | head -20 
```


