# OpenSlide

O OpenSlide é uma biblioteca em C utilizada para leitura de imagens do tipo **WSI (Whole Slide Images)**. Ele é usado como backend no **TIA Toolbox** para suportar diversos formatos de imagens.

No entanto, a versão oficial do OpenSlide possui **suporte limitado ao formato `.czi`**, que é o principal formato utilizado nos arquivos do laboratório.

Este repositório é um **fork de um fork** que adiciona melhor suporte ao formato `.czi`, incluindo casos com diferentes tipos de compressão (como JXR).

> ⚠️ **Importante:**  
> As modificações relacionadas ao suporte a `.czi` **não são de minha autoria**.  
> A única alteração neste repositório foi este `README.md`, com o objetivo de fornecer um tutorial claro de instalação e uso.

---

## 🎯 Objetivo

Facilitar a instalação de uma versão do OpenSlide compatível com arquivos `.czi`, permitindo seu uso correto dentro do **TIA Toolbox**.

---

## Instalação deste fork 
Tutorial levando em conta a utilização do Ubuntu 22:04 ou derivados.

### 1. Instalar dependências
```bash
sudo apt update
sudo apt install -y build-essential meson ninja-build pkg-config libcairo2-dev libgdk-pixbuf-2.0-dev libglib2.0-dev libjpeg-dev libpng-dev libtiff-dev libxml2-dev libopenjp2-7-dev libsqlite3-dev zlib1g-dev libzstd-dev libjxr-dev
```

### 2. Instalar o OpenSlide
```bash
meson setup builddir
meson compile -C builddir
sudo meson install -C builddir
sudo ldconfig
```

## Configuração no TIA Toolbox

### Desinstalar o pacote `openslide-bin`

O pacote OpenSlide para Python é composto por:

- `openslide-bin`: binários pré-compilados do OpenSlide  
- `openslide-python`: bindings para uso em Python  

Para que o `openslide-python` utilize a versão do OpenSlide compilada neste tutorial (com suporte a `.czi`), é necessário remover o `openslide-bin`, pois ele pode sobrescrever o uso da biblioteca do sistema.

```bash
pip uninstall openslide-bin
```
### Modificar a biblioteca para abrir `.czi`

Por padrão, o TIA Toolbox restringe os formatos de arquivo suportados e lança uma exceção caso o formato não esteja na lista permitida. Para habilitar o uso de arquivos `.czi`, é necessário modificar esse comportamento manualmente.

Abra o seguinte arquivo no código-fonte do TIA Toolbox:
>tiatoolbox/wsicore/wsireader.py

No método `verify_supported_wsi`, localize o trecho:

```python
if suffixes and suffixes[-1] not in [
    ".svs",
    ".npy",
    ".ndpi",
    ".mrxs",
    ".tif",
    ".tiff",
    ".jp2",
    ".png",
    ".jpg",
    ".jpeg",
    ".zarr",
    ".db",
    ".qptiff",
    ".json",
]:
```
Adicione .czi à lista.
>⚠️ Importante:
>Essa modificação é necessária porque o TIA Toolbox bloqueia explicitamente formatos não listados, mesmo que o OpenSlide tenha suporte para eles.

## Créditos

Este projeto utiliza e/ou é baseado nas seguintes bibliotecas:

- **OpenSlide**   
  https://github.com/openslide/openslide
  >Desenvolvido pela Carnegie Mellon University e colaboradores

- **TIAToolbox**  
  https://github.com/TissueImageAnalytics/tiatoolbox
  >Desenvolvido pela equipe do TIA Centre (University of Warwick)
  
- **Fork com suporte a CZI/JXR**  
  https://github.com/iewchen/openslide
  >Implementações adicionais para suporte ao formato `.czi` e compressão JXR foram obtidas de um fork existente do OpenSlide  

- **jxrlib**  
  https://github.com/4creators/jxrlib
  >Biblioteca utilizada para decodificação de imagens com compressão JPEG XR (JXR)  

---

> ⚠️ Este repositório não implementa modificações no código-fonte das bibliotecas acima, apenas documenta o processo de instalação e integração.
