# CKA Practice with KodeKloud
### Persistent Volume Claims
> keywords: #pv #pvc

- pv와 pvc의 accessModes가 동일해야 바인딩된다. `accessModes`는 다음과 같다.
    - `ReadWriteOnce`: 하나의 노드에서 해당 볼륨이 읽기-쓰기로 마운트 될 수 있다. ReadWriteOnce 접근 모드에서도 파드가 동일 노드에서 구동되는 경우에는 복수의 파드에서 볼륨에 접근할 수 있다.
    - `ReadOnlyMany`: 볼륨이 다수의 노드에서 읽기 전용으로 마운트 될 수 있다.
    - `ReadWriteMany`: 볼륨이 다수의 노드에서 읽기-쓰기로 마운트 될 수 있다.
    - `ReadWriteOncePod`: 볼륨이 단일 파드에서 읽기-쓰기로 마운트될 수 있다. 전체 클러스터에서 단 하나의 파드만 해당 PVC를 읽거나 쓸 수 있어야하는 경우 ReadWriteOncePod 접근 모드를 사용한다. 이 기능은 CSI 볼륨과 쿠버네티스 버전 1.22+ 에서만 지원된다.  
    > 볼륨이 여러 접근 모드를 지원하더라도 한 번에 하나의 접근 모드를 사용하여 마운트할 수 있다. 

- 볼륨은 다음과 같은 `Phase(단계)` 중 하나다.
    - `Available`: 아직 클레임에 바인딩되지 않은 사용할 수 있는 리소스
    - `Bound`: 볼륨이 클레임에 바인딩됨
    - `Released`:클레임이 삭제되었지만 클러스터에서 아직 리소스를 반환하지 않음
    - `Failed`: 볼륨이 자동 반환에 실패함

- 파드에서 사용 중인 pvc를 삭제하면 `Terminating` 상태에 빠진다. pvc가 더 이상 파드에서 사용되지 않을 때까지 연기된다. 또한 pvc에 바인딩된 pv를 삭제하면 즉시 삭제되지 않는다. 더 이상 pvc에 바인딩되지 않을 때까지 연기된다.

- Retain(보존): Retain 반환 정책은 리소스를 수동으로 반환할 수 있게 한다. pvc가 삭제되면 pv는 여전히 존재하며 `Released` 단계로 분류된다. 그러나 이전  데이터가 여전히 남아 있기 때문에 다른 요청에 대해서는 아직 허용할 수 없다. 다음 단계에 따라 pv를 수동으로 반환할 수 있다.
    1. pv를 삭제한다. pv가 삭제된 후에도 관련 스토리지 자산이 존재한다. 관련 스토리지 자산의 데이터를 수동으로 삭제한다.
    2. 연결된 스토리지 자산을 수동으로 삭제한다.
    3. 동일한 스토리지 자산을 재사용하려는 경우, 동일한 스토리지 자산 정의로 새 pv을 생성한다.

### Storage Class
> keywords: #storageclass 

- pv에서는 공간의 크기를 지정해줘야하는 단점이 있다. 이를 `Static Provisioning`이라고 한다. 

- `Dynamic Provisioning`방식은 pvc를 선언하면 자동으로 관련된 pv와 그에 대응하는 Disk가 생성된다. `StorageClass`는 `Dynamic Provisioning`에 속한다. 
