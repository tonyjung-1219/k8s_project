최신버전 falco-stack-all-in-one-gra.yaml

직전버전 falco-stack-all-in-one-ver5.yaml

-올인원파일에 그라파나는 포함이 안되어있으므로 별도로 monitoring 네임스페이스에 설치 요망 / 그라파나 대시보드는 UI가 아닌 필히 yaml 파일로 구성.
-Loki 데이터는 emptyDir라서 Pod 재시작 시 전부 날아가는 구조이므로 나중에 PersistentVolumeClaim을 따로 만든 뒤, 여기 storage를 PVC로 바꾸면 됨.

*저번에 올려둔 필수설치사항(README)랑 개별적으로 구분해둔 YAML파일들 왜인지모르겠지만 다 날아감.
