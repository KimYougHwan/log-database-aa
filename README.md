# log-database-aa
로그 수집 데이터 베이스 기술 검토

1. 아키텍처 타당성 분석
Manticore Search(실시간 모니터링 DB)

장점:

 초고속 로그 분석 성능(Elasticsearch 대비 10.09배 빠른 로그 처리)
1GB 메모리 환경에서도 효율적 운영 가능
Vector.dev 연동을 통한 실시간 로그 수집 파이프라인 구성 가능
12시간 단기 데이터에 최적화된 메모리 기반 처리

단점:

 장기 데이터 저장 시 스토리지 비용 증가
복잡한 분석 쿼리 처리에 제한적

Apache Doris(대용량 처리)

장점:

 MPP 아키텍처 기반 PB급 데이터 처리
컬럼너 스토리지로 고압축률(평균 5~10:1)
실시간 배치 처리 동시 지원
MySQL 프로토콜 호환으로 BI 연동 용이

단점:

 실시간 검색 성능은 Manticore 대비 떨어짐
인덱스 관리가 상대적으로 복잡

2. 데이터 이관 기술 검토 
기술	처리속도	자동화 수준	복잡도	비용	적합 시나리오
X2Doris	★★★★☆	★★★★★	★☆☆☆☆	무료	대용량 일괄 이관
Kafka	★★★★★	★★★☆☆	★★★☆☆	중간	실시간 스트리밍
Spark	★★★★☆	★★☆☆☆	★★★★☆	고가	복잡 변환 작업 필요 시
FluentBit	★★★☆☆	★★★★☆	★★☆☆☆	무료	지속적 증분 이관

3. 이관 기술 검토

실시간 스트리밍: Vector.dev → Manticore → Kafka → Doris (지금 필요 할까 생각 중)
배치 이관: Manticore Backup → X2Doris → Doris
긴급 마이그레이션: FluentBit 직접 연동 (지금 필요 할까 생각 중)

4. 대체 기술 비교 테스트 및 비용 예상

항목	Manticore+Doris	Elasticsearch+ClickHouse	Loki+BigQuery
초당 쿼리 처리량	150K QPS	90K QPS	50K QPS
데이터 압축률	6:1	3:1	4:1
유지 관리 비용	연간 $15K	연간 $45K	연간 $60K
학습 곡선	중간	높음	낮음
