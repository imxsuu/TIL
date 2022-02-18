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

### Ingress Networking
> keywords: #ingress #service

- Ingress는 쿠버네티스에 빌트인된 7계층 로드밸런서

- 하나의 외부 URL을 이용해 클러스터 내 다른 서비스로 라우팅, SSL Security 가능

- Ingress를 사용하기 위해서는 `Ingress Controller`가 필수적
    - istio, nginx, haproxy, traefik 등... 하나를 선택하여 배포 (빌트인되어있지않음)
    - configmap을 생성하여 keep-alive threshold, ssl setting, session timeout 등을 설정

- Ingress Controller에서 사용하기 위해 설정하는 Rules와 Configuration들은 `Ingress Resources`
    - URL을 기반으로 각 `Service`마다 라우팅 패스를 다르게 설정 가능


- `Ingress Controller`를 외부로 노출하기 위해 `Service`를 정의하고 `label-selector`를 통해 링크

- Ingress는 클러스터를 모니터링하는 기능을 갖고 있고 이를 위해 `ServiceAccount`가 필요

- ServiceAccount에는 퍼미션 권한이 필요하고 이를 위해 `Roles`와 `RolesBinding`이 필요

- 정리하자면, Ingress를 위해서 최소한으로 Deployment, Service, Configmap, Auth(ServiceAccount)가 필요

- Ingress로 들어온 요청은 다이렉트로 파드에게 가지않고 `Service`로 전달

