;===============================================================================
; Constants

PickupsMax = 40
PickupMapRow = 6

;===============================================================================
; Variables

!macro GAMEPICKUPS_VARIABLES {

    pickupsColumnArray      !fill PickupsMax, 0
    pickupsXCharArray       !fill PickupsMax, 0
    pickupsColumn           !byte 0
    pickupsXChar            !byte 0
    pickupsNumResetItems    !byte 0
}

;===============================================================================
; Macros/Subroutines

!macro GAMEPICKUPS_SUBROUTINES {
    gamePickupsReset

            lda pickupsNumResetItems
            beq gPRDone

            ldx #0
        gPRLoop
            ; reset the map position to a pickup char 0
            lda pickupsColumnArray,X
            sta pickupsColumn
            lda pickupsXCharArray,X
            sta pickupsXChar
            +LIBSCREEN_SETMAPCHAR_VAAV PickupMapRow, pickupsColumn, pickupsXChar, 0

            inx
            cpx pickupsNumResetItems
            bmi gPRLoop

            lda #0
            sta pickupsNumResetItems
        gPRDone
            
            rts

    ;===============================================================================

    gamePickupsHandle
        
            lda ZeroPageTemp
            bne gPHNoPickup ; is the character a 0? i.e. a pickup

            ; clear the screen position
            +LIBSCREEN_SETCHAR_V SpaceCharacter

            ; clear the map position
            +LIBSCREEN_SETMAPCHAR_VAAV PickupMapRow, screenColumn, playerXChar, SpaceCharacter

            ; add the map position to the reset list
            ldx pickupsNumResetItems
            lda screenColumn
            sta pickupsColumnArray,X
            lda playerXChar
            sta pickupsXCharArray,X
            inc pickupsNumResetItems
    
            ; increase the score
            jsr gameFlowIncreaseScore

            ; play the pickup sound
            +LIBSOUND_PLAY_VAA 0, soundPickupHigh, soundPickupLow

        gPHNoPickup

        rts
}
