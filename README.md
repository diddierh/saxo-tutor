# Saxo 

El objetivo de este repo es documentar la implementación de un saxofón digital basado en el proyecto PONER usando el una placa basada en el SoC T113-s3 de Allwinner

## Placa personalizada
TODO
## Saxofon digital
TODO

## Instrucciones de compilación y troubleshot
Se procede a clonar el proyecto base desde [T113_SAXO_OS](https://github.com/eljuguetero/T113_SAXO_OS). En el se encontrarán los scripts de compilación de u-boot, linux, y debian para esta PCB

### Dependencias adicionales.
Adicional a las dependencias mencionadas en el repositorio es necesario instalar swig.
```
sudo apt install swig
```

### Compilaciónde u-boot
Se procede a ejecutar el archivo `build_u-boot.sh` para crear el bootloader de primera y segunda etapa.

Se presentan unos errores basicamente a definiciones diferentes de uart, en la placa del desarrollador del proyecto original se usaba uart3, en esta se usará uart0 con otros pines.

```shell
arch/arm/dts/sun8i-t113s-saxo.dtb: ERROR (phandle_references): /soc/serial@2500000: Reference to non-existent node or label "uart0_pe2_pins"

ERROR: Input tree has errors, aborting (use -f to force output)
```

Esto sucede principalmente porque el proyecto original desarrollado por Andrés usa otra placa, aunque aplicamos un parche para configurar nuestros pines en el archivo `build_u-boot.sh` existe una linea  `git checkout -f` que revierte los patches que aplicamos inicialmente en el mismo archivo; por lo cual se comenta esta linea. El archivo final debería lucir así.

```bash
#!/bin/bash

SCRIPT_DIR="$(dirname "$(realpath "${BASH_SOURCE[0]}")")"

cd $SCRIPT_DIR

cp u-boot-patch-v2025.07/t113s_saxo_uart0_defconfig u-boot/configs/t113s_saxo_defconfig
cp u-boot-patch-v2025.07/sun8i-t113s-saxo.dts         u-boot/arch/arm/dts
cp u-boot-patch-v2025.07/sunxi-d1s-t113s-saxo.dtsi    u-boot/arch/arm/dts
cp u-boot-patch-v2025.07/sunxi-d1s-t113.dtsi          u-boot/arch/riscv/dts

cd u-boot

#git checkout -f

patch -d . -p1 <  ../u-boot-patch-v2025.07/0001-saxo-dtb.patch

make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- -j4 t113s_saxo_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- -j4

```

En la carpeta uboot aparecera un archivo `u-boot-sunxi-with-spl.bin` nuestro bootloader de primera etapa que debe ser *quemado* en la sd de manera directa usando el siguiente comando.

```bash
sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8
```

Donde sdX debe ser reemplazado con el dispositivo de bloques que se halla creado al conectar la microSD, bs indica el tamaño de bloque a escribir 1024 bytes y seek 8 indica que se salten 8 bloques de tamaño bs es decir el bootloader se grabara 8k después del primer sector físico de la microSD