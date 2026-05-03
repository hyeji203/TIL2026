# ☁️ Auto Scaling 복습 정리

---

## 1. 스케일링(Scaling)이란?

애플리케이션/시스템의 성능을 높이기 위해 **컴퓨팅 리소스를 확장하거나 축소**하는 것.
→ 요청이 많아질 때 처리할 수 있도록 서버를 키우는 개념!

---

## 2. Scale-Up vs Scale-Out

### 🔼 스케일 업 (Scale-Up / Vertical Scaling)
> 서버 **한 대**의 성능을 높이는 방식

| 항목 | t3.nano | t3.2xlarge | 차이 |
|------|---------|------------|------|
| vCPU | 2 | 8 | 4배 |
| 가격 | $0.0065 | $0.416 | 약 64배 |

**장점**
- 설정이 단순하고 쉬움

**단점**
- 물리적인 업그레이드 한계 존재
- 업그레이드 시 서버 재시작 필요
- 서버 한 대에만 의존 → 장애 발생 시 전체 위험

---

### 🔁 스케일 아웃 (Scale-Out / Horizontal Scaling)
> 서버를 **여러 대**로 늘려서 처리 성능을 확장하는 방식

**장점**
- 무중단 확장 가능

**단점**
- 아키텍처가 복잡해짐
- Load Balancer(ELB) 등 추가 구성 필요

---

## 3. Auto Scaling이란?

> 트래픽 상황에 맞춰 **서버 수를 자동으로 늘리거나 줄여주는** AWS 서비스

- 트래픽 증가 시 → 인스턴스 자동 증가
- 사용량 감소 시 → 인스턴스 자동 축소
- **리소스 비용 절감 + 고가용성 유지**

---

## 4. Auto Scaling 구성 요소

### 📋 Launch Template (시작 템플릿)
새 인스턴스를 생성할 때 필요한 설정을 담은 **설계도**

- 어떤 AMI(이미지)를 사용할지
- 인스턴스 타입 (예: t3.micro)
- 키 페어, 보안 그룹
- UserData 스크립트 (초기 설정 자동화)

---

### 🗂️ Auto Scaling Group (ASG)
인스턴스를 묶어서 관리하는 단위

- 최소 / 최대 / 원하는(Desired) 인스턴스 수 설정
- 실제 인스턴스 수를 지속적으로 모니터링 & 자동 조절
- Availability Zone 간 분산 가능

**용량 설정 규칙**
```
최소 용량 ≤ 원하는 용량 ≤ 최대 용량
```

---

### 📏 Scaling Policy (스케일링 정책)
언제, 어떻게 서버 수를 조절할지에 대한 규칙

| 유형 | 설명 |
|------|------|
| **Target Tracking** | 목표값 유지 (예: 평균 CPU 60% 유지) |
| **Step Scaling** | 단계별 조정 (예: CPU 70~80% → +1대, 80% 이상 → +2대) |
| **Scheduled Scaling** | 특정 시간에 확장 (예: 매일 오후 6시) |

---

### 📊 CloudWatch
지표를 감시하다가 스케일링 정책을 실행하는 역할

- CPU 사용률, 네트워크 트래픽, 메모리 등 모니터링
- 조건 충족 시 Scaling Policy 트리거
- 실시간 알람 + 로그 기록 가능

---

## 5. 실습 요약

### 실습 1) Auto Scaling 생성

1. EC2 콘솔 → Auto Scaling 그룹 → **Auto Scaling 그룹 생성**
2. **시작 템플릿 생성**
   - AMI: Amazon Linux 2
   - 인스턴스 유형: t3.nano
   - 키 페어, 보안 그룹 설정
   - 고급 세부정보 → **사용자 데이터 입력**
     ```bash
     #!/bin/bash
     yum update -y
     yum install -y httpd stress
     systemctl enable httpd
     systemctl start httpd
     echo "Hello from Auto Scaling Instance (Shingu Univ)" > /var/www/html/index.html
     ```
3. 가용 영역 선택 (여러 AZ 분산 권장)
4. **상태 확인 유예 기간** 설정 (기본 300초)
   - 인스턴스가 부팅되자마자 상태 확인 실패로 종료되지 않도록 하는 유예 시간
5. ASG 용량 설정: 원하는 용량 2 / 최소 2 / 최대 2

**테스트**: 인스턴스 1대를 의도적으로 종료 → ASG가 자동으로 새 인스턴스를 생성하는 것 확인

---

### 실습 2) CPU 임계치 기반 Auto Scaling

1. ASG 용량을 원하는 용량: 1 / 최소: 1 / 최대: 2로 수정
2. **동적 크기 조정 정책 생성**
   - 정책 유형: 대상 추적 크기 조정 (Target Tracking)
   - 지표: 평균 CPU 사용률
   - 대상 값: 20%
3. EC2에 SSH(PuTTY) 접속 후 부하 발생
   ```bash
   stress --cpu 2 --timeout 6000
   ```
4. CPU가 임계치 초과 → 새 인스턴스 자동 생성 확인
5. CPU가 정상으로 돌아오면 → 인스턴스가 다시 1대로 축소 확인

---

### 실습 3) 특정 시간대에 EC2 자동 증가

1. EC2 → Auto Scaling Group → **Automatic Scaling 탭**
2. **예약된 작업 생성**
   - 이름: scaling_out_test
   - 원하는 용량: 2
   - 반복: 한 번
   - 표준 시간대: Asia/Seoul
   - 특정 시작 시간 설정
3. 설정한 시간이 되면 인스턴스가 자동으로 1대 추가되는 것 확인

---

## 6. 핵심 개념 정리

| 개념 | 설명 |
|------|------|
| Scale-Up | 서버 한 대 성능 향상 (수직 확장) |
| Scale-Out | 서버 대수 증가 (수평 확장) |
| Auto Scaling | 트래픽에 따라 자동으로 서버 수 조절 |
| Launch Template | 인스턴스 생성 설계도 |
| ASG | 인스턴스 그룹 관리 단위 |
| Scaling Policy | 스케일링 규칙 (Target / Step / Scheduled) |
| CloudWatch | 지표 모니터링 및 정책 트리거 |
| 상태 확인 유예 기간 | 부팅 직후 상태 확인 실패 방지용 유예 시간 |