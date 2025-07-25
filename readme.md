# IMGI - PolyTrack Image Importing/Exporting
Export, Import, and Share PolyTrack tracks with images instead of track codes. 

## Limitations:
Track Names and Author Names MUST be less than 256 characters (1 byte)
Block position MUST be in the range -65535 to 65535 (2 bytes)

## Improvments:
The data can definatley be stored more efficiently, and scaling the amount of data each block takes up (4-5 pixels currently) would be good (e.g if all the blocks position can be stored in 2 bytes per axis)

# Encoding/Decoding System:
The images use the red, green, and blue buffers (3 bytes per pixel) to encode data. The alpha buffer is turned off due to rounding errors causing data to be lost.

## v1:
### Step 1: Calculate image dimensions
All export images are squares. This means that most data won't perfectly fit into a square. The code first calculates the amount of pixels that the data would take up:
- Normal Block: 4 pixels
- Checkpoint/Start: 5 pixels
- 12 bytes of track info
- Track name and author data

This area then gets squared and then rounded up, causing there to be extra pixels without data in most cases. The amount of data-less pixels is stored in the image.

### Track and Encoding Info

This data is stored at the beginning of the image. It stores (alpha channel skipped, each number is one byte):
- 0: Encoding Version
- 1: Track Environment
- 2: nothing
- 4: Track Name Length (bytes/characters)
- 5: Track Author Length (bytes/characters)
- 6: Track Sun Direction
- 8-10: Number of Blank Pixels at end of image

Following this, the track name then track author is stored in the next pixels.

### Block data:

Normal blocks in the game use 4 pixels of data, with one extra for bloxk type.

Since data is sorted by block type, every time the block is changed the data will have to be stored. This pixel uses the identifier of 255 <block-id> 255 in the rgb channels so that the decoder detects it. This is only checked at the beginning of a new block, which is why 255 can be used as an identifier.

Normal Blocks contain this data:
- X-pos: 3 bytes
- Y-pos: 3 bytes
- Z-pos: 3 bytes
- Rotation: 1 byte
- Rotation Axis: 1 byte
- Color: 1 byte

This fits snuggly into 4 pixels per block. However, blocks like checkpoints and start blocks require extra data (checkpointOrder and startOrder), causing their data to have to be stored in 5 pixels instead.

For the position data, 3 bytes are allocated to it:
- [sign][high][low]
The sign indicates if the number is negative or positive with values of either 0 or 128 (which is why we can use 255 as an id identifier). The remaining bytes are used to store the actual position of the block on that axis.
