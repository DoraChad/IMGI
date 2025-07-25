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
Track Info (
