# 🛠️ Fix Error 1962 – Lenovo (GPT a MBR + GRUB Legacy)

Guía para solucionar el **error 1962: No Operating System Found** en equipos Lenovo después de instalar Linux.

## ¿Por qué ocurre?

El instalador de Linux a veces recrea la tabla de particiones en formato **GPT**, pero la BIOS Legacy de algunos Lenovo solo reconoce discos con tabla **MBR**. Esto provoca que el equipo no encuentre el sistema operativo al arrancar.

---

## ✅ Solución paso a paso

> Arranca desde el **USB de instalación** de Linux, abre una terminal y sigue estos pasos.

### 1. Convertir GPT a MBR

```bash
sudo gdisk /dev/sda
```

Dentro de `gdisk`:

1. Presiona `r` → modo de recuperación/transformación
2. Presiona `g` → convertir GPT a MBR
3. Presiona `w` → escribir cambios
4. Confirma con `y`

---

### 2. Montar el sistema (Chroot)

Verifica tu partición raíz con:

```bash
lsblk
```

> Lo más probable es que sea `/dev/sda2`. Ajusta según tu caso.

Luego móntala:

```bash
sudo mount /dev/sda2 /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
```

---

### 3. Reinstalar GRUB en modo Legacy

```bash
grub-install /dev/sda
update-grub
```

---

### 4. Salir y reiniciar

```bash
exit
sudo reboot
```

> ⚠️ **Importante:** En cuanto la pantalla se apague, **desconecta el USB** para que el equipo arranque desde el disco.

---

## 💡 Notas

- Esta guía aplica para BIOS **Legacy/CSM** (no UEFI).
- Probado en equipos **Lenovo** con Linux (Ubuntu, Mint, etc.).
- Si el error persiste, verifica en la BIOS que el modo de arranque esté en **Legacy** y no en UEFI.

---

## 📋 Requisitos

- USB con instalación de Linux (booteable)
- Acceso a terminal desde el live USB
- Disco interno en `/dev/sda` (verificar con `lsblk`)
