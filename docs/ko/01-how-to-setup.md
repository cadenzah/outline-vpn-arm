# How to Setup
Raspi 4에서 Outline VPN 서비스를 구축할 때 필요한 절차들을 정리. 개인 용도의 정리 문서이므로 양해 요망.

## Prerequisite

### H/W
- Raspi 4 (ARM64)
  - [저소음 쿨러 + 케이스](https://ko.aliexpress.com/item/4000288119233.html): 기본 쿨러 소음이 상당히 크므로, 필요시 구입하면 좋다
- SD Card (32GB를 사용) - OS 설치용

### S/W
- Ubuntu 18.04.4(aarch64): 18.04 버전대이면 모두 문제없이 실행되는 것으로 보임
- Docker
- [Raspi 전용으로 포팅된 Outline VPN Server 이미지 베이스](https://github.com/ken1029/outline-vpn-arm): Credit to [ken1029](https://github.com/ken1029): Node.js 기반

## OS 환경 초기화

### 1. OS 설치
- Raspi용 이미지 중 [Ubuntu 18.04.4(aarch64)](http://old-releases.ubuntu.com/releases/18.04.4/) 다운로드
- SD카드에 이미지 굽기: [macOS 기준 안내](https://lucidmaj7.tistory.com/196)
  - 이미지 입히는 중 `Ctrl + T` 누르면 진행 상황 확인 가능
- 최초 부팅 후 계정 설정 필요 - 아래는 기본값
  - user: `ubuntu`
  - pass: `ubuntu` => 변경 필요

### 2. 네트워크 환경 설정

- 무선 연결에 사용할 AP 정보 바탕으로 `/etc/netplan/50-cloud-init.yaml` 파일을 아래와 같이 수정
- 고정 IP로 사용할 수 있는 사설 IP 대역을 확인 및 선정한 뒤 아래와 같이 수정
  - 서버 머신 리부트하여도 IP 고정

> - 수정시 `sudo` 필요
> - 아래 예시는 사설 IP 대역이 `192.168.1.100 ~ 199` 까지 허용된다고 가정
> - `nameservers`는 라우터 단에서 처리된다면 옵션 추가 하지 않아도 정상 작동

```yaml
network:
    version: 2
    ethernets:
        eth0:
            dhcp4: false
            optional: true
            addresses:
                - 192.168.1.198/24
            gateway4: 192.168.1.1
            
    # add wifi setup information here ...
    wifis:
        wlan0:
            dhcp4: false
            optional: true
            access-points:
                "YOUR-SSID-NAME":
                    password: "YOUR-NETWORK-PASSWORD"
            addresses:
                - 192.168.1.199/24
            gateway4: 192.168.1.1
```

위 내용 저장한 뒤, 설정 적용하고 리부트

```bash
$ sudo netplan --debug apply
$ sudo reboot
```

### 3. 필수 어플리케이션 설치

- `vim`, `git`, `docker`
- Docker 설치시 `sudo apt-get install docker.io` 로 설치
  - cf) [Docker 설치시 인증서 관련 이슈](https://github.com/Jigsaw-Code/outline-server/issues/951)

### 4. 서버 구동
```bash
# 도메인은 선택사항으로, 입력하지 않으면 공인 IP로 URL이 설정
$ sudo ./INSTALL-ARM64 <현재_서버_도메인>
```

위 스크립트는 아래 작업을 순차적으로 수행:

- [Raspi 전용 Outline VPN Server 이미지](https://github.com/ken1029/outline-vpn-arm)로 Outline VPN 서버 인스턴스 실행
- VPN 서버에 설정된 2개 포트에 대한 방화벽 설정(`ufw`) 적용
  - 최초로 서버 실행하는 경우, `ufw` 활성화
  - 서버를 재실행하는 경우, 기존에 설정된 방화벽 포트 설정을 제거
- VPN 서버에 설정된 포트 정보(``port_config`), Access 정보(`access.txt`)를 별도 파일로 저장

### (Optional) 5. 라우터 설정

- 네트워크 설정에 따라, 맨 앞 공유기를 브릿지 모드로 설정
- 2번 단계에서 설정된 IP에 따라 포트포워딩
- 4번 단계에서 설정된 포트에 따라 포트포워딩

## Troubleshooting

### `apt update`가 안 될 때
```bash
$ sudo killall apt apt-get

# 진행중인 프로세스가 없다고 나올 경우
$ sudo rm /var/lib/apt/lists/lock
$ sudo rm /var/cache/apt/archives/lock
$ sudo rm /var/lib/dpkg/lock*

# 그 다음 아래 실행
$ sudo dpkg --configure -a
$ sudo apt update
```

### Outline 서버 초기화 중 'Generating TLS certificate...' 에서 멈췄을 때
openssl 설치 후 다시 실행

## References
- [Ubuntu 설치 및 초기화](https://jdselectron.tistory.com/114)
- [구버전 Ubuntu 이미지](http://old-releases.ubuntu.com/releases/18.04.4/)
- [macOS SD카드에 이미지 입히기](https://lucidmaj7.tistory.com/196)
- [Ubuntu에서 Docker 설치시 인증서 관련 이슈](https://github.com/Jigsaw-Code/outline-server/issues/951)
- [Pass args for script when going thru pipe](https://stackoverflow.com/questions/14693100)
