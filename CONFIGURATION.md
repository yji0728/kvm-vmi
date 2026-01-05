# Configuration Summary - 구성 요약

이 문서는 이 리포지토리의 최적화된 설정을 요약합니다.

## System Configuration - 시스템 구성

### Host Environment - 호스트 환경
- **Hypervisor**: VMware Desktop (Workstation/Fusion)
- **Host OS**: Ubuntu 22.04 LTS
- **Memory**: 8GB
- **CPUs**: 4 cores
- **Nested Virtualization**: Enabled
- **Hardware Virtualization Extensions**: Required (Intel VT-x or AMD-V)

### Guest VM (Windows 10) - 게스트 VM
- **OS**: Windows 10 (Enterprise Evaluation)
- **Memory**: 4GB (4096 MB)
- **vCPUs**: 2 cores
- **Disk Size**: 100GB (qcow2 format, dynamically allocated)
- **Disk Interface**: IDE
- **Network**: e1000
- **Graphics**: QXL with VNC access

## Storage Configuration - 저장소 구성

### Directory Structure - 디렉토리 구조
```
/home/sec/
├── vms/              # VM disk images (.qcow2 files)
├── templates/        # Packer build templates
├── samples/          # Malware samples storage
├── analysis/         # Analysis workspace
└── reports/          # Analysis reports
```

### Libvirt Pool
- **Pool Name**: default
- **Pool Type**: directory
- **Target Path**: `/home/sec/vms`
- **Owner**: vagrant:vagrant
- **Permissions**: rwxr-xr-x

## Network Configuration - 네트워크 구성

### Default Network
- **Network Name**: default
- **Bridge**: virbr0
- **Network**: 192.168.122.0/24
- **DHCP Range**: 192.168.122.2 - 192.168.122.254

### Analysis Network (Isolated)
- **Network Name**: analysis
- **Bridge**: virbr1
- **Network**: 192.168.100.0/24
- **DHCP Range**: 192.168.100.2 - 192.168.100.254

## Enabled VMs - 활성화된 VM

```ruby
enabled_vms = {
    'winxp': false,
    'win7': false,
    'win10': true,    # ✓ Windows 10 for malware analysis
    'ubuntu': false,
}
```

## VMware Provider Settings - VMware 프로바이더 설정

```ruby
config.vm.provider "vmware_desktop" do |vmware|
    vmware.cpus = 4
    vmware.memory = 8192              # 8GB
    vmware.gui = true
    vmware.vmx["vhv.enable"] = "TRUE" # Nested virtualization
    vmware.vmx["nestedpaging"] = "TRUE"
    vmware.vmx["vpmc.enable"] = "TRUE"
end
```

## Installed Tools - 설치된 도구

### Host (Ubuntu) Tools
- **Development**: git, build-essential, gcc, cmake
- **Debugging**: gdb, strace, ltrace
- **Network Analysis**: tcpdump, wireshark
- **Utilities**: vim, htop, binutils
- **Virtualization**: qemu, kvm, libvirt
- **VMI**: libvmi with KVM support

### Analysis Tools
- KVM-VMI for virtual machine introspection
- QEMU with introspection patches
- Libvirt for VM management
- Packer for VM image building

## Packer Configuration - Packer 구성

### Windows 10 Build Settings
```json
{
    "disk_size": 102400,        # 100GB
    "cpus": 2,
    "memory": 4096,             # 4GB
    "communicator": "winrm",
    "winrm_timeout": "4h",
    "disk_compression": true
}
```

## Libvirt Domain Template - Libvirt 도메인 템플릿

### Key Settings
- **Domain Type**: kvm
- **vCPUs**: 2
- **CPU Model**: core2duo with monitor feature
- **Memory Balloon**: virtio
- **Graphics**: VNC (0.0.0.0:auto)
- **Disk Bus**: IDE
- **Console**: Serial + PTY

### Introspection Configuration
```xml
<qemu:commandline>
    <qemu:arg value='-chardev'/>
    <qemu:arg value='socket,path=/tmp/introspector,id=chardev0,reconnect=3'/>
    <qemu:arg value='-object'/>
    <qemu:arg value='introspection,id=kvmi,chardev=chardev0'/>
</qemu:commandline>
```

## Ansible Roles - Ansible 역할

### Playbook 1 (System Setup)
- System upgrade
- KVM installation and configuration
- Useful tools installation

### Playbook 2 (VM Setup)
- QEMU compilation and installation
- LibVMI compilation and installation
- VM image building/downloading
- Libvirt configuration
- Malware analysis environment setup

## Environment Variables - 환경 변수

```bash
MALWARE_ANALYSIS_HOME=/home/sec/analysis
MALWARE_SAMPLES=/home/sec/samples
MALWARE_REPORTS=/home/sec/reports
VM_STORAGE=/home/sec/vms
```

## Shell Aliases - 쉘 별칭

### VM Management
- `vm-start`, `vm-stop`, `vm-destroy`, `vm-list`
- `start-win10`, `stop-win10`, `reset-win10`
- `vm-console`, `vm-vnc`

### Snapshot Management
- `vm-snapshot`, `vm-snapshot-list`, `vm-snapshot-revert`
- `snapshot-win10`

### Network Monitoring
- `monitor-traffic`, `monitor-dns`, `monitor-http`

## Security Settings - 보안 설정

### AppArmor Profiles
- Custom profiles for libvirtd
- Custom profiles for libvirt-qemu
- QEMU binary path: `/usr/local/bin/qemu-system-x86_64`

### User Groups
User `vagrant` is member of:
- kvm (access to /dev/kvm)
- libvirt (libvirt management)
- libvirt-qemu (VM memory access)

### File Permissions
- `/dev/kvm`: 0666 (rw-rw-rw-)
- `/home/sec`: vagrant:vagrant
- `/home/sec/vms`: vagrant:vagrant

## Performance Optimizations - 성능 최적화

1. **Nested Paging**: Enabled for better VM performance
2. **Virtual Performance Counters**: Enabled
3. **Disk Compression**: Enabled for space efficiency
4. **NFS v4**: Used for shared folders (faster than v3)
5. **Multiple vCPUs**: 2 CPUs for Windows 10 for better responsiveness
6. **Large RAM**: 4GB for Windows 10 to handle malware execution

## Known Limitations - 알려진 제한사항

1. VMware Workstation/Fusion required (not VMware Player)
2. Hardware virtualization must be enabled in BIOS
3. Initial setup takes 30-60 minutes
4. Windows 10 ISO must be provided or downloaded
5. Vagrant VMware plugin requires license (commercial)

## Customization - 커스터마이징

To change settings, edit:
- `vagrant/Vagrantfile` - VM provider settings
- `vagrant/ansible/roles/vm/tasks/main.yml` - VM configurations
- `vagrant/ansible/roles/vm/files/templates/windows_10.json` - Packer settings
- `vagrant/ansible/playbook_1.yml` - System packages

## Version Information - 버전 정보

- Vagrant Box: generic/ubuntu2204 v4.3.12
- Packer: 1.6.0
- Recommended VMware: 16.x or later
- Recommended Vagrant: 2.3.x or later

---

Last Updated: 2026-01-05
Configuration Version: 1.0
