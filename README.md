from PIL import Image, ImageDraw, ImageFont
import os

# ===== CONFIGURATION =====
TEXT = "Developer by Code, Creator by Passion"
OUTPUT_FILE = "animated_bhaskara.gif"
FONT_PATH = "/usr/share/fonts/truetype/dejavu/DejaVuSans-Bold.ttf"  # Change path if needed

# ===== STYLE SETTINGS =====
W, H = 1000, 200
FONT_SIZE = 48
BG_GRADIENT = [(10, 25, 47), (33, 147, 176)]  # Dark Blue → Aqua
TEXT_COLOR = (255, 255, 255)  # White
CURSOR_COLOR = (255, 255, 255)  # White
BLINK_FRAMES = 6
FRAME_DELAY = 120  # ms per frame

# ===== CREATE BACKGROUND GRADIENT =====
def create_gradient(w, h, colors):
    base = Image.new('RGB', (w, h), colors[0])
    top = Image.new('RGB', (w, h), colors[1])
    mask = Image.new('L', (w, h))
    for y in range(h):
        mask.putpixel((0, y), int(255 * (y / h)))
    mask = mask.resize((w, h))
    base.paste(top, (0, 0), mask)
    return base

# ===== PREPARE FONT =====
try:
    font = ImageFont.truetype(FONT_PATH, FONT_SIZE)
except Exception:
    font = ImageFont.load_default()

# ===== GENERATE FRAMES =====
frames = []
words = TEXT.split()

for i in range(1, len(words) + 1):
    text = " ".join(words[:i])
    for blink in range(BLINK_FRAMES):
        img = create_gradient(W, H, BG_GRADIENT).convert("RGBA")
        draw = ImageDraw.Draw(img)

        # Center text
        w, h = draw.textsize(text, font=font)
        x = (W - w) // 2
        y = (H - h) // 2

        # Draw text
        draw.text((x, y), text, font=font, fill=TEXT_COLOR)

        # Draw blinking cursor
        if blink % 2 == 0:
            cursor_x = x + w + 8
            draw.rectangle([cursor_x, y, cursor_x + 6, y + h], fill=CURSOR_COLOR)

        frames.append(img.convert("P"))

# ===== FINAL PAUSE WITH FULL TEXT =====
for blink in range(8):
    img = create_gradient(W, H, BG_GRADIENT).convert("RGBA")
    draw = ImageDraw.Draw(img)
    w, h = draw.textsize(TEXT, font=font)
    x = (W - w) // 2
    y = (H - h) // 2
    draw.text((x, y), TEXT, font=font, fill=TEXT_COLOR)
    if blink % 2 == 0:
        cursor_x = x + w + 8
        draw.rectangle([cursor_x, y, cursor_x + 6, y + h], fill=CURSOR_COLOR)
    frames.append(img.convert("P"))

# ===== SAVE GIF =====
frames[0].save(
    OUTPUT_FILE,
    save_all=True,
    append_images=frames[1:],
    duration=FRAME_DELAY,
    loop=0,
    optimize=False,
)

print(f"✅ Animation saved as {OUTPUT_FILE}")
