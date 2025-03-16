# DevOps/Infra Engineer Portfolio

안녕하세요! 대규모 인프라와 DevOps 업무에 관심이 많은 엔지니어입니다.  
아래는 전기차 충전기 서비스 인프라 구축 시 진행했던 프로젝트입니다.

---

## 1. Rancher 기반 Kubernetes 환경 운영

### 개요
- **목적**: 사내에서 이미 구축된 MSA 아키텍처를 관리하고, Rancher를 통해 클러스터 상태와 리소스를 직관적으로 관리  
- **주요 기술**:  
  - **Rancher**, **Kubernetes**, **Docker** 

### 수행 내용
1. **클러스터 운영**  
   - Rancher UI로 멀티 클러스터와 노드 상태를 실시간 파악  
2. **운영상 개선 사항 적용**  
   - TiDB 메모리 누수 발생하여 OOM 나는 이슈 설정 값 확인 후 해결
   - RBAC 역할 재정비로 보안과 편의성 모두 충족

### 성과 / 어려움 극복
- **성과**:  
  - 쿠버네티스 운영 효율 상승 → 장애 대응 시간 단축, 서비스 안정성 향상
- **어려움 & 해결**:  
  - 기존 MSA 구조가 복잡해 리소스 배포 시 연결 부분 오류 -> 전체 워크플로우 파악 후 로드맵을 통해 정리
---

## 2. k8GB 글로벌 DNS 로드 밸런싱 테스트

### 개요
- **목적**: 멀티 클러스터 환경에서 글로벌 DNS 로드 밸런싱을 구현해, 메인 클러스터에 장애가 발생해도 서브 클러스터로 트래픽을 자동 전환하고자 함  
- **주요 기술**:
  - **k8GB** (Kubernetes Global Balancer)
  - **External DNS**, **Geo DNS**

### 수행 내용
1. **k8GB 테스트 환경 구성**  
   - 멀티 리전 혹은 멀티 클러스터를 가정해, 각 리전에 노출할 DNS 레코드를 k8GB가 자동으로 생성·관리  
   - 장애 발생 시 다른 리전으로 DNS 레코드 전환 시나리오 검증
2. **DNS 전파(캐시) 문제 파악**  
   - ISP나 사용자 측 DNS 캐싱 시간이 길어, 다운타임 발생 시 즉각 전환되지 않는 이슈 발견  
   - TTL(Time to Live)을 짧게 설정하면 캐싱 문제가 덜하지만, 그만큼 DNS 쿼리 부담 증가
3. **도입 보류 결정**  
   - DNS 캐시로 인해 서비스 다운타임이 완전히 해소되지 않아 최종적으로 프로덕션에 도입하지 않음  
   - 다른 대안(글로벌 로드 밸런서, CDN 등)과 비교 검토하여 이후 기회에 재시도 예정

### 성과 / 어려움 극복
- **성과**:  
  - 글로벌 DNS 기반 로드 밸런싱 개념과 구현 원리에 대한 이해도 상승  
  - 멀티 클러스터 장애 복구 시나리오를 사전에 테스트해, 재해복구(Disaster Recovery) 관점에서 다양한 기술을 파악
- **어려움 & 해결**:  
  - **DNS 캐싱 이슈**로 인해 트래픽 전환이 즉각적으로 이뤄지지 않아, 실제 다운타임 해소에 한계  
  - TTL을 단축해도 전 세계 ISP마다 캐시 정책이 상이 → 회사 내부적으로 최종 프로덕션 적용은 보류

---

## 3. Proxmox·Ansible 기반 테스트 환경 구축

### 개요
- **목적**: 사내 개발/테스트 서버 환경을 효율적으로 운영하고, 초기 설정을 자동화  
- **주요 기술**: 
  - **Proxmox** 
  - **Ansible** 

### 수행 내용
1. **Proxmox로 VM 관리**  
   - Dev/QA 환경에서 VM 생성·삭제, 스냅샷을 신속하게 관리  
   - 테스트 실패 시 스냅샷 복구로 재실행 시간 단축
2. **Ansible 플레이북 작성**  
   - 초기 서버 세팅, 패키지 설치 등을 자동화  

### 성과 / 어려움 극복
- **성과**:  
  - 테스트 환경 셋업 시간 단축 및 가용성 확보(자동 복구)
- **어려움 & 해결**:  
  - Ceph를 통한 데이터 자동화 백업 초기 설정

---

## 4. DB 샤딩 (MongoDB·Vitess) 도입

### 개요
- **목적**: 기존 Primary-Secondary 구조의 DB가 CPU와 메모리를 많이 소모하던 문제를, 샤딩 기반 DB로 전환하여 리소스 부담을 완화하고자 함
- **주요 기술**: 
  - **MongoDB Sharding** (자동화 샤딩 지원) 
  - **Vitess** (자동화 MySQL Sharding 솔루션)

### 수행 내용
1. **MongoDB Sharding 운영**  
   - 자동화된 샤딩 기능을 활용해 **Shard Key**를 설계, Chunk Balancer 튜닝으로 데이터 분산  
   - 기존 MongoDB 대비, Percona 버전의 이점(성능 최적화·모니터링 플러그인 등)을 적극 활용
2. **Vitess 기반 MySQL 샤딩**  
   - Vitess의 **자동화 샤딩**으로 확장성과 가용성을 모두 확보  

### 성과 / 어려움 극복
- **성과**:  
  - 시스템 성능 개선
  - 서버 증설 시 무중단 확장 가능
- **어려움 & 해결**:   
  - MySQL→Vitess 전환 중 데이터 불일치 → Vitess에서 지원하지 않는 SQL문을 파악 후 수정
  - 데이터 자동화 백업 및 복구 방식 상이 -> Vitess에서 공식적으로 지원하는 방법으로 DB백업 및 복구 정책 수립

---

## 5. IDC 서버 교체 및 NHN 클라우드 이전

### 개요
- **목적**: 노후 IDC 서버를 교체해 물리적 안정성을 확보하고, 이후 NHN 클라우드로 전환  
- **주요 기술**: 
  - **서버 하드웨어 교체**(IDC 실서버 리프레시)  
  - **NHN Cloud** (클라우드 인프라)  

### 수행 내용
1. **IDC 서버 교체(하드웨어 리프레시)**  
   - 노후 서버 HDD 디스크 장애로 인해 서버 교체
   - 신형 서버로 교체 후 안정화 테스트(네트워크 트래픽, Disk I/O 등)
   - 노후 서버 클러스터 탈구 후 새 서버 클러스터 참여(해당 서버 모든 파드 다른 서버로 이전 후 작업)
2. **NHN 클라우드로 마이그레이션**  
   - Velero+Minio로 IDC 쿠버네티스 서비스 NHN 클라우드로 마이그레이션

### 성과 / 어려움 극복
- **성과**:  
  - IDC 서버 교체로 하드웨어 장애률 감소  
  - NHN 클라우드 전환으로 물리 서버 장애 감소
- **어려움 & 해결**:  
  - IDC-클라우드 간 네트워크 VPN차이 -> 사내 VPN + 클라우드 VPN을 추가 설치(환경부 통신)

---

## 6. 모니터링/로그 시스템 고도화

### 개요
- **목적**: Rancher 기반 쿠버네티스에서 NHN 쿠버네티스로 전환 후 서비스 상태 추적 및 장애 원인 파악을 위한 모니터링·로그 체계 마련  
- **주요 기술**: 
  - **Telegraf**, **VictoriaMetrics**, **Grafana**, **Alertmanager**  
  - **promtail**, **victorialogs**

### 수행 내용
1. **지표 수집 & 시계열 DB**  
   - Telegraf로 서버/컨테이너 지표 수집  
   - VictoriaMetrics에 대규모 시계열 데이터 저장
2. **시각화·알림**  
   - Grafana로 대시보드 구성, Alertmanager로 임계값 초과 시 Email 알림
   - 실시간 대응 강화
3. **로그 수집**  
   - promtail이 컨테이너 로그를 victorialogs로 전송  
   - 로그와 지표를 연계해 장애 시점을 빠르게 추적 후 관리자 Email 알림

### 성과 / 어려움 극복
- **성과**:  
  - Rancher UI 대안으로 로그서버와 모니터링 구축
- **어려움 & 해결**:  
  - 쿼리 기반 언어 작성 -> 쿼리 기반 언어에 대해 추가적인 공부
  - 메타모스트와 연결 어려움 -> Email로 알림 변경

---

## 7. Velero + Minio 백업/복구

### 개요
- **목적**: 쿠버네티스 애플리케이션 및 데이터 백업, 재해 복구 시나리오 확립  
- **주요 기술**: 
  - **Velero**, **Minio**

### 수행 내용
1. **백업 아키텍처**  
   - Velero로 쿠버네티스 리소스·퍼시스턴트 볼륨(PV)을 주기적으로 백업  
   - Minio를 S3 호환 대상으로 구성해 백업 데이터를 보관
2. **복구 절차 테스트**  
   - 단일 파드 장애, 노드 장애 등 시나리오별 복구 절차 수립  
   - 실제 클러스터에서 반복 테스트해 신뢰도 확보

### 성과 / 어려움 극복
- **성과**:  
  - 백업/복구 자동화로 재해 발생 시 신속 복원 가능  
  - 운영 편의성과 안정성 동시 확보
- **어려움 & 해결**:  
  - 대용량 볼륨 백업 중 부하 이슈 → 비업무 시간대 백업 스케줄링
  - Stateful 파드(데이터 베이스) PV 백업 시 새로운 환경에서 PV인식 실패 -> 인프라만 복원 후 DB 마이그레이션 작업

---

## 기술 스택 요약

- **Container Orchestration**: Rancher, Kubernetes, Docker  
- **Databases**: Percona MongoDB Sharding, Vitess(MySQL Sharding), TiDB
- **Cloud/Virtualization**: NHN Cloud, Proxmox  
- **IaC & CI/CD**: Ansible, Jenkins  
- **Monitoring/Logging**: Telegraf, VictoriaMetrics, Grafana, Alertmanager, promtail, victorialogs  
- **Backup/Restore**: Velero + Minio  
- **(운영)** MSA, NATS, Harbor  

---

## 요약 및 소감

여러 프로젝트를 진행하면서 **쿠버네티스 운영**, **DB 샤딩**, **IDC 서버 교체**, **클라우드 마이그레이션**, **모니터링·로그 고도화**, **백업·복구 자동화** 등 인프라 전반을 경험할 수 있었습니다. 이미 구축된 **MSA 아키텍처** 환경에서는 **NATS** 메시지 브로커와 **Harbor** 컨테이너 레지스트리를 운영함으로써, 대규모 서비스에서의 메시지 흐름과 이미지 관리의 중요성을 체감했습니다.
