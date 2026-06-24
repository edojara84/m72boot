[SOLUCION_ERROR_1962.md](https://github.com/user-attachments/files/29274449/SOLUCION_ERROR_1962.md)
# Solución: Error 1962 en Lenovo ThinkCentre M72e (Ubuntu Server)

Este documento detalla la solución al error **"1962: No operating system found"** que ocurre frecuentemente en equipos Lenovo antiguos al instalar distribuciones modernas de Linux que intentan utilizar tablas de particiones GPT y modo UEFI en hardware diseñado para BIOS Legacy/MBR.

---

## El Problema

Al finalizar la instalación, la BIOS de la Lenovo M72e no reconoce el sector de arranque debido a una incompatibilidad entre la tabla de particiones **GPT** generada automáticamente por el instalador y el modo de arranque de la placa base (**Legacy BIOS**).

---

## Requisitos

- Pendrive booteable con Ventoy + ISO de Ubuntu Server
- Acceso a una terminal durante el proceso de recuperación

---

## La Solución: Conversión a MBR y Reinstalación de GRUB

### Paso 1: Configuración de BIOS

Antes de comenzar, asegúrate de que la BIOS tenga los siguientes parámetros:

| Parámetro | Valor |
|---|---|
| CSM (Compatibility Support Module) | Enabled |
| Boot Mode | Legacy Only |
| Secure Boot | Disabled |

Accede a la BIOS presionando **F1** al encender el equipo.

---

### Paso 2: Arrancar desde Ventoy en modo Legacy

1. Conecta el pendrive con Ventoy
2. Presiona **F12** al encender para abrir el menú de arranque
3. Selecciona el pendrive en la opción **Legacy** (sin UEFI)
4. Abre una shell desde el entorno de recuperación

---

### Paso 3: Conversión de GPT a MBR

El instalador de Ubuntu crea por defecto una tabla de particiones GPT. Debemos convertirla a MBR:

```bash
sudo gdisk /dev/sda
```

Dentro de `gdisk`, sigue estos pasos:

1. Presiona `r` → recovery/transformation mode
2. Presiona `g` → convertir GPT a MBR
3. Presiona `w` → escribir cambios
4. Confirma con `y`

---

### Paso 4: Montaje del sistema (chroot)

Si el disco usa LVM, actívalo primero:

```bash
sudo vgchange -ay
```

Luego monta el sistema instalado:

```bash
sudo mount /dev/sda2 /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
```

> Reemplaza `/dev/sda2` por tu partición raíz si es diferente.

---

### Paso 5: Reinstalación de GRUB en modo Legacy

```bash
grub-install /dev/sda
update-grub
```

---

### Paso 6: Finalización

```bash
exit
sudo reboot
```

Retira el pendrive antes de que reinicie.

---

## Resultado Esperado

El equipo debe arrancar directamente en Ubuntu Server sin mostrar el error 1962.

---

## Notas

- Esta solución aplica a equipos con BIOS Legacy que no soportan UEFI correctamente.
- Probado en **Lenovo ThinkCentre M72e** con **Ubuntu Server 26.04**.
- El instalador moderno de Ubuntu usa GPT + EFI por defecto, lo cual es incompatible con BIOS antiguas en modo Legacy.

---

*Documentación creada como referencia técnica para la resolución del error de arranque 1962.*
