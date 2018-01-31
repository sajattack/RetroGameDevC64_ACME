;===============================================================================
; Constants

MapLastColumn = 202 ; (5 * 40) + 2 extra hidden rows, i.e. start of 6th screen

;===============================================================================
; Variables

!macro GAMEMAP_VARIABLES {
    MapRAMRowStartLow ; increments are 6 screens x 40 characters per row (240)
            !byte <MAPRAM,      <MAPRAM+240,  <MAPRAM+480
            !byte <MAPRAM+720,  <MAPRAM+960,  <MAPRAM+1200
            !byte <MAPRAM+1440, <MAPRAM+1680, <MAPRAM+1920
    MapRAMRowStartHigh
            !byte >MAPRAM,      >MAPRAM+240,  >MAPRAM+480
            !byte >MAPRAM+720,  >MAPRAM+960,  >MAPRAM+1200
            !byte >MAPRAM+1440, >MAPRAM+1680, >MAPRAM+1920

    MAPCOLORRAM = MAPRAM + (6 * 8 * 40)  ; 6 screens x 8 rows x 40 characters

    MapRAMCOLRowStartLow ; increments are number of screens x 40 characters per row
            !byte <MAPCOLORRAM,      <MAPCOLORRAM+240,  <MAPCOLORRAM+480
            !byte <MAPCOLORRAM+720,  <MAPCOLORRAM+960,  <MAPCOLORRAM+1200
            !byte <MAPCOLORRAM+1440, <MAPCOLORRAM+1680, <MAPCOLORRAM+1920
    MapRAMCOLRowStartHigh
            !byte >MAPCOLORRAM,      >MAPCOLORRAM+240,  >MAPCOLORRAM+480
            !byte >MAPCOLORRAM+720,  >MAPCOLORRAM+960,  >MAPCOLORRAM+1200
            !byte >MAPCOLORRAM+1440, >MAPCOLORRAM+1680, >MAPCOLORRAM+1920
}

;===============================================================================
; Macros/Subroutines

!macro GAMEMAP_SUBROUTINES {
    gameMapInit

        jsr gameMapUpdate

        ; update the cloud colors & map row 7 colors on init
             
        ; clouds colors
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 0, 2, screenColumn
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 1, 3, screenColumn

        ; ground colors
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 7, 22, screenColumn
        
        rts

;===============================================================================

    gameMapUpdate

        ; no need to update the cloud colors or map row 7 colors
        ; as they have the same colors across the row

        ; clouds characters
        +LIBSCREEN_COPYMAPROW_VVA 0, 2, screenColumn
        +LIBSCREEN_COPYMAPROW_VVA 1, 3, screenColumn

        ; ground characters
        +LIBSCREEN_COPYMAPROW_VVA 2, 17, screenColumn
        +LIBSCREEN_COPYMAPROW_VVA 3, 18, screenColumn
        +LIBSCREEN_COPYMAPROW_VVA 4, 19, screenColumn
        +LIBSCREEN_COPYMAPROW_VVA 5, 20, screenColumn
        +LIBSCREEN_COPYMAPROW_VVA 6, 21, screenColumn
        +LIBSCREEN_COPYMAPROW_VVA 7, 22, screenColumn

        ; ground colors
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 2, 17, screenColumn
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 3, 18, screenColumn
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 4, 19, screenColumn
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 5, 20, screenColumn
        +LIBSCREEN_COPYMAPROWCOLOR_VVA 6, 21, screenColumn

        rts
}
