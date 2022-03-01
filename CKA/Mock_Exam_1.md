# CKA Practice with KodeKloud
### Mock Exam -1
>keywords: #staticpod #serviceendpoint

#### static pod

- 정의: API 서버없이 특정 노드의 kubelet 데몬에 의해 관리되는 파드
- 스태틱 파드의 정의는 서버내 특정 디렉토리에 yaml 파일 형태로 존재
- etcd, api-server 등이 대표적인 스태틱 파드
- 디렉토리의 디폴트 값은 `/etc/kubernetes/manifest`
- 삭제는 노드 kubelet의 설정파일`/var/lib/kubelet/config.yaml`에서 staticPodPath 값을 찾은 다음 해당 yaml 파일 삭제
- 스태틱 파드 생성 예시
    
    ```yaml
    kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -oyaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yam
    ```

#### service endpoint

- sevice에서 정의된 selector로 인해 파드의 label에 자동으로 매칭되면서 엔드포인트 생성
- `kubectl get endpoint` 로 엔드포인트 조회 가능