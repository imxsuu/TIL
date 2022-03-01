# CKA Practice with KodeKloud
### Mock Exam -3
>keywords: #permisstion #secret

#### service account
- 생성 순서: serviceaccount -> clusterrole -> clusterrolebinding
  ```bash
  kubectl create serviceaccount pvviewer
  kubectl create clusterrole pvviewer-role --verb=list --resource=pv
  kubectl create clusterrolebinding pvviewer-role-binding --clusterrole=pvviewer-role --serviceaccount=default:pvviewer
  ```

#### multi container environment
- `kubectl run multi-pod --image=nginx --dry-run=client -o yaml > multi-pod.yaml`
  ```yaml
  env:
  - name: name
    value: alpha
  ```
  
#### pod securtiy policy
- runAsUser: 1000 & fsGroup: 2000인 파드 생성
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    name: non-root-pod
    spec:
    securityContext:
        runAsUser: 1000
        fsGroup: 2000
    containers:
    - image: redis:alpine
      name: non-root-pod
    ```
- 검증 방법
    - `kubectl exec -it non-root-pod -- sh` 실행하여 컨테이너 접속
    - `/data $ id`와 같이 id를 입력하면 다음과 같은 결과 출력
        - `uid=1000 gid=0(root) groups=2000`

#### network policy
- 파드와 서비스가 연결되기 위해서 파드의 label와 서비스의 selector를 일치
- 네임스페이스에 해당하는 NetworkPolicy를 수정하지 않으면서 특정 파드만의 통신을 설정하는 방법
    - NetworkPolicy의 podSelector를 사용

#### taints and toleration
- taint 설정 확인 방법
    - `kubectl taint nodes node01 | grep -i taint`

### 파일에서 문자열 한꺼번에 바꾸는 명령어
- `sed -i 's/kube-contro1ler-manager/kube-controller-manager/g' kube-controller-managet.yaml`

