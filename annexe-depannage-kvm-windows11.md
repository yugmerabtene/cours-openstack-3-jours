# Annexe — Dépannage KVM sous Windows 11

Cette annexe traite l’absence de `/dev/kvm` dans la VM `lab-openstack` lorsque
l’hôte est sous Windows 11. Elle complète l’exercice 0.2 du
[jour 1](jour-1.md) et la section « Virtualisation imbriquée ».

## Symptômes

- `ls -l /dev/kvm` renvoie `No such file or directory`.
- `kvm-ok` conclut `KVM acceleration can NOT be used`.
- Les instances mettent plusieurs minutes à passer à `ACTIVE`.
- `openstack hypervisor list` affiche `QEMU` : **ce champ ne prouve rien**, le
  pilote libvirt affiche `QEMU` même lorsque l’accélération KVM est active.

## Cause

Windows 11 réserve VT-x/AMD-V lorsqu’un hyperviseur Microsoft est actif. Les
coupables les plus fréquents :

- Hyper-V (`Microsoft-Hyper-V-All`) ;
- WSL2 (`VirtualMachinePlatform`) ;
- Windows Sandbox et Containers ;
- Virtualization-Based Security (VBS) : **Intégrité de la mémoire**,
  Credential Guard, Device Guard ;
- Windows Hypervisor Platform (WHP), utilisé par VirtualBox en repli.

Quand WHP est actif, VirtualBox fonctionne mais ne peut pas exposer la
virtualisation imbriquée à la VM : KVM reste indisponible.

## 1. Diagnostic rapide

Dans PowerShell **administrateur** :

```powershell
# Un hyperviseur est-il actif ?
systeminfo | Select-String "Hyper-V"

# Le processeur expose-t-il VT-x/AMD-V au firmware ?
Get-CimInstance Win32_Processor |
    Select-Object Name, VirtualizationFirmwareEnabled, SecondLevelAddressTranslationExtensions

# Mode de lancement de l’hyperviseur Microsoft
bcdedit /enum "{current}"

# Fonctionnalités Windows susceptibles de réserver la virtualisation
Get-WindowsOptionalFeature -Online |
    Where-Object { $_.FeatureName -match 'Hyper-V|VirtualMachinePlatform|HypervisorPlatform|Sandbox|Containers' } |
    Select-Object FeatureName, State
```

Interprétation :

| Résultat | Action |
|---|---|
| `VirtualizationFirmwareEnabled` = `False` | Activer VT-x/AMD-V dans le BIOS/UEFI |
| `systeminfo` affiche « A hypervisor has been detected » | Désactiver Hyper-V/VBS (sections 2 et 3) |
| `hypervisorlaunchtype` = `Auto` | Passer à `off` (section 2) |
| Une fonctionnalité listée est `Enabled` | La désactiver (section 2) |

## 2. Désactiver Hyper-V et les plateformes associées

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName Containers -NoRestart
Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Sandbox -NoRestart

# Désactive le lancement de l’hyperviseur Microsoft au démarrage
bcdedit /set hypervisorlaunchtype off
```

Remarques :

- Sur Windows 11 Édition Famille, certaines fonctionnalités n’existent pas :
  l’erreur « feature not found » peut être ignorée.
- Cette désactivation casse WSL2, Docker Desktop (backend WSL2), Windows
  Sandbox et l’isolation du noyau. À réactiver après le laboratoire (section 9).

## 3. Désactiver VBS / Intégrité de la mémoire

Interface graphique :

1. **Sécurité Windows → Sécurité de l’appareil → Isolation du noyau**.
2. **Détails de l’isolation du noyau → Intégrité de la mémoire → Désactivé**.
3. Redémarrer.

Contrôle :

```powershell
reg query "HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity" /v Enabled
# Enabled = 0x0 : désactivé ; 0x1 : actif
```

Sur un poste géré par une entreprise, Credential Guard et Device Guard peuvent
être imposés par GPO : voir avec l’équipe sécurité ou utiliser un autre poste.

## 4. Forcer un arrêt complet

Le démarrage rapide de Windows peut conserver l’état de l’hyperviseur. Forcer
un arrêt complet :

```powershell
powercfg /h off
shutdown /s /f /t 0
```

Après le laboratoire, réactiver la mise en veille prolongée si nécessaire :

```powershell
powercfg /h on
```

## 5. Configurer VirtualBox

La VM doit être **éteinte**.

```powershell
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" modifyvm "lab-openstack" --nested-hw-virt on
& "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" showvminfo "lab-openstack" | Select-String "Nested"
```

Dans l’interface : **Configuration → Système → Processeur → Activer la
virtualisation imbriquée (VT-x/AMD-V)**, avec 4 vCPU minimum.

Contrôle décisif dans le journal VirtualBox
(`%USERPROFILE%\VirtualBox VMs\lab-openstack\Logs\VBox.log`) :

| Ligne du journal | Signification |
|---|---|
| `HM: Using VT-x/AMD-V` | Correct : VirtualBox accède directement à la virtualisation |
| `HM: Using WHV` | Hyper-V/WHP est encore actif : reprendre les sections 2 et 3 |
| `HM: Disabled` | Virtualisation désactivée côté BIOS ou VirtualBox |

VirtualBox 7.x est requis pour la virtualisation imbriquée récente.

## 6. Vérifier dans la VM Ubuntu

```bash
# Le périphérique doit exister
ls -l /dev/kvm

# Diagnostic de l’accélération
sudo apt update && sudo apt install -y cpu-checker
kvm-ok

# Présence des flags de virtualisation vus par le noyau
grep -Eoc '(vmx|svm)' /proc/cpuinfo
```

Si `/dev/kvm` apparaît et que `kvm-ok` répond « KVM acceleration can be used »,
le laboratoire utilisera KVM.

## 7. Cas AMD et Intel

- **Intel** : activer *Intel Virtualization Technology (VT-x)* et *VT-d* dans
  le BIOS/UEFI.
- **AMD** : activer *SVM Mode* et *IOMMU*. Certains fabricants masquent ces
  options ; une mise à jour du BIOS peut les faire apparaître.
- Les extensions de pagination imbriquée (EPT/NPT) sont nécessaires à de bonnes
  performances ; elles apparaissent dans
  `SecondLevelAddressTranslationExtensions`.

## 8. Repli QEMU

Si `/dev/kvm` reste absent, le cours reste faisable : DevStack bascule
automatiquement en émulation (`LIBVIRT_TYPE=qemu`, processeur virtuel
« Nehalem »). Le cloud est fonctionnel, mais les démarrages d’instances
prennent plusieurs minutes.

Pour forcer explicitement le mode, ajouter dans la section `[[local|localrc]]`
de `/opt/stack/devstack/local.conf` :

```ini
LIBVIRT_TYPE=qemu
```

Puis réinstaller ou relancer DevStack (`unstack.sh` puis `stack.sh`). Ne pas
conclure à une panne devant la lenteur des instances : c’est le comportement
attendu sans KVM.

## 9. Réactiver l’environnement après le laboratoire

```powershell
# Réactiver l’hyperviseur Microsoft au démarrage
bcdedit /set hypervisorlaunchtype auto

# Réactiver les fonctionnalités utiles (exemple : WSL2)
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform -NoRestart
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All -NoRestart

# Réactiver la mise en veille prolongée
powercfg /h on
```

Réactiver ensuite **Intégrité de la mémoire** dans Sécurité Windows, puis
redémarrer.

## 10. Checklist

- [ ] VT-x/AMD-V activé dans le BIOS/UEFI.
- [ ] `systeminfo` n’indique plus d’hyperviseur détecté.
- [ ] `bcdedit` affiche `hypervisorlaunchtype off`.
- [ ] Intégrité de la mémoire désactivée.
- [ ] `VBox.log` contient `HM: Using VT-x/AMD-V`.
- [ ] `ls -l /dev/kvm` fonctionne dans la VM.
- [ ] `kvm-ok` conclut à l’accélération KVM.
- [ ] À défaut, le mode QEMU est assumé et le laboratoire continue.
