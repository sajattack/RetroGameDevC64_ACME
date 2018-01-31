;==============================================================================
; Constants

PlayerFrame             = 1
PlayerHorizontalSpeed   = 2
PlayerVerticalSpeed     = 1
PlayerStartXHigh       = 0
PlayerStartXLow         = 175
PlayerStartY            = 229
PlayerXMinHigh          = 0     ; 0*256 + 24 = 24  minX
PlayerXMinLow           = 24
PlayerXMaxHigh          = 1     ; 1*256 + 64 = 320 maxX
PlayerXMaxLow           = 64
PlayerYMin              = 180
PlayerYMax              = 229 

;===============================================================================
; Variables

!macro GAMEPLAYER_VARIABLES {
    playerSprite    !byte 0
    playerXHigh     !byte 0
    playerXLow      !byte 175
    playerY         !byte 229
    playerXChar     !byte 0
    playerXOffset   !byte 0
    playerYChar     !byte 0
    playerYOffset   !byte 0
    playerActive    !byte False
}

;===============================================================================
; Macros/Subroutines

!macro GAMEPLAYER_SUBROUTINES {
    gamePlayerInit
        
        +LIBSPRITE_MULTICOLORENABLE_AV   playerSprite, True
        
        rts

;===============================================================================

    gamePlayerReset

        lda #True
        sta playerActive

        +LIBSPRITE_ENABLE_AV             playerSprite, True
        +LIBSPRITE_SETFRAME_AV           playerSprite, PlayerFrame
        +LIBSPRITE_SETCOLOR_AV           playerSprite, LightGray
        
        lda #PlayerStartXHigh
        sta playerXHigh
        lda #PlayerStartXLow
        sta playerXLow
        lda #PlayerStartY
        sta playerY
        +LIBSPRITE_SETPOSITION_AAAA playerSprite, playerXHigh, playerXLow, playerY
        
        rts

;===============================================================================

    gamePlayerUpdate      
        
        lda playerActive
        beq gPUSkip

        jsr gamePlayerUpdatePosition
        jsr gamePlayerUpdateFiring
        jsr gamePlayerUpdateCollisions


    gPUSkip

        rts

;===============================================================================

    gamePlayerUpdateCollisions

        +GAMEBULLETS_COLLIDED playerXChar, playerYChar, False
        beq gPUCNocollision
        lda #False
        sta playerActive
        ; run explosion animation
        +LIBSPRITE_SETCOLOR_AV     playerSprite, Yellow
        +LIBSPRITE_PLAYANIM_AVVVV  playerSprite, 4, 15, 3, False

        ; play explosion sound
        +LIBSOUND_PLAY_VAA 1, soundExplosionHigh, soundExplosionLow

        jsr gameFlowPlayerDied
                                
    gPUCNocollision

        rts

;==============================================================================


    gamePlayerUpdateFiring

            ; do fire after the ship has been clamped to position
            ; so that the bullet lines up
            +LIBINPUT_GETFIREPRESSED
            bne gPUFNofire
        
            ; fire the bullet
            +GAMEBULLETS_FIRE_AAAVV playerXChar, playerXOffset, playerYChar, White, True

            ; play the firing sound
            +LIBSOUND_PLAY_VAA 0, soundFiringHigh, soundFiringLow
            
        gPUFNofire

            rts

;===============================================================================

    gamePlayerUpdatePosition
                +LIBINPUT_GETHELD GameportLeftMask
                bne gPUPRight
                +LIBMATH_SUB16BIT_AAVVAA playerXHigh, playerXLow, 0, PlayerHorizontalSpeed, playerXHigh, playerXLow
        gPUPRight
                +LIBINPUT_GETHELD GameportRightMask
                bne gPUPUp
                +LIBMATH_ADD16BIT_AAVVAA playerXHigh, playerXLow, 0, PlayerHorizontalSpeed, playerXHigh, playerXLow
        gPUPUp
                +LIBINPUT_GETHELD GameportUpMask
                bne gPUPDown
                +LIBMATH_SUB8BIT_AVA playerY, PlayerVerticalSpeed, playerY
        gPUPDown
                +LIBINPUT_GETHELD GameportDownMask
                bne gPUPEndmove
                +LIBMATH_ADD8BIT_AVA playerY, PlayerVerticalSpeed, playerY        
        gPUPEndmove

                ; clamp the player x position
                +LIBMATH_MIN16BIT_AAVV playerXHigh, playerXLow, PlayerXMaxHigh, PlayerXMaxLow
                +LIBMATH_MAX16BIT_AAVV playerXHigh, playerXLow, PlayerXMinHigh, PlayerXMinLow
                
                ; clamp the player y position
                +LIBMATH_MIN8BIT_AV playerY, PlayerYMax
                +LIBMATH_MAX8BIT_AV playerY, PlayerYMin

                ; set the sprite position
                +LIBSPRITE_SETPOSITION_AAAA playerSprite, playerXHigh, playerXLow, playerY

                ; update the player char positions
                +LIBSCREEN_PIXELTOCHAR_AAVAVAAAA playerXHigh, playerXLow, 12, playerY, 40, playerXChar, playerXOffset, playerYChar, playerYOffset

                rts
}
