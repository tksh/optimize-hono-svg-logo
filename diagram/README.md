# Generate the Comparison Diagram

Using [D2](https://github.com/terrastruct/d2) and [ImageMagick](https://github.com/ImageMagick/ImageMagick)

```sh
# Rasterize the Diagram
d2 results.d2 results.png

# Use ImageMagick to trim extra whitespace and add a white border
magick results.png -trim -bordercolor white -border 20 results_trimmed.png
```
