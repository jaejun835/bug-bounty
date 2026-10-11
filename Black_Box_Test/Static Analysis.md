# APK 정적 분석 
APK 정적 분석은 안드로이드 앱 설치 파일을 실행하지 않고 뜯어서 안의 코드와 설정을 읽는 작업이다.  
앱 자체를 테스트 하진 않지만 API의 경로를 찾는 수단으로써 이용이 가능하다.  
(찾은 API 경로는 후보일 뿐이다. 실제로 테스트하고 증명하는 건 Burp 에서 API 서버에 직접 요청을 보내는 단계에서 한다.)  

실행:
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

실행 후 압축 해제:
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
apksigner verify --print-certs <apk_file>

for f in xapk_extracted/*.apk; do echo "== $f"; apksigner verify --print-certs "$f" | grep -E "Signer #1 certificate SHA-256|Source Stamp Signer certificate SHA-256"; done
```
