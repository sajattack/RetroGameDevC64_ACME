;==============================================================================
; Constants

EnemiesMax = 7
EnemyAnimDelay = 10

;===============================================================================
; Variables
!macro GAMEENEMIES_VARIABLES {
	enemiesXCharArray       !byte   21, 37,  55,  79, 113, 151, 189
	enemiesXChar            !byte   0
	enemiesXMoveArray       !byte   63, 95, 136, 136, 160, 112, 151
	enemiesXMove            !byte   0

	enemiesFaceLeftArray    !fill EnemiesMax, 0
	enemiesFaceLeft         !byte   0
	enemiesSprite           !byte   0
	enemiesScreenXChar      !byte   0
	enemiesXHigh            !byte   0
	enemiesXLow             !byte   0
	enemiesXOffsetArray     !fill EnemiesMax, 0
	enemiesXOffset          !byte   0
	enemiesY                !byte 205
}

;===============================================================================
; Macros/Subroutines

!macro GAMEENEMIES_SUBROUTINES {
	gameEnemiesInit

			ldx #0
			stx enemiesSprite
	gEILoop
			inc enemiesSprite ; x+1

			+LIBSPRITE_SETFRAME_AV           enemiesSprite, 11
			+LIBSPRITE_SETCOLOR_AV           enemiesSprite, Brown
			+LIBSPRITE_MULTICOLORENABLE_AV   enemiesSprite, True
			+LIBSPRITE_SETPRIORITY_AV        enemiesSprite, True
			+LIBSPRITE_PLAYANIM_AVVVV        enemiesSprite, 10, 12, EnemyAnimDelay, True
			
			; loop for each enemy
			inx
			cpx #EnemiesMax
			bne gEILoop

			rts

	;===============================================================================

	gameEnemiesUpdate

			ldx #0
			stx enemiesSprite
	gEULoop

			inc enemiesSprite ; x+1

			lda enemiesXCharArray,x
			sta enemiesXChar
		
			; calculate the on screen char position
			+LIBMATH_SUB8BIT_AAA enemiesXChar, screenColumn, enemiesScreenXChar

			; offset by 20 chars to handle going off left of screen without wrapping
			+LIBMATH_ADD8BIT_AVA enemiesScreenXChar, 20, enemiesScreenXChar

			lda enemiesScreenXChar
			cmp #60; allow more chars than screen width so it doesn't pop on
			bcs gEUOffscreen ; skip if offscreen

			lda #0
			sta enemiesXHigh

			+LIBSPRITE_ENABLE_AV enemiesSprite, True

			lda enemiesScreenXChar
			asl  ; multiply by 2
			asl  ; and again = *4
			asl  ; and again = *8

			bcc gEUNotHigh ; if greater than 255, carry bit is set

			ldy #1
			sty enemiesXHigh

	gEUNotHigh
			sta enemiesXLow

			+LIBMATH_ADD8BIT_AAA enemiesXLow, screenScrollXValue, enemiesXLow
	
			jsr gameEnemiesUpdatePosition

			+LIBSPRITE_SETPOSITION_AAAA enemiesSprite, enemiesXHigh, enemiesXLow, enemiesY
			
			jmp gEUNext
	gEUOffscreen
			+LIBSPRITE_ENABLE_AV enemiesSprite, False
	gEUNext

			; loop for each enemy
			inx
			cpx #EnemiesMax
			;bne gEULoop
			beq gEUFinished
			jmp gEULoop
	gEUFinished

			rts

	;===============================================================================

	gameEnemiesUpdatePosition

			lda enemiesFaceLeftArray,X
			beq gEUPMoveRight

	;gEUPMoveLeft
			lda enemiesXOffsetArray,X
			sta enemiesXOffset
			cmp #0
			beq gEUPTurnRight

			dec enemiesXOffset
			lda enemiesXOffset
			sta enemiesXOffsetArray,X
			jmp gEUPFinished
			
	gEUPMoveRight
			lda enemiesXMoveArray,X
			sta enemiesXMove
			lda enemiesXOffsetArray,X
			sta enemiesXOffset
			cmp enemiesXMove
			beq gEUPTurnLeft

			inc enemiesXOffset
			lda enemiesXOffset
			sta enemiesXOffsetArray,X
			jmp gEUPFinished
			
	gEUPTurnRight
			lda #False
			sta enemiesFaceLeftArray,X
			+LIBSPRITE_PLAYANIM_AVVVV enemiesSprite, 10, 12, EnemyAnimDelay, True
			jmp gEUPFinished

	gEUPTurnLeft
			lda #True
			sta enemiesFaceLeftArray,X
			+LIBSPRITE_PLAYANIM_AVVVV enemiesSprite, 13, 15, EnemyAnimDelay, True
			jmp gEUPFinished

	gEUPFinished

			+LIBMATH_ADD16BIT_AAVAAA enemiesXHigh, enemiesXLow, 0, enemiesXOffset, enemiesXHigh, enemiesXLow

			; reset by 20 chars to handle going off left of screen without wrapping
			; subtract 20chars x 8 pixels = 160
			+LIBMATH_SUB16BIT_AAVVAA enemiesXHigh, enemiesXLow, 0, 160, enemiesXHigh, enemiesXLow

			rts
}
