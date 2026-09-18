# Kali-Live Build-Scripts

_`live-build` configuration for Kali ISO images._

These are the same [build-scripts](https://gitlab.com/kalilinux/build-scripts) that the [Kali team](https://www.kali.org/) uses to generate the official Kali Linux base images, found here: [kali.org/get-kali/](https://www.kali.org/get-kali/).

_Build your Kali Linux image today!_

- - -

These images can be used to live boot into Kali, from such a USB/CD/DVD/sdCard, as well offers a basic installation. For more customization during setup, see [kali-installer](https://gitlab.com/kalilinux/build-scripts/kali-installer).

- [kali-installer](https://gitlab.com/kalilinux/build-scripts/kali-installer) uses [Simple-CDD](https://wiki.debian.org/Simple-CDD) _(which is a wrapper for [debian-cd](https://wiki.debian.org/debian-cd))_
- [kali-live](https://gitlab.com/kalilinux/build-scripts/kali-live) uses [live-build](https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html)

- - -

Have a look at [Live Build a Custom Kali ISO](https://www.kali.org/docs/development/live-build-a-custom-kali-iso/) for explanations on how to use this repository.

There are also other [code examples of live-build](https://gitlab.com/kalilinux/recipes/live-build-config-examples), as well as [code examples for pre-seed to automate/unattended installation](https://gitlab.com/kalilinux/recipes/kali-preseed-examples).

- - -

## Help

```console
$ ./build.sh --help
Usage: ./build.sh [<option>...]

  --distribution <arg>
  --proposed-updates
  --arch <arg>
  --verbose
  --debug
  --variant <arg>
  --version <arg>
  --subdir <arg>
  --get-image-path
  --no-clean
  --clean
  --help

More information: https://www.kali.org/docs/development/live-build-a-custom-kali-iso/
$
```

## Usage Examples

Both images types, using the latest packages:

```console
$ ./build.sh
[...]
```

- - -

Manually define which Kali mirror to pull from, as well as be more detailed in output:

```console
$ echo "http://kali.download/kali" > .mirror
$
$ ./build.sh --verbose
[...]
```

- - -

Build a different Live image version (GNOME and KDE Plasma):

```console
$ ./build.sh \
  --debug \
  --variant gnome
[...]
$
$ ./build.sh \
  --debug \
  --variant kde
[...]
$
```


# Guida Laga

## Creazione ISO

1. Aggiungi un nuovo disco rigido virtuale di almeno 35/40 GB (chiamato, ad esempio, kali_ISS.qcow2).
Usando `virt-manager` o direttamente `qemu-system-x86_64`.

2. Identifica il nuovo disco appena aggiunto (solitamente /dev/sdb o /dev/vdb).
  ```
  sudo fdisk -l
  ```

3. Una volta individuato (supponiamo sia `/dev/sda`), formattalo (serve solo se non è appena creato).
  ```  
  sudo mkfs.ext4 /dev/sda
  ```

4. Monta il disco e crea lo spazio di lavoro.
  ```
  sudo mkdir -p /mnt/build_workspace
  ```
  ```
  sudo mount /dev/sda /mnt/build_workspace
  ```
  ```
  sudo chown -R kali:kali /mnt/build_workspace
  ```

5. Sposta il progetto e compila.
  ```
  mv ~/Kali_ISS /mnt/build_workspace/
  ```
  ```
  cd /mnt/build_workspace/Kali_ISS
  ```
  ```
  sudo ./build.sh --clean
  ```
  ```
  sudo ./build.sh --verbose
  ```

Alla fine si ottiene l'ISO nella cartella `/mnt/build_workspace/Kali_ISS`.

## Spostare ISO

Per spostare l'ISO sull'host, si può accedere direttamente al filesystem della VM:

1. Spegni la VM Kali che monta il disco virtuale.

2. Sull'Host, carica il modulo NBD (Network Block Device) e collega il disco virtuale:
```
sudo modprobe nbd max_part=8
```
```
sudo qemu-nbd --connect=/dev/nbd0 /percorso/al/tuo/disco.qcow2
```
3. Monta la partizione della VM e copia il file:
```
sudo mount /dev/nbd0p1 /mnt
```
```
cp /mnt/build_workspace/Kali_ISS/image/*.iso ~/
```
4. Smonta il disco e scollega NBD:
```
sudo umount /mnt
```
```
sudo qemu-nbd --disconnect /dev/nbd0
```
