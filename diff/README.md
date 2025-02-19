# Generate the Diff Images

Using [resvg](https://github.com/linebender/resvg) and [ImageMagick](https://github.com/ImageMagick/ImageMagick):

```sh
# Render at the same pixel size without anti-aliasing
resvg -w 1024 --shape-rendering crispEdges ../logo_viewbox772x996_by_qrg.svg tmp0.png
resvg -w 1024 --shape-rendering crispEdges ../logo_viewbox772x996_02_svgo_optimized.svg tmp1.png
resvg -w 1024 --shape-rendering crispEdges ../logo_viewbox76x98_02_svgo_optimized.svg tmp2.png

# Convert to monochrome with a 55% threshold and a white background
magick tmp0.png -channel RGB -threshold 55% -background white -flatten -monochrome tmp0_mono.png
magick tmp1.png -channel RGB -threshold 55% -background white -flatten -monochrome tmp1_mono.png
magick tmp2.png -channel RGB -threshold 55% -background white -flatten -monochrome tmp2_mono.png

# Generate diff images for each pair
magick composite -compose difference tmp0_mono.png tmp1_mono.png diff_0_1.png
magick composite -compose difference tmp1_mono.png tmp2_mono.png diff_1_2.png
magick composite -compose difference tmp0_mono.png tmp2_mono.png diff_0_2.png
```
