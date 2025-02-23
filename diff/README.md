# Generate the Diff Images

Using [resvg](https://github.com/linebender/resvg) and [ImageMagick](https://github.com/ImageMagick/ImageMagick):

```sh
# Render SVG inputs at 1024px width with sharp edges (no anti-aliasing)
resvg -w 1024 --shape-rendering crispEdges ../logo_viewbox772x996_by_qrg.svg tmp0.png
resvg -w 1024 --shape-rendering crispEdges ../logo_viewbox772x996_02_svgo_optimized.svg tmp1.png
resvg -w 1024 --shape-rendering crispEdges ../logo_viewbox76x98_02_svgo_optimized.svg tmp2.png

# Add identifying labels to each image
magick tmp0.png -font Ubuntu -gravity center -fill black -pointsize 50 -annotate +20+0 'Red: Original' tmp0_text.png
magick tmp1.png -font Ubuntu -gravity center -fill black -pointsize 50 -annotate +20+100 'Green: 1st challenge' tmp1_text.png
magick tmp2.png -font Ubuntu -gravity center -fill black -pointsize 50 -annotate +20+200 'Blue: 2nd challenge' tmp2_text.png

# Convert images to binary black/white using optimized parameters for gradient shapes
# Note:
# - threshold 55%: Specifically chosen for preserving gradient-filled shapes
# - `-define png:color-type=2`: Forces RGB format (not grayscale) to ensure
#    black areas are detectable in the upcoming tinting process
magick tmp0_text.png -channel RGB -threshold 55% -background white -flatten -monochrome -define png:color-type=2 tmp0_mono.png
magick tmp1_text.png -channel RGB -threshold 55% -background white -flatten -monochrome -define png:color-type=2 tmp1_mono.png
magick tmp2_text.png -channel RGB -threshold 55% -background white -flatten -monochrome -define png:color-type=2 tmp2_mono.png

# Colorize the monochrome images (red for original, green for 1st, blue for 2nd)
magick tmp0_mono.png -fill red -opaque black -flatten tmp0_rgb.png
magick tmp1_mono.png -fill green -opaque black -flatten tmp1_rgb.png
magick tmp2_mono.png -fill blue -opaque black -flatten tmp2_rgb.png

# Generate difference maps between versions
magick composite -compose difference tmp0_rgb.png tmp1_rgb.png tmp_diff_0_1.png
magick composite -compose difference tmp1_rgb.png tmp2_rgb.png tmp_diff_1_2.png
magick composite -compose difference tmp0_rgb.png tmp2_rgb.png tmp_diff_0_2.png

# Invert difference maps to restore RGB representation
magick tmp_diff_0_1.png -negate diff_0_1.png
magick tmp_diff_1_2.png -negate diff_1_2.png
magick tmp_diff_0_2.png -negate diff_0_2.png

# Clean up temporary files
rm tmp*
```
