# Quick Start Guide - 빠른 시작 가이드

## Prerequisites - 사전 요구사항

1. VMware Desktop (Workstation or Fusion) installed - VMware Desktop 설치 완료
2. Vagrant installed - Vagrant 설치 완료
3. Vagrant VMware plugin installed - Vagrant VMware 플러그인 설치:
   ```bash
   vagrant plugin install vagrant-vmware-desktop
   ```

## Quick Setup - 빠른 설정

### 1. Start the Environment - 환경 시작

```bash
cd vagrant
vagrant up --provider=vmware_desktop
```

이 명령어는 다음을 수행합니다:
- Ubuntu 22.04 VM을 VMware에 생성 (8GB RAM, 4 CPU)
- KVM과 QEMU를 컴파일 및 설치
- Windows 10 VM을 위한 환경 구성
- 악성코드 분석 도구 설치

**참고**: 초기 설정은 30분~1시간 정도 소요될 수 있습니다.

### 2. Access the VM - VM 접속

```bash
vagrant ssh
```

### 3. Verify Setup - 설정 확인

```bash
# Check KVM module
lsmod | grep kvm

# Check libvirt
sudo virsh list --all

# Check storage location
ls -la /home/sec/
```

### 4. Start Windows 10 - Windows 10 시작

Windows 10 VM을 처음 시작하기 전에 이미지를 빌드하거나 다운로드해야 합니다.

#### Option A: Build from ISO (권장하지 않음 - 시간 소요)
```bash
cd /home/sec/templates
packer build -var-file windows_10.json windows_10.json
```

#### Option B: Use pre-built image (더 빠름)
미리 빌드된 Windows 10 qcow2 이미지가 있다면:
```bash
sudo cp /path/to/windows10.qcow2 /home/sec/vms/
sudo chown vagrant:vagrant /home/sec/vms/windows10.qcow2
```

#### Start the VM
```bash
start-win10
```

### 5. Create Clean Snapshot - 클린 스냅샷 생성

```bash
# Wait for Windows to fully boot, then:
sudo virsh snapshot-create-as win10 clean_state "Clean Windows 10 before malware analysis"
```

### 6. Access Windows 10 - Windows 10 접속

#### Via VNC:
```bash
# Get VNC display number
sudo virsh vncdisplay win10

# Output will be something like: :0
# Connect using VNC client to: <host-ip>:5900
```

#### Via Console (text mode):
```bash
sudo virsh console win10
```

## Daily Workflow - 일일 작업 흐름

### Before Analysis - 분석 전

```bash
# 1. Start clean VM
reset-win10

# 2. Start network monitoring
monitor-traffic &
```

### During Analysis - 분석 중

1. Windows 10 VM에 악성코드 샘플 복사
2. VNC로 접속하여 악성코드 실행
3. 행동 관찰 및 기록

### After Analysis - 분석 후

```bash
# 1. Stop network monitoring (Ctrl+C)

# 2. Save analysis results
cp /home/sec/analysis/capture_*.pcap /home/sec/reports/

# 3. Reset to clean state
reset-win10

# Or manually:
sudo virsh destroy win10
sudo virsh snapshot-revert win10 clean_state
sudo virsh start win10
```

## Useful Commands - 유용한 명령어

### VM Management
```bash
vm-list                    # List all VMs
start-win10                # Start Windows 10
stop-win10                 # Shutdown Windows 10
reset-win10                # Reset to clean state
```

### Network Monitoring
```bash
monitor-traffic            # Capture all network traffic
monitor-dns                # Monitor DNS queries
monitor-http               # Monitor HTTP/HTTPS traffic
```

### Snapshots
```bash
snapshot-win10 before_run  # Create snapshot
vm-snapshot-list win10     # List snapshots
vm-snapshot-revert win10 before_run  # Revert to snapshot
```

## Troubleshooting - 문제 해결

### VM won't start - VM이 시작되지 않음
```bash
sudo systemctl restart libvirtd
sudo virsh net-start default
```

### Permission denied errors - 권한 오류
```bash
sudo chmod 666 /dev/kvm
sudo usermod -aG libvirt,kvm,libvirt-qemu vagrant
```

### Rebuild the environment - 환경 재구성
```bash
vagrant destroy -f
vagrant up --provider=vmware_desktop
```

## Storage Locations - 저장 위치

| Purpose | Location |
|---------|----------|
| VM Images | /home/sec/vms/ |
| Malware Samples | /home/sec/samples/ |
| Analysis Workspace | /home/sec/analysis/ |
| Reports | /home/sec/reports/ |
| Packer Templates | /home/sec/templates/ |

## Next Steps - 다음 단계

1. Install analysis tools in Windows 10 VM
   - Process Monitor
   - Process Explorer
   - Wireshark
   - IDA Free
   - x64dbg

2. Configure network isolation properly

3. Set up automated snapshot management

4. Create analysis report templates

5. Install additional Linux tools:
   ```bash
   sudo apt install volatility3 yara radare2
   ```

## Support - 지원

For more information, see:
- `MALWARE_ANALYSIS_SETUP.md` - Detailed English documentation
- `악성코드_분석_설정_가이드.md` - Detailed Korean documentation
- [KVM-VMI Documentation](https://kvm-vmi.github.io/kvm-vmi/master/)
