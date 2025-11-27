# 🏨 Hotel Hub - Docker 배포 가이드

이 프로젝트는 4개의 독립적인 서비스로 구성된 호텔 관리 시스템입니다.

## 📁 프로젝트 구조

```
hotel-main-page/
├── Hotel-front-user/          # 사용자 프론트엔드 (React + Vite)
├── Hotel-back-user/           # 사용자 백엔드 (Node.js + Express)
├── Hotel-front-management/    # 관리자 프론트엔드 (React + Vite)
├── Hotel-back-management/     # 관리자 백엔드 (Node.js + Express)
├── docker-compose-user.yml    # 사용자 서비스 배포 설정
└── docker-compose-management.yml  # 관리자 서비스 배포 설정
```

## 🚀 빠른 시작

### 사전 요구사항
- Docker (version 20.10+)
- Docker Compose (version 2.0+)

### 환경 변수 설정

각 백엔드 서비스의 `.env` 파일을 생성하세요:

**Hotel-back-user/.env**
```env
NODE_ENV=production
PORT=8080
MONGODB_URI=your_mongodb_uri
JWT_SECRET=your_jwt_secret
```

**Hotel-back-management/.env**
```env
NODE_ENV=production
PORT=8080
MONGODB_URI=your_mongodb_uri
JWT_SECRET=your_jwt_secret
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret
AWS_REGION=your_region
S3_BUCKET=your_bucket
```

## 🎯 배포 방법

### 1️⃣ 사용자 애플리케이션 배포

```powershell
# 빌드 및 실행
docker-compose -f docker-compose-user.yml up -d --build

# 로그 확인
docker-compose -f docker-compose-user.yml logs -f

# 중지
docker-compose -f docker-compose-user.yml down
```

**접속 주소:**
- 프론트엔드: http://localhost:3000
- 백엔드 API: http://localhost:8080

### 2️⃣ 관리자 애플리케이션 배포

```powershell
# 빌드 및 실행
docker-compose -f docker-compose-management.yml up -d --build

# 로그 확인
docker-compose -f docker-compose-management.yml logs -f

# 중지
docker-compose -f docker-compose-management.yml down
```

**접속 주소:**
- 프론트엔드: http://localhost:3001
- 백엔드 API: http://localhost:8081

### 3️⃣ 전체 시스템 동시 배포

```powershell
# 모든 서비스 실행
docker-compose -f docker-compose-user.yml -f docker-compose-management.yml up -d --build

# 모든 서비스 중지
docker-compose -f docker-compose-user.yml -f docker-compose-management.yml down
```

## 🛠️ 주요 명령어

```powershell
# 특정 서비스만 재시작
docker-compose -f docker-compose-user.yml restart backend-user

# 컨테이너 상태 확인
docker-compose -f docker-compose-user.yml ps

# 특정 서비스 로그 확인
docker-compose -f docker-compose-user.yml logs -f backend-user

# 이미지 재빌드 (캐시 무시)
docker-compose -f docker-compose-user.yml build --no-cache

# 볼륨 포함 완전 삭제
docker-compose -f docker-compose-user.yml down -v
```

## 📊 Health Check

모든 서비스는 자동 헬스체크가 설정되어 있습니다:

```powershell
# 프론트엔드 헬스체크
curl http://localhost:3000/health
curl http://localhost:3001/health

# 백엔드 헬스체크
curl http://localhost:8080/health
curl http://localhost:8081/health
```

## 🔧 최적화 기능

### Multi-Stage Build
- **빌드 단계**: 의존성 설치 및 빌드
- **런타임 단계**: 최소한의 파일만 포함하여 이미지 크기 최소화

### 보안 설정
- Non-root 사용자로 컨테이너 실행
- `.dockerignore`로 불필요한 파일 제외
- 환경 변수로 민감 정보 관리

### 네트워크 격리
- 사용자/관리자 서비스가 독립적인 네트워크 사용
- 서비스 간 통신은 Docker 내부 네트워크로 처리

## 🐛 문제 해결

### 포트 충돌
```powershell
# 사용 중인 포트 확인
netstat -ano | findstr :3000
netstat -ano | findstr :8080

# 프로세스 종료
taskkill /PID <PID> /F
```

### 컨테이너 로그 확인
```powershell
# 실시간 로그
docker logs -f hotel-backend-user

# 최근 로그 100줄
docker logs --tail 100 hotel-backend-user
```

### 이미지 및 볼륨 정리
```powershell
# 사용하지 않는 이미지 삭제
docker image prune -a

# 사용하지 않는 볼륨 삭제
docker volume prune

# 전체 Docker 시스템 정리
docker system prune -a --volumes
```

## 📝 개발 모드 실행

개발 중에는 Docker 없이 로컬에서 실행:

```powershell
# 백엔드
cd Hotel-back-user
npm install
npm run dev

# 프론트엔드
cd Hotel-front-user
npm install
npm run dev
```

## 🔗 API 프록시 설정

프론트엔드의 `/api` 요청은 자동으로 백엔드로 프록시됩니다:
- User Frontend → User Backend
- Management Frontend → Management Backend

nginx 설정 파일에서 프록시 규칙을 확인하세요.

## 📚 추가 정보

- Node.js 버전: 20 (Alpine)
- 프론트엔드 프레임워크: React 19 + Vite 7
- 백엔드 프레임워크: Express 5
- 웹 서버: Nginx (Alpine)
- 데이터베이스: MongoDB (별도 설정 필요)

## 🤝 기여

문제가 발생하거나 개선 사항이 있으면 이슈를 등록해주세요.

## 📄 라이센스

이 프로젝트는 HotelHub Team Project의 일부입니다.
