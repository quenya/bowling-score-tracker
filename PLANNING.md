# 볼링 점수 트래커 플래닝

## 목표
- 2주에 한 번 볼링 동호회에서 3게임씩 친 점수를 기록
- Supabase에 점수 데이터를 저장
- Vue 앱에서 점수 입력 및 트렌드/통계 분석 화면 제공

---

## 개발 플랜

1. **Supabase 프로젝트 및 데이터베이스(테이블) 설계**
   - 날짜, 게임별 점수(3게임) 저장 구조 설계 및 생성

2. **Vue 앱 초기 세팅**
   - 프로젝트 생성, 기본 폴더 구조, 환경설정 등

3. **Supabase와 연동하여 날짜 및 3게임 점수 입력 폼 구현**

4. **입력된 점수를 Supabase에 insert하는 기능 구현**

5. **Supabase에서 점수 데이터 fetch 및 리스트/테이블 형태로 출력**

6. **점수 데이터 기반 트렌드/통계 분석(평균, 최고점, 변화 그래프 등) 화면 구현**

7. **UI/UX 개선 및 마무리**
   - 디자인, 반응형, 에러처리 등

8. **GitHub 저장소 생성 및 코드 푸시**
   - 프로젝트를 GitHub에 업로드, 버전 관리

9. **Vercel을 통한 배포**
   - Vercel에 프로젝트 연결 및 배포 자동화

---

각 단계별로 진행하며 궁금한 점이나 세부 구현이 필요하면 언제든 문의하세요.

### 1. Supabase 프로젝트 및 데이터베이스(테이블) 설계

#### 1) 테이블 구조 제안
- **테이블명:** games
- **컬럼**
  - id (primary key, auto increment)
  - played_at (date, 경기 날짜)
  - game1_score (integer, 1게임 점수)
  - game2_score (integer, 2게임 점수)
  - game3_score (integer, 3게임 점수)
  - created_at (timestamp, 자동 생성)

#### 2) Supabase에서 테이블 생성 방법
1. Supabase 프로젝트 생성 (supabase.io에서 새 프로젝트 생성)
2. SQL Editor에서 아래 쿼리 실행

```sql
create table games (
  id bigint generated always as identity primary key,
  played_at date not null,
  game1_score integer not null,
  game2_score integer not null,
  game3_score integer not null,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);
```

#### 3) 추가 고려사항
- 추후 여러 명의 플레이어를 관리하고 싶다면 user_id 컬럼 추가 가능
- 점수 외에 메모, 장소 등 추가 정보가 필요하면 컬럼 확장 가능

---

Supabase 콘솔에서 위 쿼리를 실행해 테이블을 먼저 만들어 주세요.
테이블 생성이 완료되면, 다음 단계(React 앱 세팅 또는 Supabase 연동)로 넘어가겠습니다.

테이블 생성이 끝나면 알려주세요!
추가로 궁금한 점이나, 컬럼 구조에 대해 수정/추가 요청이 있으면 말씀해 주세요. 

supabase-js를 이용한 클라이언트 초기화 파일(src/lib/supabase.js)은 정상적으로 생성되었습니다.

다만, .env 파일은 보안 정책상 직접 생성해드릴 수 없으니, 아래 내용을 참고하여 직접 bowling-score-tracker-frontend/.env 파일을 만들어 주세요:

```
(각각 본인의 Supabase 프로젝트에서 발급받은 URL과 anon key로 교체)

이제 Vue 컴포넌트에서 import { supabase } from '@/lib/supabase.js'로 Supabase를 사용할 수 있습니다.

.env 파일을 직접 생성하신 후,  
다음 단계(점수 입력 폼 및 Supabase insert 기능 구현)로 넘어가겠습니다!  
준비가 되면 말씀해 주세요. 