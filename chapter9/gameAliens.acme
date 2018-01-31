;===============================================================================
; Constants

AliensMax = 7
AliensHorizontalSpeed = 1
AliensFireDelay = 90
AliensRespawnDelay = 255
AliensXMoveDelay = 20
AliensXMoveNumIndices = 100

;===============================================================================
; Variables
!macro GAMEALIENS_VARIABLES {
    aliensActiveArray       !fill AliensMax, 1
    aliensActive            !byte   0
    aliensFrameArray        !byte   2,   2,   2,   2,   3,   3,   3
    aliensFrame             !byte   0
    aliensColorArray        !byte   Red, Orange, Blue, Yellow, Red, Green, Blue
    aliensColor             !byte   0
    aliensMultiColorArray   !byte   True, True, True, True, False, False, False
    aliensMultiColor        !byte   0
    aliensXHighArray        !byte   0,   0,   0,   1,   0,   0,   0
    aliensXHigh             !byte   0
    aliensXLowArray         !byte  33, 113, 193,  18,  73, 153, 233
    aliensXLow              !byte   0
    aliensYArray            !byte  80,  80,  80,  80, 130, 130, 130
    aliensY                 !byte   0
    aliensXLocalArray       !byte   0,   0,   0,   0,   0,   0,   0
    aliensXLocal            !byte   0
    aliensYLocalArray       !byte   0,   0,   0,   0,   0,   0,   0
    aliensYLocal            !byte   0
    aliensXCharArray        !byte   0,   0,   0,   0,   0,   0,   0
    aliensXChar             !byte   0
    aliensXOffsetArray      !byte   0,   0,   0,   0,   0,   0,   0
    aliensXOffset           !byte   0
    aliensYOffset           !byte   0
    aliensYCharArray        !byte   0,   0,   0,   0,   0,   0,   0
    aliensYChar             !byte   0
    aliensFireArray         !byte   0,  30,  60,  90, 120, 150, 180
    aliensFire              !byte   0
    aliensRespawnArray      !byte   0,   0,   0,   0,   0,   0,   0
    aliensRespawn           !byte   0
    aliensTemp              !byte   0
    aliensCollisionNo       !byte   0
    aliensSprite            !byte   0

    aliensXMoveIndexArray   !byte   0,   5,  10,  15,  20,  25,  30
    aliensXMoveIndex        !byte   0


                            ; right
    aliensXMoveArray        !byte    0,  0,  1,  1,  1,  2,  2,  3,  4,  5
                            !byte    6,  7,  8,  9, 10, 11, 12, 13, 14, 15
                            !byte   16, 17, 18, 19, 20, 21, 22, 23, 24, 25
                            !byte   26, 27, 28, 29, 30, 31, 32, 33, 34, 35
                            !byte   36, 37, 38, 39, 39, 40, 40, 40, 41, 41

                            ; left
                            !byte   41, 41, 40, 40, 40, 39, 39, 38, 37, 36
                            !byte   35, 34, 33, 32, 31, 30, 29, 28, 27, 26
                            !byte   25, 24, 23, 22, 21, 20, 19, 18, 17, 16 
                            !byte   15, 14, 13, 12, 11, 10,  9,  8,  7,  6
                            !byte    5,  4,  3,  2,  2,  1,  1,  1,  0,  0
}

;==============================================================================
; Macros/Subroutines
!macro GAMEALIENS_SUBROUTINES {
    gameAliensInit

                ldx #0
                stx aliensSprite
        gAILoop
                inc aliensSprite ; x+1
                
                jsr gameAliensGetVariables

                +LIBSPRITE_ENABLE_AV             aliensSprite, True
                +LIBSPRITE_SETFRAME_AA           aliensSprite, aliensFrame
                +LIBSPRITE_SETCOLOR_AA           aliensSprite, aliensColor
                +LIBSPRITE_MULTICOLORENABLE_AA   aliensSprite, aliensMultiColor
            
                jsr gameAliensSetVariables
                
                ; loop for each alien
                inx
                cpx #AliensMax
                bne gAILoop
                
                rts

    ;==============================================================================

    gameAliensUpdate

            ldx #0
            stx aliensSprite

        gAULoop
                inc aliensSprite ; x+1

                jsr gameAliensGetVariables

                lda aliensActive 
                beq gAUSkipThisAlien

                jsr gameAliensUpdatePosition
                jsr gameAliensUpdateFiring
                jsr gameAliensUpdateCollisions
                
                jmp gAUUpdated

        gAUSkipThisAlien
                jsr gameAliensUpdateInactive
        gAUUpdated

                jsr gameAliensSetVariables

                ; loop for each alien
                inx
                cpx #AliensMax
                bne gAULoop

        rts

    ;==============================================================================

    gameAliensGetVariables

            lda aliensActiveArray,X
            sta aliensActive
            lda aliensFrameArray,X
            sta aliensFrame
            lda aliensColorArray,X
            sta aliensColor
            lda aliensMultiColorArray,X
            sta aliensMultiColor
            lda aliensXHighArray,X
            sta aliensXHigh
            lda aliensXLowArray,X
            sta aliensXLow
            lda aliensYArray,X
            sta aliensY
            lda aliensXLocalArray,X
            sta aliensXLocal
            lda aliensYLocalArray,X
            sta aliensYLocal
            lda aliensFireArray,X
            sta aliensFire
            lda aliensRespawnArray,X
            sta aliensRespawn
            lda aliensXMoveIndexArray,X
            sta aliensXMoveIndex
            
            stx aliensTemp; save X register as it gets trashed

            rts

    ;==============================================================================

    gameAliensUpdatePosition

            ldy aliensXMoveIndex
            iny
            sty aliensXMoveIndex        
            cpy #AliensXMoveNumIndices
            beq gAUPIResetIndex
            jmp gAUPIDontReset

        gAUPIResetIndex
            lda #0
            sta aliensXMoveIndex
            
        gAUPIDontReset
            ldy aliensXMoveIndex
            lda aliensXMoveArray,Y
            sta aliensXLocal

            +LIBMATH_ADD16BIT_AAVAAA aliensXHigh, aliensXLow, 0, aliensXLocal, aliensXHigh, aliensXLow
            
            +LIBSPRITE_SETPOSITION_AAAA aliensSprite, aliensXHigh, aliensXLow, aliensY

            ; update the alien char positions
            +LIBSCREEN_PIXELTOCHAR_AAVAVAAAA aliensXHigh, aliensXLow, 12, aliensY, 40, aliensXChar, aliensXOffset, aliensYChar, aliensYOffset

            rts

    ;==============================================================================

    gameAliensUpdateFiring

            lda playerActive ; only fire if the player is alive
            beq gAUFDontfire

            ldy aliensFire
            iny
            sty aliensFire        
            cpy #AliensFireDelay
            beq gAUFFire
            jmp gAUFDontfire
        gAUFFire
            
            +GAMEBULLETS_FIRE_AAAVV aliensXChar, aliensXOffset, aliensYChar, Yellow, False

            lda #0
            sta aliensFire
        gAUFDontfire

            rts

    ;==============================================================================

    gameAliensUpdateCollisions

            +GAMEBULLETS_COLLIDED aliensXChar, aliensYChar, True
            
            beq gAUCNocollision
            ; run explosion animation
            +LIBSPRITE_PLAYANIM_AVVVV      aliensSprite, 4, 15, 3, False
            +LIBSPRITE_SETCOLOR_AV         aliensSprite, Yellow
            +LIBSPRITE_MULTICOLORENABLE_AV aliensSprite, True

            lda #False
            sta aliensActive
        gAUCNocollision

            rts

    ;==============================================================================

    gameAliensSetVariables

            ldx aliensTemp ; restore X register as it gets trashed

            lda aliensXLocal
            sta aliensXLocalArray,X
            lda aliensYLocal
            sta aliensYLocalArray,X
            lda aliensActive
            sta aliensActiveArray,X
            lda aliensXChar
            sta aliensXCharArray,X
            lda aliensXOffset
            sta aliensXOffsetArray,X
            lda aliensYChar
            sta aliensYCharArray,X
            lda aliensFire
            sta aliensFireArray,X
            lda aliensRespawn
            sta aliensRespawnArray,X
            lda aliensXMoveIndex
            sta aliensXMoveIndexArray,X

            rts

    ;==============================================================================

    gameAliensUpdateInactive

            ldy aliensRespawn
            iny
            sty aliensRespawn
    
            cpy #AliensRespawnDelay
            beq gAUIRespawn
            jmp gAUIDontRespawn

        gAUIRespawn

            +LIBSPRITE_ENABLE_AV             aliensSprite, True
            +LIBSPRITE_SETFRAME_AA           aliensSprite, aliensFrame
            +LIBSPRITE_SETCOLOR_AA           aliensSprite, aliensColor
            +LIBSPRITE_MULTICOLORENABLE_AA   aliensSprite, aliensMultiColor
        
            lda #0
            sta aliensRespawn
            lda #True
            sta aliensActive

        gAUIDontRespawn
            rts
}
