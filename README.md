# Saxo 

El objetivo de este repo es documentar la implementación de un saxofón digital basado en el proyecto PONER usando el una placa basada en el SoC T113-s3 de Allwinner

## Placa personalizada
TODO
## Saxofon digital
TODO

## Instrucciones de compilación y troubleshot
Se procede a clonar el proyecto base desde [T113_SAXO_OS](https://github.com/eljuguetero/T113_SAXO_OS). En el se encontrarán los scripts de compilación de u-boot, linux, y debian para esta PCB

### Compilaciónde u-boot
Se procede a ejecutar el archivo `build_u-boot.sh` para crear el bootloader de primera y segunda etapa.

Se presentan unos errores basicamente a definiciones diferentes de uart, en la placa del desarrollador del proyecto original se usaba uart3, en esta se usará uart0 con otros pines.

```shell
arch/arm/dts/sun8i-t113s-saxo.dtb: ERROR (phandle_references): /soc/serial@2500000: Reference to non-existent node or label "uart0_pe2_pins"

ERROR: Input tree has errors, aborting (use -f to force output)
```