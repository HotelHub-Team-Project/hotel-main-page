# Docker 개발 환경 가이드

## 📋 개발 환경 vs 프로덕션 환경

### 개발 환경 (Dockerfile.dev)
- ✅ Hot Reload (코드 변경 시 자동 재시작)
- ✅ 소스 코드 볼륨 마운트
- ✅ nodemon / Vite dev server 사용
- ✅ 디버깅 편의성
- ❌ 최적화 안됨

### 프로덕션 환경 (Dockerfile)
- ✅ 빌드 최적화
- ✅ 작은 이미지 크기
- ✅ Nginx 사용 (프론트엔드)
- ✅ 보안 강화
- ❌ Hot Reload 없음

## 🚀 개발 환경 실행

### 호텔 유저 시스템 개발 모드
```powershell
# 개발 환경 시작
docker-compose -f docker-compose-user.dev.yml up --build

# 백그라운드 실행
docker-compose -f docker-compose-user.dev.yml up -d --build

# 중지
docker-compose -f docker-compose-user.dev.yml down
```

**접속 정보:**
- 프론트엔드: http://localhost:5173 (Vite dev server)
- 백엔드: http://localhost:8080

### 호텔 관리 시스템 개발 모드
```powershell
# 개발 환경 시작
docker-compose -f docker-compose-management.dev.yml up --build

# 백그라운드 실행
docker-compose -f docker-compose-management.dev.yml up -d --build

# 중지
docker-compose -f docker-compose-management.dev.yml down
```

**접속 정보:**
- 프론트엔드: http://localhost:5174 (Vite dev server)
- 백엔드: http://localhost:8081

### 모든 시스템 동시 실행
```powershell
# 유저 + 관리 시스템 동시 개발
docker-compose -f docker-compose-user.dev.yml -f docker-compose-management.dev.yml up --build
```

## 🔥 Hot Reload 동작 확인

개발 환경에서는 코드를 수정하면 **자동으로 반영**됩니다:

1. **백엔드**: `hotel-user/backend` 또는 `hotel-management/backend` 폴더의 파일 수정
   - nodemon이 자동으로 서버 재시작

2. **프론트엔드**: `src` 폴더의 파일 수정
   - Vite가 자동으로 브라우저 새로고침

## 📊 로그 확인

```powershell
# 전체 로그 실시간 확인
docker-compose -f docker-compose-user.dev.yml logs -f

# 특정 서비스만
docker-compose -f docker-compose-user.dev.yml logs -f backend-user-dev
docker-compose -f docker-compose-user.dev.yml logs -f frontend-user-dev
```

## 🔧 개발 팁

### 1. 의존성 추가 시
```powershell
# 컨테이너 재빌드 필요
docker-compose -f docker-compose-user.dev.yml up --build
```

### 2. 컨테이너 내부 접속
```powershell
# 백엔드 컨테이너
docker exec -it hotel-backend-user-dev sh

# 프론트엔드 컨테이너
docker exec -it hotel-frontend-user-dev sh
```

### 3. node_modules 문제 해결
```powershell
# 볼륨 삭제 후 재빌드
docker-compose -f docker-compose-user.dev.yml down -v
docker-compose -f docker-compose-user.dev.yml up --build
```

### 4. 캐시 없이 완전 재빌드
```powershell
docker-compose -f docker-compose-user.dev.yml build --no-cache
docker-compose -f docker-compose-user.dev.yml up
```

## 🎯 개발 워크플로우

```powershell
# 1. 개발 환경 시작
docker-compose -f docker-compose-user.dev.yml up -d

# 2. 코드 수정 (에디터에서)
# - 저장하면 자동으로 반영됨

# 3. 로그 확인 (필요시)
docker-compose -f docker-compose-user.dev.yml logs -f

# 4. 작업 완료 후 중지
docker-compose -f docker-compose-user.dev.yml down
```

## 🚢 프로덕션 배포

개발이 완료되면 프로덕션 환경으로 배포:

```powershell
# 프로덕션 빌드 및 배포
docker-compose -f docker-compose-user.yml up -d --build
```

## 📝 포트 정리

### 개발 환경
| 서비스 | 포트 | URL |
|--------|------|-----|
| 유저 프론트엔드 | 5173 | http://localhost:5173 |
| 유저 백엔드 | 8080 | http://localhost:8080 |
| 관리 프론트엔드 | 5174 | http://localhost:5174 |
| 관리 백엔드 | 8081 | http://localhost:8081 |

### 프로덕션 환경
| 서비스 | 포트 | URL |
|--------|------|-----|
| 유저 프론트엔드 | 3000 | http://localhost:3000 |
| 유저 백엔드 | 8080 | http://localhost:8080 |
| 관리 프론트엔드 | 3001 | http://localhost:3001 |
| 관리 백엔드 | 8081 | http://localhost:8081 |

## ⚡ 성능 최적화

### 볼륨 마운트 최적화 (Windows)
Windows에서 Docker 볼륨 성능이 느릴 수 있습니다:

1. **WSL 2 사용 권장**
2. **파일을 WSL 파일 시스템에 위치**
3. **node_modules는 익명 볼륨으로 제외** (이미 적용됨)

## 🐛 트러블슈팅

### Hot Reload가 안 될 때
```powershell
# 1. 컨테이너 재시작
docker-compose -f docker-compose-user.dev.yml restart

# 2. 완전 재빌드
docker-compose -f docker-compose-user.dev.yml down
docker-compose -f docker-compose-user.dev.yml up --build
```

### 포트 충돌
```powershell
# 사용 중인 포트 확인
netstat -ano | findstr :5173
netstat -ano | findstr :8080

# 프로세스 종료 (관리자 권한)
taskkill /PID <PID> /F
```

### 권한 문제 (Linux/Mac)
```bash
# node_modules 권한 문제 시
sudo chown -R $USER:$USER ./hotel-user/frontend/node_modules
sudo chown -R $USER:$USER ./hotel-user/backend/node_modules
```

## 📚 추가 명령어

```powershell
# 개발 컨테이너 상태 확인
docker-compose -f docker-compose-user.dev.yml ps

# 특정 서비스만 재시작
docker-compose -f docker-compose-user.dev.yml restart backend-user-dev

# 리소스 사용량 확인
docker stats

# 불필요한 이미지/컨테이너 정리
docker system prune -a
```

## 💡 개발 환경의 장점

1. **빠른 피드백**: 코드 변경 즉시 확인
2. **일관된 환경**: 팀원 모두 동일한 환경에서 개발
3. **격리된 개발**: 로컬 환경 오염 방지
4. **쉬운 설정**: `docker-compose up` 한 번으로 모든 서비스 실행
5. **프로덕션 유사**: 배포 환경과 유사한 구조
