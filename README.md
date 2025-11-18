아주 핵심만 **“올인원 Falco Stack 구축 전에 반드시 설치해야 하는 것들”**을
딱 필요한 것만 한눈에 보이게 정리해줄게.
(불필요한 옵션/부가설명 전부 제거한 **실전 체크리스트 버전**)

---

# ✅ 올인원 Falco Stack 구축 전 **필수 설치 체크리스트**

아래 항목이 **모두 사전에 준비되어 있어야**
Falco + Promtail + Loki + Prometheus + Grafana + Sidekick 스택이
오류 없이 “한 방에” 올라간다.

---

## 1️⃣ **쿠버네티스(Kubernetes) 클러스터**

필수 조건:

* kubeadm init / join 완료된 클러스터
* kubectl 정상 동작
* 클러스터 노드 Ready 상태

필수 패키지:

* kubeadm
* kubelet
* kubectl

확인:

```bash
kubectl get nodes
```

---

## 2️⃣ **Container Runtime (containerd 또는 Docker)**

Falco가 커널 Syscall(eBPF/Driver)을 캡처해야 하므로 **반드시 필요**.

추천:
✔ containerd (K8s 1.24+ 기본 런타임)

확인:

```bash
ctr version
```

---

## 3️⃣ **CNI 플러그인 (Flannel / Calico)**

Pod 네트워크가 없으면:

* Promtail → Loki 전송 안 됨
* Falco Sidekick → Webhook 전송 안 됨

이미 설치되어 있어야 함.

확인:

```bash
kubectl get pods -n kube-system | grep flannel
```

또는

```bash
kubectl get pods -n kube-system | grep calico
```

---

## 4️⃣ **Helm 3**

**Falco, Loki, Promtail, Sidekick, Grafana 설치의 핵심 도구.**

설치:

```bash
curl -fsSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

확인:

```bash
helm version
```

---

## 5️⃣ **StorageClass (Loki 저장소 필수)**

Loki는 로그 저장을 위해 PVC를 사용함.
기본 StorageClass가 없으면 Loki가 Pending됨.

확인:

```bash
kubectl get storageclass
```

없으면 하나 설치(예: nfs-subdir-external-provisioner).

---

## 6️⃣ **노드 커널 버전(=Falco 드라이버 가능 여부)**

Falco는 Syscall을 잡기 때문에 커널이 너무 낮거나 특수 커널이면 동작 안 함.

권장:

* Linux kernel **5.x** 이상
* 가능하면 eBPF 모드 활성화

확인:

```bash
uname -r
```

---

## 7️⃣ **네트워크 연결 (필수)**

클러스터 내부 Pod 간 통신이 모두 되어야 함:

* Falco → stdout → Promtail → Loki
* Falcosidekick → webhook/Slack
* Prometheus → Falco metrics (/metrics)
* Grafana → Loki/Prometheus

= CNI + KubeDNS 정상 동작 필요

확인:

```bash
kubectl get pods -n kube-system | grep coredns
```

---

# 🟦 선택(있으면 좋음)

## 8️⃣ **Prometheus Operator (kube-prometheus-stack)**

있으면 Grafana/Prometheus 설치가 자동화됨.
없어도 OK.

---

## 9️⃣ **Argo Workflow / SOAR 도구**

Falco Sidekick → SOAR 자동대응을 할 계획이면 필요.

없어도 Falco Stack 구축 자체에는 영향 없음.

---

# 🟩 최종 요약 (딱 7개만 기억하면 됨)

| 구분                    | 필수 여부 | 왜 필요한지                                |
| --------------------- | ----- | ------------------------------------- |
| Kubernetes 클러스터       | ✔ 필수  | Falco/Loki/Promtail/Grafana 모두 K8s 기반 |
| containerd(or Docker) | ✔ 필수  | Falco Syscall/Ebpf 캡처                 |
| CNI (Flannel/Calico)  | ✔ 필수  | Promtail→Loki 등 Pod 간 네트워크            |
| Helm 3                | ✔ 필수  | Falco, Loki Stack, Sidekick 설치        |
| StorageClass          | ✔ 필수  | Loki 저장소 PVC                          |
| 커널 버전                 | ✔ 필수  | Falco 드라이버/eBPF 동작                    |
| KubeDNS(CoreDNS)      | ✔ 필수  | 서비스 이름 기반 통신                          |

**이 7개만 갖춰지면 ‘올인원 Falco Stack’은 100% 올라간다.**
