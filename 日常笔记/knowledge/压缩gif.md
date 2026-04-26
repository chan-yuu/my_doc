用的是 ffmpeg 的 GIF 调色板压缩流程，核心是这条滤镜链：

fps=12,scale=960:-1,split -> palettegen -> paletteuse

具体做了三件事：

 1. 降帧率：从原始帧率降到 12fps
 2. 降分辨率：宽度统一到 960（高度按比例）
 3. 重新量化颜色：先生成最优调色板（palettegen），再用它重编码（paletteuse）

所以体积会明显下降，同时比“直接导出 GIF”画质更稳。