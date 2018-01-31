;===============================================================================
; Constants

FlowGameTime    = $30
FlowStateMenu   = 0
FlowStateAlive  = 1
FlowStateDying  = 2

;===============================================================================
; Variables

!macro GAMEFLOW_VARIABLES { 

flowScoreX      !byte 1
flowScoreNumX   !byte 0
flowScoreY      !byte 24
score1          !byte 0
score2          !byte 0
score3          !byte 0

flowTimeX       !byte 18
flowTimeNumX    !byte 0
flowTimeY       !byte 24
time1           !byte 0
time2           !byte 0

flowHiScoreX    !byte 30
flowHiScoreNumX !byte 0
flowHiScoreY    !byte 24
hiscore1        !byte 0
hiscore2        !byte 0
hiscore3        !byte 0

flowStartX      !byte 11
flowStartY      !byte 11
flowStartText           !scr "press fire to start"
                        !byte 0
flowStartClearText      !scr "                   "
                        !byte 0

flowScoreText           !scr "score:"
                        !byte 0
flowTimeText            !scr "time:"
                        !byte 0
flowHiScoreText         !scr "hi:"
                        !byte 0

flowState               !byte FlowStateMenu

gameFlowJumpTableLow
        !byte <gameFlowUpdateMenu
        !byte <gameFlowUpdateAlive
        !byte <gameFlowUpdateDying

gameFlowJumpTableHigh
        !byte >gameFlowUpdateMenu
        !byte >gameFlowUpdateAlive
        !byte >gameFlowUpdateDying
}

;===============================================================================
; Macros/Subroutines

!macro GAMEFLOW_SUBROUTINES {
    gameFlowInit

            +LIBSCREEN_DRAWTEXT_AAAV flowScoreX, flowScoreY, flowScoreText, White
            jsr gameFlowScoreDisplay

            +LIBSCREEN_DRAWTEXT_AAAV flowTimeX, flowTimeY, flowTimeText, White
            jsr gameFlowTimeDisplay

            +LIBSCREEN_DRAWTEXT_AAAV flowHiScoreX, flowHiScoreY, flowHiScoreText, White
            jsr gameFlowHiScoreDisplay

            rts

    ;===============================================================================

    gameFlowUpdateMenu

            ; display the menu text
            +LIBSCREEN_DRAWTEXT_AAAV flowStartX, flowStartY, flowStartText, White
            
            +LIBINPUT_GETFIREPRESSED
            bne gFUMEnd

            ; clear the menu text
            +LIBSCREEN_DRAWTEXT_AAAV flowStartX, flowStartY, flowStartClearText, White

            ; reset 
            jsr gameFlowResetTime
            jsr gamePickupsReset
            jsr gamePlayerReset

            ; change state
            lda #FlowStateAlive
            sta flowState

    gFUMEnd
            rts

    ;===============================================================================

    gameFlowUpdateAlive

            jsr gameFlowDecreaseTime ; countdown time

            rts

    ;===============================================================================

    gameFlowUpdateDying
            
            +LIBSPRITE_ISANIMPLAYING_A playerSprite
            bne gFUDEnd

            ; change state
            lda #FlowStateMenu
            sta flowState
    gFUDEnd

            rts

    ;===============================================================================

    gameFlowUpdate

            ; get the current state
            ldy flowState 

            ; write the subroutine address to a zeropage location
            lda gameFlowJumpTableLow,y
            sta ZeroPageLow
            lda gameFlowJumpTableHigh,y
            sta ZeroPageHigh

            ; jump to the subroutine the zeropage location points to
            jmp (ZeroPageLow)

    ;===============================================================================

    gameFlowIncreaseScore
            
            sed             ;set decimal mode
            clc
            lda #$50        ;50 points scored
            adc score1      ;ones and tens
            sta score1
            lda score2      ;hundreds and thousands
            adc #00
            sta score2
            lda score3      ;ten-thousands and hundred-thousands
            adc #00
            sta score3
            cld             ;clear decimal mode

            jsr gameFlowScoreDisplay

            rts

    ;===============================================================================

    gameFlowResetScore
            
            lda #0
            sta score1
            sta score2
            sta score3

            jsr gameFlowScoreDisplay 

            rts

    ;==============================================================================

    gameFlowDecreaseTime

            sed             ;set decimal mode
            sec             ; sec is the same as clear borrow
            lda time1       ; Get first number
            sbc #1          ; Subtract 1
            sta time1       ; Store in first number
            lda time2       ; Get 2nd first number
            sbc #0          ; Subtract borrow
            sta time2       ; Store 2nd number
            cld              ;clear decimal mode

            jsr gameFlowTimeDisplay

            lda time2
            bne gFDTNotDied
            jsr gameFlowPlayerDied
    gFDTNotDied
            rts


    ;==============================================================================

    gameFlowClearTime

            lda #0
            sta time1
            sta time2

            jsr gameFlowTimeDisplay

            ; clear the menu text
            +LIBSCREEN_DRAWTEXT_AAAV flowStartX, flowStartY, flowStartClearText, White

            rts

    ;==============================================================================

    gameFlowResetTime

            lda #$60
            sta time1

            lda #FlowGameTime
            sta time2

            jsr gameFlowTimeDisplay

            ; clear the menu text
            +LIBSCREEN_DRAWTEXT_AAAV flowStartX, flowStartY, flowStartClearText, White

            rts

    ;===============================================================================

    gameFlowPlayerDied

            jsr gamePlayerDie
            jsr gameFlowUpdateHiScore
            jsr gameFlowResetScore
            jsr gameFlowClearTime

            ; change state
            lda #FlowStateDying
            sta flowState

            rts

    ;===============================================================================

    gameFlowPlayerEnded

            jsr gameFlowUpdateHiScore
            jsr gameFlowResetScore
            jsr gameFlowClearTime

            ; change state
            lda #FlowStateMenu
            sta flowState

            rts

    ;===============================================================================

    gameFlowUpdateHiScore

            ; http://6502.org/tutorials/decimal_mode.html#4.2
            ; a common technique for comparing multi-byte numbers
            lda score1
            cmp hiscore1
            lda score2
            sbc hiscore2
            lda score3
            sbc hiscore3

            bcc gFUHNotHi

            lda score1
            sta hiscore1
            lda score2
            sta hiscore2
            lda score3
            sta hiscore3

            jsr gameFlowHiScoreDisplay
    gFUHNotHi
            rts

    ;===============================================================================

    gameFlowScoreDisplay

            +LIBMATH_ADD8BIT_AVA flowScoreX, 6, flowScoreNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowScoreNumX, flowScoreY, score3, White

            +LIBMATH_ADD8BIT_AVA flowScoreX, 8, flowScoreNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowScoreNumX, flowScoreY, score2, White

            +LIBMATH_ADD8BIT_AVA flowScoreX, 10, flowScoreNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowScoreNumX, flowScoreY, score1, White

            rts


    ;===============================================================================

    gameFlowTimeDisplay

            +LIBMATH_ADD8BIT_AVA flowTimeX, 5, flowTimeNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowTimeNumX, flowTimeY, time2, White

            rts


    ;===============================================================================

    gameFlowHiScoreDisplay

            +LIBMATH_ADD8BIT_AVA flowHiScoreX, 3, flowHiScoreNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowHiScoreNumX, flowHiScoreY, hiscore3, White

            +LIBMATH_ADD8BIT_AVA flowHiScoreX, 5, flowHiScoreNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowHiScoreNumX, flowHiScoreY, hiscore2, White

            +LIBMATH_ADD8BIT_AVA flowHiScoreX, 7, flowHiScoreNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowHiScoreNumX, flowHiScoreY, hiscore1, White

            rts

}
