# QEMU-Mac

Un fork de QEMU modificado para emular **Mac OS X 10.0 a 10.9** (Cheetah → Mavericks) con más velocidad que el QEMU original.

> Estado: **proyecto en etapa inicial.** Todavía no hay código ni binarios. Este README describe los objetivos y el plan.

## Objetivos

- Arrancar y usar Mac OS X **10.0, 10.1, 10.2, 10.3, 10.4, 10.5** (PowerPC) y **10.6, 10.7, 10.8, 10.9** (Intel x86_64).
- Ser **más rápido que el QEMU original** en el mismo hardware.
- Que funcione en Windows (objetivo principal) y, si es posible, en Linux.
- Mantener los parches pequeños y ordenados, para poder actualizar el fork con QEMU upstream.

## Versiones y arquitecturas

| macOS | Nombre | Arquitectura del guest | Máquina QEMU base | Aceleración posible |
|---|---|---|---|---|
| 10.0 | Cheetah | PowerPC | `mac99` (ppc) | Solo TCG (emulación) |
| 10.1 | Puma | PowerPC | `mac99` (ppc) | Solo TCG |
| 10.2 | Jaguar | PowerPC | `mac99` (ppc) | Solo TCG |
| 10.3 | Panther | PowerPC | `mac99` (ppc) | Solo TCG |
| 10.4 | Tiger | PowerPC | `mac99` (ppc) | Solo TCG |
| 10.5 | Leopard | PowerPC | `mac99` (ppc) | Solo TCG |
| 10.6 | Snow Leopard | x86_64 | `q35` (x86) | WHPX / HAXM / KVM |
| 10.7 | Lion | x86_64 | `q35` (x86) | WHPX / HAXM / KVM |
| 10.8 | Mountain Lion | x86_64 | `q35` (x86) | WHPX / HAXM / KVM |
| 10.9 | Mavericks | x86_64 | `q35` (x86) | WHPX / HAXM / KVM |

Importante: 10.0 a 10.5 son sistemas para PowerPC, así que en una PC x86 solo se pueden correr con emulación completa (TCG), que es la parte más lenta. Las versiones 10.6 a 10.9 son para Intel y pueden usar aceleración por hardware, por eso van mucho más rápido.

## Cómo se busca la velocidad

Ideas de optimización previstas (a medir, no a prometer):

1. **TCG para PowerPC**: optimizar la traducción de instrucciones PPC → x86 (bloques más grandes, mejor encadenado de bloques, menos barreras de memoria innecesarias).
2. **Aceleración por hardware para x86**: usar WHPX (Windows Hypervisor Platform) en Windows y KVM en Linux para 10.6–10.9.
3. **Gráficos**: framebuffer más rápido y mejor soporte de VGA/VMware SVGA para el guest.
4. **Disco y red**: imágenes `qcow2` con caché bien configurada, `virtio` donde el guest lo soporte.
5. **Compilación**: builds con `-O3`, LTO y PGO.

Cada mejora se va a documentar con una medición propia (tiempo de arranque, FPS de la interfaz) frente al QEMU original, en el mismo equipo.

## Qué NO incluye este repositorio

- **No incluye imágenes, instaladores ni ISOs de macOS.** Hay que aportar los propios.
- **No incluye claves ni archivos de firmware de Apple** (por ejemplo la clave OSK que usan los macOS Intel en QEMU).
- Apple permite ejecutar macOS solo en hardware de Apple según su licencia. Cada persona es responsable de cumplirla.

## Requisitos (previstos)

- Windows 10/11 de 64 bits (con *Windows Hypervisor Platform* activada para 10.6–10.9) o Linux.
- CPU de 64 bits con virtualización activada en BIOS.
- Mínimo 4 GB de RAM (recomendado 8 GB).
- Una imagen de instalación de macOS que tengas legalmente.

## Compilar (plan)

```bash
git clone https://github.com/JereyBeni/QEMU-Mac.git
cd QEMU-Mac
mkdir build && cd build
../configure --target-list=ppc-softmmu,x86_64-softmmu
make -j$(nproc)
```

Los pasos exactos para Windows (MSYS2/MinGW) se van a agregar cuando haya código.

## Ejemplos de uso (previstos)

**PowerPC (10.0–10.5):**

```bash
qemu-system-ppc -M mac99 -m 1024 -hda macosx.qcow2 -cdrom instalador.iso -boot d
```

**Intel (10.6–10.9) con aceleración en Windows:**

```bash
qemu-system-x86_64 -M q35 -accel whpx -m 4096 -smp 2 -hda macosx.qcow2
```

Los parámetros definitivos pueden cambiar a medida que avance el proyecto.

## Hoja de ruta

- [ ] Importar el código base de QEMU upstream como punto de partida
- [ ] Boot de Mac OS X 10.4 (PowerPC) como primer hito
- [ ] Boot de 10.6–10.9 con WHPX en Windows
- [ ] Boot de 10.0–10.3 y 10.5
- [ ] Primeras optimizaciones de TCG y mediciones contra el QEMU original
- [ ] Guía de instalación paso a paso
- [ ] Builds de Windows publicados en Releases

## Contribuir

Los reportes de bugs, pruebas de compatibilidad y parches son bienvenidos. Al reportar un problema, incluí la versión de macOS, tu CPU, tu sistema operativo y el comando de QEMU que usaste.

## Licencia

QEMU se distribuye bajo GPLv2, y este fork mantiene la misma licencia.

## Créditos

- [QEMU](https://www.qemu.org/) y sus contribuidores.
