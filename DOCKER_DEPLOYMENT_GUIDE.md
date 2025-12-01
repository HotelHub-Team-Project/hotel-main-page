# 호텔 유저 시스템 Docker 배포 가이드

## 📋 사전 준비사항

1. **Docker 및 Docker Compose 설치 확인**
   ```powershell
   docker --version
   docker-compose --version
   ```

2. **환경변수 설정**
   - `hotel-user/backend/.env` 파일이 존재하는지 확인
   - 없다면 `.env.example`을 복사하여 실제 값으로 수정

## 🚀 배포 방법

### 1. 기본 배포 (빌드 및 실행)
```powershell
# 프로젝트 루트 디렉토리에서 실행
docker-compose -f docker-compose-user.yml up --build
```

### 2. 백그라운드 실행
```powershell
docker-compose -f docker-compose-user.yml up -d --build
```

### 3. 개별 서비스 재빌드
```powershell
# 프론트엔드만 재빌드
docker-compose -f docker-compose-user.yml up --build frontend-user

# 백엔드만 재빌드
docker-compose -f docker-compose-user.yml up --build backend-user
```

## 🔍 상태 확인

### 실행 중인 컨테이너 확인
```powershell
docker-compose -f docker-compose-user.yml ps
```

### 로그 확인
```powershell
# 전체 로그
docker-compose -f docker-compose-user.yml logs -f

# 백엔드 로그만
docker-compose -f docker-compose-user.yml logs -f backend-user

# 프론트엔드 로그만
docker-compose -f docker-compose-user.yml logs -f frontend-user
```

### 헬스 체크
```powershell
# 백엔드 헬스 체크
curl http://localhost:8080/health

# 프론트엔드 접속 확인
curl http://localhost:3000
```

## 🛑 중지 및 정리

### 컨테이너 중지
```powershell
docker-compose -f docker-compose-user.yml down
```

### 컨테이너 및 볼륨 삭제
```powershell
docker-compose -f docker-compose-user.yml down -v
```

### 이미지까지 삭제
```powershell
docker-compose -f docker-compose-user.yml down --rmi all
```

### 전체 정리 (이미지, 컨테이너, 볼륨, 네트워크)
```powershell
docker-compose -f docker-compose-user.yml down -v --rmi all
docker system prune -af
```

## 📊 서비스 접속 정보

- **프론트엔드**: http://localhost:3000
- **백엔드 API**: http://localhost:8080
- **백엔드 헬스체크**: http://localhost:8080/health

## 🔧 트러블슈팅

### 포트 충돌 시
```powershell
# 포트 사용 확인
netstat -ano | findstr :3000
netstat -ano | findstr :8080

# 프로세스 종료 (관리자 권한 필요)
taskkill /PID <PID번호> /F
```

### 빌드 캐시 초기화
```powershell
docker-compose -f docker-compose-user.yml build --no-cache
```

### 컨테이너 내부 접속
```powershell
# 백엔드 컨테이너 접속
docker exec -it hotel-backend-user sh

# 프론트엔드 컨테이너 접속
docker exec -it hotel-frontend-user sh
```

### 네트워크 문제 해결
```powershell
# 네트워크 확인
docker network ls
docker network inspect hotel-user-network

# 네트워크 재생성
docker network rm hotel-user-network
docker network create hotel-user-network
```

## 📝 환경변수 설정 체크리스트

백엔드 `.env` 파일에 다음 항목들이 설정되어 있는지 확인:

- ✅ `PORT=8080` (docker-compose와 일치)
- ✅ `FRONT_ORIGIN=http://localhost:3000` (프론트엔드 주소)
- ✅ `JWT_SECRET_KEY` (보안키 설정)
- ✅ `TOSS_SECRET_KEY` (토스 결제 키)
- ✅ `MONGO_URI` (MongoDB 연결 문자열)
- ✅ SMTP 설정 (이메일 발송용)

## 🐳 Docker 이미지 관리

### 이미지 목록 확인
```powershell
docker images | Select-String "hotel"
```

### 불필요한 이미지 정리
```powershell
docker image prune -a
```

## 📦 프로덕션 배포 시 권장사항

1. **환경변수 관리**
   - `.env` 파일을 `.gitignore`에 추가
   - 프로덕션 환경에서는 환경변수를 안전하게 주입

2. **로그 관리**
   - 로그 로테이션 설정
   - 중앙 로그 수집 시스템 고려

3. **보안**
   - 프로덕션용 시크릿 키 별도 생성
   - HTTPS 사용
   - 불필요한 포트 노출 제거

4. **모니터링**
   - Health check 엔드포인트 활용
   - 컨테이너 리소스 모니터링

## 🔄 업데이트 배포

```powershell
# 1. 최신 코드 pull
git pull origin main

# 2. 기존 컨테이너 중지 및 삭제
docker-compose -f docker-compose-user.yml down

# 3. 새로 빌드 및 실행
docker-compose -f docker-compose-user.yml up -d --build

# 4. 로그 확인
docker-compose -f docker-compose-user.yml logs -f
```
