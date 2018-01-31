
;===============================================================================
; Constants

FlowNumLives    = 3
FlowStateMenu   = 0
FlowStateAlive  = 1
FlowStateDying  = 2

;===============================================================================
; Variables
!macro GAMEFLOW_VARIABLES {
    flowScoreX      !byte 0
    flowScoreNumX   !byte 0
    flowScoreY      !byte 0
    score1          !byte 0
    score2          !byte 0
    score3          !byte 0

    flowLivesX      !byte 17
    flowLivesNumX   !byte 0
    flowLivesY      !byte 0
    lives           !byte 0

    flowHiScoreX    !byte 31
    flowHiScoreNumX !byte 0
    flowHiScoreY    !byte 0
    hiscore1        !byte 0
    hiscore2        !byte 0
    hiscore3        !byte 0

    flowStartX      !byte 11
    flowStartY      !byte 20

    flowStartText           !scr "press fire to start"
                            !byte 0
    flowStartClearText      !scr "                   "
                            !byte 0

    flowScoreText           !scr "score:"
                            !byte 0
    flowLivesText           !scr "lives:"
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

            +LIBSCREEN_DRAWTEXT_AAAV flowLivesX, flowLivesY, flowLivesText, White
            jsr gameFlowLivesDisplay

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
            jsr gameFlowResetLives
            jsr gamePlayerReset

            ; change state
            lda #FlowStateAlive
            sta flowState

    gFUMEnd
            rts

    ;===============================================================================

    gameFlowUpdateAlive

            rts

    ;===============================================================================

    gameFlowUpdateDying

            +LIBSPRITE_ISANIMPLAYING_A playerSprite
            bne gFUDEnd

            lda lives
            bne gFUDHasLives

            ; change state
            lda #FlowStateMenu
            sta flowState
            jmp gFUDEnd

    gFUDHasLives

            +LIBINPUT_GETFIREPRESSED
            bne gFUDEnd

            ; reset 
            jsr gamePlayerReset

            ; change state
            lda #FlowStateAlive
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

    ;===============================================================================

    gameFlowResetLives

            lda #FlowNumLives
            sta lives

            jsr gameFlowLivesDisplay

            rts


    ;===============================================================================

    gameFlowPlayerDied

            jsr gameBulletsReset ; stops in flight bullets from scoring
            jsr gameAliensReset

            dec lives
            bne gFPDHasLivesLeft 
            
            jsr gameFlowUpdateHiScore
            jsr gameFlowResetScore

    gFPDHasLivesLeft
            jsr gameFlowLivesDisplay

            ; change state
            lda #FlowStateDying
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

    gameFlowLivesDisplay

            +LIBMATH_ADD8BIT_AVA flowLivesX, 6, flowLivesNumX
            +LIBSCREEN_DRAWDECIMAL_AAAV flowLivesNumX, flowLivesY, lives, White

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

