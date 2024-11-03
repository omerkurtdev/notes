---
title: "Effortless Subtitle Generation with MPV and Whisper"
author: ["Omer Kurt"]
draft: false
---

Whether you’re watching a local video on your computer or streaming content from a service, you can easily generate subtitles through MPV.


## Dependencies {#dependencies}

-   Whisper.cpp
-   yt-dlp
-   ffmpeg
-   WhisperSubs (Lua script): [GhostNaN/whisper-subs](https://github.com/GhostNaN/whisper-subs)


## Using CUDA Cores with an Nvidia Graphics Card (For Arch) {#using-cuda-cores-with-an-nvidia-graphics-card--for-arch}

You can increase subtitle generation speed by utilizing CUDA support. Follow these steps:

1.  Install CUDA:
    ```sh
    sudo pacman -S cuda
    ```
2.  Download Whisper.cpp
    ```shell
    git clone https://github.com/ggerganov/whisper.cpp.git
    cd whisper.cpp
    bash ./models/download-ggml-model.sh {desired_model*}
    make clean
    WHISPER_CUDA=1 make -j
    ```


### Supported Model Names {#supported-model-names}

`tiny.en`, `tiny`, `tiny`, `base.en`, `base`, `small.en`, `small`, `medium.en`, `medium`, `large-v1`, `large-v2`, `large-v3`


## Running with CPU {#running-with-cpu}

To run without CUDA support, using CPU:

1.  Clone Whisper.cpp
    ```bash
    git clone https://github.com/ggerganov/whisper.cpp.git
    bash ./models/download-ggml-model.sh {desired_model*}
    make
    ```


## Lua Script Setup {#lua-script-setup}

1.  Place the whispersubs.lua file in the `~/.config/mpv/scripts folder.`
2.  Edit the WHISPER_CMD line in the script to reflect the installation path of Whisper.cpp:

<!--listend-->

```sh
local WHISPER_CMD = "/models/whisper.cpp/main -m /models/whisper-ggml-medium.bin --threads 6 --language en"
```

Open the video and press `CTRL + .` to generate subtitles.
