IDEAL
model small
stack 100
DATASEG
;------

	player_turn db 1	 ;1 is o, 2 is x
	game_grid db 0,0,0
			 db 0,0,0
			 db 0,0,0
	game_grid_coordinates_x dw 80,144,208,80,144,208,80,144,208
	game_grid_coordinates_y dw 34,34,34,76,76,76,116,116,116
	center_delta dw 15
	grid_row_place db ?
	grid_column_place db ?
	game_array_index dw 0
	current_sign db ?
	circle_radius dw 15
	y_value dw 0
	x_center dw 0
	y_center dw 0
	decision dw 1
	colour db 0fh 
	
	;texts
	openingTitle DB "Tic-Tac-Toe$"
	option_rules DB "2 - rules$"
	rules_title DB "RULES$"
	pressEsc DB "ppress ESC to return to menu$"
	pressSpace db "ppress SPACE to continue$"
	option_start db "1 - start$"
	option_exit db "3 - exit$"
	oWin db "O Won!$"
	xWin db "X Won!$"
	tie db "Tie!$"
	O_turn db "O's turn$"
	X_turn db "X's turn$"
	rules1 db "Two Players, X and O, take turns marking$"
	rules2 db " the spaces in a 3x3 grid.$"
	rules3 db "The player who succeeds in placing three$"
	rules4 db "of their marks in a horizontal, vertical$"
	rules5 db "or diagonal row is the winner!$"
	
CODESEG

proc draw_cross
	push di
	push cx
	
	mov ax, 2 	; hide mouse so the croser wont hide the drawing
	int 33h
	
	mov di, offset game_grid_coordinates_x 
	mov si, offset game_grid_coordinates_y
	add di, [game_array_index]
	add si, [game_array_index]
	mov cx, [di]
	mov dx, [si]
	
	mov si,30 ;si length of the diagonal (used as a counter)
	mov bh,0h ;screen number (0)
	mov ah,0ch ;change color for a single pixel
	mov al,0fh ; white color
	draw_cross_loop1: ;first diagonal line
		
		int 10h ;draws a pixel
		inc dx ;y++
		inc cx ;x++
		dec si ;counter--
		cmp si, 0 ;checking if counter = 0
		jne draw_cross_loop1	
		
	mov si, 30 ;count for second diagonal
	sub dx, 30 ;setting y coordinates
	draw_cross_loop2: ;second diagonal line
	
		int 10h ; drawing a pixel
		inc dx ; y++
		dec cx ;x--
		dec si ;counter--
		cmp si, 0 ;checking if counter = 0
		jne draw_cross_loop2

	
	pop cx	
	pop di
	mov ax, 1 ; show mouse
	int 33h
	ret
endp draw_cross
; ------
proc draw_circle

;drawing a circle using midpoint circle algorithm https://en.wikipedia.org/wiki/Midpoint_circle_algorithm

	push cx
	push dx
	push si
	push di
	push ax
	push bx
	

	mov ax, 2 	; hide mouse so the croser wont hide the drawing
	int 33h
	
	mov di, offset game_grid_coordinates_x 
	mov si, offset game_grid_coordinates_y
	add di, [game_array_index]
	add si, [game_array_index]
	mov cx, [di]
	mov dx, [si]
	add cx, [center_delta]
	add dx, [center_delta]
	mov [x_center], cx
	mov [y_center], dx
	mov [decision], 1
	mov [circle_radius], 15 ; circle radius = 15
	mov [y_value], 0	
	
drawcircle:
	mov al, [colour ];colour goes in al
	mov ah, 0ch
	
	mov cx, [circle_radius] ;Octonant 1
	add cx, [x_center] ;( [circle_radius] + [x_center],  [y_value] + [y_center])
	mov dx, [y_value]
	add dx, [y_center]
	int 10h

	mov cx, [circle_radius] ;Octonant 4
	neg cx
	add cx, [x_center] ;( -[circle_radius] + [x_center],  [y_value] + [y_center])
	int 10h
	
	mov cx, [y_value] ;Octonant 2
	add cx, [x_center] ;( [y_value] + [x_center],  [circle_radius] + [y_center])
	mov dx, [circle_radius]
	add dx, [y_center]
	int 10h

	mov cx, [y_value] ;Octonant 3
	neg cx
	add cx, [x_center] ;( -[y_value] + [x_center],  [circle_radius] + [y_center])
	int 10h

	mov cx, [circle_radius] ;Octonant 7
	add cx, [x_center] ;( [circle_radius] + [x_center],  -[y_value] + [y_center])
	mov dx, [y_value]
	neg dx
	add dx, [y_center]
	int 10h

	mov cx, [circle_radius] ;Octonant 5
	neg cx
	add cx, [x_center] ;( -[circle_radius] + [x_center],  -[y_value] + [y_center])
	int 10h

	mov cx, [y_value] ;Octonant 8
	add cx, [x_center] ;( [y_value] + [x_center],  -[circle_radius] + [y_center])
	mov dx, [circle_radius]
	neg dx
	add dx, [y_center]
	int 10h

	mov cx, [y_value] ;Octonant 6
	neg cx
	add cx, [x_center] ;( -[y_value] + [x_center],  -[circle_radius] + [y_center])
	int 10h

	inc [y_value]

	condition1:
	cmp [decision],0
	jg condition2
	mov cx, [y_value]
	mov ax, 2
	imul cx
	add cx, 1
	inc cx
	add [decision], cx
	mov bx, [y_value]
	mov dx, [circle_radius]
	cmp bx, dx
	jg endFunc
	jmp drawcircle

	condition2:
	dec [circle_radius]
	mov cx, [y_value]
	sub cx, [circle_radius]
	mov ax, 2
	imul cx
	inc cx
	add [decision], cx
	mov bx, [y_value]
	mov dx, [circle_radius]
	cmp bx, dx
	jg endFunc
	jmp drawcircle

	endFunc:

	mov ax, 1 ; show mouse
	int 33h
	
	pop bx
	pop ax
	pop di
	pop si
	pop dx
	pop cx
	
ret
ENDp draw_circle

;------
proc reset_data
	push cx
	push dx
	push si
	push di
	push ax
	push bx
	
	mov [game_array_index], 0 ;
	mov [byte ptr current_sign], 1 ;starting from player 1 (o)
	mov si, offset game_grid ;index 0 in array
	mov cx, 9 ;loop counter 
	
	reset_array: ; loop that resets every index in the game grid
	mov [byte ptr si],0
	inc si
	loop reset_array
	
	pop bx
	pop ax
	pop di
	pop si
	pop dx
	pop cx
ret
endp reset_data
;------
proc print_turn
	cmp [player_turn], 1
	je print_o_turn
	
	cmp [player_turn], 2
	je print_x_turn
	
	print_o_turn:
	;Set the cursor's location to (23,15)
	mov bh, 0 ;screen number (0)
	mov dh, 23 ;row
	mov dl, 15 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset O_turn
	mov ah, 9h
	int 21h 
	
	jmp end_print_turn
	
	print_x_turn:
	;Set the cursor's location to (23,15)
	mov bh, 0 ;screen number (0)
	mov dh, 23 ;row
	mov dl, 15 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset X_turn
	mov ah, 9h
	int 21h 
	
	jmp end_print_turn
	
	end_print_turn:
	ret
	endp print_turn
; ------
proc draw_menu 
	
	call reset_data
	call clear_screen
	
	;Set the cursor's location to (3,14)
	mov bh, 0 ;screen number (0)
	mov dh, 3 ;row
	mov dl, 14 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset openingTitle
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (5,15)
	mov bh,0 ;screen number (0)
	mov dh,5 ;row
	mov dl,15 ;column
	mov ah,2 ;set cursor position
	int 10h
	
	;printing the start option
	mov dx, offset option_start
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (7,15)
	mov bh,0 ;screen number (0)
	mov dh,7 ;row
	mov dl,15 ;column
	mov ah,2 ;set cursor position
	int 10h
	
	;printing the rules option
	mov dx, offset option_rules
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (9,15)
	mov bh,0 ;screen number (0)
	mov dh,9 ;row
	mov dl,15 ;column
	mov ah,2 ;set cursor position
	int 10h
	
	;printing the exit option
	mov dx, offset option_exit
	mov ah, 9h
	int 21h 
		
	call starting_options
ret
endp draw_menu
; ------
proc starting_options 
	mov dl,0ffh ; input request
	mov ah,6 ; reads a character from the standard input device
	int 21h
	
	cmp al,'1'
	je call_draw_grid
	
	cmp al,'2'
	je call_draw_rules

	cmp al,'3'
	je call_end_menu
	
	jmp starting_options

call_draw_grid:
	call draw_grid
	jmp starting_options

call_draw_rules:
	call draw_rules
	jmp starting_options
	
call_end_menu:
	call exit

ret
endp starting_options
; ------
proc draw_rules 
	call clear_screen
	;Set the cursor's location to (3,14)
	mov bh, 0 ;screen number (0)
	mov dh, 3 ;row
	mov dl, 14 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the openingTitle
	mov dx, offset openingTitle
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (5,16)
	mov bh, 0 ;screen number (0	
	mov dh, 5 ;row
	mov dl, 16 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the rules
	mov dx, offset rules_title
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (7,0)
	mov bh, 0 ;screen number (0)
	mov dh, 7 ;row
	mov dl, 0 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset rules1
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (9,6)
	mov bh, 0 ;screen number (0)
	mov dh, 9 ;row
	mov dl, 6 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset rules2
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (11,0)
	mov bh, 0 ;screen number (0)
	mov dh, 11 ;row
	mov dl, 0 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset rules3
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (13,0)
	mov bh, 0 ;screen number (0)
	mov dh, 13 ;row
	mov dl, 0 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset rules4
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (15,5)
	mov bh, 0 ;screen number (0)
	mov dh, 15 ;row
	mov dl, 5 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset rules5
	mov ah, 9h
	int 21h 
	
	;Set the cursor's location to (25,7)
	mov bh, 0 ;screen number (0)
	mov dh, 25 ;row
	mov dl, 7 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing Press SPACE
	mov dx, offset pressSpace
	mov ah, 9h
	int 21h 
	
	
	call WaitForSpace
	ret
endp draw_rules
; ------
proc draw_grid
	call clear_screen
	
	;Set the cursor's location to (3,14)
	mov bh, 0 ;screen number (0)
	mov dh, 3 ;row
	mov dl, 14 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset openingTitle
	mov ah, 9h
	int 21h 
	
	mov cx,64;start line at column 64
	mov dx,80;row=80
	
	horizontal_line1:

	mov ah,0ch ; ah=0ch to draw a line
	mov al,0eh ; yellow color
	int 10h ;invoke the interrupt to draw the line
	inc cx ;increment the horizontal position
	cmp cx,256 ;draw line until column 256
	
	jnz horizontal_line1

	mov cx,64;start line at column 64
	mov dx,120;row=120
	
	horizontal_line2:

	mov ah,0ch ; ah=0ch to draw a line
	mov al,0eh ; yellow color
	int 10h ; invoke the interrupt to draw the line
	inc cx ; increment the horizontal position
	cmp cx,256 ; draw line until column 256
	
	jnz horizontal_line2

	mov cx,128 ;start at column 128
	mov dx,40
	
	vertical_line1:

	mov ah,0ch
	mov al,0eh ; yellow color
	int 10h
	inc dx
	cmp dx,160 ;draw until row 160
	
	jnz vertical_line1

	mov cx,192 ;start at column 192
	mov dx,40
	
	vertical_line2:

	mov ah,0ch
	mov al,0eh ; yellow color
	int 10h
	inc dx
	cmp dx,160 ;draw until row 160
	
	jnz vertical_line2
	
	;Set the cursor's location to (25,5)
	mov bh,0 ;screen number (0)
	mov dh,25 ;row
	mov dl,5 ;column
	mov ah,2 ;set cursor position
	int 10h
	
	;printing the esc option
	mov dx, offset pressEsc
	mov ah, 9h
	int 21h 
	
	call checking_and_drawing
	
ret
endp draw_grid
; ------
proc checking_and_drawing 

jmp check_coordinates

ESCPressed: ;when esc pressed
	
	call clear_screen
	call draw_menu 
	
check_coordinates:

	call print_turn	
	call check_if_won
	in al,60h	; get input from keyboard
	cmp al,1h	; if clicked escape
	je ESCPressed
	mov ax,1h
	int 33h ;mouse visablity
	mov ax,3h 
	int 33h ;getting the mouse's state
	cmp bx, 1h ;checking if left mouse button clicked
	jne check_coordinates
	; y boundary check
	cmp dx, 40
	jb check_coordinates
	cmp dx, 160
	ja check_coordinates	
	; x boundary check
	shr cx,1 ;dividing by 2
	cmp cx, 256
	ja check_coordinates
	cmp cx, 64
	jb check_coordinates
	
	; calculate x, y indexes
	mov ax,dx
	mov bl,40 ;dividing the y of the mouse by 40
	div bl ; al is the number ah is the remainder
	sub al, 1
	mov [grid_row_place], al
	mov ax,cx
	mov bl,64 ;dividing the x of the mouse by 64
	div bl ; al is the number ah is the remainder
	sub al, 1
	mov [grid_column_place], al
	
	; calculate array index (from x and y)
	mov ax, offset game_grid
	mov al, [grid_row_place]
	mov bl, 3
	mul bl ;al*bl
	add al, [grid_column_place]
	mov bx, offset game_grid
	add bx, ax  ; game_grid[grid_index]
	
	mov al, [player_turn]
	cmp [byte ptr bx], 0
	jne check_coordinates 
	mov [bx], al ;places in the array the player turn (1/2)
	cmp [player_turn], 1
	jne Decrease
	inc [player_turn]
	jmp draw_sign
	
Decrease: ;dec player turn
	dec [player_turn]
	
draw_sign:
	mov [game_array_index], 0
	mov di, offset game_grid
	push cx
	mov cx,9 ;counter
	
	check_next_index:
		cmp [byte ptr di], 1 ; if 1
		jne after_call1 ;if not check if 2
		call draw_circle ;draw circle
		jmp after_call2
		
		after_call1:
		cmp [byte ptr di], 2 ;if 2
		jne after_call2 ;if not 2
		call draw_cross ;draw cross
		
		after_call2:
		add di, 1 ;index ++
		add [game_array_index], 2 ;adding to arr 2 because arr is word not byte
		loop check_next_index ;loop

	pop cx
	jmp check_coordinates
	ret
endp checking_and_drawing
;-------
proc clear_screen
    mov     ah, 0fh
    int     10h   
    
    mov     ah, 0
    int     10h
    
    ret
endp clear_screen
;-----------
proc WaitForSpace 

	;check if there is a a new key in buffer
	Wait_For_Space:
	in al, 60h
	cmp al, 39h
	jne Wait_For_Space

KeyPressed2:
	mov ax,13h
	int 10h
	call draw_grid
	
	ret
endp WaitForSpace
; ------
proc exit 
	;Return to text mode
	mov ah, 0
	mov al, 2
	int 10h
	mov ax, 4c00h
	int 21h
	ret
endp exit
;------
proc check_if_won

	call check_rows ;checking rows
	call check_columns ;checking columns
	call check_diagonals ;checking diagonals
	call check_tie ;checking tie
	
	ret
endp check_if_won
;------
proc check_rows

	push cx ;counter of loop
	push si ;index in array
	push di
	push ax
	jmp start_check_rows
	
	call_player_won:
		call player_won
		
	start_check_rows:
		mov si, offset game_grid ;si = game grid [0]
		mov cx,3 ;counter 3 times
		
		check_one_row:
			mov al, [byte ptr si]
			mov [current_sign], al ; al =  the current sign(1/2/0)
			cmp [current_sign],0 ;if 0 end check 
			je end_check_one_row
			inc si ; index + 1
			mov al, [byte ptr si] ;al = second place in row 
			cmp [current_sign], al ;if not equal end check
			jne end_check_one_row
			inc si ; index + 1
			mov al, [byte ptr si]; al = third place in row
			cmp [current_sign], al ;if same then won
			je call_player_won
			
		end_check_one_row:
			cmp cx,3 ;if finished first row set next starting index 
			je set_index_1
			cmp cx,2 ;if finished second row set next starting index
			je set_index_2
			set_index_1:
				mov si, offset game_grid
				add si,3 ;placing the pointer in the second row
				jmp finish_end_check_one_row
			set_index_2:
				mov si, offset game_grid
				add si,6 ;placing the pointer in the third row
				jmp finish_end_check_one_row
				
		finish_end_check_one_row:
		loop check_one_row
		
	pop ax 
	pop di
	pop si
	pop cx
ret
endp check_rows
;------
proc check_columns
	push cx ;counter of loop
	push si ;index in array
	push di
	push ax
	jmp start_check_columns
	
	call_player_won1:
		call player_won
	
	start_check_columns:
		mov si, offset game_grid
		mov cx,3 ;counter
		check_one_column:
		mov al, [byte ptr si]
		mov [current_sign], al ; al =  the current sign(1/2/0)
		cmp [current_sign],0
		je end_check_one_column
		add si, 3 ;index + 3 (next row)
		mov al, [byte ptr si] ;al = second place in column
		cmp [current_sign], al
		jne end_check_one_column
		add si,3 ; index + 3 (next row)
		mov al, [byte ptr si]; al = third place in column
		cmp [current_sign], al
		je call_player_won1
		
	end_check_one_column:
		cmp cx,3 ;if finished first column set next starting index 
		je set_index_3
		cmp cx,2 ;if finished second column set next starting index 
		je set_index_4
		
		set_index_3:
		
			mov si, offset game_grid
			add si,1 ;placing the pointer in the second column
			jmp finish_end_check_one_column
			
		set_index_4:
		
			mov si, offset game_grid
			add si,2 ;placing the pointer in the third column
			jmp finish_end_check_one_column
				
		finish_end_check_one_column:
		
		loop check_one_column
		
	pop ax 
	pop di
	pop si
	pop cx
ret
endp check_columns
;------
proc check_diagonals
	push cx ;counter of loop
	push si ;index in array
	push di
	push ax
	jmp start_check_diagonals
	
	call_player_won2:
	call player_won
	
	start_check_diagonals:
	
	mov si, offset game_grid
	mov al, [byte ptr si]
	mov [current_sign], al ; al =  the current sign(1/2/0)
	cmp [current_sign],0
	je end_check_first_diagonal
	add si, 4 ;index +4
	mov al, [byte ptr si] ;al = second place in diagonal
	cmp [current_sign], al
	jne end_check_first_diagonal
	add si, 4 ;index  +4
	mov al, [byte ptr si] ;al = third place in diagonal
	cmp [current_sign], al
	je call_player_won2
	
	end_check_first_diagonal:
	
	mov si, offset game_grid
	add si,2 ;starting from 3rd place in grid
	mov al, [byte ptr si]
	mov [current_sign], al ; al =  the current sign(1/2/0)
	cmp [current_sign],0 ;checking if sign is 0
	je end_check_diagonals
	add si,2 ;index +2 
	mov al, [byte ptr si] ;al = second place in the second diagonal
	cmp [current_sign], al 
	jne end_check_diagonals
	add si,2 ;index + 2 
	mov al, [byte ptr si] ;al = third place in the second diagonal
	cmp [current_sign], al ;checking if same sign
	je call_player_won2
	
	end_check_diagonals:
	
	pop ax 
	pop di
	pop si
	pop cx
	
ret
endp check_diagonals
;------
proc player_won
	
	call clear_screen
	jmp start_print_won_screen
	
	o_won_text:
	;Set the cursor's location to (10,16)
	mov bh, 0 ;screen number (0)
	mov dh, 10 ;row
	mov dl, 16 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset oWin
	mov ah, 9h ;output of a string
	int 21h 
	
	jmp after_print_won
	
	x_won_text:
	;Set the cursor's location to (10,16)
	mov bh, 0 ;screen number (0)
	mov dh, 10 ;row
	mov dl, 16 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset xWin
	mov ah, 9h ;output of a string
	int 21h 
	
	jmp after_print_won
	
	start_print_won_screen:
	;Set the cursor's location to (3,14)
	mov bh, 0 ;screen number (0)
	mov dh, 3 ;row
	mov dl, 14 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset openingTitle
	mov ah, 9h ;output of a string
	int 21h 
	
	;Set the cursor's location to (25,5)
	mov bh,0 ;screen number (0)
	mov dh,25 ;row
	mov dl,5 ;column
	mov ah,2 ;set cursor position
	int 10h
	
	;printing the esc option
	mov dx, offset pressEsc
	mov ah, 9h ;output of a string
	int 21h 
	
	cmp [current_sign], 1 ;if the sign is 1 then o won
	je o_won_text
	cmp [current_sign],2 ; if the sign is 2 then x won
	je x_won_text
	
	after_print_won:
	in al,60h	; get input from mouse
	cmp al,1h	; if clicked escape
	jne after_print_won
	call draw_menu

ret
endp player_won
;------
proc check_tie

	mov si, offset game_grid
	mov cx,9 ;counter 
	
	check_for_tie:
	mov al, [byte ptr si] 
	mov [current_sign], al ; al =  the current sign(1/2/0)
	cmp [current_sign],0 ;if 0 then there cant be a tie
	je no_tie_found
	inc si ;index ++
	loop check_for_tie
	
	tie_found:
	call draw_tie_screen
	
	no_tie_found: ;if not found exit proc
	
ret
endp check_tie
;------
proc draw_tie_screen
	
	call clear_screen
	
	;Set the cursor's location to (3,14)
	mov bh, 0 ;screen number (0)
	mov dh, 3 ;row
	mov dl, 14 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset openingTitle
	mov ah, 9h ;output of a string
	int 21h 
	
	;Set the cursor's location to (25,5)
	mov bh,0 ;screen number (0)
	mov dh,25 ;row
	mov dl,5 ;column
	mov ah,2 ;set cursor position
	int 10h
	
	;printing the esc option
	mov dx, offset pressEsc
	mov ah, 9h  ;output of a string
	int 21h 
	
	;Set the cursor's location to (10,18)
	mov bh, 0 ;screen number (0)
	mov dh, 10 ;row
	mov dl, 18 ;column
	mov ah, 2 ;set cursor position
	int 10h
	
	;printing the title
	mov dx, offset tie
	mov ah, 9h  ;output of a string
	int 21h 
	
	wait_for_esc:
	in al,60h	; get input from mouse
	cmp al,1h	; if clicked escape
	jne wait_for_esc
	call draw_menu
	
ret
endp draw_tie_screen
;------
start:
	mov ax, @data   ;make ds point to our DATA segment
    mov ds, ax
	mov ax,0a000h ;the offset to video memory
	mov es,ax	
	mov ax,0013h ;graphic mode
	int 10h
	
main:
	call draw_menu

END start
