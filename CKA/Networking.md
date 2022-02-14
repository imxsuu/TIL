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
> keywords: #

- kubelet에 관련된 정보 확인
    ```bash
    ps -aux | grep kubelet
    ```

- 모든 CNI 관련 파일들은 `/opt/cni/bin` 경로에 위치한다.
