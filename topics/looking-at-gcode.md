# Looking at GCODE
## 27th December 2020
Did some GCODE investigation comparing CURA and PRUSA slicers. Note that there is no accounting for "paper width" offsets in either g-code. Both slicers allow such an offset to be set in z-offset which will translate all Z values in the output gcode. A better option is to account for the feeler guage width in the firmware z-offset calibration for BLTouch.

