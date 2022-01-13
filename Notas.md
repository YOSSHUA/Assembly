# Operadores en operandos
Son palabras reservadas que solo se ejecutan durante el ensamble,no
son código ejecutable como tal.

- OFFSET - Distancia en bytes desde el 0h relativo de la memoria

- TYPE - Número de bytes de los elementos que incluye la declaración
- LENGTHOF - Cuenta el número de elementos en una declaración
- SIZEOF - Tamaño en bytes de una declaración, incluyendo todos los elementos. O bien, LENGTHOF*TYPE

<br>

# Symbols 
Son etiquetas para hacer más legible el código, pero durante el ensamble el ensamblador
sustituye el símbolo por su valor. Entonces, no reservan memoria.
<br>
<br>

# Instrucciones en ensamblador

<br>
<h2><details><summary> ADD \ SUB</summary>
<p>

Opciones
~~~nasm
ADD reg,reg 
ADD reg,imm
ADD mem,reg 
ADD mem,imm
ADD reg,mem 
ADD accum,imm
~~~
	
</p>
</details></h2>


<br>
<h2><details><summary> ADC \ SBB (Add with carry \ subtract with borrow)</summary>
<p>

Same syntax as ADD \ SUB.
Opciones
~~~nasm
ADD reg,reg 
ADD reg,imm
ADD mem,reg 
ADD mem,imm
ADD reg,mem 
ADD accum,imm
~~~
	
</p>
</details></h2>


<br>
<h2><details><summary> XCHG </summary>
<p>

Exchanges the contents of the source and destination operands.
~~~nasm
XCHG reg,reg 
XCHG mem,reg
XCHG reg,mem
~~~
	
</p>
</details></h2>

<br>
<h2><details><summary> MOV </summary>
<p>

Opciones
~~~nasm
MOV reg,reg 
MOV reg,imm
MOV mem,reg 
MOV mem,imm
MOV reg,mem 
MOV mem16,segreg
MOV reg16,segreg 
MOV segreg,mem16
MOV segreg,reg16
~~~
	
</p>
</details></h2>


<br>
<h2><details><summary> MOVSX (Move with Sign-Extend) </summary>
<p>

Opciones
~~~nasm
MOVSX reg32,reg8
MOVSX reg32,reg16 
MOVSX reg32,mem16
MOVSX reg16,reg8 
MOVSX reg16,m8
	
~~~
	
</p>
</details></h2>


<br>
<h2><details><summary> MOVZX (Move with Zero-Extend) </summary>
<p>

Opciones
~~~nasm
MOVZX reg32,reg8
MOVSX reg32,reg16 
MOVSX reg32,mem16
MOVSX reg16,reg8 
MOVSX reg16,m8

~~~
	
</p>
</details></h2>


<br>
<h2><details><summary> DumpRegs (It desplays all the general registers and the flags)</summary>
<p>

~~~nasm
.CODE
CALL    DumpRegs
~~~
</p>
</details></h2>
</h2>

<br>
<h2><details><summary> DumpMem (It writes a range/block of memory to the console window in hexadecimal)</summary>
<p>
Pass in ESI the starting address of the block, in ECX the number of units or elements, and in EBX the unit size       (1 : byte, 2 : word, 4 : doubleword).

~~~nasm
MOV     ESI, OFFSET inicio
MOV     ECX, elementos
MOV     EBX, tamDeElem                          
CALL    DumpMem
~~~
</p>
</details></h2>

<br>
<h2><details><summary> WriteInt (Writes a 32-bit signed integer in the console in decimal format with a leading sign and no leading zeros.)</summary>
<p>
Pass the integer into EAX.

~~~nasm
MOV     EAX, valInt
CALL    WriteInt
CALL    Crlf ; salto de linea
~~~
</p>
</details></h2>

<br>
<h2><details><summary> WriteHex (Writes a 32-bit unsigned integer to the console window in 8-digit hexadecimal format with leading zeroes if necessary)</summary>
<p>

Pass the integer into EAX.
~~~nasm
MOV     EAX, valHex
CALL    WriteHex
CALL    Crlf
~~~
</p>
</details></h2>

<br>
<h2><details><summary> WriteString (It writes a null-terminated string to the console window.)</summary>
<p>

Pass in EDX register the string’s offset.

~~~nasm
.DATA
      line1 BYTE  “Enter the data: “, 0
 
.CODE
      MOV   EDX, OFFSET line
      CALL  WriteString
~~~
</p>
</details></h2>

<br>
<h2><details><summary> Crlf (It advances the cursor, inwindow console, to the beginning of the next line.)</summary>
<p>

~~~nasm
call    Crlf
~~~
</p>
</details></h2>

<br>
<h2><details><summary> ReadInt (Reads a 32-bit signed integer from the keyboard)</summary>
<p>

Returns the value in EAX register.
Can be typed an optional leading plus or minus sign.
It sets the Overflow flag and display an error message if the value cannot be represented as  a 32-bit signed integer (-2,147,483,648 to + 2,147,483,647)

~~~nasm
.DATA
     valInt SDWORD ?
 
.CODE
    CALL    ReadInt
    MOV     valInt, EAX
~~~
</p>
</details></h2>
	
<br>
<h2><details><summary> ReadHex (Reads a 32-bit hexadecimal integer from the keyboard)</summary>
<p>

Returns the value in EAX register.
No error checking is performed for invalid characteres.
Can use both uppercase letters and lowercase letters for the digits A through F.
~~~nasm
.DATA
     valHex  DWORD ?
 
.CODE
    CALL    ReadHex
    MOV     valHex, EAX
~~~
</p>
</details></h2>

<br>
<h2><details><summary> ReadString (It reads a string from the keyboard, stopping when the user types the ENTER key.)</summary>
<p>

Pass the buffer’s offset in EDX register.
Set ECX to the maximum number of characters that the user can type, plus 1 to save space for the terminting null byte.
It returns, in EAX, the count of the number of characters typed by the user.

~~~nasm
.DATA
      bufferR   BYTE 81 DUP(0)    ; 80 characters plus 0 (terminator)
      charCountR  DWORD ?
.CODE
      MOV       EDX, OFFSET bufferR
      MOV       ECX, 81
      CALL      ReadString
      MOV       charCountR, EAX
~~~
</p>
</details></h2>

<br>
<h2><details><summary> type PTR (Overrides the declared size of an operand, then allows the selection of some part of a variable)</summary>
<p>

It can also be used to combine elements of a smaller data type and move them into a larger operand.
	
~~~nasm
.DATA
      .DATA
      myDouble DWORD 12345678h
      myBytes BYTE 12h,34h,56h,78h

.CODE
      ; From larger to smaller
      MOV EAX, myDouble
      MOV AX, WORD PTR myDouble		; loads 5678h
      MOV WORD PTR myDouble, 4A9Bh		; saves 4A9Bh
      
      ; From smaller to larger
      MOV AX, WORD PTR [myBytes]		
      MOV AX, WORD PTR [myBytes+2]		
      MOV EAX, DWORD PTR myBytes
~~~
</p>
</details></h2>
	  

<br>
<h2><details><summary> MUL (Multiplicación sin signo)</summary>
<p>

Usa AL, AX, EAX, RAX de forma implícita para multiplicar por el parámetro que se le de a MUL.

|Multiplicando|	Multiplicador|		Producto	|
|---|---|---|
|AL|				reg/mem8|			AH:AL = AL*reg8		|
|AX|				reg/mem16|			DX:AX = AX*reg/mem16|
|EAX|				reg/mem32|			EDX:EAX = EAX*reg/mem32|

Checa la bandera de Carry después de MUL para saber si hay bit significativos en la mitad superior del producto que se guarda en AH, DX, EDX.
El producto es del doble del tamaño.
~~~nasm
.DATA
      val1 WORD 2000h
      val2 WORD 100h

.CODE

      MOV AX,val1
      MUL val2	; DX:AX = 00200000h, CF=1

      MOV EAX,12345h
      MOV EBX,1000h
      MUL EBX	; EDX:EAX = 0000000012345000h, CF=0

~~~
</p>
</details></h2>

<br>
<h2><details><summary> IMUL (Multiplicación con signo)</summary>
<p>

Usa AL, AX, EAX, RAX de forma implícita para multiplicar por el parámetro que se le de a IMUL.
	
Preserva el signo del producto usando extensión de signo en la mitad superior.
	
CF y OF están en 1 si la mitad superior del producto NO es una extensión de signo de la mitad inferior.

|Multiplicando	|Multiplicador|		Producto	|
|---|---|---|
|AL		|		reg/mem8		|	AH:AL = AL*reg8	|	
|AX		|		reg/mem16		|	DX:AX = AX*reg/mem16|
|EAX		|		reg/mem32		|	EDX:EAX = EAX*reg/mem32|
</p>
</details></h2>

<br>
<h2><details><summary> IMUL (Multiplicación con signo de dos operandos)</summary>
<p>
Preserva el signo del producto usando extensión de signo en la mitad superior.
CF y OF están en 1 si los dígitos significativos se pierden.

IMUL reg16,reg/mem16
IMUL reg16,imm8
IMUL reg16,imm16

IMUL reg32,reg/mem32
IMUL reg32,imm8
IMUL reg32,imm32

|Multiplicando	|Multiplicador	|	Producto	|
|---|---|---|
|reg16	|		reg/mem16	|		reg16 = reg16*reg/mem16/imm8/imm16|
|reg32	|		reg/mem32	|		reg32 = reg32*reg/mem32/imm32/imm32


</p>
</details></h2>

<br>
<h2><details><summary> IMUL (Multiplicación con signo de tres operandos)</summary>
<p>
El resultado se guarda en el primer operando.
	
CF y OF están en 1 si los dígitos significativos se pierden.
	
El resultado se guarda en el primer registro.

<br>
Three-operand 16-bit formats:
	
IMUL reg16, reg/mem16, imm8
	
IMUL reg16, reg/mem16, imm16

<br>
Three-operand 32-bit formats:
	
IMUL reg32, reg/mem32, imm8
	
IMUL reg32, reg/mem32, imm32



</p>
</details></h2>

<br>
<h2><details><summary> DIV (División sin signo)</summary>
<p>

Solo se da un operando que funge como el divisor.

DIV reg/mem8    
DIV reg/mem16  <br>
DIV reg/mem32  <br>
DIV reg/mem64  <br>


|Dividendo |Divisor | Cociente |Reminder|
|---|---|---|---|
|AH:AL | reg/mem8 | AL | AH|
|DX:AX | reg/mem16| AX | DX|
|EDX:EAX | reg/mem32| EAX | EDX|


Limpiar reminder, cargar el diviendo en AL, AX, EAX y el número que lo va a dividir en reg o en mem.

Todas las banderas quedan indefinidas.

Si el cociente no cabe en el destino o el divisor es 0 se interrumple el CPU y el programa se detiene.

</p>
</details></h2>

<br>
<h2><details><summary> IDIV (División con signo)</summary>
<p>

Se debe hacer la extensión de signo antes de que se haga la división. Se llena el Hight byte/word/doubleword
con el bit de signo.

Atajos:
The CBW, CWD, and CDQ instructions provide important sign-extension operations:
-	CBW (convert byte to word) extends AL into AH
- 	CWD (convert word to dword) extends AX into DX
- 	CDQ (convert dword to qword) extends EAX into EDX
- 	CQO (convert qword to oword) extends RAX into RDX

|Dividendo |Divisor | Cociente |Reminder|
|---|---|---|---|
|AH:AL | reg/mem8 | AL | AH|
|DX:AX | reg/mem16| AX | DX|
|EDX:EAX | reg/mem32| EAX | EDX|


Limpiar reminder, cargar el diviendo en AL, AX, EAX y el número que lo va a dividir en reg o en mem..

Todas las banderas quedan indefinidas.

Si el cociente no cabe en el destino o el divisor es 0 se interrumple el CPU y el programa se detiene.

</p>
</details></h2>
<br/>

<h2><details><summary> PUSH/POP</summary>
<p>

Push guarda temporalmente en el stack el valor del registro que se le pase.
Pop borra del stack lo que se guardó con push y recupera en el registro lo que se había gurdado.

~~~nasm

PUSH reg

POP reg

~~~


</p>
</details></h2>
<br/>


# Instrucciones de transferencia de control (jumps)

<h2><details><summary> JMP(Da un salto a la etiqueta especificada) </summary>
<p>

~~~nasm

.CODE
	 JMP lbl3
	 instrucR
	 instrucT
lbl3:
	...
~~~

</p>
</details></h2>
<br/>

<h2><details><summary> Jcond Based on one Flag </summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/138994603-0218aef6-5e2a-4b3d-9c70-340ada08d981.png)

</p>
</details></h2>
<br/>


<h2><details><summary> CMP </summary>
<p>

La lógica es hacer leftOp - rightOp. Afecta todas las banderas.
	
CMP reg, reg
	
CMP reg, mem
	
CMP reg, imm
	
CMP mem, reg
	
CMP mem, imm
	
~~~nasm

.CODE
	 CMP leftOp rightOp 
~~~

</p>
</details></h2>
<br/>


<h2><details><summary> Jcond para igualdad </summary>
<p>

Funcionada para signado o sin signo.
![image](https://user-images.githubusercontent.com/42878951/138995091-50d1861c-86ed-432f-953e-804d2eab3e1b.png)

	
</p>
</details></h2>
<br/>


<h2><details><summary> Jcond unsigned </summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/138995158-67edd185-faaa-4963-9250-211d19941c35.png)
	
</p>
</details></h2>
<br/>



<h2><details><summary> Jcond signed </summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/138995211-348531ae-416d-495c-b637-275eb18a525b.png)
	
</p>
</details></h2>
<br/>


<h2><details><summary> AND </summary>
<p>

Es un bitwise operator. Modifies Sign, Zero, and Parity.
	
Sintaxis: AND destination, source
	
Opciones permitidas
	
AND reg, reg
	
AND reg, mem
	
AND reg, imm
	
AND mem, reg
	
AND mem, imm

</p>
</details></h2>
<br/>

<h2><details><summary> OR </summary>
<p>

Es un bitwise operator. Modifies Sign, Zero, and Parity.
	
Sintaxis: OR destination, source
	
Opciones permitidas
	
OR reg, reg
	
OR reg, mem
	
OR reg, imm
	
OR mem, reg
	
OR mem, imm

</p>
</details></h2>
<br/>

<h2><details><summary> XOR </summary>
<p>

Es un bitwise operator. With 0 retains its value, with 1 reverses value. Modifies Sign, Zero, and Parity.
	
Sintaxis: XOR destination, source
	
Opciones permitidas
	
XOR reg, reg
	
XOR reg, mem
	
XOR reg, imm
	
XOR mem, reg
	
XOR mem, imm

</p>
</details></h2>
<br/>

<h2><details><summary> NOT </summary>
<p>

No flags are affected. Opciones:
	
NOT reg
	
NOT mem
	
</p>
</details></h2>
<br/>

<h2><details><summary> TEST (nondestructive AND)</summary>
<p>
Performs a logical AND operation that affects the flags but not the destination operand.
Modifies the Sign, Zero, and Parity flags.
	
~~~nasm
TEST reg,reg 
TEST reg,imm
TEST mem,reg 
TEST mem,imm
TEST reg,mem 
TEST accum,imm
	
~~~
	
</p>
</details></h2>
<br/>

<h2><details><summary> PUSH \ POP</summary>
<p>

~~~nasm

PUSH reg/mem16  ; POP
PUSH reg/mem32  ; POP
PUSH reg/mem64	; POP          
PUSH imm32              
PUSH imm64              
~~~
	
</p>
</details></h2>
<br/>


# MACRO

Es un bloque de código con nombre.
Durante el pre-procesamiento del ensamblador a lenguaje máquina se susyituye el nombre del macro con el fragmento de código que representa. Es una utilería para simplificar el código.

~~~nasm
;Incluir librería de macros
INCLUDE \masm32\Irvine\Macros.inc

nameMacro MACRO [arg1, arg2, ...]

ENDM
~~~

<h2><details><summary> Ejemplos </summary>
<p>


~~~nasm
;Incluir librería de macros
INCLUDE \masm32\Irvine\Macros.inc

; Macro definition
mNewLine  MACRO
	CALL CrLf
ENDM

mDumpMem MACRO address, itemsCount, itemSize
	push ebx
	push ecx
	push esi
	mov  esi,address
	mov  ecx,itemsCount
	mov  ebx,itemSize
	call DumpMem
	pop  esi
	pop  ecx
	pop  ebx
ENDM
	
mWriteStr   MACRO   str
    push EDX
    MOV EDX, OFFSET str
    CALL WriteString
    pop EDX
ENDM

mReadInt    MACRO   val
    push EAX
    CALL ReadInt
    MOV val, EAX
    pop EAX
ENDM

mWriteInt   MACRO   dest
    push EAX
    MOV EAX, dest
    CALL WriteInt
    pop EAX
ENDM

mNewLine    MACRO
    CALL Crlf
ENDM
	
.DATA
    
.CODE
    mNewLine


ENDM
~~~	
</p>
</details></h2>
<br/>




# Directivas de control de flujo condicional

<h2><details><summary> Directivas</summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/139165226-90744aaf-7038-4fe1-8e51-7e7a98f0c652.png)
	
</p>
</details></h2>
<br/>

<h2><details><summary> Operadores lógicos y relacionales</summary>
<p>

![image](https://user-images.githubusercontent.com/42878951/139165268-44330c91-a106-4476-89be-1b600648e3e4.png)

</p>
</details></h2>
<br/>


# Direccionamiento 

## Direccionamiento directo
The operand has the location to place a value, or has the content to get a value. 

## Direccionamiento indirecto
The operand has the memory address (offset) of the location to place a value, or has the memory address (offset) of the content to get a value.
- Operadores indirectos. ESI and EDI prefered registers. [reg] where reg contains an address(offset, pointer, o reference)
<details><summary> EJEMPLOS </summary>
<p>

~~~nasm
.DATA
val1 BYTE 10h,20h,30h

.CODE
MOV ESI,OFFSET val1
MOV AL,[ESI]	
INC ESI
MOV AL,[ESI]	

INC ESI
MOV AL,[ESI]
~~~

Use PTR to clarify the size attribute of a memory operand!

~~~nasm
.DATA
myCount WORD 0

.CODE
MOV ESI,OFFSET myCount
;INC [ESI]	; error: ambiguous
INC WORD PTR [ESI]	
~~~

</p>
</details>
<br/>

- Operadores indexados. 

constant [reg]       or             [constant + reg] 

where constant must represent an address/offset

and reg contains a displacement value  

<details><summary> EJEMPLOS </summary>
<p>
	
~~~nasm
.DATA
; Starting address 500h
arrayW WORD 1000h,2000h,3000h
;    index:   0,    1,    2

.CODE
	; Basic Indexed or INDEXED
	MOV ESI,0                   ; index=0, displacement=0
	MOV AX, arrayW[ESI]	 ; AX = 1000h
	MOV AX, [arrayW + ESI] 	 ; Alternative

	ADD ESI,2                   ; index=1, displacement=2
	ADD AX, [arrayW + ESI]        
	ADD AX, arrayW[ESI] 
~~~
</p>
</details>
<br/>

- Index scaling. You can scale an indirect or indexed operand to the offset of an array element. This is done by multiplying the index by the array's TYPE.

<details><summary> EJEMPLOS </summary>
<p>
	
~~~nasm
.DATA
;      index  0,  1,  2,  3,  4,  5
arrayB BYTE  10, 11, 12, 13, 14, 15
arrayW WORD  10, 11, 12, 13, 14, 15
arrayD DWORD 10, 11, 12, 13, 14, 15

.CODE
MOV ESI,4        ; index 4
MOV AL, arrayB[ESI * TYPE arrayB]    ; 14
MOV BX, arrayW[ESI * TYPE arrayW]	    ; 0014
MOV EDX, arrayD[ESI * TYPE arrayD]    ; 00000014

~~~
</p>
</details>
<br/>

# Procedures

It's a named block of statements.
Below is the basic structure of a procedure.
~~~nasm
nomPro PROC
.
.
RET    ;return instruction
nomPro ENDP
~~~

<details><summary> Ejemplos </summary>
<p>


~~~nasm

main PROC
    . . .
    CALL SumOf
    . . .
    EXIT
main ENDP 
;---------------------------------------------------------
SumOf PROC
; Calculates and returns the sum of three 32-bit integers.
; Receives: EAX, EBX, ECX, the three integers. May be
; signed or unsigned.     Like Irvine
; Returns: EAX = sum, and the status flags (Carry,
; Overflow, etc.) are changed.    Like Irvine
; Requires: nothing
;---------------------------------------------------------
ADD EAX,EBX
ADD EAX,ECX
RET
SumOf ENDP
END main


~~~	
</p>
</details>

The CALL instruction calls the procedure and pushes the offset of next intruction on the stack, after CALL.

The RET instruction returns from a procedure, pops the top of stack to get the offset into the Program Counter.

In order to pass parameters you can pass it through the stack.

When calling a PROCEDURE, you can pass arguments PUSHing them on the stack.

Inside a PROCEDURE, you can recover arguments POPing them from the stack.

Don't forget that inside the procedure the first element is the offset to the next instruction where it was called. If you pass parameters you will have to pop the offset and then your parameters, remember to push again the offset before the RET.


# Shift and Rotate

<details><summary> SHR(Logical Right Shift) </summary>
<p>

Se recorren los bits a la derecha, llenado los bits vacíos de la izquierda con 0's y el último LSB se va al CF. Si se recorren n bits es como dividir 
el número entre $2^n$
	
~~~nasm
.DATA

.CODE
SHR destination, shifts_count
SHR reg,imm8
SHR mem,imm8
SHR reg,CL
SHR mem,CL

~~~
</p>
</details>
<br/>

<details><summary> SHL(Logical Left Shift) </summary>
<p>

Se recorren los bits a la izquierda, llenado con 0's los bits vacíos de la derecha y el último MSB se va al CF. Si se recorren n bits es como multiplicar 
el número entre $2^n$
	
~~~nasm
.DATA

.CODE
SHL destination, shifts_count
SHL reg,imm8
SHL mem,imm8
SHL reg,CL
SHL mem,CL

~~~
</p>
</details>
<br/>

<details><summary> SAR (Shift Arithmetic Right) </summary>
<p>

Se recorren los bits a la derecha n veces. El último LSB se va al CF, pero
del lado izquierdo se rellena con el signo del número.
	
~~~nasm
.DATA

.CODE
SAR destination, shifts_count
SAR reg,imm8
SAR mem,imm8
SAR reg,CL
SAR mem,CL

~~~
</p>
</details>
<br/>

<details><summary> SAL (Shift Arithmetic Left) </summary>
<p>

Es idéntico a SHL.

</p>
</details>
<br/>

<details><summary> ROL (Rotate Left) </summary>
<p>

Se hace shift a la iquierda n bits. Sin embargo, el MSB es copiado al CF y al LSB. No se pierden los bits.
	
~~~nasm
.DATA

.CODE
	
MOV AL,11110000b
ROL AL,1	; AL = 11100001b

MOV DL,3Fh
ROL DL,4

~~~
</p>
</details>
<br/>

<details><summary> ROR (Rotate Right) </summary>
<p>

Se hace shift a la derecha n bits. Sin embargo, el LSB es copiado al CF y al MSB. No se pierden los bits.
	
~~~nasm
.DATA

.CODE
	
MOV AL,11110000b
ROR AL,1	; AL = 01111000b

MOV DL,3Fh
ROR DL,4	; DL = F3h

~~~
</p>
</details>
<br/>


<details><summary> RCR (Rotate Carry Right) </summary>
<p>
Shifts each bit to the right.

Copies the Carry flag to the Most significant bit.

Copies the Least significant bit to the Carry flag.

~~~nasm
.DATA

.CODE

STC	; CF = 1
MOV AH,10h	; CF= 1, AH= 00010000b
RCR AH,1	; CF= 0, AH= 10001000b

~~~

</p>
</details>
<br/>


<details><summary> RCL (Rotate Carry Left) </summary>
<p>

Shifts each bit to the left.

Copies the Carry flag to the Least significant bit.

Copies the Most significant bit to the Carry flag.

~~~nasm
.DATA

.CODE

CLC		; CF = 0
MOV BL,88h		; CF= 0, BL= 10001000b
RCL BL,1		; CF= 1, BL= 00010000b
RCL BL,1		; CF=0, BL= 00100001b

~~~

</p>
</details>
<br/>

<details><summary> SHLD(Shift Left Double) </summary>
<p>

Shifts a destination operand given a number of bits to the left.
The positions opened up by the shift are copied from the most significant bits
of a source operand. 

~~~nasm
.DATA

.CODE

SHLD destination, source, shiftscount
SHLD reg8/16/32, reg8/16/32, imm8/CL
SHLD mem8/16/32, reg8/16/32, imm8/CL

MOV AL,11100000b
MOV BL,10011101b
SHLD AL,BL,1     ; dest=AL, source=BL , AL = 11000001 , BL = BL, CF = 1 


~~~

</p>
</details>
<br/>

<details><summary> SHRD(Shift Right Double) </summary>
<p>

Shifts a destination operand given a number of bits to the right.
The positions opened up by the shift are copied from the least significant bits
of a source operand. 

~~~nasm
.DATA

.CODE

SHRD destination, source, shiftscount
SHRD reg8/16/32, reg8/16/32, imm8/CL
SHRD mem8/16/32, reg8/16/32, imm8/CL

MOV AL,11000001b
MOV BL,00011101b
SHRD AL,BL,1      ; dest=AL, source=BL, AL = 11100000, BL = same,  CF = 1


~~~

</p>
</details>
<br/>


# Floating Point Decimal(Real) Number

The representation contains three components.
For example, for - 38.7512 x 10<sup>5</sup> is represented as below 
| sign | significand | exponent |
| --- | --- | --- |
| - | 38.7512 | 5 |

## IEEE Floating-Point Binary Reals


|Type| Total bits | Sign( 1 = -, 0 = +) |  Exponent | Significand  | Approx. normalized Range |
|---| --- | --- | --- | --- | --- |
| Single Precision(short real) | 32 | 1 | 8 | 23  | 2<sup>-126</sup> to 2<sup>127</sup> |
| Double Precision(long real) | 64 | 1 | 11 | 52  | 2<sup>-1022</sup> to 2<sup>1023</sup> |
| Double Extended Precision(extended real) | 80 | 1 | 16 | 63  | 2<sup>-16382</sup> to 2<sup>16383</sup> |

In all the formats, the structure is SIGN + EXPONENT + FRACTION

where fraction is the significand, after normalize it(a single 1 appears to the left of the binary point).
The exponent is represented as Unsigned Integer; to get the biased exponent, we have to add the biggest power of the real.

![](https://github.com/YOSSHUA/Assembly/blob/main/Images/realNumEncode.png?raw=true)


# FPU Instruction set

1. Instruction mnemonics begin with letter F.

2. Second letter identifies data type of memory operand:
    - B : BCD
    - I : Binary integer
    - no letter : real numbers

<br>

Considerations for the operands:

- they could be zero, one, or two
- there aren't immediate operands (integers)
- you can't use general-purpose registers (EAX, EBX, ...)
- integers must be loaded (pushed) from memory onto the stack and converted to floating-point before being used in FPU calculations
- if a FPU instruction has two operands, one must be a FPU register

Types

![](https://github.com/YOSSHUA/Assembly/blob/main/Images/realTypes.png?raw=true)

## Procedures from Irvine library

<details><summary> ReadFloat </summary>
<p>

Reads FP value, pushes it on the FPU stack.

TOP--

ST(0) <- Read value

~~~nasm
.DATA

.CODE

CALL ReadFloat

~~~

</p>
</details>
<br/>

<details><summary> WriteFloat </summary>
<p>

Writes value from ST(0) in exponential format

~~~nasm
.DATA

.CODE

CALL WriteFloat

~~~

</p>
</details>
<br/>

<details><summary> ShowFPUStack </summary>
<p>

Display the whole content of FPU stack.

</p>
</details>
<br/>

## FPU Instructions

<details><summary> FINIT </summary>
<p>

Initializes the FPU.

</p>
</details>
<br/>

<details><summary> FLD (Load a Floating-Point Value) </summary>
<p>

Loads the operand from memory into the top of the FPU stack ST(0).

TOP -- 

ST(0) <- memory location

~~~nasm
.DATA
dblOne REAL8 234.56
dblTwo REAL8 10.1
.CODE

FLD  m32fp
FLD  m64fp
FLD  m80fp
FLD  ST(i)

FLD dblOne ; ST(0) = dblOne
FLD dblTwo ; ST(0) = dblTwo, ST(1) = dblOne

~~~

</p>
</details>
<br/>

<details><summary> FST / FSTP (Store Floating-Point Value) </summary>
<p>

Copies floating point operand from the top of the FPU stack and store it into memory.

FST

memory location / ST(i)  <- ST(0)

FSTP

memory location / ST(i)  <- ST(0)

TOP++ ; POP

~~~nasm
.DATA


.CODE

FST/FSTP  m32fp
FST/FSTP  m64fp
FST/FSTP  m80fp
FST/FSTP  ST(i)

~~~

</p>
</details>
<br/>

Other operations

![](https://github.com/YOSSHUA/Assembly/blob/main/Images/realOps.png?raw=true)



<details><summary> FADD (Floating-Point Addition) </summary>
<p>

Adds source to destination.

~~~nasm
.DATA


.CODE

FADD ; pops the FPU stack
FADD  m32fp
FADD  m64fp
FADD  ST(0), ST(i)
FADD  ST(i), ST(0)

fadd mySingle                   ; ST(0)  +=  mySingle
fadd REAL8 PTR[esi]        ; ST(0)  +=  REAL8 PTR[esi]

~~~

</p>
</details>
<br/>

<details><summary> FSUB (Floating-Point Subtract) </summary>
<p>

Subtracts source from destination.

~~~nasm
.DATA


.CODE

FSUB ; pops the FPU stack
FSUB  m32fp
FSUB  m64fp
FSUB  ST(0), ST(i)
FSUB  ST(i), ST(0)


fsub mySingle               ; ST(0)  -=  mySingle
fsub array[edi*8]         ; ST(0)  -=  array[edi*8]

~~~

</p>
</details>
<br/>

<details><summary> FILD / FIST </summary>
<p>

FILD 

Load integer. The instruction converts a 16-, 32-, 64-bit SIGNED integer source operand to double-precision floating point and loads it into ST(0).

FIST

Store Integer. Instruction converts the value in ST(0) to SIGNED integer and stores the result in the destination operand.


~~~nasm
.DATA


.CODE

FILD/FIST  m16int
FILD/FIST  m32int
FILD/FIST  m64int

~~~

</p>
</details>
<br/>

<details><summary> FCHS / FABS(Change sign / absolute sign) </summary>
<p>

Any of the following instructions need operands.

FCHS

Reverses the sign of the floating point value.

FABS

Clears the sign of the number in ST(0) to create its absolute value.

</p>
</details>
<br/>

<details><summary> FMUL / FDIV (Floating-Point Multiply / Division) </summary>
<p>

FMUL 

Multiplies source by destination, stores the product in destination.

FDIV

Divides destination by source, then pops the stack.


~~~nasm
.DATA

dblOne  REAL8  1234.56
dblTwo  REAL8  10.0
dblQuot  REAL8  ?

.CODE

FMUL/FDIV
FMUL/FDIV  m32fp
FMUL/FDIV  m64fp
FMUL/FDIV  ST(0), ST(i)
FMUL/FDIV  ST(i), ST(0)

fmul mySingle   ; ST(0)  *=  mySingle
fdiv mySingle   ; ST(0)  /=  mySingle

fld dblOne      ; load  into  ST(0),   (push dblOne)   ST(0)=1234.56
fdiv dblTwo     ; divide  ST(0)  by  dblTwo,    ST(0)=ST(0)/dblTwo=1234.56/10.0
fstp dblQuot    ; store  ST(0)  to  dblQuot,    (pop  dblQuot)   dblQuot=123.456

~~~

</p>
</details>
<br/>

<details><summary> FNSTSW </summary>
<p>

Store FPU Status Word in a 16-bit integer.

~~~nasm
.DATA

.CODE

FNSTSW AX

~~~

</p>
</details>
<br/>

## Comparing Floating-Points Values

![](https://github.com/YOSSHUA/Assembly/blob/main/Images/fcom.png?raw=true)

FCOM family compares two floating-values, affecting the flags of the FPU Status Word.
To use jconds we should pass the FPU Status Word to EFLAGS.

<details><summary> Example </summary>
<p>

Store FPU Status Word in a 16-bit integer.

~~~nasm
.DATA
        X   REAL8  1.2
        Y   REAL8  3.0
        N  DWORD  0
.CODE
    ; C++
    ; double X = 1.2;
    ; double Y = 3.0;
    ; int N = 0;
    ; if( X < Y )
    ;   N = 1;


    ; if( X < Y )
    ;      N = 1
    fld  X                  ; ST(0) = X
    FCOMP  Y            ; compare ST(0) to Y
    FNSTSW  ax          ; move FPU Status Word into AX
    SAHF                    ; copy AH into EFLAGS
    jnb  L1                ; X not < Y?  skip
        mov  N, 1           ; N = 1
    L1:


~~~

</p>
</details>
<br/>

<details><summary> FCOMI </summary>
<p>

It compares two floating-point values and sets the Zero, Parity, and Carry flags directly, in EFLAGS

~~~nasm
.DATA
    X  REAL8 1.2
        Y  REAL8 3.0
        N  DWORD 0

.CODE
   
    FCOMI  ST(0), ST(i)

    ; EXAMPLE ----
    ; if( X < Y )
    ;     N = 1
    fld  Y                          ; ST(0) = Y
    fld  X                          ; ST(0) = X
    fcomi  ST(0), ST(1)            ; compare ST(0) to ST(1)
    jnb  L1                        ; ST(0) not < ST(1)?  skip
        mov  N, 1               ; N = 1

~~~

</p>
</details>
<br/>
