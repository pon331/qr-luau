# qr-luau
Luau module for encoding data to QR-Code 

# Features
- Native codegen: `--!native`
- Strict typing: `--!strict` and full type annotations
- All four EC levels (`L`/`M`/`Q`/`H`), automatic version selection with optional ECC boosting
- 1-40 versions support

# API

## `type ErrorCorrection`
Reed–Solomon error correction level

Can be `"L"`, `"M"`, `"Q"` and `"H"`

## `type EncodeMode`
Data encoding mode

Can be `"Auto"`, `"Numeric"`, `"Alphanumeric"`, `"Byte"` and `"Kanji"`

`Auto` - Automaticaly selects encoding mode

`Numeric` - Can encode only numbers (0-9)

`Alphanumeric` - Can encode chars, numbers, spaces and special chars

`Byte` - Can encode any 8-bit data

`Kanji` - Can encode UTF-8 chars (which are encodable in Shift-JIS) characters



## `type EncodeOptions`
QR-Code encoding options

`ECC` - Error correction level

`MinVersion` and `MaxVersion` - inclusive version window, each in `1..40`

`Mask` - Mask pattern selection. Default: `-1`

`Mode` - Encode mode. Default: `"Auto"`.

`BoostECC` - After the version is chosen, the EC level is raised (M -> Q -> H) as far as
  the data still fits at no size cost, since smaller EC blocks leave unused room.
  Default `true`. Set to `false` when you need the exact requested level
  
`BlockSize` - Pixels per module side in `Encode` output. Default: `4`.

`Margin` - quiet zone width in modules, added on every side. Default: `4`
  
## `QR.Encode(Data: buffer, Options: EncodeOptions?) -> (buffer, number, number)`
Encodes `Data`  into a QR-Code and returns RGBA pixels buffer and its dimensions

## `QR.Matrix(Data: buffer, Options: EncodeOptions?) -> buffer`
Similar to `QR.Encode`, but returns the raw module matrix instead of RGBA pixels buffer

# Usage Examples

Applying encoded QR-Code RGBA buffer to EditableImage
```luau
const AssetService = game:GetService("AssetService")

const Image = script.Parent
const QR = require(path.to.qr)

-- Encoding link to this repo
const Pixels, Width, Height = QR.Encode(buffer.fromstring("https://github.com/pon331/qr-luau"))

const EditableImage = AssetService:CreateEditableImage({
	Size = Vector2.new(Width, Height)
})
EditableImage:WritePixelsBuffer(Vector2.zero, EditableImage.Size, Pixels)

Image.ResampleMode = Enum.ResamplerMode.Pixelated -- Pixelated resample mode for best look (will look blurry if ResampleMode is set to Default)
Image.ImageContent = Content.fromObject(EditableImage)

```
