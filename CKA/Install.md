# CKA Practice with KodeKloud
### Installation using Kubeadm
> keywords:

- kubeadm init을 통해 클러스터를 만들고 난 후 빼먹지 말고 꼭 실행
    ```bash
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```

- root 계정이면 아래의 명령도 함께 진행
    ```bash
    export KUBECONFIG=/etc/kubernetes/admin.conf
    ```
