	;Marian Zlateva
	; performs the Fibonacci sequence and prints it

	INCLUDE Irvine32.inc

	.386
	.model flat,stdcall
	.stack 4096
	ExitProcess proto,dwExitCode:dword
	.data
		;messages for user
		;Note: 0ah moves to next line, 0dh moves to the beggining of a line
		message1 byte "Enter an integer value for the length of the fibonachi sequence",0ah,0dh,0
		message2 byte "Enter the memory adress that you would like it stored at",0ah,0dh,0
		message3 byte "Your fibonachi sequence is:",0ah,0dh,0
		fibArray DWORD 1000 dup(0) ;an array of numbers for the fibonachi number series (I set it to 1000 just to reserve 1000 spaces in memory)
		count DWORD 47-2	;how many times the fibonachi sequence will be called (the 47 is just here for debug purposes, there is a -2 b/c the first 2 spots will be filled with 1s)

	.code
	main proc


		;gets an input from the client and sets it to the count

		mov edx, OFFSET message1	;moves the first message into the edx register so that it could be called by writestring
		call writestring	;writes the value that is in the edx register
		call readint	;reads the user's input and puts the value into the eax register
		sub eax,2 ;substracts the edx register by 2 b/c the first 2 spots in the array are already filled
		mov esi,OFFSET count	;esi now has the adress of count						
		mov [esi],eax	;count now has the value of eax (the input)


		;writes message3

		mov edx,OFFSET message3
		call writestring ;skips a line


		;getting the memory adress of fibArray

		mov esi, OFFSET fibArray	;we are putting the address of of the fibArray into the esi register
									;(esi register is usually only used as a pointer register so that's why we are putting it in here)
	

		;storing 1s in the first 2 spots of fibArray

		mov eax,1	
		call writeint	
		call writeint	;print the first 2 spots
		mov [esi],eax	;filling the first spot of the fibarray with 1
		add esi, TYPE fibArray	;moving to the second spot of the array
		mov [esi],eax	;filling the second spot of the fibarray with 1


		;generate the fibanochi sequence

		add esi, TYPE fibArray	;moving to the third spot in fibarray
		mov ecx, count	;set the ecx register to the count (ecx is automatically decrimented when it is used in a loop)
		FibanochiGeneratorLoop:	;generates the fibanochi sequence in a loop for the amount of times the client specified
			call FibanochiSequence	;calls the generateFibonacci procedure below
			mov eax,[esi]
			call writeint	;outputs the spot in the array that was just calculated
			add esi, TYPE fibArray	;increment the fibArray by 1 spot (we use type because we need to increment it by the memory it takes up)
			Loop FibanochiGeneratorLoop
		INVOKE ExitProcess,0
	main endp

	;---------------------------------------------------------
	FibanochiSequence proc
	;
	;Calculates Fibanochi Sequence
	;recives esi, the adress of the array
	;returns [esi] = sum of [esi-4]+[esi-8]
	;---------------------------------------------------------
		;a dword is 4 bytes so if we want to access the 2 previous registers we need to decrement by 4
		mov eax, [esi-4]	;assign eax to the adress stored in esi -4 (go back 1 spot in the array)
							;this will be the length of the first side

		mov ebx, [esi-8]	;assign eax to the adress stored in esi -8 (go back 2 spots in the array)
							;this will be the length of the second side
		add eax,ebx	;adds the 2 lengths together
		mov [esi], eax	;assign the memory adress to the 2 lengths added together
		ret	;return
	FibanochiSequence endp
	end main

	COMMENT !
	Enter an integer value for the length of the fibonachi sequence
	47
	Your fibonachi sequence is:
	+1+1+2+3+5+8+13+21+34+55+89+144+233+377+610+987+1597+2584+4181+6765+10946+17711+28657+46368+75025+12
	1393+196418+317811+514229+832040+1346269+2178309+3524578+5702887+9227465+14930352+24157817+39088169+
	63245986+102334155+165580141+267914296+433494437+701408733+1134903170+1836311903-1323752223

	!