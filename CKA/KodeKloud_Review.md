# KodeKloud Review

---

## Install(using kubeadm)

[Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

- 클러스터 구성을 위한 kubeadm 설치
    - iptables(방화벽)가 브리지된 트래픽을 보게 하기 → br_netfilter 모듈 활성화
    - `br_netfilter`: kubernetes 포드 간 통신을 위한 virtual LAN 트래픽을 사용하는데 필요한 모듈
    - `kubeadm`: kubernetes 클러스터를 구축하기 위해 사용하는 툴
    - `kubelet`: 클러스터의 모든 머신에서 실행되며 Pod 및 컨테이너 시작 등의 작업을 수행하는 구성 요소
    - `kubectl`: 클러스터와 통신하는 커맨드라인 인터페이스 유틸

[Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

- controlplane 초기화
    - `kubeadm init`
        - `--apiserver-advertise-address`: 특정 마스터 노드의 API Server 주소를 설정할 때 사용,  마스터 노드에서 ifconfig의 결과로 나온 eth0의 주소
        - `--control-plane-endpoint`: 마스터 노드를 다중으로 구성하였을 때, 모든 마스터 노드들에 대한 공유 엔드 포인트 설정
        - `--apiserver-cert-extra-sans`: API 서버 제공 인증서에 사용할 선택적 추가 주체 대체 이름, controlplane 또는 마스터 노드의 IP
        - `--pod-network-cidr`: Pod 네트워크를 설정할 때 사용, ex) 10.244.0.0/16(flannel)

## Troubleshooting

### a**pplication failure (using DB)**

[Service](https://kubernetes.io/docs/concepts/services-networking/service/)

- svc 제대로 연결되어 있지 않은 문제
    1. svc name이나 selector가 제대로 명시되어 있는지 확인
    2. svc port와 targetPort가 일치하는지 확인
    3. deploy나 pod의 env에 user와 password가 제대로 명시되어 있는지 확인
    4. nodePort가 제대로 명시되어 있는지 확인
- user에게 DB 접근 권한이 없는 경우
    1. user를 root로 변경
    2. DB 내부에서 user 생성(k8s 관련 해결방법이 아님)

### control plane failure

- static pod (path: /etc/kubernetes/manifests/)
    - etcd.yaml
    - kube-apiserver.yaml
    - kube-controller-manager.yaml
    - kube-scheduler.yaml
- 발생할 수 있는 이슈는 static pod에서의 오류
    - kubectl get pod
    - kubectl describe pod
    - kubectl logs
- static pod에서 특정 파일을 찾을 수 없다는 에러일 때, hostPath가 제대로 마운트되었는지 확인

### worker node failure

- 발생할 수 있는 이슈는 kubelet에서의 오류
    1. `kubectl get nodes` 명령을 통해 노드들의 상태 확인
    2. 문제가 있는 노드로 ssh 접속
    3. `systemctl status kubelet` 명령을 통해 kubelet의 상태 확인
    - Notreday상태의 worker node에서 kubelet이 죽어있는 경우 - inactive(dead)
        - `systemctl start kubelet` 명령을 통해 kubelet 실행
    - Notreday상태의 worker node에서 kubelet이 활성화되고 있는 경우 - activating(auto-restart)
        - `journalctl -u kubelet` 명령을 통해 kubelet 로그 확인
        - `/var/lib/kubelet/config.yaml` 에서 로그와 관련된 코드 수정(ca.crt 파일명)
        - `systemctl restart kubelet` 명령을 통해 kubelet 재실행
    - Notreday상태의 worker node에서 kubelet이 동작하고 있는 경우 - active(running)
        - `journalctl -u kubelet` 명령을 통해 kubelet 로그 확인
        - `/etc/kubernetes/kubelet.conf` 에서 로그와 관련된 코드 수정(포트번호)
        - `systemctl restart kubelet` 명령을 통해 kubelet 재실행

### network failure

- 발생할 수 있는 이슈는 network plugin 이슈와 kube-porxy 이슈
    - network plugin이 제대로 설치되어 있지 않은 경우
    - kube-proxy에서의 오류가 발생한 경우
        - kube-proxy의 로그 확인 후 관련된 오류 수정
            - `/var/lib/kube-proxy/config.conf`
            - kube-proxy는 데몬셋에 의해 실행
            - kube-proxy 데몬셋의 설정과 configmap의 kube-proxy 설정이 동일한지 확인
                - `kubectl describe cm -n kube-system kube-proxy`