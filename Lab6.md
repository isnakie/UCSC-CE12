# UCSC-CE12
Sean Sjahrial;
First Assembly Program; decimal to binary converter

; Sean Sjahrial
; 1299914
; CMPE 12
; LAB 6
; LAB Section 5 MW 8-10

	.ORIG   x3000					
GREET
	LEA	R0, HELLO	; R0 = HELLO
	PUTS
	LD	R5, int		; R5 = int for the duration
INPUT
	GETC			; Takes input
	LD	R6, Xfunc
	ADD	R1, R0, R6
	BRz	Done
	ADD 	R1, R0, #-10	; Checks if R0 = 10, which is the "enter" key
	BRz 	InDone	
	LD	R6, NEG		; Checks if negative, -45 is out of range of "#"
	ADD	R1, R0, R6	; if R0 = -45, move to "Negative" Label
	BRz	Negative
	OUT
	LD 	R1, DTB		; Loads -48 
	ADD 	R0, R0, R1	; Adds -48 to Input
	JSR	Multiplication
	AND	R1, R1, #0	; Set R1 to 0
	BR	INPUT		
Negative
	AND	R6, R6, #0	; Set R6 back to zero
	LD	R1, False
	ST	R1, FLAG	; Store 1 into FLAG
	AND	R1, R1, #0	; Sets R1 back to Zero
	OUT
	BR	INPUT
InDone
	AND	R0, R0, #0
	ADD 	R0, R0, #10	; Since we subtracted 10 from R0 to get InDone condition.
	OUT
	BR	NCheck
MASK
	LD	R1, Count	; R1 = 15
	LD	R6, I		; Set R6 to be the index (default is zero?)
	LEA	R2, MASK15	; R2 = address of MASK15
	LD 	R3, int		; R3 = the compared to number, ex. 48
	ADD	R6, R6, R2	; R6 = I + address of MASK15
Loop
	ST	R6, index	; Not sure if the following works, but it gets
	LDI	R5, index	; the value inside the address of MASK15[index]
	AND	R4, R5, R3	; Digit = int AND Mask
	BRz	Zero
	BRp	One
	BRn	One
Zero
	LEA	R0, Print0	;
	PUTS
	ADD	R6, R6, #1	; increment I
	ADD 	R1, R1, #-1	; decrement count
	BRp	Loop
	BR	HEX
One	
	LEA	R0, Print1	;
	PUTS
	ADD	R6, R6, #1	; increment I
	ADD 	R1, R1, #-1	; decrement count
	BRp	Loop
	BR	HEX
Done
	TRAP	x25		; this HALTS the program
RESET
	LD	R0, True
	LD	R1, False
	ADD	R1, R1, #15
	ST	R0, FLAG
	ST	R0, int	
	ST	R1, Count
	AND	R0, R0, #0
	AND	R1, R1, #0
	AND	R2, R2, #0
	AND	R3, R3, #0
	AND	R4, R4, #0
	AND	R5, R5, #0
	AND	R6, R6, #0
	BR	GREET
Multiplication
	AND	R1, R1, #0	; stores 10 in R1
	ADD	R1, R1, #10	; R1 = 10
	LD	R2, int		; R2 helps multiply in this case
	AND	R5, R5, #0
	ST	R0, digit	; Stores each digit of user input
	LD	R4, digit	; R4 = digit
Mult	ADD	R5, R5, R2	; int = int + int (10 times, effectively multiplying by 10)
	ADD	R1, R1, #-1	; R1 - 1
	BRp	Mult		;
	ADD	R5, R5, R4	; int = int*10 + digit
	ST	R5, int
	AND	R1, R1, #0	; reset R1
	RET
NCheck				; this should change stuff for negative inputs
	LD	R2, FLAG	
	BRz	MASK
	LD	R2, int
	NOT	R2,R2
	ADD	R2,R2,#1
	ST	R2, int 
	BR	MASK
HEX	
	LD	R1, int
	LEA	R2, HEXCK	; address of HEXCK
	LEA	R3, HEXVAL	; address of HEXVAL
	LDR	R2, R2, 1	; Load value of HEXCK+1 down
	AND	R4, R1, R2
	STR	R4, R3, 1
	LEA	R0, END
	PUTS
	BR	Done

;DIVIDE
	
	
; VARIABLE LIST----------------------------------------------------------------
FLAG	.FILL		#0 ; default is zero, but it can be 1 if negative
NEG	.FILL		#-45
DTB	.FILL		#-48
digit	.FILL		#0 ; default is zero, but stores user input per digit
int	.FILL		#0 ; default is zero, but stores whole user input
Count	.FILL		#16 ; default is zero, but stores how many digits are entered
False	.FILL		#1 ; the value for all falses
True	.FILL		#0 ; the value for all trues
Print0	.STRINGZ	"0"
Print1	.STRINGZ	"1"
Xfunc	.FILL		#-88
index	.FILL		#0
HELLO	.STRINGZ	"\nHello, please enter a decimal number or press X to exit\n"
HEXCK	.FILL		x0000 ;needed so LDR starts at right place
	.FILL		xF000
	.FILL		x0F00
	.FILL		x00F0
	.FILL		x000F

HEXVAL	.FILL		x0000
	.FILL		x0000
	.FILL		x0000
	.FILL		x0000
	.FILL		x0000

I	.FILL		x0000
MASK15	.FILL		x8000
	.FILL		x4000
	.FILL		x2000
	.FILL		x1000
	.FILL		x0800
	.FILL		x0400
	.FILL		x0200
	.FILL		x0100
	.FILL		x0080
	.FILL		x0040
	.FILL		x0020
	.FILL		x0010
	.FILL		x0008
	.FILL		x0004
	.FILL		x0002
	.FILL		x0001	
END	.STRINGZ	"\nENDED: Negative, need to finish code"
	.END
