# CKA Practice with KodeKloud
### Explore Enviornment
> keywords: #arp 

- 다른 노드의 Mac Address 확인
    ```bash
    arp $NODENAME
    ```

- 특정 인터페이스에 관한 정보 확인
    ```bash
    ip link show $INTERFACENAME
    ```

- 외부로 나가는 기본 게이트웨이 주소 확인
    ```bash
    ip route show default
    ```

- 특정 파드가 사용하고 있는 포트번호 확인
    ```bash
    netstat -nplt | grep $PODNAME
    ```

- 특정 파드의 포트가 여러 개일 때, 더 양호한 연결상태 확인
    ```bash
    netstat -anp | grep $PODNAME | grep $PORTNUMBER | wc -l
    ```

### CNI Weave
> keywords: #cni #weave

- kubelet에 관련된 정보 확인
    ```bash
    ps -aux | grep kubelet
    ```

- 모든 CNI 관련 파일들은 `/opt/cni/bin` 경로에 위치

- CNI 관련 bridge 정보는 `/etc/cni/net.d` 경로에 위치

### Service Networking
> keywords: #kube-proxy #daemonsets

- 노드의 IP 대역은 `ip addr` 명령을 통해 eth0 정보 확인

- 파드의 IP 대역은 CNI를 통해서 확인
    - kube-system에 떠있는 CNI 관련 파드의 로그 확인
    - `ipalloc-range`가 파드가 할당되는 IP 대역

### Coredns in Kubernetes
> keywords: #coredns #configmap

- CoreDNS 설정은 `/etc/coredns/Corefile`에 정의

- Corefile은 파드에 configmap으로 전달

- 기본적으로 kube-dns라는 이름으로 서비스가 배포되고, 이 서비스의 주소가 파드에 nameserver로 구성

- kubelet config file을 보면 DNS 서버 주소 확인 가능 `/var/lib/kubelet/config.yaml`
