### Image to Bitmap Converter

---

## This is the code I used to convert images to bitmap so the thermal printer could print them. the code converts an image to a bitmap and writes it out as a Arduino .h file. this file can then be included into the thermal printer code and printed. **Hasn't been used in a long time, will be checked and updated prior to final release**

---
'
        """
        image_to_thermal.py

        Converts any image into a 1-bit dithered bitmap, sized for a 384px-wide
        thermal printer, and writes it out as an Arduino .h header file containing
        a byte array ready to feed into Adafruit_Thermal's printBitmap().

        Usage:
            pip install Pillow
            python image_to_thermal.py my_photo.jpg output.h

        Then in your Arduino sketch:
            #include "output.h"
            printer.printBitmap(IMG_WIDTH, IMG_HEIGHT, IMG_DATA);
        """

        import sys
        from PIL import Image

        PRINTER_WIDTH = 384  # pixels — matches your 58mm printer


        def convert(input_path, output_path):
            img = Image.open(input_path).convert("L")  # greyscale

            # Resize to printer width, keep aspect ratio
            ratio = PRINTER_WIDTH / img.width
            new_height = int(img.height * ratio)
            img = img.resize((PRINTER_WIDTH, new_height), Image.LANCZOS)

            # Floyd-Steinberg dither down to 1-bit black/white
            img = img.convert("1")

            width, height = img.size
            pixels = img.load()

            # Pack into bytes: 8 pixels per byte, MSB first, 1 = white in PIL's
            # mode "1", but thermal printers expect 1 = black dot, so we invert.
            row_bytes = (width + 7) // 8
            data = bytearray(row_bytes * height)

            for y in range(height):
                for x in range(width):
                    pixel_is_white = pixels[x, y]  # 255 = white, 0 = black
                    if not pixel_is_white:  # black pixel -> set the bit
                        byte_index = y * row_bytes + (x // 8)
                        bit_index = 7 - (x % 8)
                        data[byte_index] |= (1 << bit_index)

            # Write header file
            var_name = "IMG_DATA"
            with open(output_path, "w") as f:
                f.write(f"// Auto-generated from {input_path}\n")
                f.write(f"#define IMG_WIDTH {width}\n")
                f.write(f"#define IMG_HEIGHT {height}\n")
                f.write(f"const uint8_t {var_name}[] PROGMEM = {\n")
                for i in range(0, len(data), 16):
                    chunk = data[i:i + 16]
                    line = ", ".join(f"0x{b:02X}" for b in chunk)
                    f.write(f"  {line},\n")
                f.write("};\n")

            size_kb = len(data) / 1024
            print(f"Done: {width}x{height}px, {len(data)} bytes ({size_kb:.1f} KB)")
            print(f"Written to {output_path}")
            if size_kb > 25:
                print("Warning: this is a big image for an Uno's 32KB flash.")
                print("Consider a smaller/shorter image if the sketch won't compile.")


        if __name__ == "__main__":
            if len(sys.argv) != 3:
                print("Usage: python image_to_thermal.py <input_image> <output.h>")
                sys.exit(1)
            convert(sys.argv[1], sys.argv[2])
'