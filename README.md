# Kubernet


- Sebelum melakukan instalasi kubernetes diwajibkan terlebih dahulu untuk NON-Aktifkan firewall dan SWAP
  
      ufw disable

  ![image](https://user-images.githubusercontent.com/40049149/191898965-a7b88d56-b36a-4383-98e2-14db8791b733.png)

      swapoff -a; sed -i '/swap/d' /etc/fstab

  ![image](https://user-images.githubusercontent.com/40049149/191899042-eca78148-13bc-428d-9e0b-c24c7af3971f.png)

- kemudian kita perlu juga update kernel

      cat >>/etc/sysctl.d/kubernetes.conf<<EOF
      net.bridge.bridge-nf-call-ip6tables = 1
      net.bridge.bridge-nf-call-iptables = 1
      EOF

- Kemudian Restart system

      sysctl --system

  ![image](https://user-images.githubusercontent.com/40049149/191899398-90c27601-bac6-4318-99a9-3f4c83b2f836.png)

- Konfigurasi docker daemon

      cat <<EOF | sudo tee /etc/docker/daemon.json
      {
      "exec-opts": ["native.cgroupdriver=systemd"],
      "log-driver": "json-file",
      "log-opts": {
      "max-size": "100m"
      },
      "storage-driver": "overlay2"
      }
      EOF

  ![image](https://user-images.githubusercontent.com/40049149/191899505-a9204a60-12bd-4a65-9c04-19f546cfbbac.png)

- Restart Docker

      sudo systemctl enable docker
      sudo systemctl daemon-reload
      sudo systemctl restart docker

  ![image](https://user-images.githubusercontent.com/40049149/191899624-51b248c6-4e85-4005-b40e-0ef0d2bcbf82.png)

- Instalasi Kubernetes kubelet kubeadm kubectl

      sudo apt-get update && sudo apt-get install -y apt-transport-https curl
      curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
      cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
      deb https://apt.kubernetes.io/ kubernetes-xenial main
      EOF
      sudo apt-get update

  ![image](https://user-images.githubusercontent.com/40049149/191902202-fec46f5f-5d4e-44d1-9cf5-644a6cfb9ebb.png)

      sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni

  ![image](https://user-images.githubusercontent.com/40049149/191902265-73a2056d-ec38-4cc5-891c-ea619827dff4.png)

      sudo systemctl daemon-reload
      sudo systemctl restart kubelet

  ![image](https://user-images.githubusercontent.com/40049149/191902323-f3494584-ee4e-46b8-8220-ae1e97d6c5c5.png)

- Konfigurasi kubeadm

      sudo sudo kubeadm init

  ![image](https://user-images.githubusercontent.com/40049149/191902387-16f3e2db-6fc3-408c-bbdd-60c41342ccaa.png)

- Konfigurasi kubernetes agar dapat menjalankan perintah

mkdir -p .kube
sudo cp -i /etc/kubernetes/admin.conf .kube/config
sudo chown $(id -u):$(id -g) .kube/config


