최신버전 falco-stack-all-in-one-gradash.yaml   ->  falco-promtail-loki-grafana-grafana dashboard까지 한 번에 생성

직전버전 falco-stack-all-in-one-ver5.yaml

참고사항 Loki 데이터는 emptyDir라서 Pod 재시작 시 전부 날아가는 구조이므로 나중에 PersistentVolumeClaim을 따로 만든 뒤, 여기 storage를 PVC로 바꾸면 됨.


*저번에 올려둔 필수설치사항(README)랑 개별적으로 구분해둔 YAML파일들 왜인지모르겠지만 다 날아감.
